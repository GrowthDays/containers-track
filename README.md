# Growth Days Q3 2022

## Containairization

### Instructions

#### Access to the remote servers

Each participant has its own Ubuntu 22.01 server in order to have a common environment to follow along the process.

So, for the user `john.smith@praxent.com`, this is the command that you should run in order to be able to access the server:

```bash
$ ssh john@john-smith.growthdays22.live
```

The first time you run the previous command, you can get a message like this:

```
The authenticity of host 'john-smith.growthdays22.live (54.225.193.82)' can't be established.
ED25519 key fingerprint is SHA256:25OmDdMOBv575j/BhbcRFjUP3InR2K55pKij+wY0i+Y.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Type `yes` and hit enter.

**Recomendation**: in order to make it easier to connect to your sandbox, you could add these lines to your `ssh` configuration file, which should be located in `~/.ssh/config`:

```
Host *.growthdays22.live
    StrictHostKeyChecking accept-new
    UserKnownHostsFile /dev/null
    IdentityFile ~/.ssh/prxnt_id_rsa
    User john
```
Please not that `john` is just an example, you should use the first part (just your name) of your praxent email. Then, you should be able access your box with a simplified command:

```bash
$ ssh john-smith.growthdays22.live
```

If you're using another filename for your private key, you should set it up accordingly (using the `IdentityFile` config option).

---

http://john-smith.growthdays22.live:9000/

---

#### Let's install docker

1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```bash
$ sudo apt-get update
$ sudo apt-get install -y ca-certificates curl gnupg lsb-release
```

2. Add Docker’s official GPG key:

```bash
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

3. Use the following command to set up the repository:

```bash
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

4. Update the apt package index again, and install the latest version of Docker Engine, containerd, and Docker Compose:

```bash
$ sudo apt-get update
$ sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

5. Check if the docker daemon is running:

```bash
$ sudo service docker status
```

You should see something like this:

```
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-09-24 16:28:58 UTC; 25s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 1800 (dockerd)
      Tasks: 7
     Memory: 22.1M
     CGroup: /system.slice/docker.service
             └─1800 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.099412793Z" level=warning msg="Your kernel does not support CPU realtime scheduler"
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.099767503Z" level=warning msg="Your kernel does not support cgroup blkio weight"
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.099926116Z" level=warning msg="Your kernel does not support cgroup blkio weight_device"
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.100279926Z" level=info msg="Loading containers: start."
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.288459746Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/1>
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.366967615Z" level=info msg="Loading containers: done."
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.403225461Z" level=info msg="Docker daemon" commit=e42327a graphdriver(s)=overlay2 version=20.10.>
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.403708066Z" level=info msg="Daemon has completed initialization"
Sep 24 16:28:58 ip-172-16-40-30 systemd[1]: Started Docker Application Container Engine.
Sep 24 16:28:58 ip-172-16-40-30 dockerd[1800]: time="2022-09-24T16:28:58.442575712Z" level=info msg="API listen on /run/docker.sock"
```

#### Post installation steps

The Docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user `root` and other users can only access it using `sudo`. The Docker daemon always runs as the `root` user.

If you don’t want to preface the `docker` command with sudo, create a Unix group called `docker` and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the `docker` group.

1. Create the docker group.

```bash
$  sudo groupadd docker
```
If you get the error message _`groupadd: group 'docker' already exists`_, don't worry, just move to the next step.

2. Add your user to the docker group

```bash
$ sudo usermod -aG docker "${USER}"
```

4. Run the following command to activate the changes we just made
```bash
$ newgrp docker
```
