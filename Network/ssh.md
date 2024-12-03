# Configure ssh connections
- `ssh-keygen`: genereate public key
- put the public key into the target remote server's `~/.ssh/authorized_keys`

# Configure alias
- in `~/.ssh/config`:
```
Host <alias>
    HostName <node_ip_or_hostname>
    User <your_username>
    Port <optional_ssh_port>
    IdentityFile <path_to_id_file>
```