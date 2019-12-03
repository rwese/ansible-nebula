ansible-nebula
=========

This role installs and deploys a configuration for [Nebula](https://github.com/slackhq/nebula)

Requirements
------------

Currently you need to generate and deploy certificates before running this (see example)

Role Variables
--------------

| Variable Name | Type | Purpose | Default | Required |
|---|---|---|---|---|
| nebula_version | String | Version to download | 1.0.0 | |
| pki.ca | String | Path to CA file | NA | |
| pki.cert | String | Path to Certificate | NA | |
| pki.key | String | Path to Certificate Key| NA | |
| pki.blacklist | List | List of Blacklisted certificate hashes | NA | |
| static_host_map | List | List of static hosts for discovery | NA | |
| static_host_map[*].internal_ip | String | Private IP for discovery | NA | |
| static_host_map[*].public_ip | String | Public IP / Hostname for discovery | NA | |
| static_host_map[*].public_port | String | Public Port for discovery | NA | |
| lighthouse.am_lighthouse | Boolean | Is this instance a Lighthouse | false | |
| lighthouse.serve_dns | Boolean | Should this instance serve DNS | false | |
| lighthouse.interval | List | Lighthouse nodes within the network. | NA | |
| listen.host | String | IP to listen on | 0.0.0.0 | |
| listen.port | Integer | Port to listen on | 4242 | |
| listen.batch | Integer | Sets the max number of packets to pull from the kernel for each syscall | 64 | | 
| listen.read_buffer | Integer | Configure socket buffers for the udp side | NA | |
| listen.write_buffer | Integer | Configure socket buffers for the udp side | NA | |
| punchy | Boolean | Punchy continues to punch inbound/outbound at a regular interval to avoid expiration of firewall nat mappings | true | |
| punch_back | Boolean | punch_back means that a node you are trying to reach will connect back out to you if your hole punching fails | true | |
| cipher | String | Cipher allows you to choose between the available ciphers for your network. | NA | | 
| local_range | String | Local range is used to define a hint about the local network range | NA | |
| sshd.enabled | Boolean | sshd can expose informational and administrative functions via ssh | NA | |
| sshd.listen | String | IP / Port for admin SSH functions | NA | |
| outbound | List | Outbound rules for the built in firewall | | |
| inbound | List | Inbound rules for the built in firewall | | |


Firewall rule example
```
outbound:
  - port: any
    proto: any
    host: any

inbound:
  - port: any
    proto: icmp
    host: any
```


Dependencies
------------

None

Example Playbook
----------------

```
---
- hosts: all
  remote_user: root
  vars:
    static_host_map:
      - internal_ip: 10.255.0.1
        public_ip: 123.231.1.2
        public_port: 4242
    lighthouse:
      nodes:
        - 10.255.0.1
  pre_tasks:
    - name: Create Nebula directory
      file:
        path: /etc/nebula
        state: directory
        mode: '0750'
    - name: Deploy Nebula certificates
      copy:
        src: files/{{item}}
        dest: /etc/nebula/{{item}}
        owner: root
        group: root
        mode: '0600'
      with_items:
        - ca.crt
        - host.crt
        - host.key
  roles:
    - trozz.ansible-nebula
```

```
---
- hosts: all
  remote_user: root
  vars:
    static_host_map:
      - internal_ip: 10.255.0.1
        public_ip: 123.231.1.2
        public_port: 4242
    lighthouse:
      nodes:
        - 10.255.0.1
  roles:
    - ansible-nebula
```

License
-------

MIT

Author Information
------------------

This role is provided as is, Nebula is maintained by Slack and the community