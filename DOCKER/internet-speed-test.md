# Using speedtest-cli for Internet Speed Testing in a docker container

```bash
$ docker run --rm -it alpine sh
/ #

/ # apk add --no-cache py3-pip

/ # apk add speedtest-cli
fetch https://dl-cdn.alpinelinux.org/alpine/v3.20/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.20/community/x86_64/APKINDEX.tar.gz
(1/2) Installing speedtest-cli (2.1.3-r7)
(2/2) Installing speedtest-cli-pyc (2.1.3-r7)
Executing busybox-1.36.1-r29.trigger
OK: 75 MiB in 41 packages 

/ # speedtest-cli
Retrieving speedtest.net configuration...
Testing from Airtel (223.185.45.62)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Bharat FiberNet (Hyderabad) [1631.59 km]: 34.339 ms
Testing download speed................................................................................
Download: 8.73 Mbit/s
Testing upload speed......................................................................................................
Upload: 50.90 Mbit/s


```