# Install Docker Engine on Ubuntu

### Full documentation at: https://docs.docker.com/engine/install/ubuntu/

1. Remove unofficial packages if present

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Images, containers, volumes, and networks stored in /var/lib/docker/ not automatically removed. Remove if necessary.

## Install using the apt repository

2. Update and apt via HTTPS

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
```

3. Add official docker gpg key

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

4. Setup repository

```bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

check $VERSION_CODENAME present in ```/etc/os-release``` if not use ```$UBUNTU_CODENAME```

5. Update

```bash
sudo apt-get update
```

6. Install latest docker

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

7. Verify docker running

```bash
sudo docker run hello-world
```

## Post Install steps: Manage Docker as a non-root user link

The Docker daemon binds to a Unix socket, not a TCP port. By default it's the root user that owns the Unix socket, and other users can only access it using sudo. The Docker daemon always runs as the root user.

If you don't want to preface the docker command with sudo, create a Unix group called docker and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the docker group. On some Linux distributions, the system automatically creates this group when installing Docker Engine using a package manager. In that case, there is no need for you to manually create the group.

8. Create docker group

```bash
sudo groupadd docker
```

9. Add the user to docker group

```bash
sudo usermod -aG docker $USER
```

10. Log out and log back in so that your group membership is re-evaluated. Or run following.

```bash
newgrp docker
```

11. Verify that you can run docker commands without sudo.

```bash
docker run hello-world
```

## Configure Docker to start on boot with systemd

Many modern Linux distributions use systemd to manage which services start when the system boots. On Debian and Ubuntu, the Docker service starts on boot by default. To automatically start Docker and containerd on boot for other Linux distributions using systemd, run the following commands:

12. Auto start using systemctl

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```
