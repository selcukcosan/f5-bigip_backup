# f5-bigip_backup

This Ansible script takes backup of F5 BIG-IP devices in your inventory. The script uses the official f5networks.f5_modules Ansible modules on https://galaxy.ansible.com/ui/repo/published/f5networks/f5_modules/

The f5-bigip_backup.yaml script generates UCS,SCF, QKview, Master key, and running configuration files and retrieves these files from the F5 devices.

- User Configuration set (UCS) Archieve File
- Master Key
- Single Configuration File (SCF)
- QKView Support File
- Running Configuration
- Device Facts, Hardware and Software Information

The inventory yaml format is simple as below.


```yml
---
all:
  children:
    all_f5:
      hosts:
        bigip1:
          inventory_network_os: f5.bigip
          inventory_host: 192.168.1.245
          inventory_port: 443
          inventory_user: admin
          inventory_pass: password
        bigip2:
          inventory_network_os: f5.bigip
          inventory_host: 192.168.1.246
          inventory_port: 443
          inventory_user: admin
          inventory_pass: password
        bigip3:
          inventory_network_os: f5.bigip
          inventory_host: 192.168.1.247
          inventory_port: 443
          inventory_user: admin
          inventory_pass: password
```




## Prerequisite
F5 Ansible Imperative Modules must be installed before running the script.
```bash
ansible-galaxy collection install f5networks.f5_modules
```

## Usage:
```bash

ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -vvv --vault-password-file vault_pass.yaml -i inventory-vault.yaml f5-bigip_backup.yaml --extra-vars="bigip=bigip1" --extra-vars="get_ucs=true" --extra-vars="get_config=true" --extra-vars="get_qkview=true" --extra-vars="get_scf=true" --extra-vars="get_facts=true"
```

**_NOTE:_**  `output` folder must be created before running the script.

## Files
- f5-bigip_backup.yaml >> Ansible script file
- vault_pass.yaml >> Inventory vault password information
- inventory-vault.yaml >> Inventory vault file encrypted by "Inventory vault password"

## Variables
- `bigip` variable shows which F5 device will be connected.The value can be "bigip1", "bigip2" or "all_f5" as per the example inventory file above.
- `get_ucs` variable shows whether to retrieve UCS backup file. If the value is "true", the Ansible script will take UCS backup and Master Key files and copy these files into "output" folder.
- `get_config` variable shows whether to retrieve the running configuration of the F5 device. If the value is "true", the script will copy the running configuration into "output" folder.
- `get_qkview` variable shows whether to retrieve the QKview support file from the device.  If the value is "true", the script will copy the QKview file into "output" folder.
- `get_scf` variable shows whether to create SCF files into /var/local/scf/ folder. If the value is "true", the script will copy .scf and .scf.tar files into "output" folder.
- `get_facts` variable shows whether to retrieve F5 BIG-IP Facts information from the device.

## Task Explanations in f5-bigip_backup.yaml
- name: 01- Create a new UCS and Download >> bigip_ucs_fetch module creates UCS file on the remote F5 BIG-Ip Device and copy the ucs file into dest: "./output/{{ backup_file_name }}.ucs"
- name: 02- Remove the UCS from the device >> bigip_ucs module deletes the file created in Task 01
- name: 03- Get running config on remote device && - name: 04- Copy output of running config to file >> bigip_command module gets the running configuration and copy it into dest=./output/{{ backup_file_name }}.txt
- name: 05- Get master key on remote device && - name: 06- Copy master key to file >> bigip_command module gets the Master key file from F5 BIG-IP and copy it into dest=./output/{{ backup_file_name }}.key
- name: 07- Writing SCF Files  >> Takes scf files backup into /var/local/scf/ folder.
- name: 08-01 and 08-02 Copying SCF Files to local system. 
- name: 09- Collect all BIG-IP information && - name: 10- Copy all BIG-IP information >> bigip_device_info retrieves the F5 BIG-IP Facts information and copy it into dest=./output/{{ backup_file_name }}.info
- name: 11- BIG-IP Hostname information >> This task it just writes the qkview file name information
- name: 12- Fetch QKView BIG-IP information >> bigip_qkview module creates QKview support file on the remote F5 BIG-IP device and copy it into dest: "./output/{{ backup_file_name }}.qkview"

## output Folder Example Files
- bigip1-2024-06-18-22-37-54.ucs
- bigip1-2024-06-18-22-37-54.txt
- bigip1-2024-06-18-22-37-54.key
- bigip1-2024-06-18-22-37-54.info
- bigip1-2024-06-18-22-37-54.scf
- bigip1-2024-06-18-22-37-54.scf.tar
- bigip1-2024-06-18-22-37-54.qkview



