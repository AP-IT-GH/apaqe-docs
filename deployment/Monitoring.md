# Monitoring

There will be 2 playbooks, 1 is the **checkmk-server** which will be to set up the central connection point of all devices that will be getting monitored. **this has to be a very secure server. 
**

*[Here](https://www.liquidweb.com/blog/secure-server/) is help on how to secure a server for serving as a checkmk-server.*

the other playbook is for all servers/systems that need to be monitored, this is called an agent. installing this should automatically add the device to the main checkmk server.

*Please note that setting the variables correct, according to your infrastructure, is always necessary.*

## User guide

To make the handling of Checkmk as easy as possible, the articles in this User Guide follow rather unusual approaches in many places. It is almost never a matter of simply copying a prefabricated sequence of individual steps. Rather, it is intended to give you, the reader, a deeper understanding of a feature in Checkmk.

Here is a link to the official documentation on how to use [Checkmk](https://docs.checkmk.com/latest/en/) .

## Additional security options

[Here](https://checkmk.com/blog/security-best-practices-checkmk#:~:text=For%20%27cmkadmin%27%2C%20Checkmk%20randomly,the%20Global%20settings%20of%20Checkmk) you can find additional configuration settings for securing the checkmk as an application.
