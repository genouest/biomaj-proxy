# Running

## Public proxy

For public communication, must be accessible to users

sudo docker run -p 6000:80 -d --name biomaj-public-proxy -e --link biomaj-consul:biomaj-consul -v PATH_TO/nginx-public.ctmpl:/nginx.ctmpl:ro  biomaj-proxy

## Private proxy

For internal communication, must be accessible to services only

sudo docker run -p 5000:80 -d --name biomaj-proxy -e --link biomaj-consul:biomaj-consul -v PATH_TO/nginx.ctmpl:/nginx.ctmpl:ro  biomaj-proxy

# Consul template

To test generation, once, of the template (you need consul-template)

    ./consul-template -consul 127.0.0.1:8500 -template nginx.ctmpl:/tmp/nginx.conf -once
    cat /tmp/nginx.conf

# Consul

you need a consul instance (or a cluster)

    sudo docker run --name biomaj-consul --rm -p 8400:8400 -p 8500:8500 -p 8600:53/udp -h node1 progrium/consul -server -bootstrap -advertise IP_OF_NODE -ui-dir /ui

and biomaj services need to declare consul host in their config.yml

To test if registration if fine, connect to consul ui or test with dig

    $ dig @IP_Of_CONSUL_NODE -p 8600 biomaj_download.service.dc1.consul. SRV

Example output:

    ; <<>> DiG 9.10.3-P4-Ubuntu <<>> @131.254.17.40 -p 8600 biomaj_download.service.dc1.consul. SRV
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28589
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 2

    ;; QUESTION SECTION:
    ;biomaj_download.service.dc1.consul. IN	SRV

    ;; ANSWER SECTION:
    biomaj_download.service.dc1.consul. 0 IN SRV	1 1 5013 node1.node.dc1.consul.
    biomaj_download.service.dc1.consul. 0 IN SRV	1 1 5003 node1.node.dc1.consul.

    ;; ADDITIONAL SECTION:
    node1.node.dc1.consul.	0	IN	A	131.254.17.40
    node1.node.dc1.consul.	0	IN	A	131.254.17.40

    ;; Query time: 0 msec
    ;; SERVER: 131.254.17.40#8600(131.254.17.40)
    ;; WHEN: Mon Oct 03 15:10:21 CEST 2016
    ;; MSG SIZE  rcvd: 276
