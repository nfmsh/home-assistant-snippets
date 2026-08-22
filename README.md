# Home Assistant Snippets

A collection of useful Home Assistant configurations, dashboard cards, automations and other small projects I've built for my own smart home.

These are things that may be useful to others but don't necessarily justify becoming standalone integrations or HACS projects.

## Snippets

### 📹 Frigate GenAI Event Browser

A Home Assistant dashboard card for browsing recent Frigate GenAI events.

Features include:

- Displays the latest Frigate GenAI event
- Event thumbnail, title and full AI description
- Camera and event time
- Rolling history of recent GenAI events
- Previous, Latest and Next navigation
- Automatically returns to the newest event when a new GenAI result arrives
- Uses Frigate's MQTT review events
- Works with Frigate GenAI regardless of the configured AI provider

[View the Frigate GenAI Event Browser](./frigate-genai-event-browser/)

### ☁️ Frigate GenAI with Ollama Cloud

A guide to using a separate Ollama host as a gateway between Frigate GenAI and Ollama Cloud.

Covers:

- Installing and configuring an Ollama host
- Authenticating with Ollama Cloud
- Exposing the Ollama API securely on the local network
- Connecting Frigate GenAI to Ollama
- Testing the complete inference pipeline
- Optional Ollama Cloud usage monitoring in Home Assistant

[View the Frigate GenAI with Ollama Cloud guide](./frigate-ollama-cloud/)

---

More snippets will be added as I find useful bits worth sharing.

## Requirements

Requirements vary between snippets. Each folder contains its own README with installation instructions and any required Home Assistant integrations or custom cards.

## Disclaimer

These configurations are primarily built and tested against my own Home Assistant installation. They may require adjustment for different installations, entity names or configurations.

Always review YAML before adding it to your Home Assistant configuration.
