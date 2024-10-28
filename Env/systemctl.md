-  Start a Service
Starts a service immediately.

```bash
sudo systemctl start <service-name>
# Example: sudo systemctl start docker
```

- Stop a Service
Stops a running service.

```bash
sudo systemctl stop <service-name>
# Example: sudo systemctl stop docker
```

-  Restart a Service
Restarts the service, which is useful for applying changes without needing to reboot.

```bash
sudo systemctl restart <service-name>
```

- Enable a Service
Sets the service to start automatically on boot.

```bash
sudo systemctl enable <service-name>
```

- Disable a Service
Prevents the service from starting automatically on boot.

```bash
sudo systemctl disable <service-name>
```

- Check Service Status
Displays the current status of a service, showing whether it is active, inactive, or if there are any errors.

```bash
sudo systemctl status <service-name>
```

Example output for docker.service might show if Docker is running, any associated tasks, and resource usage.

- View Logs for a Service
Shows logs related to a specific service. It’s helpful for debugging.

```bash
sudo journalctl -u <service-name>
```
- List All Services
Lists the status of all services, which is useful to see which services are running or failed.

```bash
sudo systemctl list-units --type=service
```

-  Reload systemd Configuration
Reloads systemd’s configuration files, which is useful if you’ve made changes to service files.

```bash
sudo systemctl daemon-reload
```
Using systemctl, you can effectively control service states and manage systemd configurations on a Linux system.