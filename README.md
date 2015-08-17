Install OpenVPN server using Ansible over AWS VPC
----------------
There's a blog post that I wrote to go along with this. [Check it out!]

We'll use the below scenario in which we’ll configure the custom NAT instance to OpenVPN as well using Ansible to access the resources inside the private subnet(s).
![Please refer this AWS VPC scenario](http://s11.postimg.org/7r6lml9hf/modify_vpc.jpg)

Please modify the following things for the above mentioned post:

– Open the UDP port 1194 inside the NAT instance Security Group

– Allow the desired traffic inside the desired desination server(s) Security Group for NAT instance

Edit the variable file `openvpn/vars/main.yml` as per your requirement, I have added the necessary detail as comments:

```yaml
---
 openvpn_key_country:  "PK"
 openvpn_key_province: "Punjab"
 openvpn_key_city: "Lahore"
 openvpn_key_org: "Tendo Pvt ltd"
 openvpn_key_ou: "IT Department"
 openvpn_days_valid: "1825"
 openssl_request_subject: "/C={{ openvpn_key_country }}/ST={{ openvpn_key_province }}/L={{ openvpn_key_city }}/O={{ openvpn_key_org }}/OU={{ openvpn_key_ou }}"
 openvpn_key_size: "2048"
 openvpn_cipher: "BF-CBC"
 openvpn_auth_digest: "SHA1"
 openvpn_path: "/etc/openvpn"
 openvpn_ca: "{{ openvpn_path }}/ca"
 openvpn_dhparam: "{{ openvpn_path }}/dh{{ openvpn_key_size }}.pem"
 openvpn_hmac_firewall: "{{ openvpn_path }}/ta.key"
 openvpn_server: "tendo.local"
 openvpn_port: "1194"
 openvpn_protocol: "udp"
 # It assume that the subnet mask is /24
 openvpn_network: "10.8.0.0" 
 # Private Subnet(s) in VPC that you want to reach through OPENVPN, it assume that the subnet mask is /24
 private_networks:
   - 10.10.20.0
   - 10.10.30.0
 # Please mentioned the clients' name at this location
 openvpn_clients:
   - laptop
   - phone
   - tablet
```
After that edit the hosts file, mentioned the public ip address of your instance which you want to make as OpenVPN server and login username:
```shell
[openvpn]
52.18.167.161 ansible_ssh_user=ubuntu
```
After editing the vars and hosts file as per requirements, run this command:
```shell
ansible-playbook -i hosts site.yml
```

[Check it out!]:https://rbgeek.wordpress.com/2015/08/17/install-openvpn-server-using-ansible-over-aws-vpc/
