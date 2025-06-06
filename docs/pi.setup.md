

## Connection

### On the workstation

```bash
# Generate a new SSH key:
ssh-keygen -t rsa -f ~/.ssh/rpi_bob
# Add the key to SSH agent:
ssh-add ~/.ssh/rpi_bob
```

Copy the public key to your Raspberry Pi:

```bash
ssh-copy-id -i ~/.ssh/rpi_bob.pub k33g@bob.local
```

> Optional?
Create SSH config file (~/.ssh/config):

```raw
Host bob.local
   HostName bob.local
   PreferredAuthentications publickey
   IdentityFile ~/.ssh/rpi_bob
```


## Create a Docker Remote Context (on the workstation)

```bash
docker context create \
    --docker host=ssh://k33g@bob.local \
    --description="Remote engine on bob.local" \
    bob-remote

# Then
docker context ls
docker context use bob-remote
# back to local context: docker context use desktop-linux
```

## Deploy

> make sure to use the `bob-remote` context

check `agents/.env`
```bash
DMR_BASE_URL=http://172.17.0.1:12434
MODEL_RUNNER_CHAT_MODEL=ai/qwen2.5:0.5B-F16
```


```bash
cd agents
docker compose up -d
```



## Tools

### ctop

```bash
sudo apt-get install ca-certificates curl gnupg lsb-release
curl -fsSL https://azlux.fr/repo.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/azlux-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/azlux-archive-keyring.gpg] http://packages.azlux.fr/debian \
  $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/azlux.list >/dev/null
sudo apt-get update
sudo apt-get install docker-ctop
```

### lazydocker

```bash
curl https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash
```
> restart the session