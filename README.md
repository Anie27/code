Configuration

In this section:

One-time set up

List of events, services entities and attributes created

Example automation

Optional settings

Step 1: configuration of component

Install the custom component (preferably using HACS) and then use the Configuration --> Integrations pane to search for 'Smart Irrigation'. You will need to specify the following:

Names of sensors that supply required measurements (optional). Only required in mode 2) and 3). See Measurements and Units for more information on the measurements and units expected by this component.

API Key for Open Weather Map (optional). Only required in mode 1) and 3). See Getting Open Weater Map API Key below for instructions.

Reference Evapotranspiration for all months of the year. See Getting Monthly ET values below for instructions. Note that you can specify these in inches or mm, depending on your Home Assistant settings.

Number of sprinklers in your irrigation system

Flow per spinkler in gallons per minute or liters per minute. Refer to your sprinkler's manual for this information.

Area that the sprinklers cover in square feet or m2

Multi-zone support: For irrigation systems that have multiple zones which you want to run in series (one after the other), you need to add an instance of this integration for each zone. Of course, the configuration should be done for each zone, including the area the zone covers and the sprinkler settings.

When entering any values in the configuration of this component, keep in mind that the component will expect inches, sq ft, gallons, gallons per minute, or mm, m2, liters, liters per minute respectively depending on the settings in Home Assistant (imperial vs metric system). For sensor configuration take care to make sure the unit the component expects is the same as your sensor provides.

If you want to go back and change your settings afterwards, you can either delete the instance and re-create it or edit the entity registry under config/.storage at your own risk.

Step 2: checking services, events and entities

After successful configuration, you should end up with three entities and their attributes, listed below as well as three services.

Services

For each instance of the component the following services will be available:

ServiceDescriptionsmart_irrigation.[instance]_calculate_daily_adjusted_run_timeTriggers the calculation of daily adjusted run time. Use only if you disabled automatic refresh in the options.smart_irrigation.[instance]_calculate_hourly_adjusted_run_timeTriggers the calculation of hourly adjusted run time. Use for debugging only.smart_irrigation.[instance]_enable_force_modeEnables force mode.smart_irrigation.[instance]_disable_force_modeDisables force mode.smart_irrigation.]instance]_reset_bucketResets the bucket to 0. Needs to be called after done irrigating (see below).smart_irrigation.[instance]_set_bucketSets the bucket to the provided value. Use for debugging only.

Events

The component uses a number of events internally that you do not need to pay attention to unless you need to debug things. The exception is the _start event.

EventDescription[instance]_startFires depending on daily_adjusted_run_time value and sunrise. You can listen to this event to optimize the moment you irrigate so your irrigation starts just before sunrise and is completed at sunrise. See below for examples on how to use this.[instance]_bucketUpdFired when the bucket is calculated. Happens at automatic refresh time or as a result of the reset_bucket, set_bucket or calculate_daily_adjusted_run_time service.[instance]_forceModeTogFired when the force mode is disabled or enabled. Result of calling enable_force_mode or disable_force_mode[instance]_hourlyUpdFired when the hourly adjusted run time is calculated. Happens approximately every hour and when calculate_hourly_adjusted_run_time service is called.

Entities

sensor.smart_irrigation_base_schedule_index

The number of seconds the irrigation system needs to run assuming maximum evapotranspiration and no rain / snow. This value and the attributes are static for your configuration. Attributes:

AttributeDescriptionnumber of sprinklersnumber of sprinklers in the systemflowamount of water that flows through a single sprinkler in liters or gallon per minutethroughputtotal amount of water that flows through the irrigation system in liters or gallon per minute.reference evapotranspirationthe reference evapotranspiration values provided by the user - one for each month.areathe total area the irrigation system reaches in m2 or sq ft.precipitation ratethe output of the irrigation system across the whole area in mm or inch per hourbase schedule index minutesthe value of the entity in minutes instead of secondsauto refreshindicates if automatic refresh is enabled.auto refresh timetime that automatic refresh will happen, if enabled.force mode durationduration of irrigation in force mode (seconds)
sensor.smart_irrigation_hourly_adjusted_run_time

The adjusted run time in seconds to compensate for any net moisture losses that are not compensated by rain. Updated approx. every 60 minutes. In contrast to the daily adjusted run time, no lead time is added and no capping to any maximum is applied. Attributes:

AttributeDescriptionrainthe predicted (when using Open Weather Map) or cumulative daily (when using a sensor) rainfall in mm or inchsnowthe predicted (when using Open Weather Map, will be 0 when using a sensor) snowfall in mm or inchprecipitationthe total precipitation (which is the sum of rain and snow in mm or inch)evapotranspirationthe expected evapotranspirationnetto precipitationthe net evapotranspiration in mm or inch, negative values mean more moisture is lost than gets added by rain/snow, while positive values mean more moisture is added by rain/snow than what evaporates, equal to precipitation - evapotranspirationwater budgetpercentage of expected evapotranspiration vs peak evapotranspirationadjusted run time minutesadjusted run time in minutes instead of seconds.

sensor.smart_irrigation_daily_adjusted_run_time

The adjusted run time in seconds to compensate for any net moisture lost. Updated every day at 11:00 PM / 23:00 hours local time. Use this value for your automation (see step 3, below). Attributes:

AttributeDescriptionwater budgetpercentage of net precipitation / base schedule indexbucketrunning total of net precipitation. Negative values mean that irrigation is required. Positive values mean that more moisture was added than has evaporated yet, so irrigation is not required. Should be reset to 0 after each irrigation, using the smart_irrigation.reset_bucket servicelead_timetime in seconds to add to any irrigation. Very useful if your system needs to handle another task first, such as building up pressure.maximum_durationmaximum duration in seconds for any irrigation, including any lead_time.adjusted run time minutesadjusted run time in minutes instead of seconds.

creating automation

Since this component does not interface with your irrigation system directly, you will need to use the data it outputs to create an automation that will start and stop your irrigation system for you. This way you can use this custom component with any irrigation system you might have, regardless of how that interfaces with Home Assistant. In order for this to work correctly, you should base your automation on the value of sensor.smart_irrigation_daily_adjusted_run_time as long as you run your automation after it was updated (11:00 PM / 23:00 hours local time). If that value is above 0 it is time to irrigate. Note that the value is the run time in seconds. Also, after irrigation, you need to call the smart_irrigation.reset_bucket service to reset the net irrigation tracking to 0.

Example automation 1: one valve, potentially daily irrigation

Here is an example automation that runs when the smart_irrigation_start event is fired. It checks if sensor.smart_irrigation_daily_adjusted_run_time is above 0 and if it is it turns on switch.irrigation_tap1, waits the number of seconds as indicated by sensor.smart_irrigation_daily_adjusted_run_time and then turns off switch.irrigation_tap1. Finally, it resets the bucket by calling the smart_irrigation.reset_bucket service. If you have multiple instances you will need to adjust the event, entities and service names accordingly.
alias: Smart Irrigation description: 'Start Smart Irrigation at 06:00 and run it only if the adjusted_run_time is >0 and run it for precisely that many seconds' trigger: - 
event_data: {} 
event_type: alias: 
Smart Irrigation description: 'Start Smart Irrigation at 06:00 and run it only if the adjusted_run_time is >0 and run it for precisely that many seconds' 
trigger: - event_data: {} 
event_type: smart_irrigation_start platform: 
event condition: - above: '0' 
condition: numeric_state entity_id: 
sensor.smart_irrigation_daily_adjusted_run_time action: - 
data: {} 
entity_id: switch.irrigation_tap1 service: switch.turn_on - 
delay: seconds: 
'{{states("sensor.smart_irrigation_daily_adjusted_run_time")}}' - 
data: {} 
entity_id: switch.irrigation_tap1 service: 
switch.turn_off - 
data: {} 
service: smart_irrigation.reset_bucket
configuring optional settings
After setting up the component, you can use the options flow to configure the following:

OptionDescriptionDefaultLead timeTime in seconds to add to any irrigation. Very useful if your system needs to handle another task first, such as building up pressure.0Change percentagePercentage to change adjusted run time by. For example, you want to run 80% of the calculated adjusted run time, enter 80 here. Or, if you want to run 150% of the calculated adjusted run time, enter 150.100Maximum durationMaximum duration in seconds for any irrigation, including any lead_time. -1 means no maximum.-1Show unitsIf enabled, attributes values will show units. By default units will be hidden for attribute values.FalseAutomatic refreshBy default, automatic refresh is enabled. Disabling it will require the user to call smart_irrigation.calculate_daily_adjusted_run_time manually.TrueAutomatic refresh timeSpecifies when to do the automatic refresh if enabled.23:00 
Initial update delayDelay before first sensor update after reboot. This is useful if using sensors that do not have a status right after reboot.30CoastalIf the location you are tracking is situated on or adjacent to coast of a large land mass or anywhere else where air masses are influenced by a nearby water body, enable this setting.FalseSolar Radiation calculationFrom v0.0.50 onwards, the component estimates solar radiation using temperature, which seems to be more accurate. If for whatever reason you wanted to revert back to the pre v0.0.50 behavior (which used a estimation of sun hours) disable this.True
