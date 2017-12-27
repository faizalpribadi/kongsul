# KongSul
How to work Kong Api Gateway with Consul Service Discover (MicroService)

# Kong Introduction
see part of kong api gateway from official site [kong-api](https://getkong.org/)
 
    * how to register api on kong 

# Consul Section
install consul and at the `services.json`, put the file into `/etc/consul.d/services.json` and you can run consule command like this:

    $ consul agent -dev -config-dir=/etc/consul.d


By example, this is working on localhost, check you're `consul` ns is working correctly: 

    $ dig @127.0.0.1 -p 8600 go-svc-1.service.consul
    $ dig @127.0.0.1 -p 8600 go-svc-1.service.consul SRV

    ; <<>> DiG 9.9.7-P3 <<>> @127.0.0.1 -p 8600 go-svc-1.service.consul SRV
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 26325
    ;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 3
    ;; WARNING: recursion requested but not available

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;go-svc-1.service.consul.	IN	SRV

    ;; ANSWER SECTION:
    go-svc-1.service.consul. 0	IN	SRV	1 1 8082 Faizals-MacBook-Pro.local.node.dc1.consul.

    ;; ADDITIONAL SECTION:
    Faizals-MacBook-Pro.local.node.dc1.consul. 0 IN	A 127.0.0.1
    Faizals-MacBook-Pro.local.node.dc1.consul. 0 IN	TXT "consul-network-segment="

    ;; Query time: 0 msec
    ;; SERVER: 127.0.0.1#8600(127.0.0.1)
    ;; WHEN: Wed Dec 27 22:51:37 WIB 2017
    ;; MSG SIZE  rcvd: 165

and check you're file at `/etc/resolv.conf`, register nameserver localhost like this:

    $ nameserver 127.0.0.1

and you must set on `kong.conf` at line `dns_resolver` like this:

    $ dns_resolver: 127.0.0.1:8600 // consul host & port 
    
and register the name of `consul` ns as `upstream_url` on kong api like this : 

    $ curl -i -X POST \
        --url http://localhost:8001/apis/ \
        --data 'name=your-api' \
        --data 'hosts=your-api.com' \
        --data 'upstream_url=http://go-svc-1.service.consul'

Testing `Kong api` and `Consul Service Discovery` Working Great Together:

    $ curl -i -X GET \
        --url http://localhost:8000/ \
        --header 'Host: your-api.com'