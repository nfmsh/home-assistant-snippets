# Ollama Host Setup

This guide sets up a small Ubuntu Server machine to act as a local Ollama API endpoint for Ollama Cloud.

When cloud models are used, the host does not perform the model inference itself. It provides the local Ollama API used by Frigate and forwards cloud-model requests to Ollama Cloud.

## 1. Install Ubuntu Server

Install a current Ubuntu Server release on the machine.

During installation:

- Configure a network connection
- Enable OpenSSH Server
- Choose a hostname for the server
- A minimal Ubuntu Server installation is sufficient

After installation, log in locally or connect using SSH.

Update the system:

```bash
sudo apt update
sudo apt upgrade -y
```

Reboot if required:

```bash
sudo reboot
```

## 2. Install Ollama

Install Ollama using the official installation command:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

The installer creates and enables the Ollama systemd service.

Check that it is running:

```bash
systemctl status ollama --no-pager
```

You should see:

```text
Active: active (running)
```

Check the installed version:

```bash
ollama --version
```

## 3. Sign in to Ollama

Sign the server into your Ollama account:

```bash
ollama signin
```

Ollama will provide a URL to complete authentication in a web browser.

Open the URL, sign in to your Ollama account and authorise the server.

No Ollama Cloud API key needs to be manually configured when using this authentication method.

## 4. Test a cloud model

Choose a cloud model suitable for your intended workload.

For example:

```bash
ollama run gemma4:cloud
```

Ollama will prepare the cloud model and open an interactive prompt.

Enter a simple test message and confirm that the model responds.

Exit the interactive prompt when finished.

You can check which models are available to the local Ollama installation with:

```bash
ollama list
```

## 5. Allow LAN access

By default, Ollama may only listen on the local machine.

Frigate needs to reach the Ollama API across your LAN.

Create a systemd override:

```bash
sudo systemctl edit ollama
```

Add:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

Save and exit the editor.

Reload systemd and restart Ollama:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

Confirm Ollama is listening on port `11434`:

```bash
ss -ltnp | grep 11434
```

A successful result should show Ollama listening on all interfaces, for example:

```text
LISTEN 0 4096 *:11434 *:*
```

## 6. Give the host a stable IP address

Frigate will be configured to connect directly to this machine.

Use either:

- A DHCP reservation on your router, or
- A static IP configured on the server

A DHCP reservation is usually the simpler option.

The examples in this documentation use:

```text
192.168.1.19
```

Replace this with the address of your own Ollama host.

## 7. Test the API from another machine

Before configuring Frigate, verify that another device on the LAN can reach Ollama.

### Linux/macOS

```bash
curl http://OLLAMA_HOST_IP:11434/api/tags
```

### Windows PowerShell

```powershell
Invoke-RestMethod http://OLLAMA_HOST_IP:11434/api/tags
```

For example:

```powershell
Invoke-RestMethod http://192.168.1.19:11434/api/tags
```

The response should contain the cloud model you previously tested.

For example:

```text
gemma4:cloud
```

At this point the Ollama host is ready for Frigate.

## Security

`OLLAMA_HOST=0.0.0.0:11434` makes the Ollama API accessible through the server's network interfaces.

Only use this configuration on a trusted LAN.

Do **not** port-forward TCP `11434` through your router or otherwise expose the Ollama API directly to the public internet.

If your network is not trusted, use appropriate firewall rules to restrict which systems can access the Ollama API.

## Next step

Continue with the Frigate configuration to connect Frigate GenAI to the Ollama host.
