# Description
Very simple PoC for exabgp in frr talking to an Arista switch and injecting routes.

It's based on containerlab, therefore we need to load exabgp manually. The rest of the, individual, files are bound manually to the containers via the clab.yml file.

## exabgp in the frr containers
In our case, the containers don't have internet access, so we just download the exabgp blob and transfer it to the FRR intance/s:

    # In the host
    containerlab deploy -t clab.yml    # to start the lab
    netlab initial    # to load basic ip configuration
    wget -O 4.0.0.tar.gz https://github.com/Exa-Networks/exabgp/archive/4.0.0.tar.gz
    docker ps -q | xargs -n 1 docker inspect --format '{{ .Name }} {{range .NetworkSettings.Networks}} {{.IPAddress}}{{end}}' | sed 's#^/##';   # this is to find the IP of the FRR container/s
    scp 4.0.0.tar.gz root@<container-mgmt-IP>:/
    # Now in FRR instance
    tar -xzvf 4.0.0.tar.gz

## Topology and how it works
servera -- r1 -- rr -- r2 -- serverb
+ servera and serverb are linux alpine instances
+ r1 is cEOS
+ rr and r2 are FRR instances


We log into rr and we run exabgp:
`sudo env exabgp.daemon.user=root exabgp-4.0.0/sbin/exabgp exabgp.conf`

And we are able to see the routes (defined in the python script in 'rr'), injected to the RIB in the Arista cEOS 'rr2'


    r2#sh ip route bgp
    [...]
     B E      100.10.0.0/24 [200/0] via 10.1.0.2, Ethernet1
     B E      200.20.0.0/24 [200/0] via 10.1.0.2, Ethernet1
