# Boat Dashboard Data

This public repository is the data handoff between Adam's ChatGPT morning briefing and the ESP32 boat dashboard.

At 6:30 a.m. each day, a scheduled ChatGPT task should:

1. Research and prepare the full morning briefing shown in ChatGPT.
2. Create a shorter, strictly structured dashboard edition.
3. Update `briefing/latest.json` on the `main` branch of this repository.
4. Confirm that the GitHub write succeeded before saying that the dashboard was updated.

The Waveshare ESP32-S3-Touch-LCD-4.3B-BOX downloads the raw JSON file, validates it, caches the last valid copy locally, and displays it across the briefing screens. If downloading fails, the ESP continues showing the cached copy with an offline or stale indicator.

Raw data URL:

`https://raw.githubusercontent.com/hughesy2111/boat-dashboard-data/main/briefing/latest.json`

## Dashboard briefing screens

1. **At a Glance** — current conditions, forecast wind and gusts, tides, alerts, thunderstorm timing, and the best weather window.
2. **Day Ahead** — wind, gust, and rain timeline plus marine conditions and named-storm outlook.
3. **Morning Headlines** — five important AI/technology, US, or world-news stories. Marine and sailing stories are not included merely to fill categories.
4. **Story Detail** — tapping a headline opens its concise summary, source, publication time, and source URL/QR-code option.

Forecast values must be labelled as forecasts so they are not confused with live Orca/NMEA 2000 readings elsewhere on the dashboard.

## Public-repository privacy

Do not publish exact boat position, marina or anchorage, future family travel dates, email content, tank readings, battery readings, credentials, tokens, or other private information. Use a broad forecast region such as `Coastal Georgia` or `Brunswick area`.

## Dashboard JSON contract

The scheduled task should write valid UTF-8 JSON using this structure:

~~~json
{
  "schema_version": 1,
  "briefing_date": "2026-08-27",
  "generated_at": "2026-08-27T06:30:00-04:00",
  "timezone": "America/New_York",
  "region": "Coastal Georgia",
  "at_a_glance": {
    "condition": "Partly cloudy",
    "temperature_f": 78,
    "forecast_wind": "SW 8–12 kt",
    "forecast_gusts": "Up to 18 kt",
    "storm_timing": "Risk increases after 3 PM",
    "alert_status": "No active alerts",
    "best_window": "Now–noon",
    "confidence_10": 7,
    "recheck_at": "11:00 AM",
    "tides": [
      { "type": "high", "time": "8:42 AM", "height_ft": 7.1 },
      { "type": "low", "time": "3:06 PM", "height_ft": 0.8 }
    ]
  },
  "day_ahead": {
    "timeline": [
      { "time": "NOW", "wind_kt": 8, "gust_kt": 12, "rain_percent": 10 },
      { "time": "9 AM", "wind_kt": 10, "gust_kt": 15, "rain_percent": 10 },
      { "time": "NOON", "wind_kt": 12, "gust_kt": 18, "rain_percent": 20 },
      { "time": "3 PM", "wind_kt": 15, "gust_kt": 23, "rain_percent": 60 },
      { "time": "6 PM", "wind_kt": 11, "gust_kt": 17, "rain_percent": 40 }
    ],
    "marine_summary": "Seas 1–2 ft; visibility good; thunderstorms possible after 3 PM.",
    "storm_outlook": "No named storm currently threatens coastal Georgia."
  },
  "stories": [
    {
      "category": "AI & Technology",
      "headline": "Example headline",
      "summary": "A concise factual summary written for the dashboard.",
      "source_name": "Example source",
      "published_at": "2026-08-27T05:45:00-04:00",
      "source_url": "https://example.com/article"
    }
  ],
  "sources_checked_at": "2026-08-27T06:25:00-04:00"
}
~~~

Required limits:

- Exactly five stories.
- Headline: no more than 55 characters where reasonably possible.
- Story summary: no more than 300 characters and no more than two short paragraphs.
- `marine_summary` and `storm_outlook`: no more than 160 characters each.
- Recommendation/best-window wording: no more than 70 characters.
- Exactly five timeline points.
- Use numbers for numeric values and `null` when a reliable value is unavailable; never invent a value.
- Escape all JSON strings correctly and do not include Markdown in `latest.json`.

## Scheduled ChatGPT task prompt

Copy the following into the 6:30 a.m. scheduled task. The GitHub plugin must remain installed with write access to this repository.

~~~text
Create my daily morning briefing for 6:30 a.m. America/New_York.

Research current, authoritative information and clearly distinguish observed conditions from forecasts. Prioritize:

1. AI and technology developments.
2. Major US news.
3. Major world news.
4. Operational weather for the Brunswick/coastal Georgia area, including wind, gusts, rain and thunderstorm timing, visibility, temperature, tides with heights, coastal marine conditions, active official alerts, and confidence.
5. Tropical developments anywhere in the Atlantic or Gulf of Mexico, with special attention to any named storm that could affect Brunswick or coastal Georgia.

Select the five most important news stories overall from AI/technology, US, and world news. Do not reserve slots for marine or sailing news. Use reliable current sources, include links in the ChatGPT briefing, avoid sensationalism, and state uncertainty or source disagreement.

First, show me the complete readable briefing in ChatGPT.

Then create a compact dashboard edition matching schema_version 1 documented in the README of the public GitHub repository `hughesy2111/boat-dashboard-data`. Enforce every field, type, count, and character limit in that schema. Use America/New_York timestamps. Forecast data must be labelled as forecast. Do not invent missing values; use null. The five dashboard stories must be the same five stories selected for the readable briefing.

Privacy is mandatory because the repository is public. Do not include an exact boat position, marina or anchorage, future family travel dates, email content, tank or battery readings, credentials, tokens, or any other private information. Identify the forecast region only as `Coastal Georgia` or `Brunswick area`.

Validate the dashboard edition as strict JSON. Using the GitHub plugin, create or replace `briefing/latest.json` on the `main` branch of `hughesy2111/boat-dashboard-data`. Commit directly to `main` with the message `Morning briefing YYYY-MM-DD`, substituting the briefing date.

After writing, read `briefing/latest.json` back from GitHub and verify that it is valid JSON, has today's `briefing_date`, uses schema_version 1, and contains exactly five stories. Only say `Dashboard updated` if both the write and verification succeed. If publication fails, still show the full briefing in ChatGPT, but clearly say `Dashboard update failed` and include a concise reason.
~~~

## Repository status

`test.json` is the initial connector test. It may be removed after the scheduled briefing successfully writes and verifies `briefing/latest.json`.
