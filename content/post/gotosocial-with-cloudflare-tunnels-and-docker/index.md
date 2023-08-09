---
title: "Gotosocial With Cloudflare Tunnels and Docker"
description: Getting started with GoToSocial
date: 2023-08-09T14:33:33+05:30
image: 002.jpg
math: 
license: 
hidden: false
comments: true
categories:
  - Technical
tags:
  - docker
  - microblogging
  - golang
  - Self-Hosting
---

## What is GoToSocial

> GoToSocial provides a lightweight, customizable, and safety-focused entryway into the [Fediverse](https://en.wikipedia.org/wiki/Fediverse), and is comparable to (but distinct from) existing projects such as [Mastodon](https://joinmastodon.org/), [Pleroma](https://pleroma.social/), [Friendica](https://friendi.ca), and [PixelFed](https://pixelfed.org/).  _-[GitHub - gotosocial](https://github.com/superseriousbusiness/gotosocial#what-is-gotosocial)_

In simple words it is a simple micro-blogging application with connectivity to other fediverse applications.

It is also compatible with mastodon API.

![GoToSocial](https://i.imgur.com/vZQ3xlR.png#center)

### GoToSocial over Mastodon

There are quite a few reasons to run _GoToSocial_ instance.

1. It is simpler to run and very easy to setup.

2. Personal instances don't need a big heavy mastodon server.

3. [Customizable CSS](https://github.com/superseriousbusiness/gotosocial/blob/main/docs/user_guide/custom_css.md) for account pages.

### A few cons

1. It is still under development and is not feature complete with mastodon, plemora and other fediverse microblogging programs.

2. It doesn't have an integrated client front-end, basically you cannot post directly from the site. Personally I like this way more, since I find 3rd party clients much better.
   
   > I use [Tuba](https://tuba.geopjr.dev/) for desktop and [Tusky](https://tusky.app/) for my phone.

3. 3rd party apps still have bugs, with _GoToSocial_ and sometimes bug out.
    
   > [Fedilab](https://febilab.app) just keeps on crashing for me.

---

## Docker

> **Docker** is a set of [platform as a service](https://en.wikipedia.org/wiki/Platform_as_a_service "Platform as a service") (PaaS) products that use [OS-level virtualization](https://en.wikipedia.org/wiki/OS-level_virtualization "OS-level virtualization") to deliver software in packages called *[containers](https://en.wikipedia.org/wiki/Container_(virtualization) "Container (virtualization)")*.[[5]](https://en.wikipedia.org/wiki/Docker_(software)#cite_note-SYS-CON_Media-5) 
> The service has both free and premium tiers. The software that hosts the containers is called **Docker Engine**.[[6]](https://en.wikipedia.org/wiki/Docker_(software)#cite_note-what-is-a-container-6) It was first started in 2013 and is developed by [Docker, Inc.](https://en.wikipedia.org/wiki/Docker,_Inc. "Docker, Inc.")[[7]](https://en.wikipedia.org/wiki/Docker_(software)#cite_note-os4u-7) ~ [Docker (software) - Wikipedia](https://en.wikipedia.org/wiki/Docker_(software))

It containerizes apps and makes them easier to deploy and maintain. It is the most beautiful thing ever. Once you use it, you want everything to be a docker app. So simple so elegant.

Get Started here: [Overview | Docker Documentation](https://docs.docker.com/get-started/)

---

## A bit about Cloudflare Tunnels

A good way to describe it for **my purpose** would be a cornered man's port-forwarding. They do have their advantages in terms of security and ease of use and security compared to normal port forwarding.

A cloudflare tunnel, routes a service, without needing port-forwarding or a publicly routable IP address. You can use it with _CGNAT ip addresses!._

There are other more complex and desirable features that cloudflare provides with their cloudflare tunnels, you should check it out. Over the years cloudflare is one of the companies, I've truly had a good experience with.

There are other providers and self hosting options for this purpose available, I just use cloudflare because they are my domain registrar. The process for other providers should be similar. Their UI is also a joy to use albeit a bit slow.

> I use it at home because my ISP blocks port-forwarding and for my Oracle cloud instance. Oracle provides a free tier for hosting cloud instances, but the ways in which they have scarred me.... by using their god awful UI, laggy ssh sessions which kill themselves mid command, I could simply use an `A` name record to point to the oracle provided ip-address, but that is giving _Oracle_ too much power.
> 
> > My two cents about using _Oracle_:
> > 
> > 1. Don't.
> > 
> > 2. If there is no other choice, use as less of _Oracle_ as possible.
> > 
> > _I applied to their **pay as you go** plan so that they don't just delete my free instance, for **"verification"** they deducted 100 USD from my account, that scared the ever living shit out of me, until I got the message 1 minute later from my bank, that it was reversed. At least give a warning saying you will be checking for 100 USD, by deducting and refunding it._
> > 
> > I have since terminated my _Oracle Cloud Account_.

---

## Checklist before starting

- A device with docker installed. (If the device is a windows machine, change the bash command to however they suit you, I have no idea how it works.)

- Internet connection.

- Domain name to point to. If using cloudflare ensure the domain is available at cloudflare. For other services, do whatever they say for you having the needed rights for pointing a domain name.

---

## Instructions

### Step 1: Installing GoToSocial

1. On your machine create directory `gotosocial` and sub directory `data` and file `docker-compose.yaml`:
   
   > Alternatively: follow the [official docs](https://docs.gotosocial.org/en/latest/getting_started/installation/container/) and edit `docker-compose.yml` accordingly in next step.
   
   ```bash
   mkdir -p ~/gotosocial/data
   cd gotosocial
   touch docker-compose.yaml
   ```

2. Paste the following in your `docker-compose.yaml`:
   
   Edit the following parameters:
   
   1. `GTS_HOST`
   
   2. `GTS_ACCOUNT_DOMAIN`, remove if you do not want account delegation. Having is makes your handle as `@handle@example.com` instead of `@handle@gts.example.com`.
   
   3. `path/to/data` should be the complete path, do not use `~` like `~/gotosocial/data` , it errors out and the container keeps restarting. [Read more about it here.](https://github.com/superseriousbusiness/gotosocial/issues/476)
   
   ```yaml
   version: "3.3"
   
   services:
     gotosocial:
       image: superseriousbusiness/gotosocial:latest
       container_name: gotosocial
       user: 1000:1000
       hostname: gts.docker # If you change this remember the hostname
       networks:
         - gotosocial
       environment:
         GTS_HOST: gts.example.com # Change Me
         GTS_ACCOUNT_DOMAIN: example.com # Change Me
         GTS_DB_TYPE: sqlite
         GTS_DB_ADDRESS: /gotosocial/storage/sqlite.db
         GTS_LETSENCRYPT_ENABLED: "false"
         GTS_LETSENCRYPT_EMAIL_ADDRESS: ""
       volumes:
         - /path/to/data:/gotosocial/storage #Change path
       restart: "always"
   
   networks:
     gotosocial:
       ipam:
         driver: default
   ```
   
   4. Now simply `docker-compose up -d` or `docker compose up -d`.
   
   5. Check if `gotosocial` is working correctly by running `docker logs -f gotosocial`. If it is running correctly, you should get similar message:
      
      ```bash
      time=2022-04-19T09:48:35Z level=info msg=connected to SQLITE database
      time=2022-04-19T09:48:35Z level=info msg=MIGRATED DATABASE TO group #1 (20211113114307, 20220214175650, 20220305130328, 20220315160814) func=doMigration
      time=2022-04-19T09:48:36Z level=info msg=instance account example.org CREATED with id 01EXX0TJ9PPPXF2C4N2MMMVK50
      time=2022-04-19T09:48:36Z level=info msg=created instance instance example.org with id 01PQT31C7BZJ1Q2Z4BMEV90ZCV
      time=2022-04-19T09:48:36Z level=info msg=media manager cron logger: start[]
      time=2022-04-19T09:48:36Z level=info msg=media manager cron logger: schedule[now 2022-04-19 09:48:36.096127852 +0000 UTC entry 1 next 2022-04-20 00:00:00 +0000 UTC]
      time=2022-04-19T09:48:36Z level=info msg=started media manager remote cache cleanup job: will run next at 2022-04-20 00:00:00 +0000 UTC
      time=2022-04-19T09:48:36Z level=info msg=listening on 0.0.0.0:8080
      ```

### Step 2: Setting up Cloudflare Zero Trust

1. Login to your cloudflare account and select `Zero Trust` in the sidebar.

2. Signup for a free account.

### Step 3: Create a tunnel

1. Login to your Zero Trust account.

2. On the sidebar select `Access` then in the sub-menu `Tunnels`.

3. Select `Create a tunnel`, then name your tunnel and `Save Tunnel`.

4. **Do not close the webpage yet.**

### Step 4: Installing cloudflared

1. In a new folder create a new `docker-compose.yaml`.
   
   ```bash
   cd ~ # Or wherever you want to create the folder
   mkdir cloudflared
   cd cloudflared
   touch docker-compose.yaml
   ```

2. In the `docker-compose.yaml` file add the following, replacing `<token>` with the token from the cloudflare tunnels webpage.
   
   ```yaml
   version: "3.9"
   services:
    tunnel:
      container_name: cloudflared
      networks:
        - gotosocial_gotosocial
      image: cloudflare/cloudflared
      restart: unless-stopped
      command: tunnel run
      environment:
        - TUNNEL_TOKEN=<token>
   
   networks:
    gotosocial_gotosocial:
      external: true
   ```

3. Save the file and `docker-compose up -d`or`docker compose up -d`.

Back on your cloudflare tunnels page, scroll down to check if connector is connected. If it shows a connector hit save.

> If you want to add other services to this cloudflare tunnel simply add their networks to the above docker file like how the `gotosocial_gotosocial` network is added and run `docker compose up -d`.
> 
> You can also do this the other way round, creating a cloudflared specific network and then adding it to your apps as you create them, but i don't recommend it because then all your services are connected to that network and can access each other. This way only cloudflared is connected to your services.
> 
> > If you want a good GUI for docker [portainer](https://www.portainer.io/) is an incredible tool.

### Step 5: Pointing your domain name

1. Choose whatever `domain` you want.

2. In `Service` add:
   
   - Type: HTTP
   
   - URL: `gts.docker:8080` (replace `gts.docker` with whatever hostname you put in Step 1 and `8080`is the port where GoToSocial is listening.)

3. Select `Save hostname`.

![Basic Idea](https://i.imgur.com/ZrmWHv7.png#center)

> remove/change sub-domain as you see fit.

## Voila!!

If you did everything correctly your GoToSocial instance should be up and running. Access it at `gts.whatever-your-domain-is`. Now go **wild.**

## Bonus Round: Creating users

To create a user:

```bash
docker exec -it CONTAINER_NAME_OR_ID \
 /gotosocial/gotosocial \
 admin account create \
 --username some_username \
 --email someone@example.org \
 --password 'some_very_good_password'
```

And then to promote the created user to admin:

```bash
docker exec -it CONTAINER_NAME_OR_ID \
    /gotosocial/gotosocial \
    admin account promote \
    --username some_username
```

---

## Further Reading

I recommend reading the [GoToSocial Documentation](https://docs.gotosocial.org/en/latest/) as it was quite helpful making this. Overall it is well-written, missing a few things here and there. You can also find further configuration options there.
