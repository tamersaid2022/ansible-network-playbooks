<div align="center">

# 📚 Ansible Network Playbooks

[![Ansible](https://img.shields.io/badge/Ansible-2.14+-EE0000?style=for-the-badge&logo=ansible&logoColor=white)](https://ansible.com)
[![Cisco](https://img.shields.io/badge/Cisco-IOS%2FNXOS-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://cisco.com)
[![Juniper](https://img.shields.io/badge/Juniper-Junos-84B135?style=for-the-badge&logo=juniper-networks&logoColor=white)](https://juniper.net)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

**Production-ready Ansible playbooks for multi-vendor network automation**

[Playbooks](#-playbooks) • [Quick Start](#-quick-start) • [Inventory](#-inventory) • [Variables](#-variables)

---

</div>

## 🎯 Overview

This repository contains a comprehensive collection of **Ansible playbooks** for automating common network operations across multi-vendor environments. Each playbook is production-tested, idempotent, and includes proper error handling.

### Supported Platforms

| Vendor | Modules | Connection |
|--------|---------|------------|
| **Cisco IOS/IOS-XE** | `cisco.ios` | network_cli |
| **Cisco NX-OS** | `cisco.nxos` | network_cli |
| **Cisco ASA** | `cisco.asa` | network_cli |
| **Juniper Junos** | `junipernetworks.junos` | netconf |
| **Arista EOS** | `arista.eos` | network_cli |
| **Palo Alto** | `paloaltonetworks.panos` | API |

---

## ⚡ Playbooks

### 📋 Available Playbooks

| Category | Playbook | Description |
|----------|----------|-------------|
| **Config** | `backup_configs.yml` | Backup running configurations |
| **Config** | `deploy_banner.yml` | Deploy login banners |
| **Config** | `deploy_snmp.yml` | Configure SNMP settings |
| **Config** | `deploy_ntp.yml` | Configure NTP servers |
| **Config** | `deploy_syslog.yml` | Configure syslog servers |
| **Security** | `deploy_acl.yml` | Deploy access control lists |
| **Security** | `harden_device.yml` | Apply security hardening |
| **Security** | `rotate_credentials.yml` | Rotate local credentials |
| **VLAN** | `create_vlan.yml` | Create VLANs |
| **VLAN** | `delete_vlan.yml` | Remove VLANs |
| **Interface** | `configure_interface.yml` | Configure interfaces |
| **Interface** | `shutdown_interface.yml` | Admin down interface |
| **Routing** | `deploy_ospf.yml` | Configure OSPF routing |
| **Routing** | `deploy_bgp.yml` | Configure BGP peering |
| **Ops** | `gather_facts.yml` | Collect device facts |
| **Ops** | `health_check.yml` | Run health diagnostics |
| **Ops** | `generate_report.yml` | Generate compliance report |

---

## 🚀 Quick Start

### Prerequisites

```bash
# Install Ansible
pip install ansible>=2.14

# Install network collections
ansible-galaxy collection install cisco.ios
ansible-galaxy collection install cisco.nxos
ansible-galaxy collection install junipernetworks.junos
ansible-galaxy collection install arista.eos
```

### Clone Repository

```bash
git clone https://github.com/tamersaid2022/ansible-network-playbooks.git
cd ansible-network-playbooks
```

### Run Your First Playbook

```bash
# Backup all device configurations
ansible-playbook -i inventory/hosts.yml playbooks/backup_configs.yml

# Deploy NTP to specific group
ansible-playbook -i inventory/hosts.yml playbooks/deploy_ntp.yml -l datacenter

# Dry-run mode (check mode)
ansible-playbook -i inventory/hosts.yml playbooks/deploy_snmp.yml --check --diff
```

---

## 📁 Repository Structure

```
ansible-network-playbooks/
├── ansible.cfg                 # Ansible configuration
├── inventory/
│   ├── hosts.yml              # Main inventory
│   ├── group_vars/
│   │   ├── all.yml            # Global variables
│   │   ├── cisco_ios.yml      # Cisco IOS vars
│   │   ├── cisco_nxos.yml     # Cisco NXOS vars
│   │   └── juniper_junos.yml  # Juniper vars
│   └── host_vars/
│       └── core-router-01.yml # Host-specific vars
├── playbooks/
│   ├── backup_configs.yml
│   ├── deploy_banner.yml
│   ├── deploy_snmp.yml
│   ├── deploy_ntp.yml
│   └── ...
├── roles/
│   ├── common/
│   ├── cisco_baseline/
│   └── security_hardening/
├── templates/
│   ├── banner.j2
│   ├── snmp.j2
│   └── acl.j2
├── files/
│   └── compliance_baseline.yml
└── requirements.yml            # Galaxy requirements
```

---

## 📋 Inventory

### hosts.yml

```yaml
---
all:
  children:
    # Cisco IOS Devices
    cisco_ios:
      hosts:
        core-router-01:
          ansible_host: 192.168.1.1
        core-router-02:
          ansible_host: 192.168.1.2
        dist-switch-01:
          ansible_host: 192.168.1.10
      vars:
        ansible_network_os: cisco.ios.ios
        ansible_connection: ansible.netcommon.network_cli
        
    # Cisco NXOS Devices
    cisco_nxos:
      hosts:
        nexus-01:
          ansible_host: 192.168.2.1
        nexus-02:
          ansible_host: 192.168.2.2
      vars:
        ansible_network_os: cisco.nxos.nxos
        ansible_connection: ansible.netcommon.network_cli
        
    # Juniper Devices
    juniper_junos:
      hosts:
        srx-fw-01:
          ansible_host: 192.168.3.1
        ex-switch-01:
          ansible_host: 192.168.3.10
      vars:
        ansible_network_os: junipernetworks.junos.junos
        ansible_connection: ansible.netcommon.netconf
        
    # Logical Groups
    datacenter:
      children:
        cisco_nxos:
    
    branch:
      hosts:
        branch-router-01:
          ansible_host: 10.1.1.1
          ansible_network_os: cisco.ios.ios
          
    firewalls:
      children:
        juniper_junos:
```

---

## 🔧 Variables

### group_vars/all.yml

```yaml
---
# Global variables for all devices
ansible_user: "{{ lookup('env', 'NETWORK_USER') | default('admin', true) }}"
ansible_password: "{{ lookup('env', 'NETWORK_PASSWORD') }}"
ansible_become: yes
ansible_become_method: enable
ansible_become_password: "{{ ansible_password }}"

# Standard configurations
ntp_servers:
  - 10.10.10.1
  - 10.10.10.2
  
dns_servers:
  - 8.8.8.8
  - 8.8.4.4
  
syslog_servers:
  - host: 10.10.20.1
    port: 514
    level: informational
    
snmp:
  community: "{{ lookup('env', 'SNMP_COMMUNITY') | default('public', true) }}"
  location: "Data Center"
  contact: "netops@company.com"
  
login_banner: |
  ******************************************************************
  *                    AUTHORIZED ACCESS ONLY                      *
  *  This system is for authorized users only. All activities are  *
  *  monitored and logged. Unauthorized access is prohibited.      *
  ******************************************************************

backup_dir: "./backups/{{ inventory_hostname }}"
```

### group_vars/cisco_ios.yml

```yaml
---
# Cisco IOS specific variables
platform: cisco_ios

# Security hardening
enable_secret: "{{ lookup('env', 'ENABLE_SECRET') }}"
password_encryption: true
ssh_version: 2
ssh_timeout: 60
exec_timeout: 10
vty_acl: "VTY-ACCESS"

# Logging
logging_buffered_size: 64000
logging_console: warnings

# AAA (if using TACACS+/RADIUS)
aaa_enabled: false
tacacs_servers: []
```

---

## 📜 Sample Playbooks

### backup_configs.yml

```yaml
---
# Backup running configurations from all network devices
# Usage: ansible-playbook -i inventory/hosts.yml playbooks/backup_configs.yml

- name: Backup Network Device Configurations
  hosts: all
  gather_facts: no
  
  vars:
    backup_root: "./backups"
    timestamp: "{{ lookup('pipe', 'date +%Y%m%d_%H%M%S') }}"
    
  tasks:
    - name: Create backup directory
      delegate_to: localhost
      file:
        path: "{{ backup_root }}/{{ inventory_hostname }}"
        state: directory
        mode: '0755'
      run_once: false
      
    - name: Backup Cisco IOS configuration
      when: ansible_network_os == 'cisco.ios.ios'
      cisco.ios.ios_config:
        backup: yes
        backup_options:
          filename: "{{ inventory_hostname }}_{{ timestamp }}.cfg"
          dir_path: "{{ backup_root }}/{{ inventory_hostname }}"
      register: ios_backup
      
    - name: Backup Cisco NXOS configuration
      when: ansible_network_os == 'cisco.nxos.nxos'
      cisco.nxos.nxos_config:
        backup: yes
        backup_options:
          filename: "{{ inventory_hostname }}_{{ timestamp }}.cfg"
          dir_path: "{{ backup_root }}/{{ inventory_hostname }}"
      register: nxos_backup
      
    - name: Backup Juniper configuration
      when: ansible_network_os == 'junipernetworks.junos.junos'
      junipernetworks.junos.junos_config:
        backup: yes
        backup_options:
          filename: "{{ inventory_hostname }}_{{ timestamp }}.cfg"
          dir_path: "{{ backup_root }}/{{ inventory_hostname }}"
      register: junos_backup
      
    - name: Display backup results
      debug:
        msg: "Configuration backed up to {{ backup_root }}/{{ inventory_hostname }}"
```

### deploy_ntp.yml

```yaml
---
# Deploy NTP configuration to network devices
# Usage: ansible-playbook -i inventory/hosts.yml playbooks/deploy_ntp.yml

- name: Configure NTP on Network Devices
  hosts: all
  gather_facts: no
  
  vars:
    ntp_servers:
      - 10.10.10.1
      - 10.10.10.2
      
  tasks:
    - name: Configure NTP on Cisco IOS
      when: ansible_network_os == 'cisco.ios.ios'
      cisco.ios.ios_ntp_global:
        config:
          servers:
            - server: "{{ item }}"
              prefer: "{{ loop.first }}"
        state: merged
      loop: "{{ ntp_servers }}"
      notify: save_ios_config
      
    - name: Configure NTP on Cisco NXOS
      when: ansible_network_os == 'cisco.nxos.nxos'
      cisco.nxos.nxos_ntp_global:
        config:
          servers:
            - server: "{{ item }}"
              prefer: "{{ loop.first }}"
        state: merged
      loop: "{{ ntp_servers }}"
      notify: save_nxos_config
      
    - name: Configure NTP on Juniper
      when: ansible_network_os == 'junipernetworks.junos.junos'
      junipernetworks.junos.junos_config:
        lines:
          - "set system ntp server {{ item }}"
        comment: "Ansible: Configure NTP"
      loop: "{{ ntp_servers }}"
      notify: commit_junos_config
      
  handlers:
    - name: save_ios_config
      cisco.ios.ios_config:
        save_when: modified
        
    - name: save_nxos_config
      cisco.nxos.nxos_config:
        save_when: modified
        
    - name: commit_junos_config
      junipernetworks.junos.junos_config:
        confirm_commit: yes
```

### health_check.yml

```yaml
---
# Run health diagnostics on network devices
# Usage: ansible-playbook -i inventory/hosts.yml playbooks/health_check.yml

- name: Network Health Check
  hosts: all
  gather_facts: no
  
  vars:
    health_report_dir: "./reports"
    cpu_threshold: 80
    memory_threshold: 80
    
  tasks:
    - name: Gather device facts
      block:
        - name: Gather Cisco IOS facts
          when: ansible_network_os == 'cisco.ios.ios'
          cisco.ios.ios_facts:
            gather_subset:
              - hardware
              - interfaces
          register: ios_facts
          
        - name: Gather Cisco NXOS facts
          when: ansible_network_os == 'cisco.nxos.nxos'
          cisco.nxos.nxos_facts:
            gather_subset:
              - hardware
              - interfaces
          register: nxos_facts
          
    - name: Get CPU utilization (Cisco IOS)
      when: ansible_network_os == 'cisco.ios.ios'
      cisco.ios.ios_command:
        commands:
          - show processes cpu | include CPU utilization
      register: cpu_output
      
    - name: Get memory utilization (Cisco IOS)
      when: ansible_network_os == 'cisco.ios.ios'
      cisco.ios.ios_command:
        commands:
          - show memory statistics | include Processor
      register: memory_output
      
    - name: Get interface status
      when: ansible_network_os == 'cisco.ios.ios'
      cisco.ios.ios_command:
        commands:
          - show ip interface brief
      register: interface_output
      
    - name: Check for interface errors
      when: ansible_network_os == 'cisco.ios.ios'
      cisco.ios.ios_command:
        commands:
          - show interfaces | include errors|drops
      register: errors_output
      
    - name: Generate health report
      delegate_to: localhost
      template:
        src: templates/health_report.j2
        dest: "{{ health_report_dir }}/{{ inventory_hostname }}_health.txt"
      vars:
        hostname: "{{ inventory_hostname }}"
        cpu_info: "{{ cpu_output.stdout[0] | default('N/A') }}"
        memory_info: "{{ memory_output.stdout[0] | default('N/A') }}"
        interfaces: "{{ interface_output.stdout[0] | default('N/A') }}"
        errors: "{{ errors_output.stdout[0] | default('N/A') }}"
        
    - name: Display health summary
      debug:
        msg: |
          ═══════════════════════════════════════════
          Health Check: {{ inventory_hostname }}
          ═══════════════════════════════════════════
          CPU: {{ cpu_output.stdout[0] | default('N/A') | regex_search('\\d+%') }}
          Report: {{ health_report_dir }}/{{ inventory_hostname }}_health.txt
```

### harden_device.yml

```yaml
---
# Apply security hardening to Cisco IOS devices
# Usage: ansible-playbook -i inventory/hosts.yml playbooks/harden_device.yml -l cisco_ios

- name: Security Hardening - Cisco IOS
  hosts: cisco_ios
  gather_facts: no
  
  vars:
    enable_secret: "{{ lookup('env', 'ENABLE_SECRET') }}"
    vty_acl_name: "VTY-ACCESS"
    allowed_networks:
      - 10.10.10.0 0.0.0.255
      - 192.168.1.0 0.0.0.255
      
  tasks:
    - name: Enable password encryption
      cisco.ios.ios_config:
        lines:
          - service password-encryption
          
    - name: Configure enable secret
      cisco.ios.ios_config:
        lines:
          - "enable secret {{ enable_secret }}"
      no_log: true
      
    - name: Disable unused services
      cisco.ios.ios_config:
        lines:
          - no ip http server
          - no ip http secure-server
          - no cdp run
          - no ip source-route
          - no ip finger
          - no service finger
          - no ip bootp server
          - no service tcp-small-servers
          - no service udp-small-servers
          - no service pad
          
    - name: Configure SSH
      cisco.ios.ios_config:
        lines:
          - ip ssh version 2
          - ip ssh time-out 60
          - ip ssh authentication-retries 3
          
    - name: Configure console timeout
      cisco.ios.ios_config:
        lines:
          - exec-timeout 10 0
          - logging synchronous
        parents: line console 0
        
    - name: Create VTY access ACL
      cisco.ios.ios_acls:
        config:
          - afi: ipv4
            acls:
              - name: "{{ vty_acl_name }}"
                aces:
                  - sequence: 10
                    grant: permit
                    source:
                      address: "{{ item.split()[0] }}"
                      wildcard_bits: "{{ item.split()[1] }}"
        state: merged
      loop: "{{ allowed_networks }}"
      
    - name: Apply VTY ACL and hardening
      cisco.ios.ios_config:
        lines:
          - access-class {{ vty_acl_name }} in
          - transport input ssh
          - exec-timeout 10 0
          - logging synchronous
        parents: line vty 0 15
        
    - name: Enable logging
      cisco.ios.ios_config:
        lines:
          - logging buffered 64000 informational
          - logging console warnings
          - service timestamps log datetime msec localtime show-timezone
          - service timestamps debug datetime msec localtime show-timezone
          
    - name: Save configuration
      cisco.ios.ios_config:
        save_when: modified
        
    - name: Display hardening completion
      debug:
        msg: "✅ Security hardening completed for {{ inventory_hostname }}"
```

---

## 🔐 Credential Management

### Using Environment Variables

```bash
# Set credentials
export NETWORK_USER=admin
export NETWORK_PASSWORD='SecureP@ss123'
export ENABLE_SECRET='EnableSecret123'
export SNMP_COMMUNITY='snmpcommunity'

# Run playbook
ansible-playbook -i inventory/hosts.yml playbooks/deploy_snmp.yml
```

### Using Ansible Vault

```bash
# Create encrypted vars file
ansible-vault create inventory/group_vars/vault.yml

# Edit encrypted file
ansible-vault edit inventory/group_vars/vault.yml

# Run playbook with vault
ansible-playbook -i inventory/hosts.yml playbooks/deploy_snmp.yml --ask-vault-pass
```

### vault.yml Example

```yaml
---
vault_network_user: admin
vault_network_password: SecureP@ss123
vault_enable_secret: EnableSecret123
vault_snmp_community: snmpcommunity
```

---

## 📊 Output Examples

### Backup Playbook Output

```
PLAY [Backup Network Device Configurations] ***********************************

TASK [Create backup directory] ************************************************
changed: [core-router-01]
changed: [core-router-02]
changed: [dist-switch-01]

TASK [Backup Cisco IOS configuration] *****************************************
changed: [core-router-01]
changed: [core-router-02]
changed: [dist-switch-01]

TASK [Display backup results] *************************************************
ok: [core-router-01] => {
    "msg": "Configuration backed up to ./backups/core-router-01"
}
ok: [core-router-02] => {
    "msg": "Configuration backed up to ./backups/core-router-02"
}
ok: [dist-switch-01] => {
    "msg": "Configuration backed up to ./backups/dist-switch-01"
}

PLAY RECAP ********************************************************************
core-router-01   : ok=3    changed=2    unreachable=0    failed=0    skipped=0
core-router-02   : ok=3    changed=2    unreachable=0    failed=0    skipped=0
dist-switch-01   : ok=3    changed=2    unreachable=0    failed=0    skipped=0
```

---

## 🤝 Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

### 👨‍💻 Author

**Tamer Khalifa** - *Network Automation Engineer*

[![CCIE](https://img.shields.io/badge/CCIE-68867-1BA0D7?style=flat-square&logo=cisco&logoColor=white)](https://www.cisco.com/c/en/us/training-events/training-certifications/certifications/expert.html)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin)](https://linkedin.com/in/tamerkhalifa2022)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github)](https://github.com/tamersaid2022)

---

⭐ **Star this repo if you find it useful!** ⭐

</div>
