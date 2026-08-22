# Frigate Configuration

Once the Ollama host is authenticated, running and accessible from the network, Frigate can use it as its GenAI provider.

## 1. Configure the GenAI provider

In your Frigate configuration, add or replace the `genai:` section:

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

The model must support the vision capabilities required by Frigate GenAI.

## 2. API authentication

No Ollama Cloud API key is required in the Frigate configuration when Frigate is connecting to a local Ollama installation that has already been authenticated with your Ollama account.

The request path is:

```text
Frigate
   ↓
Local Ollama API
   ↓
Ollama Cloud
   ↓
Cloud model
```

The local Ollama installation handles the authenticated connection to Ollama Cloud.

## 3. Keep existing GenAI settings

Changing the provider does not require removing your existing Frigate GenAI configuration for cameras, objects or review descriptions.

For example, if GenAI object descriptions are already enabled for your cameras, leave those settings in place and change only the provider configuration.

This makes it easy to compare Ollama Cloud with another GenAI provider without redesigning the rest of your Frigate configuration.

## 4. Restart Frigate

Save the configuration and restart Frigate.

Check the startup logs for errors relating to:

- Ollama
- GenAI
- Connection refused
- Model not found
- Authentication

If Frigate starts normally, generate a new review event that will receive a GenAI description.

## 5. Verify a real event

Allow the event to complete and check the Frigate Review page.

A successful event should receive GenAI metadata including information such as:

- Title
- Scene description
- Summary
- Confidence
- Potential threat level

This confirms the complete pipeline is working:

```text
Camera
  ↓
Frigate
  ↓
Ollama host
  ↓
Ollama Cloud
  ↓
Vision model
  ↓
Frigate GenAI metadata
```

## Troubleshooting

### Connection refused

Confirm that Ollama is listening on the network:

```bash
ss -ltnp | grep 11434
```

From another machine, test:

```bash
curl http://OLLAMA_HOST_IP:11434/api/tags
```

or from Windows PowerShell:

```powershell
Invoke-RestMethod http://OLLAMA_HOST_IP:11434/api/tags
```

If the API works remotely but Frigate cannot connect, check network routing and firewall rules between the Frigate host and Ollama host.

### Model not found

On the Ollama host, check:

```bash
ollama list
```

Confirm that the model name used by Frigate matches the Ollama model name exactly.

You can test the model directly with:

```bash
ollama run YOUR_CLOUD_MODEL
```

### Frigate starts but no descriptions appear

Check that GenAI object descriptions are actually enabled in your Frigate configuration.

Also confirm that the event is one that Frigate is configured to process with GenAI.

The Ollama provider configuration only selects the AI backend. It does not by itself enable GenAI descriptions for cameras or objects.

## Example working configuration

A minimal provider configuration used during testing:

```yaml
genai:
  provider: ollama
  base_url: http://192.168.1.19:11434
  model: gemma4:cloud
```

The Ollama host was authenticated using `ollama signin`, so no Ollama Cloud API key was stored in Frigate.
