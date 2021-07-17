---
title: "User Systemd Service"
date: 2021-07-17T14:23:04+12:00
draft: false
toc: false
images:
tags:
  - systemd
  - Linux
---
![systemdlogo](/static/page-logo.png)

Creating your own systemd services. 

I recently created a discord bot and was sick of putting it online each time my 
friends tried to use it. As most linux distributions come with `systemd` I decided
to learn how to use it and solve my problem of forgetting to put the bot online
each time.

Though this blog post refers to a python script you can use it with any script 
or program of your choice.

### Dependencies 
- A "bloated" linux distro that uses systemd
- A script or program to manage 

---
Systemd service files are located in the `/etc/systemd/system/` directory.

Create a unit file with your chosen name of your service followed by `.service`
, mine for instance was:
```
/etc/systemd/system/ $ sudo touch discord-bot.service
```
This is my file I will explain the relevant parts below
```
[Unit]
Description=<short_description_of_program>
After=network.target
StartLimitIntervalsec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=<your_username>
WorkingDirectory=<path_to_program_files>
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=<program_ID>
ExecStart=/usr/bin/python3 /home/<user>/discordBot/main.py

[Install]
WantedBy=multi-user.target
```
In the `[Unit]` section we can handle dependencies, as my bot needs to be 
connected to the internet we specify the `After` option for the service to only
start after the network interfaces are up.

In the `[Service]` section we can set the type of the service, the Arch-wiki
provides an in depth description of the different service types and their uses 
which can be found [here](https://wiki.archlinux.org/title/Systemd#Service_types).
For most cases `Type=simple` will be all you need. `User` needs to be the user 
who is running the service.

For `ExecStart` we specify the command that is being run. 
In my case the line in the unit file is the same as running:
```
$ python3 main.py
``` 
if I was `cd` in to the directory with my bot. Specify a working directory that 
may contain any other files needed for the runtime of your service with the 
`WorkingDirectory` option, for instance in my case this directory contained  the 
token files for the discord api.

### logging

We added 3 special options to enable logging of our service
  - `StandardOutput=syslog`
  - `StandardError=syslog`
  - `SyslogIdentifier=<program_ID>`

This redirects all stdout and stderr to the syslog, we need to create a unique 
program identifier.

Next create a file in `/etc/rsyslog.d/<service_name>.conf`
```
if $programname = '<program_ID>' then /path/to/log/file.log
& stop
```
and then set the logfile ownership to the syslog user with: 
```
# chown syslog:adm /path/to/file.log
```
---
All thats left is to restart the restart the rsyslog service.
```
# systemctl restart rsyslog
```
Enable our newly created service so it starts on boot.
```
# systemctl enable discordBot.service
```
Start the service.
```
# systemctl start discordBot.service
```

### References 
- [systemd logo used under creative commons license 4.0](https://github.com/systemd/brand.systemd.io/blob/master/assets/page-logo.png)

- [Archwiki](https://wiki.archlinux.org/title/systemd)

- [Stack Overflow](https://stackoverflow.com/questions/37585758/how-to-redirect-output-of-systemd-service-to-a-file)








