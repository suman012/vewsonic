/ ip firewall address-list
add list="Allowed-Internet" address=172.18.1.0/24 comment="" disabled=no


/ ip firewall address-list
add list="WAN-01" address=172.18.1.24/32 comment="" disabled=no
add list="WAN-02" address=172.18.1.76/32 comment="" disabled=no



/ ip firewall nat 
add chain=srcnat action=masquerade out-interface="WAN - 01" src-address-list="Allowed-Internet" comment="Gateway 10.0.1.1/24" disabled=no 
add chain=srcnat action=masquerade out-interface="WAN - 02" src-address-list="Allowed-Internet" comment="Gateway 10.0.2.1/24" disabled=no


Using RouterOS's Mangle Tool to mark specific traffic

/ ip firewall mangle 
add chain=prerouting action=mark-routing new-routing-mark="WAN-01" src-address-list="WAN-01" passthrough=no comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="WAN-02" src-address-list="WAN-02" passthrough=no comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="HTTP traffic" passthrough=no dst-port=80 protocol=tcp comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="SSL traffic" passthrough=no dst-port=443 protocol=tcp comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="POP3 traffic" passthrough=no dst-port=110 protocol=tcp comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="SMTP traffic" passthrough=no dst-port=25 protocol=tcp comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="P2P traffic" passthrough=no p2p=all-p2p comment="" disabled=no 
add chain=prerouting action=mark-routing new-routing-mark="Unknown traffic" passthrough=no comment="" disabled=no 




Using the routing functions of RouterOS to force traffic out to certain internet connections





/ ip route 
add dst-address=0.0.0.0/0 gateway=10.0.1.1 scope=255 target-scope=10 routing-mark="WAN - 01" comment="" disabled=no
add dst-address=0.0.0.0/0 gateway=10.0.2.1 scope=255 target-scope=10 routing-mark="WAN - 02" comment="" disabled=no
add dst-address=0.0.0.0/0 gateway=10.0.2.1 scope=255 target-scope=10 routing-mark="HTTP traffic" comment="" disabled=no 
add dst-address=0.0.0.0/0 gateway=10.0.2.1 scope=255 target-scope=10 routing-mark="SSL traffic" comment="" disabled=no 
add dst-address=0.0.0.0/0 gateway=10.0.2.1 scope=255 target-scope=10 routing-mark="POP3 traffic" comment="" disabled=no 
add dst-address=0.0.0.0/0 gateway=10.0.1.1 scope=255 target-scope=10 routing-mark="SMTP traffic" comment="" disabled=no 
add dst-address=0.0.0.0/0 gateway=10.0.1.1 scope=255 target-scope=10 routing-mark="P2P traffic" comment="" disabled=no 
add dst-address=0.0.0.0/0 gateway=10.0.1.1 scope=255 target-scope=10 routing-mark="Unknown traffic" comment="" disabled=no
