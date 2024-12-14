# Setting Up Ollama with Qwen 2.5:1.5b on CentOS and API Integration

This guide will help you install Ollama, set up the Qwen 2.5:1.5b model, configure API access, and enable automatic startup using `systemd` on CentOS.

---

## 1. Prerequisites

### System Requirements
- **Operating System**: CentOS 7 or later.
- **Dependencies**:
  - Docker (if required by Ollama).
  - Sufficient disk space for model storage.
  - Adequate RAM and CPU resources for model execution.

### Install Required Tools
Ensure `curl` is installed for API testing:
```bash
sudo yum install -y curl
```

---

## 2. Install Ollama CLI

### Download and Install
1. Visit the [Ollama website](https://ollama.com) or their [GitHub releases page](https://github.com/ollama/ollama/releases).
2. Download the appropriate binary for CentOS.
3. Move the binary to a directory in your `PATH`:
   ```bash
   sudo mv ollama /usr/local/bin/
   sudo chmod +x /usr/local/bin/ollama
   ```

### Verify Installation
Run the following command to verify the installation:
```bash
ollama --version
```

---

## 3. Load the Qwen 2.5:1.5b Model

Download the model using the `pull` command:
```bash
ollama pull qwen2.5:1.5b
```

This will fetch the model and store it in Ollama's local cache.

---

## 4. Start the API Server

To run Ollama as an API server:
```bash
ollama serve
```
The server will start on `http://localhost:11434` by default.

### Test the Server
Check the server health with:
```bash
curl http://localhost:11434/health
```
You should see a response confirming the server is running.

---

## 5. Using the API

### Example Request
Send a test request to generate a response from the model:
```bash
curl -X POST http://localhost:11434/api/generate -d '{
  "model": "qwen2.5:1.5b",
  "prompt": "What is the capital of France?",
  "stream": false
}'
```

### Example Response
The API will return a JSON object like this:
```json
{
  "model": "qwen2.5:1.5b",
  "response": "The capital of France is Paris.",
  "done": true
}
```

---

## 6. Configure Automatic Startup with systemd

Create a `systemd` service file to ensure the Ollama server starts automatically on boot.

### Create Service File
1. Create a new file:
   ```bash
   sudo nano /etc/systemd/system/ollama.service
   ```
2. Add the following configuration:
   ```ini
   [Unit]
   Description=Ollama API Server
   After=network.target

   [Service]
   ExecStart=/usr/local/bin/ollama serve
   Restart=always
   User=root
   WorkingDirectory=/root

   [Install]
   WantedBy=multi-user.target
   ```

### Enable and Start the Service
1. Reload systemd to recognize the new service:
   ```bash
   sudo systemctl daemon-reload
   ```
2. Enable the service to start on boot:
   ```bash
   sudo systemctl enable ollama.service
   ```
3. Start the service:
   ```bash
   sudo systemctl start ollama.service
   ```
4. Check the status:
   ```bash
   sudo systemctl status ollama.service
   ```

---

## 7. Additional Notes

### Updating Ollama
To update the Ollama CLI, download the latest version from their [GitHub releases page](https://github.com/ollama/ollama/releases) and replace the existing binary.

### Logs
View service logs for debugging:
```bash
sudo journalctl -u ollama.service
```

### Stopping the Service
To stop the service manually:
```bash
sudo systemctl stop ollama.service
```

---

## 8. References
- [Ollama API Documentation](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Ollama GitHub Repository](https://github.com/ollama/ollama)
- [Docker Installation Guide](https://docs.docker.com/get-docker/) (if applicable)
