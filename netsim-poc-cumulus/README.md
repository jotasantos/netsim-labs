# NOTES:
- This works for a router (acting as a device) to join a multicast group. Interface towards the RP:
 interface swp1
  ip igmp join 224.10.2.1


## TODO:
- Find a working solution to generate multicast traffic in cumulus/linux
  - iperf only accepts ctrl/reserved multicast ranges
  iperf -c 224.0.0.1 -u -B 10.1.0.54 -t 3600 -T 255
  iperf -c 233.0.0.1 -u -B 10.1.0.53 -t 3600 -T 255
  bind failed: Cannot assign requested address
  connect failed: Invalid argument
- mz doesn't seem to generate anything. Tested only with multicast. TODO: test it with other simple packets and see if it really does anything:
  mz swp1 -c 0 -d 1000msec -B 233.1.1.1 -t udp "dp=32000,dscp=46" -P "Multicast test packet"
- 'socat' doesn't work. mtools gives me compiling error


- This was issue in an effort to make multicast work:
  ip nht resolve-via-default

 

  


