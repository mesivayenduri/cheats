# NETWORK SPEED TEST BETWEEN DOCKER CONTAINERS

## You can test network speeds within a Docker container using tools like iperf3

### Run an iperf3 server in a container:
```bash
$ docker run -d --name iperf-server -p 5201:5201 networkstatic/iperf3 -s
9012490d471c

$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                    NAMES
9012490d471c   networkstatic/iperf3   "iperf3 -s"              8 minutes ago   Up 8 minutes   0.0.0.0:5201->5201/tcp   iperf-server

$ docker inspect 9012490d471c | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

### Run an iperf3 client in another container:
```bash

$ docker run --rm networkstatic/iperf3 -c 172.17.0.2 -p 5201
Connecting to host 172.17.0.2, port 5201
[  5] local 172.17.0.5 port 36968 connected to 172.17.0.2 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  8.75 GBytes  75.2 Gbits/sec    0    670 KBytes       
[  5]   1.00-2.00   sec  8.78 GBytes  75.4 Gbits/sec   41    822 KBytes       
[  5]   2.00-3.00   sec  8.40 GBytes  72.2 Gbits/sec    0    824 KBytes       
[  5]   3.00-4.00   sec  6.26 GBytes  53.8 Gbits/sec    0    829 KBytes       
[  5]   4.00-5.00   sec  8.37 GBytes  71.9 Gbits/sec    1    839 KBytes       
[  5]   5.00-6.00   sec  7.28 GBytes  62.5 Gbits/sec    0    841 KBytes       
[  5]   6.00-7.00   sec  7.47 GBytes  64.2 Gbits/sec    0    841 KBytes       
[  5]   7.00-8.00   sec  7.27 GBytes  62.4 Gbits/sec    0    844 KBytes       
[  5]   8.00-9.00   sec  7.61 GBytes  65.4 Gbits/sec   48    847 KBytes       
[  5]   9.00-10.00  sec  6.56 GBytes  56.4 Gbits/sec   47    850 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  76.8 GBytes  65.9 Gbits/sec  137             sender
[  5]   0.00-10.00  sec  76.8 GBytes  65.9 Gbits/sec                  receiver

iperf Done.


```