# Frigate GenAI Telegram Notifications

Send Frigate GenAI review events to Telegram with an animated preview, AI-generated description, and buttons to view the associated clip or snapshot.

The automation waits for Frigate's completed `genai` review update before sending the notification. This means the GenAI metadata is already available and avoids sending an initial notification followed by a second AI update.

## Features

- Animated Frigate review preview (GIF)
- GenAI event title and description
- Camera and event time
- **View Clip** button
- **View Snapshot** button
- Optional away-only notifications using a Home Assistant `person` entity
- Handles events from multiple cameras
- No additional helpers or scripts required
- Uses a single Home Assistant automation

## Requirements

- Home Assistant
- Frigate integrated with Home Assistant
- Frigate GenAI enabled and producing GenAI review metadata
- MQTT configured for Frigate and Home Assistant
- Home Assistant Telegram Bot integration
- Home Assistant accessible via an external URL for the Clip and Snapshot buttons

## Installation

1. Copy [`automation.yaml`](automation.yaml) into a new Home Assistant automation using **Edit in YAML**.

2. Edit the configuration variables near the top:

```yaml
variables:
  chat_id: YOUR_CHAT_ID
  external_url: "https://home.example.com"
  away_only: false
  person_entity: person.your_name
```

3. Change the internal Home Assistant address used for the animated preview:

```yaml
gif_url: >
  http://HOME_ASSISTANT_IP:8123/api/frigate/notifications/{{ review_id }}/review_preview.gif
```

For example:

```yaml
gif_url: >
  http://192.168.1.100:8123/api/frigate/notifications/{{ review_id }}/review_preview.gif
```

4. Save and enable the automation.

## Telegram Chat ID

Set `chat_id` to the Telegram chat that should receive the notifications.

For example:

```yaml
chat_id: 123456789
```

Your Telegram Bot integration must already be configured in Home Assistant and able to send messages to this chat.

## Away-only notifications

By default:

```yaml
away_only: false
```

notifications are sent regardless of your Home Assistant presence state.

To send notifications only when a person is away:

```yaml
away_only: true
person_entity: person.your_name
```

When enabled, the automation checks the configured person entity and suppresses the notification while its state is `home`.

No helper is required.

## How it works

The automation listens to:

```text
frigate/reviews
```

It ignores the initial Frigate review messages and waits for:

```text
type: genai
```

The resulting payload contains both the review information and GenAI metadata.

Two different Frigate identifiers are used:

- **Review ID** — used to retrieve `review_preview.gif`
- **Detection ID** — used for the event clip and snapshot

This distinction is important. Using the review ID for the clip will result in an **Event not found** response.

The completed notification contains the animated review preview, GenAI description, and links to the original Frigate media.

## Multiple cameras

No camera names are hard-coded.

The camera is taken directly from the Frigate review event, so the same automation can handle multiple Frigate cameras.

The automation uses:

```yaml
mode: queued
max: 10
```

so events arriving close together can be processed without one replacing another.

## Notes

The notification is sent only when Frigate publishes its GenAI update. There will therefore be a short delay between the original detection and the Telegram notification while Frigate completes GenAI processing.

The GIF is fetched internally by Home Assistant and uploaded to Telegram. The Clip and Snapshot buttons use the configured external Home Assistant URL because those links are opened by the Telegram client.

## License

See the repository's root `LICENSE` file.
