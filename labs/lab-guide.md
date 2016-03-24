## Lab 1 - netmiko

### Task 1 - Using Netmiko

This task will show you how to establish an interactive SSH session to a Cisco Cloud Services Router (CSR) running IOS XE using the netmiko Python library.

##### Step 1

Enter the Python Dynamic Interactive Interpreter by first opening up a Linux Terminal Session (double-click the Terminal icon on the desktop) and then type in "Python" at the prompt.

```
ntc@ntc:~$ python
Python 2.7.10 (default, Oct 14 2015, 16:09:02) 
[GCC 5.2.1 20151010] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```


##### Step 2

Import the netmiko `ConnectHandler` object and establish an SSH session to the Cloud Services Router device switch with the following details:

* hostname: csr1
* Username: ntc
* Password: ntc123
* SSH port: 22

```python
>>> from netmiko import ConnectHandler
>>> 
>>> device = ConnectHandler(device_type='cisco_ios', ip='csr1', username='ntc', password='ntc123')
>>> 
```

##### Step 3

Issue a `dir()` on `device` to see available methods that can be called.

```python
>>> dir(device)
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__',
'__module__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
'__subclasshook__', '__weakref__', 'ansi_escape_codes', 'base_prompt', 'check_config_mode', 'check_enable_mode',
'cleanup', 'clear_buffer', 'commit', 'config_mode', 'device_type', 'disable_paging', 'disconnect', 'enable',
'establish_connection', 'exit_config_mode', 'exit_enable_mode', 'find_prompt', 'ip', 'normalize_linefeeds', 'password',
'port', 'remote_conn', 'remote_conn_pre', 'secret', 'send_command', 'send_command_expect', 'send_config_from_file',
'send_config_set', 'session_preparation', 'set_base_prompt', 'special_login_handler',
'strip_ansi_escape_codes', 'strip_command', 'strip_prompt', 'username',
'wait_for_recv_ready']
>>> 
```

##### Step 4

Execute the command `show version` using netmiko:

```python
>>> output = device.send_command('show version')
>>> 
>>> print output
Cisco IOS XE Software, Version 03.14.01.S - Standard Support Release
Cisco IOS Software, CSR1000V Software (X86_64_LINUX_IOSD-UNIVERSALK9-M), Version 15.5(1)S1, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2015 by Cisco Systems, Inc.
Compiled Sun 01-Mar-15 03:58 by mcpre


Cisco IOS-XE software, Copyright (c) 2005-2015 by cisco Systems, Inc.
All rights reserved.  Certain components of Cisco IOS-XE software are
licensed under the GNU General Public License ("GPL") Version 2.0.  The
software code licensed under GPL Version 2.0 is free software that comes
with ABSOLUTELY NO WARRANTY.  You can redistribute and/or modify such
GPL code under the terms of GPL Version 2.0.  For more details, see the
documentation or "License Notice" file accompanying the IOS-XE software,
or the applicable URL provided on the flyer accompanying the IOS-XE
software.


ROM: IOS-XE ROMMON

csr1 uptime is 2 hours, 55 minutes
Uptime for this control processor is 2 hours, 57 minutes
System returned to ROM by reload
System image file is "bootflash:packages.conf"
Last reload reason: <NULL>



This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

License Level: ax
License Type: Default. No valid license found.
Next reload license Level: ax

cisco CSR1000V (VXE) processor (revision VXE) with 2152318K/6147K bytes of memory.
Processor board ID 9LT7GSJVCI8
4 Gigabit Ethernet interfaces
32768K bytes of non-volatile configuration memory.
3988508K bytes of physical memory.
7774207K bytes of virtual hard disk at bootflash:.

Configuration register is 0x2102

>>>
```


##### Step 5

Shutdown interface GigabitEthernet3 sending one command at a time.

```python
>>> device.config_mode()
u'config term\nEnter configuration commands, one per line.  End with CNTL/Z.\ncsr1(config)#'
>>> 
>>> device.send_command('interface Gigabit3')
u''
>>> device.send_command('shutdown')
u''
>>>
```

Exit configuration mode:

```python
>>> device.exit_config_mode()
u'end\ncsr1#'
>>>
```


##### Step 6

Re-enable interface Gi3 *sending a list of commands*.

```python
>>> commands = ['interface Gi3', 'no shut']
>>> 
>>> device.send_config_set(commands)
u'config term\nEnter configuration commands, one per line.  End with CNTL/Z.\n
csr1(config)#interface Gi3\ncsr1(config-if)#no shut\ncsr1(config-if)#end\ncsr1#'
>>>
```

If you save the output in a variable and use the print command, you will see a cleaner output:

```python
>>> response = device.send_config_set(commands)
>>> 
>>> print response
config term
Enter configuration commands, one per line.  End with CNTL/Z.
csr1(config)#interface Gi3
csr1(config-if)#no shut
csr1(config-if)#end
csr1#
>>>
```

You can also use `help()` to learn more about the methods, for example on `send_config_set`:

```python
>>> help(device.send_config_set)

Help on method send_config_set in module netmiko.base_connection:

send_config_set(self, config_commands=None, exit_config_mode=True, **kwargs) method of netmiko.cisco.cisco_nxos_ssh.CiscoNxosSSH instance
    Send group of configuration commands down the SSH channel.
    
    config_commands is an iterable containing all of the configuration commands.
    The commands will be executed one after the other.
    
    Automatically exits/enters configuration mode.
    
    **kwargs will allow passing of all the arguments to send_command
    strip_prompt and strip_command will be set to False if not explicitly set in
    the method call.
(END)
```

##### Step 7

Disconnect from the device

```python
>>> device.disconnect()
>>> 
```

Exit the Python shell.


## Lab 2 - pyntc

### Task 1 - Exploring pyntc on IOS Devices

In this task, you will explore working with pyntc, a Python library built to simplify working with Cisco IOS devices as well as other device types such as Cisco Nexus, Arista switches, and Juniper devices.

For this lab, you'll use two different IOS routers.

> Note: the same approach would be used on Nexus devices too.

##### Step 1

While you are in your home directory, enter the Python shell:

```python
$ python

Python 2.7.6 (default, Jun 22 2015, 17:58:13) 
[GCC 4.8.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.

>>> 
```


##### Step 2

Build two (2) device objects, one for each Cisco CSR device:  csr1 and csr2.

```python
>>> 
>>> from pyntc import ntc_device as NTC
>>> 
>>> r1 = NTC(host='csr1', username='ntc', password='ntc123', device_type='cisco_ios_ssh')
>>> r2 = NTC(host='csr2', username='ntc', password='ntc123', device_type='cisco_ios_ssh')
>>> 
```

##### Step 3

View the the device facts for csr1.

```python
>>> r1.facts
{'uptime': 3720, 'vendor': 'cisco', 'uptime_string': '00:01:02:00',
'interfaces': [u'GigabitEthernet1', u'GigabitEthernet2', u'GigabitEthernet3',
u'GigabitEthernet4', u'Loopback100'], 'hostname': u'csr1',
'ios': {'config_register': u'0x2102'}, 'fqdn': 'N/A',
'os_version': u'15.5(1)S1', 'serial_number': '', 'model': u'CSR1000V', 'vlans': []}
>>> 
```

Print them again, this time using `json.dumps`

```python
>>> import json
>>> 
>>> print json.dumps(r1.facts, indent=4)
{
    "uptime": 3720, 
    "vendor": "cisco", 
    "uptime_string": "00:01:02:00", 
    "interfaces": [
        "GigabitEthernet1", 
        "GigabitEthernet2", 
        "GigabitEthernet3", 
        "GigabitEthernet4", 
        "Loopback100"
    ], 
    "hostname": "csr1", 
    "ios": {
        "config_register": "0x2102"
    }, 
    "fqdn": "N/A", 
    "os_version": "15.5(1)S1", 
    "serial_number": "", 
    "model": "CSR1000V", 
    "vlans": []
}
```

Notice how the first time you printed facts, it took a few seconds and the second time was instantaneously.  This is because they are now cached.

If the device changes and you need to update the facts for some reason, you can use the `facts_refresh()` method, i.e. `r1.refresh_facts()` and then print them again with `r1.facts`.


##### Step 4

View the facts for csr2.


##### Step 5

View the running_config and startup_config for each device.

Rather than view it in realtime, we'll get it from both devices and print them one at a time.

You will now create a dictionary that has two (32) keys, one per device equal to csr1 and csr2.  The value for each will also be a dictionary that has two key-value pairs.  One called 'running' that will store the running config and one called 'startup' that will store the startup configuration.

> In this step, you are creating a dictionary of dictionaries.  The main dictionary is to be called `routers`.  In this dictionary, there will be two key-value pairs.  Each key will be the hostname of each router and this is pulled from the facts previously gathered.  It then has two more key-value pairs (per router) that is the running and startup config.  

This is an advanced concept and topics like this are covered in much more detail given more time in our instructor-led courses.


```python
>>> routers = {}
>>> for csr in [r1, r2]:
...     hostname = csr.facts['hostname']
...     routers[hostname] = {}
...     routers[hostname]['running'] = csr.running_config
...     routers[hostname]['startup'] = csr.startup_config
... 
>>>
```

> Note: once we get to Ansible, the time/speed of working with multiple devices will improve dramatically because Ansible is multi-threaded by default.

You now have the startup and running configs stored for each device.

Print two of them out (output not shown):

```python
>>> print routers['csr1']['running']
>>>
>>> print routers['csr2']['startup']
>>>
```

##### Step 8

There is a file called `cisco-ios-fake-image.bin` in your home directory, which is just a text file, but we are going copy this to each router simulating the process of getting ready for a system upgrade.

```python
>>> csr_devices = [r1, r2]
>>> for csr in csr_devices:
...     csr.file_copy('cisco-ios-fake-image.bin')
...     print 'File copied for: ', csr.facts['hostname']
... 
File copied for:  csr1
File copied for:  csr2

```

Feel free to login to each router and verify the file exists.


##### Step 9

Perform a save (or a copy a run start) using the `save` method for csr2.

```python
>>> r2.save()
True

```

Note: you can also supply a filename if you want to use something different than the default the startup-config such as this:

```python
>>> r2.save('latest-cfg')
True
>>> 
```

Which is the equivalent to `copy run latest-cfg`.

##### Step 10

Backup the running configuration for csr1 and store it in the existing `backups` directory (it's a sub-directory in your home dir).

```python
>>> r1.backup_running_config('backups/csr1.cfg')
```

Feel free to check it out in a different terminal window (do not log out of the Python shell yet)!

##### Step 11

There are four methods that can be used to send commands directly to the device: `show`, `show_list`, `config`, `config_list`.

`show` and `show_list` can be used to send show commands.  `show` is used to send a single show command and `show_list` is used to send a list of show commands.

`config` and `config_list` can be used to send show commands.  `config` is used to send a single show command and `config_list` is used to send a list of show commands.

Here are a few examples to try:

Example:

```python
>>> print r1.show('show ip int brief')
>>>
```

You should see this output:

```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       10.0.0.50       YES NVRAM  up                    up      
GigabitEthernet2       10.254.13.1     YES NVRAM  up                    up      
GigabitEthernet3       unassigned      YES NVRAM  administratively down down    
GigabitEthernet4       10.254.12.1     YES NVRAM  up                    up      
Loopback100            1.1.1.1         YES NVRAM  up                    up      
>>> 

```

Example:

```python
>>> commands = ['show run interface Gi1', 'show run | inc route']
>>> 
>>> output = r1.show_list(commands)
>>> 
>>> for out in output:
...     print out
... 
Building configuration...

Current configuration : 150 bytes
!
interface GigabitEthernet1
 description MANAGEMENT
 vrf forwarding Mgmt-intf
 ip address 10.0.0.50 255.255.255.0
 negotiation auto
 cdp enable
end

router ospf 100
 router-id 1.1.1.1
ip route vrf Mgmt-intf 0.0.0.0 0.0.0.0 10.0.0.2

```


## Lab 3 - Cisco NX-OS Modules

In this lab, you will learn how to use Ansible modules to automate specific features and tasks in a Nexus switched environment.

##### Step 1

In your home directory, create a directory called `ansible`.  Then navigate to the new directory.

```
ntc@ntc:~$ mkdir ansible
ntc@ntc:~$ 
ntc@ntc:~$ cd ansible/
ntc@ntc:~/ansible$
ntc@ntc:~$ 
```

##### Step 2

Create an Ansible inventory file.

First, open Sublime Text by clicking on the icon on the desktop.

Then, while in the Linux terminal and in the `ansible` directory, create and open a new file:

```
ntc@ntc:~/ansible$ subl inventory
```

##### Step 3

In the inventory file, create a group called "nexus" that has two hosts "nxos-spine1" and "nxos-spine2".

```
[nexus]
nxos-spine1
nxos-spine2

```

##### Step 4

Also in the inventory file, define group variables that can be used across *all* groups.  These variables should be "un" and "pwd" and should be assigned the values of "ntc" and "ntc123," respectively.

```
[nexus]
nxos-spine1
nxos-spine2


[all:vars]
un=ntc
pwd=ntc123

```


##### Step 5

Verify Connectivity to each device.  You should be able to ping each device by name.

```
ntc@ntc:~/ansible$ ping nxos-spine1
ntc@ntc:~/ansible$ ping nxos-spine2

```

##### Step 6

In the `ansible` directory, create a playbook called `nexus.yml`.

It should have one task, which will add a new VLAN to each switch.

VLAN ID 10 with the name "web_vlan"

```yaml

---

  - name: CISCO PLAYBOOK
    hosts: nexus
    connection: local
    gather_facts: no

    tasks:

      - name: ENSURE VLAN EXISTS
        nxos_vlan: vlan_id=10 name=web_vlan username={{ un }} password={{ pwd }} host={{ inventory_hostname }}

```

##### Step 7

Take a minute and use `ansible-doc` to learn how to use `nxos_vlan`.

Enter `ansible-doc nxos_vlan` on the Linux terminal.  This can be done for any module.

The docs displayed will tell you how to use the module and also include example tasks.

##### Step 8

Save and run the playbook.

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory nexus.yml
```

```
PLAY [CISCO PLAYBOOK] ********************************************************* 

TASK: [ENSURE VLAN EXIST] ***************************************************** 
changed: [nxos-spine1]
changed: [nxos-spine2]

PLAY RECAP ******************************************************************** 
nxos-spine1                : ok=1    changed=1    unreachable=0    failed=0   
nxos-spine2                : ok=1    changed=1    unreachable=0    failed=0   

```


##### Step 9

Now manually SSH into *nxos-spine2* and **remove** the VLAN that was just added.

You can do this by using the command *ssh ntc@nxos-spine2* and then using the password "ntc123".

##### Step 10

Run the playbook again, but this time using verbose mode (using the `-v` flag).

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory nexus.yml -v

PLAY [CISCO PLAYBOOK] ********************************************************* 

TASK: [ENSURE VLAN EXIST] ***************************************************** 
ok: [nxos-spine1] => {"changed": false, "commands": "", "end_state": {"admin_state": "up", "name": "web_vlan", "vlan_id": "10", "vlan_state": "active"}, "end_state_vlans_list": ["1", "10"], "existing": {"admin_state": "up", "name": "web_vlan", "vlan_id": "10", "vlan_state": "active"}, "existing_vlans_list": ["1", "10"], "proposed": {"name": "web_vlan"}, "proposed_vlans_list": ["10"], "state": "present"}
changed: [nxos-spine2] => {"changed": true, "commands": "vlan 10 ; name web_vlan ; exit ;", "end_state": {"admin_state": "up", "name": "web_vlan", "vlan_id": "10", "vlan_state": "active"}, "end_state_vlans_list": ["1", "10"], "existing": {}, "existing_vlans_list": ["1"], "proposed": {"name": "web_vlan"}, "proposed_vlans_list": ["10"], "state": "present"}

PLAY RECAP ******************************************************************** 
nxos-spine1                : ok=1    changed=0    unreachable=0    failed=0   
nxos-spine2                : ok=1    changed=1    unreachable=0    failed=0   

   
```

Every module *returns* data.  To display that data, you can use verbose mode.  This data can also be saved and stored to use as inputs into other tasks or in templates.

You can also notice this module is idempotent.  This means that you can run the module repeatedly and it will only make the change once!  

It only made the change on nxos-spine2 because nxos-spine1 already had the VLAN configured.


##### Step 11

Run the playbook again.

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory nexus.yml 

PLAY [CISCO PLAYBOOK] ********************************************************* 

TASK: [ENSURE VLAN EXIST] ***************************************************** 
ok: [nxos-spine1]
ok: [nxos-spine2]

PLAY RECAP ******************************************************************** 
nxos-spine1                : ok=1    changed=0    unreachable=0    failed=0   
nxos-spine2                : ok=1    changed=0    unreachable=0    failed=0   


```

You can see that both resulted in "green" since now both have VLAN 10 configured.

##### Step 12

Use ansible-doc to explore the following four modules:

* nxos_interface
* nxos_vrf
* nxos_vrf_interface
* nxos_ipv4_interface


Add the tasks below to the playbook to perform the following:

Ensure interface Eth2/3 is:

*  a routed port
*  is in the VRF named NTC
*  is assigned the IP address 10.100.100.1

```yaml
      - name: ENSURE INTERFACE IS A L3 PORT
        nxos_interface: interface=Eth2/3 mode=layer3 username={{ un }} password={{ pwd }} host={{ inventory_hostname }}

      - name: ENSURE VRF NTC EXISTS ON SWITCH
        nxos_vrf: vrf=NTC username={{ un }} password={{ pwd }} host={{ inventory_hostname }}

      - name: ENSURE INTERFACE IS PART OF VRF NTC
        nxos_vrf_interface: interface=Eth2/3 vrf=NTC username={{ un }} password={{ pwd }} host={{ inventory_hostname }}

      - name: ENSURE GIVEN IP IS ASSIGNED TO INTERFACE
        nxos_ipv4_interface: interface=Eth2/3 ip_addr=10.100.100.1 mask=24 username={{ un }} password={{ pwd }} host={{ inventory_hostname }}
```


The full playbook should look like this:

```yaml
---

  - name: CISCO PLAYBOOK
    hosts: nexus
    connection: local
    gather_facts: no

    tasks:

      - name: ENSURE VLAN EXIST
        nxos_vlan: vlan_id=10 name=web_vlan username={{ un }} password={{ pwd }} host={{ inventory_hostna
me }}
      - name: ENSURE INTERFACE IS A L3 PORT
        nxos_interface: interface=Eth2/3 mode=layer3 username={{ un }} password={{ pwd }} host={{ invento
ry_hostname }}

      - name: ENSURE VRF NTC EXISTS ON SWITCH
        nxos_vrf: vrf=NTC username={{ un }} password={{ pwd }} host={{ inventory_hostname }}

      - name: ENSURE INTERFACE IS PART OF VRF NTC
        nxos_vrf_interface: interface=Eth2/3 vrf=NTC username={{ un }} password={{ pwd }} host={{ invento
ry_hostname }}

      - name: ENSURE GIVEN IP IS ASSIGNED TO INTERFACE
        nxos_ipv4_interface: interface=Eth2/3 ip_addr=10.100.100.1 mask=24 username={{ un }} password={{ 
pwd }} host={{ inventory_hostname }}
```

For more detail on Ansible modules specific to Nexus switches, please take a look at this GitHub site:

##### Step 13

Save and run the playbook using the `--limit` option to only run these tasks on `nxos-spine1`.

> Note: `--limit` is used to limit the tasks to a sub-set of hosts.  You can limit to a specific group or host defined in the inventory file.

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory nexus.yml --limit nxos-spine1

PLAY [CISCO PLAYBOOK] ********************************************************* 

TASK: [ENSURE VLAN EXIST] ***************************************************** 
ok: [nxos-spine1]

TASK: [ENSURE INTERFACE IS A L3 PORT] ***************************************** 
changed: [nxos-spine1]

TASK: [ENSURE VRF NTC EXISTS ON SWITCH] *************************************** 
changed: [nxos-spine1]

TASK: [ENSURE INTERFACE IS PART OF VRF NTC] *********************************** 
changed: [nxos-spine1]

TASK: [ENSURE GIVEN IP IS ASSIGNED TO INTERFACE] ****************************** 
changed: [nxos-spine1]

PLAY RECAP ******************************************************************** 
nxos-spine1                : ok=5    changed=4    unreachable=0    failed=0   
```

##### Step 14

Feel free to run the playbook again and you'll see that nothing changes.


## Lab 4 - Building and Pushing Config Files

This lab will show how to use Ansible to automate the process of building full network configuration files and pushing them to network devices.  

You will take existing configuration files, de-construct them, and then create Jinja2 templates and YAML based variable files.  The templates and variables will be rendered together using the Ansible *template* module to create configuration files.  They will then be pushed to network devices using an open source module from the NAPALM community.

### Task 1 - Update the Inventory File

##### Step 1

Update the inventory file to have a new group called "routers" - it should have two routers, namely "csr1" and "csr2".

Ensure your inventory file looks like the following:

```
[nexus]
nxos-spine1
nxos-spine2


[all:vars]
un=ntc
pwd=ntc123

[routers]
csr1
csr2

```

Verify connectivity to each device:

```
ntc@ntc:~/ansible$ ping csr1
ntc@ntc:~/ansible$ ping csr2

```


### Task 2 - Create Templates and Vars Files

Your goal now is to create 2 configuration files using a single template.

In reality, this could be 5 or 1000 config files!  The process would be the same.

##### Step 1

Within the `ansible` directory, create a directory called `templates` using the command `mkdir templates`.

It'll be on the same level as your inventory file.

You can use the `tree` command within the terminal to view the directory structure:

```
ntc@ntc:~/ansible$ tree
.
├── inventory
├── nexus.yml
└── templates

1 directory, 2 files
```

The configuration below is a partial final config from one of the routers, namely *csr1*.  The same configuration needs to be applied across all devices (with the exception of hostname, IP addressing, router-id, etc.).


```
!
ip domain name ntc.com
!
no ip domain lookup
lldp run
cdp run
!
vrf definition Mgmt-intf
 !
 address-family ipv4
 exit-address-family
 !
 address-family ipv6
 exit-address-family
!
!
interface Loopback100
 description OSPF ROUTER ID
 ip address 1.1.1.1 255.255.255.255
 no shutdown
!
interface GigabitEthernet1
 description MANAGEMENT
 vrf forwarding Mgmt-if
 ip address 10.0.0.50 255.255.255.0
 cdp enable
 no shutdown
!
interface GigabitEthernet2
 description CONNECTS_CSR3
 ip address 10.254.13.1 255.255.255.0
 cdp enable
 no shutdown
!
interface GigabitEthernet3
 shutdown
!
interface GigabitEthernet4
 description CONNECTS_CSR2
 ip address 10.254.12.1 255.255.255.0
 cdp enable
 no shutdown
!
!
router ospf 100
 router-id 1.1.1.1
 log-adjacency-changes
 network 1.1.1.1 0.0.0.0 area 0.0.0.0
 network 10.254.13.1 0.0.0.0 area 0.0.0.0
 network 10.254.12.1 0.0.0.0 area 0.0.0.0
!
ip route vrf Mgmt-intf 0.0.0.0 0.0.0.0 10.0.0.2
!
!
snmp-server community networktocode RO
snmp-server location NYC_NY_DC1
snmp-server contact NETWORK_ADMIN
!
!
end

```

Your job is to now create a single template and the associated groups vars and host vars files required to generate the require configuration files.


##### Step 2

Create a file called `csr-ospf.j2` in the `templates` directory and open it in Sublime Text. It will serve as the template for the switches.

##### Step 3

Now create a new directory called `group_vars` and in the directory create a file called `all.yml`.  The name of the directory called `group_vars` is an important name within Ansible.  It will store "group based variables" - these map directly to the groups that are found in the inventory file.  For example, the variables that end up in `group_vars/all.yml` will be available to all devices.

Updated directory structure:

```
ntc@ntc:~/ansible$ tree
.
├── group_vars
│   └── all.yml
├── inventory
├── nexus.yml
└── templates
    └── csr-ospf.j2

2 directories, 4 files

```

Ensure you have both `csr-ospf.j2` and `all.yml` open in your text editor.

Now, we will start to break down the configuration file into a series of Jinja2 template snippets and variables.

We'll break this down into sections in order to create the appropriate template file.

> **For real-world use, consider using different template files per service (per section of the configuration) as reviewed in the course.**

First we'll look at this snippet going top down from the partial configuration file:

```
!
ip domain name ntc.com
!
no ip domain lookup
lldp run
cdp run
!
vrf definition Mgmt-intf
 !
 address-family ipv4
 exit-address-family
 !
 address-family ipv6
 exit-address-family
!
```

There are always multiple ways to build a template for a given configuration file, but for this snippet, we will only be modifying the domain configuration and the cdp/lldp configuration.  The VRF definition will remain static as it should always be configured on the routers.

##### Step 4

Take the template snippet and put it into the `csr-ospf.j2` file.

```
!
ip domain name {{ domain_name }}
!
{% for feature in features %}
{% if feature.enabled %}
{{ feature.cmd }}
{% else %}
no {{ feature.cmd }}
{% endif %}
{% endfor %}
!
vrf definition Mgmt-intf
 !
 address-family ipv4
 exit-address-family
 !
 address-family ipv6
 exit-address-family
!
```

As you can see, all that is required is to substitute the proper values using double curly braces around your new variable name.  Pretty easy as you can see for the `domain_name`.

We also show an option for enabling/disabling features using a list of dictionaries.  We also could have done each feature separately too.

The snippet above *templated* a few features and the domain name.  The following lines designate the changes made to the file:

We now need to do something with the original configuration parameters for features.

##### Step 5

Within the `group_vars/all.yml`  file, you need to store the values you just removed from the config file (now the template).  Take the snippet and store it in `all.yml` in `group_vars`

Variables Snippet (`group_vars/all.yml`):

```yaml
---

domain_name: ntc.com

features:
  - { cmd: 'ip domain lookup', enabled: false }
  - { cmd: 'lldp run', enabled: true }
  - { cmd: 'cdp run', enabled: true }

```

You're starting to de-couple the inputs from the underlying CLI syntax.  You can now version control each and just make changes to your vars files going forward.

As you can see, if you need to enable more global features on the switch, all you have to do now is modify the list called `features` in `all.yml`.  Of course, this model assumes `no` is used to remove the command, but this is why making templates could be fun (and tedious!).

##### Step 6

Next up are the interfaces.

This is the next section we'll template:

```
!
interface Loopback100
 description OSPF ROUTER ID
 ip address 1.1.1.1 255.255.255.255
 no shutdown
!
interface GigabitEthernet1
 description MANAGEMENT
 vrf forwarding Mgmt-if
 ip address 10.0.0.50 255.255.255.0
 cdp enable
 no shutdown
!
interface GigabitEthernet2
 description CONNECTS_CSR3
 ip address 10.254.13.1 255.255.255.0
 cdp enable
 no shutdown
!
interface GigabitEthernet3
 shutdown
!
interface GigabitEthernet4
 description CONNECTS_CSR2
 ip address 10.254.12.1 255.255.255.0
 cdp enable
 no shutdown
!
```

Anything that repeats itself should be screaming "use a loop" for me!  We can reduce the interfaces down to a single for loop.  We will use a loop that will  iterate through a list of dictionaries.  

This time, we'll create the variables files first.

Create the variables (list of dictionaries) for the interfaces.  Remember, it needs to be done on a per-device basis because the IP addresses used per device are different for every device (think group_vars vs. host_vars).

Create a new directory called `host_vars` also in the `ansible` directory.

Now create three new files in the `host_vars` directory - one per device.  These files need to be named `csr1.yml` and `csr2.yml` because those are the names we gave them in the inventory file. 

File: `host_vars/csr1.yml`

```yaml
---

interfaces:
  - { name: Loopback100, state: up, ip: 1.1.1.1, mask: 255.255.255.255, description: 'OSPF ROUTER ID' }
  - { name: GigabitEthernet1, state: up, ip: 10.0.0.50, mask: 255.255.255.0, description: 'MANAGEMENT', cdp: true, vrf: Mgmt-if }
  - { name: GigabitEthernet2, state: up, ip: 10.254.13.1, mask: 255.255.255.0, description: 'CONNECTS_CSR3', cdp: true }
  - { name: GigabitEthernet3, state: down, cdp: false }
  - { name: GigabitEthernet4, state: up, ip: 10.254.12.1, mask: 255.255.255.0, description: 'CONNECTS_CSR2', cdp: true }

```


File: `host_vars/csr2.yml`

```yaml
---

interfaces:
  - { name: Loopback100, state: up, ip: 2.2.2.2, mask: 255.255.255.255, description: 'OSPF ROUTER ID' }
  - { name: GigabitEthernet1, state: up, ip: 10.0.0.51, mask: 255.255.255.0, description: 'MANAGEMENT', cdp: true, vrf: Mgmt-if }
  - { name: GigabitEthernet2, state: down, cdp: false }
  - { name: GigabitEthernet3, state: up, ip: 10.254.23.2, mask: 255.255.255.0, description: 'CONNECTS_CSR3', cdp: true }
  - { name: GigabitEthernet4, state: up, ip: 10.254.12.2, mask: 255.255.255.0, description: 'CONNECTS_CSR1', cdp: true }

```

##### Step 7

Add the associated template snippet to the `templates/csr-ospf.j2` template.

Please take a few minutes to go through this snippet slowly and look at the vars files simultaneously.

```
!
{% for interface in interfaces %}
interface {{ interface.name }}
{% if interface.get('description') %}
 description {{ interface.description }}
{% endif %}
{% if interface.get('vrf') %}
 vrf forwarding {{ interface.vrf }}
{% endif %}
{% if interface.get('ip') %}
 ip address {{ interface.get('ip') }} {{ interface.get('mask') }}
{% endif %}
{% if interface.get('cdp') %}
 cdp enable
{% endif %}
{% if interface.get('state') == "up" %}
 no shutdown
{% elif interface.get('state') == "down" %}
 shutdown
{% endif %}
!
{% endfor %}
!
```


##### Step 8

The last section that needs to be templated is this:

```
!
router ospf 100
 router-id 1.1.1.1
 log-adjacency-changes
 network 1.1.1.1 0.0.0.0 area 0.0.0.0
 network 10.254.13.1 0.0.0.0 area 0.0.0.0
 network 10.254.12.1 0.0.0.0 area 0.0.0.0
!
ip route vrf Mgmt-intf 0.0.0.0 0.0.0.0 10.0.0.2
!
!
snmp-server community networktocode RO
snmp-server location NYC_NY_DC1
snmp-server contact NETWORK_ADMIN
!
!
end
```

There are a few parameters that will be the same for each router such as OSPF instance tag (or process ID), enabling log-adjacency-changes, the static route, and SNMP settings, but there are also a few parameters that need to be different per device such as the router ID and interfaces that will have OSPF enabled on.  

Make the appropriate changes host vars files, and group vars `all.yml` file.  The text below is only showing what should be added to each vars file.

File: `grup_vars/all.yml`

```yaml

ospf:
  instance_tag: 100
  area: 0.0.0.0
  log_adj: true

mgmt_default_route_next_hop: 10.0.0.2

snmp:
  ro_comm: networktocode
  location: NYC_NY_DC1
  contact: NETWORK_ADMIN


```


File: `host_vars/csr1.yml`

```yaml
router_id: 1.1.1.1

ospf_interfaces:
  - GigabitEthernet2
  - GigabitEthernet4
  - Loopback100

```


File: `host_vars/csr2.yml`

```yaml
router_id: 2.2.2.2

ospf_interfaces:
  - GigabitEthernet3
  - GigabitEthernet4
  - Loopback100

```

##### Step 9


Finally, this is last snippet to be added into the template:

```
router ospf {{ ospf.instance_tag }}
 router-id {{ router_id }}
{% if ospf.log_adj %}
 log-adjacency-changes
{% endif %}
{% for interface in interfaces %}
{% if interface.name in ospf_interfaces %}
 network {{ interface.get('ip')}} 0.0.0.0 area {{ ospf.area }}
{% endif %}
{% endfor %}
!
ip route vrf Mgmt-intf 0.0.0.0 0.0.0.0 {{ mgmt_default_route_next_hop }}
!
!
snmp-server community {{ snmp.ro_comm }} RO
snmp-server location {{ snmp.location }}
snmp-server contact {{ snmp.contact }}
!
!
end
```

Again, review this template with the vars files from the previous step to ensure you understand the logic and Jinja2 syntax.

By this point, your directory structure should look like this:

```
ntc@ntc:~/ansible$ tree
.
├── group_vars
│   └── all.yml
├── host_vars
│   ├── csr1.yml
│   ├── csr2.yml
│   └── csr3.yml
├── inventory
└── templates
    └── csr-ospf.j2

3 directories, 6 files
```

Please note that there isn't a right or wrong way to create templates.  You have to find what works for you and your team, for your configuration.  

For the configuration per interface, you need to see what type of requirements you have in the given environment.  For the config snippet here, we have a few basic parameters to worry about including state (shut or no shut), mode (routed or access), and OSPF parameters, but as you know, there can be many more parameters configured on an interface. 

##### Step 10

**Status Check.**

At this point, you should have created the following template and variables files:

TEMPLATE (`templates/csr-ospf.j2`):

```
!
ip domain name {{ domain_name }}
!
{% for feature in features %}
{% if feature.enabled %}
{{ feature.cmd }}
{% else %}
no {{ feature.cmd }}
{% endif %}
{% endfor %}
!
vrf definition Mgmt-intf
 !
 address-family ipv4
 exit-address-family
 !
 address-family ipv6
 exit-address-family
!
!
{% for interface in interfaces %}
interface {{ interface.name }}
{% if interface.get('description') %}
 description {{ interface.description }}
{% endif %}
{% if interface.get('vrf') %}
 vrf forwarding {{ interface.vrf }}
{% endif %}
{% if interface.get('ip') %}
 ip address {{ interface.get('ip') }} {{ interface.get('mask') }}
{% endif %}
{% if interface.get('cdp') %}
 cdp enable
{% endif %}
{% if interface.get('state') == "up" %}
 no shutdown
{% elif interface.get('state') == "down" %}
 shutdown
{% endif %}
!
{% endfor %}
!
router ospf {{ ospf.instance_tag }}
 router-id {{ router_id }}
{% if ospf.log_adj %}
 log-adjacency-changes
{% endif %}
{% for interface in interfaces %}
{% if interface.name in ospf_interfaces %}
 network {{ interface.get('ip')}} 0.0.0.0 area {{ ospf.area }}
{% endif %}
{% endfor %}
!
ip route vrf Mgmt-intf 0.0.0.0 0.0.0.0 {{ mgmt_default_route_next_hop }}
!
!
snmp-server community {{ snmp.ro_comm }} RO
snmp-server location {{ snmp.location }}
snmp-server contact {{ snmp.contact }}
!
!
end
```

VARS FILES:

`group_vars/all.yml`:

```yaml
---

domain_name: ntc.com

features:
  - { cmd: 'ip domain lookup', enabled: false }
  - { cmd: 'lldp run', enabled: true }
  - { cmd: 'cdp run', enabled: true }

ospf:
  instance_tag: 100
  area: 0.0.0.0
  log_adj: true

mgmt_default_route_next_hop: 10.0.0.2

snmp:
  ro_comm: networktocode
  location: NYC_NY_DC1
  contact: NETWORK_ADMIN

```


`host_vars/csr1.yml`

```yaml
---

interfaces:
  - { name: Loopback100, state: up, ip: 1.1.1.1, mask: 255.255.255.255, description: 'OSPF ROUTER ID' }
  - { name: GigabitEthernet1, state: up, ip: 10.0.0.50, mask: 255.255.255.0, description: 'MANAGEMENT', cdp: true, vrf: Mgmt-if }
  - { name: GigabitEthernet2, state: up, ip: 10.254.13.1, mask: 255.255.255.0, description: 'CONNECTS_CSR3', cdp: true }
  - { name: GigabitEthernet3, state: down, cdp: false }
  - { name: GigabitEthernet4, state: up, ip: 10.254.12.1, mask: 255.255.255.0, description: 'CONNECTS_CSR2', cdp: true }

router_id: 1.1.1.1

ospf_interfaces:
  - GigabitEthernet2
  - GigabitEthernet4
  - Loopback100


```

`host_vars/csr2.yml`

```yaml
---

interfaces:
  - { name: Loopback100, state: up, ip: 2.2.2.2, mask: 255.255.255.255, description: 'OSPF ROUTER ID' }
  - { name: GigabitEthernet1, state: up, ip: 10.0.0.51, mask: 255.255.255.0, description: 'MANAGEMENT', cdp: true, vrf: Mgmt-if }
  - { name: GigabitEthernet2, state: down, cdp: false }
  - { name: GigabitEthernet3, state: up, ip: 10.254.12.2, mask: 255.255.255.0, description: 'CONNECTS_CSR3', cdp: true }
  - { name: GigabitEthernet4, state: up, ip: 10.254.23.2, mask: 255.255.255.0, description: 'CONNECTS_CSR2', cdp: true }


router_id: 2.2.2.2

ospf_interfaces:
  - GigabitEthernet3
  - GigabitEthernet4
  - Loopback100

```

##### Step 11

Once the new configurations are generated from the template and vars file, they need to be stored somewhere.  

We will store them in a new directory called `configs`. 

Create a new sub-directory called `configs` within your `ansible` directory.

The new directory structure will look like this:

```
ntc@ntc:~/ansible$ tree
.
├── configs
├── group_vars
│   └── all.yml
├── host_vars
│   ├── csr1.yml
│   └── csr2.yml
├── inventory
├── nexus.yml
└── templates
    └── csr-ospf.j2

4 directories, 6 files
```

### Task 4 - Building the Configurations

It's time to create the playbook that will auto-generate all of the configuration files.

##### Step 1

Create and open a file called `build-push.yml`.

Save it in the `ansible` directory.

```
ntc@ntc:~/ansible$ tree
.
├── build-push.yml
├── configs
├── group_vars
│   └── all.yml
├── host_vars
│   ├── csr1.yml
│   └── csr2.yml
├── inventory
├── nexus.yml
└── templates
    └── csr-ospf.j2

4 directories, 7 files
```


The playbook will consist of a single play and a single task.  The task will use the "template" module.

Two parameters will be passed to the module:  `src` which is the source template, i.e. `csr-ospf.j2` and the `dest`, which is the where the final rendered config file will be stored.

```yaml
---

  - name: Build Configuration Files
    hosts: routers
    connection: local
    gather_facts: no

    tasks:
      - name: BUILD CONFIGS
        template: src=templates/csr-ospf.j2 dest=configs/{{ inventory_hostname }}.cfg

```

> Note: we are using the variable `inventory_hostname` in the value for the `dest` parameter.  This will make a file with the name of the device as it is defined in the inventory file.


##### Step 2

Save the playbook (don't close it) and go back to the Linux command line.

Execute the playbook using the following command:

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory build-push.yml
```

You will see the following output during execution:

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory build-push.yml

PLAY [Build Configuration Files] ********************************************** 

TASK: [BUILD CONFIGS] ********************************************************* 
changed: [csr1]
changed: [csr2]

PLAY RECAP ******************************************************************** 
csr1                       : ok=1    changed=1    unreachable=0    failed=0   
csr2                       : ok=1    changed=1    unreachable=0    failed=0   



```

##### Step 3

Take notice of your new file/directory structure.

```
ntc@ntc:~/ansible$ tree
.
├── build-push.yml
├── configs
│   ├── csr1.cfg
│   └── csr2.cfg
├── group_vars
│   └── all.yml
├── host_vars
│   ├── csr1.yml
│   └── csr2.yml
├── inventory
├── nexus.yml
└── templates
    └── csr-ospf.j2

4 directories, 9 files


```

Open one or more of the new configuration files and a take look at them.

You should understand that the template was *rendered* with multiple variables (including all of the host vars and group vars) that you defined.

You could now easily add more device in the inventory and add more files in the host_vars for each new device you want to generate a config for.


### Task 5 - Pushing the Configurations

Now that the configs are built, they are ready to be pushed.

The Ansible module that will be used to push the configurations is called "napalm_install_config".

This module is a multi-vendor module supporting vendors/platforms such as Arista, Cisco IOS, Cisco Nexus, Cisco IOS-XR, and Juniper.  It has the ability to automatically apply a configuration and make it the device's active running configuration.  By using this module, we will apply the configuration generated in the previous task.

The following parameters will be used for the napalm_install_config module (as shown below):
  - hostname: this is the IP / FQDN of the node you are automating.  We are using the IP from the inventory file
  - username: to login to the switch
  - password: to login to the switch
  - dev_os: platform of the target node
  - config_file: that will be sent and applied to the device.  Notice how we are using a variable to push the correct file.
  - commit_changes: if True, they will be applied (it's possible not to apply and just generate diffs)  Diffs cannot be generated for IOS (yet)
  - replace_config: it's possible to do a replace/overwrite or a merge.  We are doing a merge.
  - get_diffs: is false because the platform we are using does not support generating diffs.  This means we will always apply the config (not idempotent).

> Also take notice of the `tags` directive for each task below.  This will allow us to run just this task from the terminal.

##### Step 3

Add the following task to the `build-push.yml` playbook:

```yaml
---

  - name: Build Configuration Files
    hosts: routers
    connection: local
    gather_facts: no

    tasks:
      - name: BUILD CONFIGS
        template: src=templates/csr-ospf.j2 dest=configs/{{ inventory_hostname }}.cfg
        tags: build

      - name: PUSH CONFIGS
        napalm_install_config:
          optional_args:
            dest_file_system: 'bootflash:'
          hostname: "{{ inventory_hostname }}"
          username: "{{ un }}"
          password: "{{ pwd }}"
          dev_os: ios
          config_file: configs/{{ inventory_hostname }}.cfg
          commit_changes: true
          replace_config: false
          get_diffs: false
        tags: push


```

**IMPORTANT: ENSURE YOU HAVE THE CORRECT MANAGEMENT IP ADDRESS IN EACH OF THE HOST_VARS FILES.  OTHERWISE, YOU WILL LOSE CONNECTIVITY TO THE DEVICES!!!!**

##### Step 4

Execute ONLY the new task by using tags.

> SSH to one or more of the routers if you want to look at the configs before and after you push the new configs.

```
ntc@ntc:~/ansible$ ansible-playbook -i inventory build-push.yml --tags=push

PLAY [Build Configuration Files] ********************************************** 

TASK: [PUSH CONFIGS] ********************************************************** 
changed: [csr2]
changed: [csr1]

PLAY RECAP ******************************************************************** 
csr1                       : ok=1    changed=1    unreachable=0    failed=0   
csr2                       : ok=1    changed=1    unreachable=0    failed=0   
```

Congratulations!  You just pushed configuration to two routers!






