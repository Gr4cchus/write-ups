# mDNS
<b>Trying to get something to reflect mDNS traffic across vlans on a multihomed vm, especially for chromecast.</b>

### Quick note
* IGMP is l2
* PIM is l3
* mDNS, mDNS-SD (reflector?), Bonjour (3 rfc's)
    * Multihome host - nics in several networks
    * Restricting reflection would be nice
* Cisco has SDG but platform restrictions
* Ubiqiti has mDNS something in the controller but requires their hardware products
* chomecast uses linklocal, ttl=1, so problematic
    * hodgepodge of protocols, dont forget firewall issues on both ends

# Heading
Through testing with ubuntu 20.04 LTS the avahi mDNS was problematic or inconsistent. Testing Vyos 1.4 with its [mDNS repeater](https://docs.vyos.io/en/latest/configuration/service/mdns.html) option was more successful. Oddly enough enabling PIM which then turned on all of IGMP caused issues so disabling it allowed certain things to work. Using Ubiquitiy community [created containers](https://github.com/scyto/multicast-relay) was the most successful. IGMP & PIM was definately needed for this option. All were built in vSphere 6.7 and multihomed with portgroup vlans.

Using photon 4: get image from github, user is root password is changeme, tdnf to update, install packages to keep it automatically updated? Pull image:
`docker run -d --network=host --name mDNS --restart=always -e INTERFACES="eth1 eth0" scyto/multicast-relay`
-d to daemonize(send to background), --network must be somehow briding interfaces of vm, name the vm, --restart will bring up container on host reboots, interfaces will be your vms interfaces, scyto/multicast-relay is the docker container name to pull from.

Firewall rule needed for chromecast
`iptables -I INPUT -m pkttype --pkt-type multicast -j ACCEPT`
save iptables to persist between reboots
`iptables-save >/etc/systemd/scripts/ip4save`

Missed some things.

## Todo
* check how much traffic is leaking
    * control direction or restriction of reflection?
* make docker compose
* Figure out why IGMP & PIM broke on some solutions, but needed on others?