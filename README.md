# Foundry using Tailscale for Remote Access

This application allows for remote access to Foundry for players and the game
master. Here's what it aims accomplish:

- Access to Foundry without port forwarding or publicly exposing the Foundry host
- Easy setup and access for players

## Tech

> :warning: Please note that I am using a Debian host to run this;
> however, regardless of the operating system docker-compose should take away
> most compatiblity issues.

This application uses two images;
[felddy/foundryvtt](https://github.com/felddy/foundryvtt-docker) to serve
foundry and [tailscale/tailscale](https://github.com/tailscale/tailscale) as an encrypted tunnel.

### Pre-Reqs

- Docker (version ~> 26.1.5)
- Docker Compose (version ~> 1.29.2)
- FoundryVTT active license
- Tailscale account
- Tailscale MagicDNS and HTTPS Certificates enabled (settings are in the admin console [here](https://login.tailscale.com/admin/dns)

### Installation

#### Setting up the host for docker

```sh
sudo apt update
sudo apt install docker.io docker-compose git
git clone https://github.com/crag-h4k/ts-foundryvtt
sudo groupadd --gid 421 foundry
sudo useradd -u 421 -g foundry -M foundry
sudo usermod -a -G docker,foundry $USER
touch .env
```

#### Tailscale OAuth Client Setup and Funnel Policy

TODO - add  instructions for OAuth Client setup

Add the following to your [Tailscale Access Control policy](https://login.tailscale.com/admin/acls/file):

```hujson
    "tagOwners": {
        "tag:container": ["autogroup:admin"],
    },
    "nodeAttrs": [
        {
            // Funnel policy, which lets tailnet members control Funnel
            // for their own devices.
            // Learn more at https://tailscale.com/kb/1223/tailscale-funnel/
            "target": ["autogroup:member"],
            "attr":   ["funnel"],
        },
    ],
```

#### Secrets

Fill out the `.env` file with the appropriate secrets below:

```sh
TS_AUTHKEY=""
FOUNDRY_PASSWORD=""
FOUNDRY_USERNAME=""
FOUNDRY_ADMIN_KEY=""
FOUNDRY_LICENSE_KEY=""
```

## Running the App

```sh
docker-compose up
```

Useful command for debugging

```sh
docker-compose up || docker-compose rm -fsv
```
