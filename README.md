## dnsmasq-nginx

## TL;DR

This has a Docker Compose that runs `dnsmasq` (DNS server) + `nginx` (reverse proxy) to redirect hostnames to specific Docker containers in a local network.

## What this does

- Runs `dnsmasq` by declaring the `*.local` hostnames (for example, `subdomain1.example.local`) to a local server IP
- Runs `nginx` and redirects the same local server to different Docker containers based on the hostname.
  - For example, `grafana.local` will proxy to `http://host.docker.internal:3000`, where Grafana is running

## Prerequisites

You will need:
- Docker
- If you are running this in Ubuntu you will need to disable the `systemd-resolved` DNS server since `dnsmasq` needs to bind to port 53 and install the `resolvconf` package to keep resolving domains in Ubuntu.
  - To do this, [see this answer](https://askubuntu.com/a/1392751)

## Running

To run this:

1. Create an `dsnsmasq.conf` file like this.
```
address=/local/<your server IP>

no-resolv

server=8.8.8.8
server=8.8.4.4

# this should only be enabled for troubleshooting
# since it creates too many logs
# log-queries
```

2. Configure the `nginx.conf` appropriately to your needs

3. Run Docker Compose
```shell
docker compose up -d
```

4. Before changing the DNS server in your router manually change the DNS server in one of your devices to test to avoid breaking the devices in your network

5. If things go well change the DNS server in your router to use the DNS from `dnsmasq`. 