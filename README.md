# Nextcloud AIO with Cloudflare Tunnel
Documentantion for future reference on how I setup nextcloud aio with cloudflare tunnel using docker compose

I used [this](https://github.com/nextcloud/all-in-one/discussions/2845) nextcloud issue as a template for my `docker-compose.yml` file.

## Pre-instalation

Create a .env file and replace `{YOUR-TUNNEL-TOKEN-HERE}` with your cloudflare tunnel token.
```
TUNNEL_TOKEN={YOUR_TUNNEL_TOKEN_HERE}
```

## Installation

Run `docker compose up -d`

And

Point your tunnel to the your nextcloud server IP at port `11000`

![Untitled](https://github.com/user-attachments/assets/d3175d51-cd13-4e83-b8ef-595d0450e9b4)

Proceed to AIO normal installation.

## Updating

Run `docker compose down`

Run `docker compose pull`

Run `docker compose up -d`


## Bug/Error encountered (how I fixed it!)

Nextcloud container stuck at:
```
Updater run in non-interactive mode.

Start update
Info: Pressing Ctrl-C will finish the currently running step and then stops the updater.
 
[ ] Check for expected files ...
] Check for expected files
2024-11-09T22:04:30.202716464Z [ ] Check for write permissions ...
] Check for write permissions
2024-11-09T22:04:30.246988546Z [ ] Create backup ...
] Create backup
```

Run `sudo docker exec -it nextcloud-aio-nextcloud bash` and inside the container run `ps aux`, look for `php '/var/www/html/updater/updater.phar --no-interaction --no-backup'` PID.

Kill update script with `kill -9 {PID}` replacing {PID} with the execution PID. This should allow nextcloud container to continue the installation.

---

Notify-push container never starts, logs:
```
Connection to nextcloud-aio-nextcloud (172.19.0.8) 9001 port [tcp/*] succeeded!
The notify_push binary was not found.
Most likely is DNS resolution not working correctly.
You can try to fix this by configuring a DNS server globally in dockers daemon.json.
See https://dockerlabs.collabnix.com/intermediate/networking/Configuring_DNS.html
Afterwards a restart of docker should automatically resolve this.
Additionally, make sure to disable VPN software that might be running on your server
Also check your firewall if it blocks connections to github
```

And Nextcloud container did not installed any apps on setup, as shown in logs:
```
Error: Could not download app notify_push
chmod: cannot access '/var/www/html/custom_apps/notify_push/bin/': No such file or directory
[...]
Error: Could not download app app_api
Error: Could not download app whiteboard
```

Solution:

`sudo docker exec -it nextcloud-aio-nextcloud apk update`

`sudo docker exec -it nextcloud-aio-nextcloud apk upgrade`

Go to AIO setup page and restart the containers, it should now be able to install apps.
