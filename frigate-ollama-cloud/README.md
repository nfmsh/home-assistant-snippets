# Frigate GenAI with Ollama Cloud

Use a separate Ollama host as a gateway between Frigate and Ollama Cloud for generative AI object descriptions.

This setup allows Frigate to use Ollama Cloud models without requiring a powerful local GPU. Frigate sends GenAI requests to the local Ollama API, and Ollama handles authentication and cloud inference.

## Architecture

```text
Frigate
   │
   │ HTTP
   ▼
Ollama host :11434
   │
   │ authenticated Ollama connection
   ▼
Ollama Cloud
   │
   ▼
Vision model
```

The Ollama host does not perform the model inference when a cloud model is used. It acts as the local API endpoint and authenticated gateway to Ollama Cloud.

## What this guide covers

- Installing Ollama on a separate Ubuntu Server host
- Signing the Ollama host into an Ollama account
- Running and testing a cloud model
- Exposing the Ollama API to the local network
- Connecting Frigate GenAI to Ollama
- Testing the complete Frigate → Ollama → Cloud pipeline
- Optional Ollama Cloud usage monitoring in Home Assistant

## Requirements

- Frigate with GenAI support
- A Linux host for Ollama
- Network connectivity between Frigate and the Ollama host
- An Ollama account
- A vision-capable Ollama Cloud model

The Ollama machine does not require a powerful GPU when using cloud models because inference is performed by Ollama Cloud.

## Example configuration

The final Frigate configuration is very small:

```yaml
genai:
  provider: ollama
  base_url: http://OLLAMA_HOST_IP:11434
  model: YOUR_CLOUD_MODEL
```

For example:

```yaml
genai:
  provider: ollama
  base_url: http://192.168.1.19:11434
  model: gemma4:cloud
```

Replace the IP address and model with those appropriate for your installation.

> Do not blindly copy the example IP address. Give your Ollama host a stable address or DHCP reservation on your own network.

## Documentation

Follow the guides in this order:

1. [Ollama Host Setup](ollama-host-setup.md)  
   Install Ollama, authenticate with Ollama Cloud, configure LAN access and test the API.

2. [Frigate Configuration](frigate-configuration.md)  
   Configure Frigate to use the local Ollama host as its GenAI provider.

3. [Testing](testing.md)  
   Verify each stage of the Frigate → Ollama → Ollama Cloud pipeline.

4. [Home Assistant Usage Monitoring](home-assistant-usage-monitoring.md) *(optional)*  
   Monitor Ollama Cloud session and weekly usage from Home Assistant.
## Security

Exposing Ollama on your LAN makes its API available to other devices that can reach the host.

Only expose the service on networks you trust. Do not expose port `11434` directly to the public internet.

No Ollama Cloud API key is required in Frigate when Frigate connects to a local Ollama installation that has already been signed into Ollama.

## Related snippet

For displaying and browsing Frigate GenAI results in Home Assistant, see the [Frigate GenAI Event Browser](../frigate-genai-event-browser/).

## References

- [Frigate Generative AI configuration](https://docs.frigate.video/configuration/genai/genai_config/)
- [Ollama Cloud documentation](https://docs.ollama.com/cloud)
- [Ollama authentication documentation](https://docs.ollama.com/api/authentication)

## Disclaimer

This documents a working home installation rather than an officially supported reference architecture.

Review all commands and network configuration before applying them to your own system.
