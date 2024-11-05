# Monitor data usage

In order to monitor data usage, which can be really important when using a 4G modem on the WAGO PFC200 4G, you can use this container : 

https://github.com/vergoh/vnstat-docker

```
docker run -d \
--restart=unless-stopped \
--network=host \
-e HTTP_PORT=8685 \
-v /etc/localtime:/etc/localtime:ro \
-v /etc/timezone:/etc/timezone:ro \
--name vnstat \
vergoh/vnstat
```


## Web interface

It will provide a simple web interface to display the network data usage over the different interface.

## Script usage

You can additionnaly use it in command line, in order to trigger an action when a threshold is reached (see script monitor_usage)

```shell
#!/bin/bash

vnstat='docker exec -ti vnstat vnstat'

if ! $vnstat -i wwan0 --alert 0 3 monthly total 50 MiB; then
    # The wwab0 interface exceeded the 50 MiB transmitted limit
    # You can send an alert or trigger any other action
   echo "threshold reached"
fi
```
Download and place the script monitor_container_led i.e. folder /etc/config-tools.

Give it execution permission :

```shell
chmod 750 /etc/config-tools/monitor_usage
```

## Set periodic check
Then you need to use CRON in order to trigger a period call of the script.

To open crontab with nano (easier than vim ?)
```shell
export VISUAL=nano; crontab -e
```
Copy/Paste the following (adapt to your script location) :
```shell
* * * * *  /etc/config-tools/monitor_usage
```
This will check the data usage every minute.
