# Network Diagnostics Quick Notes

## fping
Fast multi-host ping.

### Common usage
```bash
fping -e dns.google


fping -e dns.google -e one.one.one.one -e x.com

	 dns.google is alive (9.86 ms)
	 one.one.one.one is alive (2.02 ms)
	 x.com is alive (2.05 ms)```

### Scan ranges
```bash
fping -a -q -e -g 10.0.4.24 10.0.4.180
fping -a -q -e -g 10.0.4.0/24
fping -a -q -e -g 10.0.4.0/24 -s

```
![[Pasted image 20260419132955.png|500]]
### Options
- `-e` → show latency (ms)
- `-a` → alive hosts only
- `-q` → quiet output
- `-g` → generate IP range
- `-s` → summary stats

### Count / interval
```bash
fping -c 100 -p 1000 10.0.4.24
fping -q -c 100 -p 10 10.0.4.24 10.0.4.83

	10.0.4.24 : xmt/rcv/%loss = 100/0/100%
	10.0.4.83 : xmt/rcv/%loss = 100/0/100%
```

- `-c` → number of pings
- `-p` → interval (ms)

### MTU testing
```bash
fping -b 1472 -m 10.0.4.83
fping -b 1473 -m 10.0.4.83
```

- `-b` → packet size
- `-m` → do not fragment check

---

## gping
Graphical ping.

```bash
gping dns.google
gping dns.google x.com 10.0.4.83
```
![[Pasted image 20260419133235.png|700]]



---

## traceroute
Trace path to host.

```bash
traceroute -n 10.0.4.83
traceroute -n 10.0.4.83 1492

	traceroute -n 10.0.4.83 1492
traceroute to 10.0.4.83 (10.0.4.83), 30 hops max, 1492 byte packets
 1  192.168.3.1  33.418 ms  33.367 ms  33.424 ms
 2  10.0.4.83  324.443 ms  324.419 ms  329.849 ms
❯ traceroute -n 10.0.4.83
traceroute to 10.0.4.83 (10.0.4.83), 30 hops max, 60 byte packets
 1  192.168.3.1  30.983 ms  30.939 ms  30.916 ms
 2  10.0.4.83  175.070 ms  175.052 ms  175.025 ms
```

- `-n` → no DNS resolution
- `1492` → packet size (MTU testing)


---


## Tracepath


```bash
tracepath -n 10.0.4.83 -l 1492

 1?: [LOCALHOST]                      pmtu 1420
 1:  192.168.3.1                                          33.549ms 
 1:  192.168.3.1                                          27.086ms 
 2:  10.0.4.83                                            58.561ms reached
     Resume: pmtu 1420 hops 2 back 2 

#verify: 
╰─ sudo tcpdump -v -ni eth0 host 10.0.4.83 or icmp
```


---



## mtr
Ping + traceroute combined.

```bash
sudo mtr 1.1.1.1
sudo mtr -bzwr 1.1.1.1
```

### Options inside the command after

- `n` → dns resolution
- `j` → Porcentage
- `y` → More info
- `r` → restart


``` bash
❯ sudo mtr -bzwr 1.1.1.1

Start: 2026-04-19T13:42:41-0500
HOST: thinkpad                                                             Loss%   Snt   Last   Avg  Best  Wrst StDev
  1. AS???         unifi.localdomain (10.0.4.1)                             0.0%    10    1.9   1.7   1.5   2.0   0.2
  2. AS???         10.2.0.1                                                 0.0%    10   59.0  60.4  59.0  64.4   1.5
  3. AS???         ???                                                     100.0    10    0.0   0.0   0.0   0.0   0.0
  4. AS9009        te-0-0-0-16.bb1n.lon1.uk.m247.ro (193.9.115.146)        20.0%    10   61.3  62.0  58.9  68.2   3.3
  5. AS???         146.70.4.30                                             70.0%    10   65.4  64.1  61.6  65.4   2.1
  6. AS???         cloudflare.as13335.any2ix.coresite.com (206.72.211.63)   0.0%    10   60.7  69.8  59.8  91.3  13.9
  7. AS13335       141.101.72.105                                          90.0%    10   61.0  61.0  61.0  61.0   0.0
  8. AS13335       141.101.72.113                                           0.0%    10   75.6  64.9  60.2  75.6   5.2
  9. AS13335       one.one.one.one (1.1.1.1)                               90.0%    10   61.0  61.0  61.0  61.0   0.0
```



---

## trippy (trip)
Modern traceroute tool.

```bash
sudo trip 1.1.1.1 10.0.4.83
sudo trip mic2.4rji.com -r google -G /opt/4rji/GeoLite2-City.mmdb -e
```

### Options inside the command after
- `m` → Map mode
- `arrows` → Move between targets 
- `i` → Ip address
- `n` → Host names
- `i` → Ip address
- `b` → Host and Ip address
- `Ctrl + f` →Freeze
- `c` → chart graphic mode
- `Ctrl + r` → Restart
- `Ctrl + k ` → Flush dns cache
- `arrows up down` → select hop
- `d` → More info (with selected hop)
- `i` → Ip address
Also you can modify trippy-config-sample.toml on github


## Hop details  - d 

![[Screenshot 2026-04-19 at 1.55.10 PM.png]]


## Map mode  - m 
![[Screenshot 2026-04-19 at 1.56.17 PM.png]]