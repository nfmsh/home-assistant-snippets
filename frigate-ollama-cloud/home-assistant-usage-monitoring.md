# Home Assistant Ollama Cloud Usage Monitoring

Ollama Cloud usage can optionally be monitored from Home Assistant.

This is useful when Frigate is generating descriptions automatically because it allows session and weekly usage to be monitored without repeatedly checking the Ollama website.

## Ollama Cloud Limit Viewer

This setup uses the third-party Home Assistant integration:

**Ollama Cloud Limit Viewer**

https://github.com/vithurshanselvarajah/ha-ollama-cloud-limit-viewer

> This is a third-party project and is not part of Home Assistant, Frigate or Ollama.

The integration currently uses an authenticated Ollama browser session to retrieve account usage information.

Treat the Ollama session cookie as a credential. Do not publish it, commit it to Git or include it in screenshots.

## Installation

Add the repository to HACS as a custom integration repository:

```text
https://github.com/vithurshanselvarajah/ha-ollama-cloud-limit-viewer
```

Install the integration and restart Home Assistant if requested.

Then add **Ollama Cloud Usage** from:

```text
Settings → Devices & services → Add integration
```

Follow the integration's instructions to provide your Ollama session cookie.

## Sensors

At the time of testing, the integration provided entities for:

- Model information
- Session usage
- Session remaining
- Session reset time
- Weekly usage
- Weekly remaining
- Weekly reset time

Entity IDs may vary, so check the entities created by the integration before copying the example dashboard card below.

## Example dashboard card

The following example uses Mushroom cards and card-mod to create a compact usage display.

Replace the example entity IDs with those created by your installation.

```yaml
type: vertical-stack
cards:
  - type: custom:mushroom-template-card
    primary: Ollama Cloud
    secondary: "{{ states('sensor.ollama_main_model_info') }}"
    icon: mdi:cloud-outline
    tap_action:
      action: none
    card_mod:
      style: |
        ha-card {
          background: transparent !important;
          border: none !important;
          box-shadow: none !important;
        }
        .primary {
          font-size: 17px !important;
          font-weight: 600 !important;
        }

  - type: custom:mushroom-template-card
    primary: >-
      Session · {{ states('sensor.ollama_main_session_usage') }}% used
    secondary: >-
      {{ states('sensor.ollama_main_session_remaining') }}% remaining ·
      resets {{ states('sensor.ollama_main_session_resets_in') }}
    icon: mdi:speedometer
    tap_action:
      action: none
    card_mod:
      style: |
        ha-card {
          background: transparent !important;
          border: none !important;
          box-shadow: none !important;
        }

  - type: custom:mushroom-template-card
    primary: >-
      Weekly · {{ states('sensor.ollama_main_weekly_usage') }}% used
    secondary: >-
      {{ states('sensor.ollama_main_weekly_remaining') }}% remaining ·
      resets {{ states('sensor.ollama_main_weekly_resets_in') }}
    icon: mdi:chart-bar
    tap_action:
      action: none
    card_mod:
      style: |
        ha-card {
          background: transparent !important;
          border: none !important;
          box-shadow: none !important;
        }
```

## Dashboard card requirements

The example card requires:

- Mushroom
- card-mod

Both are available through HACS.

Neither is required for the Ollama Cloud Limit Viewer integration itself. You can display the sensors using standard Home Assistant cards instead.

## Observed behaviour

During initial testing with Frigate and `gemma4:cloud`:

- Multiple vision requests consumed only a small fraction of the displayed allowance
- Session usage reset independently of weekly usage
- The observed session window was approximately five hours
- Weekly usage continued accumulating across the session reset

These observations are included only as an example from testing.

Ollama may change its limits, accounting or reset behaviour, and different models or prompts may consume different amounts of allowance.

Do not rely on these observations as guaranteed Ollama Cloud limits.

## Security

The integration requires access to an authenticated Ollama browser session.

The session cookie should be treated like any other authentication credential:

- Do not commit it to Git
- Do not include it in YAML stored in a public repository
- Do not share it in screenshots or logs
- Revoke or replace it if it is accidentally exposed

## Related dashboard

The usage card works particularly well alongside the:

[Frigate GenAI Event Browser](../frigate-genai-event-browser/)

This provides a dashboard showing both recent Frigate GenAI results and the cloud allowance being consumed to generate them.
