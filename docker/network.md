# Docker network notes

Let's first explore docker network.

Basic commands:
`docker network ls` and `docker network inspect [network_name: bridge]`

I did a little experiment. The goal is simple, ping docker container from another docker container. I only used the basic `alpine:latest` image. I opened up two terminals. Then I run following command for each terminal: `docker run -it -entrypoint /bin/sh alpine`. Thus, I logged into two alpine docker images.

In order to find out whether these two containers are connected to the docker network: `docker network inspect bridge`. I can clearly see two images listed in "containers" field in the return JSON text.

I `cat`ed the `/etc/hosts` and `/etc/hostname` and found out their were each assigned a randomly generated hostname, looks like some sort of base64 digest. So I tried to `ping` each other by hostname. There was no luck. Then I tried to ping them by ip addresses assigned by docker(docker's network interface controller) within each other and it worked. So I concluded they just can't resolve each other's hostname.(`nslookup` on them didn't work)

I was stuck. So I sought for help from Google. And I did find [something](https://docs.docker.com/v17.09/engine/userguide/networking/default_network/configure-dns/). It was a simple `mount` command. (I'll explain this command in the next section) Then I successfully `ping`ed them from each other's shell. And their host/ip combo showed up in `etc/hosts` as well.

More about [mount and busybox](system/busybox.md)