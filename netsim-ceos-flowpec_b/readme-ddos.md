




---

Deploy DDOS topology:

`containerlab deploy -t ddos.yml`

Simulate DDoS attack against `victim`:

`docker exec -it clab-ddos-attacker hping3 --flood --udp -k -a 198.51.100.1 -s 53 192.0.2.129`

Connect to http://localhost:8008/ for analytics, see [Quickstart](https://sflow-rt.com/intro.php) for more information.

