# networkdevice

networkdevice
-------------
Python modules to execut command on remote network device.

1. Introduction

networkdevice is a python module that allow you run command on remote network
devices just like locally.

For example, define a network devices and show the interface on it, it's
especially useful for network test automation:

    pc = networkdevice.LinuxDevice(device)
    print pc.cmd("ifconfig")

See demo/ftp_test.py for examples and use help command show the documents.

    import from networkdevice import junos, linux
    help(cisco)
    help(junos)
    help(linux)

2. Feature

    1) Python Based: Plenty of feature
    2) Environmentally friendly: can run anywhere where there is python and connect to the devices.
    3) Easy to Learn: Don't need to know anything about Python
    4) Easy to write: One case only have several to dozens of lines.
    5) Faster: run the testbed from local and is much faster.
    6) object oriented: Flexible and Easy to extend
    7) WYWIWYT(What you write is what you think): Not write SCRIPT, but write ACTION and THOUGHT

3. Test architecture based on networkdevice

           +---------------------------------+------------+----------------------+
           |                                 |            | case1                |
           |                                 |            +----------------------+
           |  One case                       | Test Suite | ...                  |
           |                                 |            +----------------------+
           |                                 |            | caseN                |
           +---------------------------------+------------+----------------------+
           |  networkdevice                                                      |
           |                                                                     |
           |  PC1                  DUT                  DUT                      |
           |  +---------------+    +---------------+    +---------------+        |
           |  | Linux devices |    | Junos devices |    | Cisco devices |  ...   |
           |  +---------------+    +---------------+    +---------------+        |
           |                       | Linux devices |    | Linux devices |  ...   |
           |                       +---------------+    +---------------+        |
           +---------------------------------------------------------------------+

                   Figure 1. test Architecture based on networkdevice
 
4. An example

        #!/usr/bin/env python

        from networkdevice import cisco, junos, linux

        tangshan = {
                "name": "tangshan",
                "host": "tangshan",
                "username": "dev",
                "password": "1234",
                "root_password": "5678",
                "interface": [
                    { "ip": "1.1.1.2/24", "ip6": "2001::2/64", "name": "fe-0/0/2.0", "zone": "untrust" },
                    { "ip": "4.4.4.1/24", "ip6": "2004::1/64", "name": "fe-0/0/6.0", "zone": "trust" }],
                "preconfig": [ "set routing-options static route 1.1.1.0/24 next-hop 2.2.2.1"] 
                }

        ent_vm01 = { "name": "ent-vm01",
                "host": "ent-vm01",
                "prompt": "root@ent-vm01 ~",
                "username": "root",
                "password": "5678",
                }

        if __name__ == '__main__':
            '''
            Topology:

                                 +----------+
                                 |  server  |
                                 +----+-----+
                                      | int0
                                      |
                                      | int0
                                 +----+-----+
                                 |   DUT    |
                                 +----------+
                                      | int1
                                      |
                                      | int0
                                 +----+-----+
                                 |  client  |
                                 +----------+
            '''
            # Use device descriptor to create a linux and junos device
            dut = junos.JunosDevice(tangshan)
            client = linux.LinuxDevice(ent_vm01)
            # Use parameter list to create a linux device
            server = linux.LinuxDevice(name = "ent-vm02", host = "ent-vm02",
                    prompt = "root@ent-vm02 ~", username = "root", password = "5678")

            # Dump all the dut's attributes
            dut.dumps()

            # execute a non-interactive command and return the result
            print client.cmd('date')

            # execute an interactive command
            client.cmd('ftp 1.1.1.2', expect = "Name")
            client.cmd('%s' %(server["username"]), expect = "Password")
            client.cmd('%s' %(server["password"]), expect = "ftp")
            print client.cmd('ls', expect = "ftp")

            # check session on the Juniper srx firewall
            print dut.cli('show security flow session application ftp')
            # check session in dict format
            displayed = dut.cli('show security flow session application ftp',
                    format = "dict",
                    force_list=('flow-session'))
            print "Total %s session found" %(displayed['flow-session-information']['displayed-session-count'])

            # tear down the ftp session
            client.cmd('bye')
