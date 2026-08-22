# Testing the Ollama Cloud Pipeline

Use these checks to verify each stage independently before relying on Frigate GenAI.

## 1. Check the Ollama service

On the Ollama host:

```bash
systemctl status ollama --no-pager
```

The service should report:

```text
Active: active (running)
```

Check the installed version:

```bash
ollama --version
```

## 2. Check the listening address

```bash
ss -ltnp | grep 11434
```

For LAN access, Ollama should be listening on more than just `127.0.0.1`.

For example:

```text
LISTEN 0 4096 *:11434 *:*
```

## 3. Check the cloud model locally

On the Ollama host:

```bash
ollama run YOUR_CLOUD_MODEL
```

For example:

```bash
ollama run gemma4:cloud
```

Send a simple prompt and confirm that the model responds.

## 4. Check the API across the LAN

From another machine:

### Linux/macOS

```bash
curl http://OLLAMA_HOST_IP:11434/api/tags
```

### Windows PowerShell

```powershell
Invoke-RestMethod http://OLLAMA_HOST_IP:11434/api/tags
```

The response should include the cloud model.

This proves:

```text
LAN device → Ollama host
```

is working before Frigate is involved.

## 5. Restart Frigate

After configuring the Ollama provider, restart Frigate and inspect the startup logs.

Look for errors relating to:

```text
ollama
genai
connection refused
model not found
authentication
```

A clean startup confirms that Frigate has accepted the configuration, but a real GenAI event is still required to test inference.

## 6. Generate a real Frigate event

Trigger an event that is configured to receive a GenAI description.

Allow the review event to complete.

Open the event in Frigate and check that GenAI metadata has been generated.

Depending on the Frigate version and configuration, this may include:

- Title
- Scene description
- Summary
- Confidence
- Potential threat level

This proves the complete path:

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
Frigate GenAI result
```

## 7. Optional Home Assistant checks

If Ollama Cloud usage monitoring has been configured in Home Assistant, check whether the request count or usage sensors change after the test event.

If using the Frigate GenAI Event Browser, the completed GenAI event should also appear there.

## Troubleshooting by stage

If the local `ollama run` test fails, investigate Ollama authentication or the selected cloud model.

If the local test works but `/api/tags` cannot be reached from another machine, investigate `OLLAMA_HOST`, the firewall or network connectivity.

If the remote API works but Frigate cannot connect, investigate connectivity between the Frigate host and Ollama host and confirm the `base_url`.

If Frigate connects but no GenAI result is generated, check the Frigate GenAI configuration and confirm that the event is eligible for GenAI processing.

Testing each layer separately makes it much easier to identify where a failure occurs.
