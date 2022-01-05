# Usecase

Turn your HVAC (heater in this instance) on one hour before your wakeup alarmclock goes off.

# Requirements

- [Home Assistant](https://www.home-assistant.io/)
- [Home Assistant mobile app](https://play.google.com/store/apps/details?id=io.homeassistant.companion.android)
  - Enable [next alarm sensor](https://companion.home-assistant.io/docs/core/sensors/#next-alarm-sensor)
- A heater ([Mill](https://www.home-assistant.io/integrations/mill/) in my case)

_P.S. This is only available for Android for now. Your experience on iOS might be different, not sure what sensors you're able to get on iOS._

# Example

Lets say I set my alarm to 06:00 (24-hour clock), then the template would shift the time by the number of seconds defined (3600) which in the example is one hour. So the heater should then turn on at 05:00. In `weekday_routine_working_from_home.yaml` the conditions are used to evaluate if the heater is needed at all to turn on.
The first condition checks if it's a working day, if this is false then it's the weekend when you might have other routines.
The second condition checks if you have set the drop-down for vacation. This is just an override that you can use to disable automations you normally use when not on vacation.
The third condition checks if you're at home, no point in turning the heater on if you're not at home.
The fourth condition checks if you're working from home or if you are planning to work from the office. This is a drop-down selection.
The fifth and last condition checks the season, normally theres no point in turning the heater on during summer.

If all the condtions are true then the heater will be set to 22 degreeces (Celsius for my setup, this might differ if you're using this with Fahrenheit).

# Multiple alarmclocks

If you don't want to make this dependent on just one alarmclock you could add several to the value_template in the automation like this:

```
    value_template: >
      {% set a = as_timestamp(states('sensor.YOUR_MOBILE_PHONE_next_alarm')) - 3600 %}
      {% set b = as_timestamp(states('sensor.YOUR_SECOND_MOBILE_PHONE_next_alarm')) - 3600 %}
      {% set c = as_timestamp(states('sensor.YOUR_THIRD_MOBILE_PHONE_next_alarm')) - 3600 %}
      {% set d = as_timestamp(states('sensor.JUST_KEEP_ADDING_MOBILE_PHONE_next_alarm')) - 3600 %}
      {{ now().timestamp() | int in [a, b, c, d] }}
```

Then the earliest alarmclock triggers your heater to turn on. For easier logic handling you might want to split the automation up in several automations that triggers eachother.
