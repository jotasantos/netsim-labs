# Description
Very simple PoC for exabgp in frr talking to an Arista switch and injecting routes.

It's based on containerlab, therefore we need to load exabgp manually. The rest of the, individual, files are bound manually to the containers via the clab.yml file.

## exabgp in the frr containers
In our case, the containers don't have internet access, so we just download the exabgp blob and transfer it to the FRR intance/s:

    # In the host
    netlab initial
    wget -O 4.0.0.tar.gz https://github.com/Exa-Networks/exabgp/archive/4.0.0.tar.gz
    docker ps -q | xargs -n 1 docker inspect --format '{{ .Name }} {{range .NetworkSettings.Networks}} {{.IPAddress}}{{end}}' | sed 's#^/##';   # this is to find the IP of the FRR container/s
    scp 4.0.0.tar.gz root@<rr-mgmt-IP>:/
    # Now in FRR instance
    tar -xzvf 4.0.0.tar.gz

## Topology and how it works
servera -- r1 -- r3 -- r2 -- serverb
		 |
		 rr
+ servera and serverb are linux alpine instances
 + servera is in 172.16.0.2/24
 + serverb is in 172.16.1.2/24 
+ r1,r2 and r3 are cEOS
+ rr in a FRR instance from where we run exabgp


We log into rr and we run exabgp:
`sudo env exabgp.daemon.user=root exabgp-4.0.0/sbin/exabgp exabgp.conf`

And we are able to see the routes (defined in the python script in 'rr'), injected to the RIB in the Arista cEOS 'rr2'

    r2#sh ip route bgp
    [...]
     B E      100.10.0.0/24 [200/0] via 10.1.0.2, Ethernet1
     B E      200.20.0.0/24 [200/0] via 10.1.0.2, Ethernet1


In addition, we insert a flowspec rule aimed to deny all traffic from 
