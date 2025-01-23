# File transfer
- `tar`: `-3`: level of compression, the smaller the faster, the larger the more compact
`tar c pool/ | zstd -3 -v > pool.tar.zst`

- `rsync -avz --partial --progress username@remote_host:/path/to/remote/file /path/to/local/destination`