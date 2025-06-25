THE ACHIEVED GOAL OF THIS PROJECT IS TO INTEGRATE INTUIS (EX MULLER-INTUITIV) HEATERS IN HOME ASSISTANT (DOCKERIZED)
PREREQUIST  :
- HOME ASSISTANT (HA)
- NODE RED
- MQTT
THE RESULT IN HA IS :
- 1 DASHBOARD TO PILOT THE STATES OF ALL HEATERS, AND HOME MODE
- 1 DASHBOARD WHICH IS THE PLANNING MANAGER AS THE MOBILE APP IS NOT USER FRIENDLY ENOUGHT WHEN YOU HAVE A LOT OF ROOMS.

HERE ARE SOME USEFULL SOURCES  :
- https://dev.netatmo.com/apidocumentation/energy#documentation
- https://forum.hacf.fr/t/integration-radiateurs-intuis-connect-sans-homekit/41313 (IN FRENCH)
- https://flows.nodered.org/flow/3d7f77cef8a7c6aa4fc33e2dbf26f15e (THE ROOT OF THIS PROJECT)
- https://github.com/shun84/jeedom-plugin-mullerintuitiv/blob/master/core/api/mullerintuitivApi.php (HELPFULL TO UNDERSTAND INTUIS API)

  





# Time Planning for Home Assistant

`index.html` provides a small web interface to manage heating schedules stored in Home Assistant sensors. The page sends updates through MQTT so they can be handled in Node-RED.

## Loading the page

Place `index.html` in your Home Assistant `www` folder and open it from the sidebar (or directly in a browser). When loaded inside Home Assistant the page automatically accesses the `hass` object.

Select the planner sensor from the drop-down list at the top. Only sensors exposing a `timetable` attribute are listed.

## Editing the schedule

- Each day of the week is displayed with its time slots. Click `+` to add a slot or click an existing coloured segment to edit it.
- Set the start and end times and choose the target zone. Cross‑midnight slots are supported.
- Temperatures for each zone and room can be edited below the planner.
- Press **Enregistrer** to publish the configuration via MQTT. The payload is of the form:

```json
{
  "timetable": [ {"zone_id": 0, "m_offset": 0}, ... ],
  "zones": [ {"id": 0, "name": "Salon", "rooms_temp": [ {"room_id": "salon", "temp": 19.5} ]}, ... ],
  "away_temp": 16,
  "hg_temp": 8
}
```

The message is sent to `plannings/<planner_id>/set` with the retain flag so Node‑RED can process it.

## Data format

- **timetable** – ordered list of change points. Each entry stores the zone ID to apply from the minute offset (`m_offset`) since Monday 00:00.
- **zones** – array of zone definitions. Each zone contains an `id`, a human readable `name` and a list of `rooms_temp` describing the temperature setpoint for each room.
- **away_temp** and **hg_temp** – optional temperatures for away and frost‑protection modes.

## Multi-day editing

A future update will add the ability to select a different end day when editing a slot, making it easier to program several days at once.
