# Steps to install docker on Ubuntu 

I have used the convenience script method.

## Step-1 Open terminal and run: 
    
```bash
curl -fsSL https://test.docker.com -o test-docker.sh
sudo sh test-docker.sh
```

## Step-2 Create the docker group and add your user:

```bash 
sudo groupadd docker
sudo usermod -aG docker $USER
```

## Step-3 Check if docker is enabled or not:

```bash
systemctl is-enabled docker
```

If this returns `disabled` then run these:

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

## Step-4 Run `hello-world` first container

```bash
docker run hello-world
```

Or 

```bash
sudo docker run hello-world
```
