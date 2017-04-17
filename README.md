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

                   test Architecture based on networkdevice
 
