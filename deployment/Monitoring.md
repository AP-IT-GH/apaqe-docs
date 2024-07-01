# Monitoring

There will be 2 playbooks, 1 is the checkmk-server which will be to set up the central connection point of all devices that will be getting monitored. this has to be a very secure server.

the other playbook is for all devices that need to be monitored, this is called an agent. installing this should automatically add the device to the main checkmk server.

Please note that setting the variables correct, according to your infrastructure, is always necessary.

## User guide

To make the handling of Checkmk as easy as possible, the articles in this User Guide follow rather unusual approaches in many places. It is almost never a matter of simply copying a prefabricated sequence of individual steps. Rather, it is intended to give you, the reader, a deeper understanding of a feature in Checkmk.

Here is a link to the official documentation on how to use [Checkmk](https://docs.checkmk.com/latest/en/) .
