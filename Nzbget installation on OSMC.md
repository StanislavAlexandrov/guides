# Nzbget installation guide on OSMC 2021

## Basic idea (tl:dr)

use the [official guide](https://nzbget.net/installation-on-linux/)  with a few specific OSMC (Debian) changes as follows:
- install into /opt
- chown the nzbget folder (user: osmc)
- autostart using systemd
- (optional) require external drives to be available before starting: to be added

## Step by step (beginner friendly)

1. Use the terminal to SSH into the device. You can find out the *IP_ADDRESS* of the device in your router settings (something like 192.168....). On a Mac, the first password you will be asked to provide is your Mac password (the one you type in when you restart your Mac), the second password is for the device (default: osmc). Hint: passwords are NOT shown in the terminal, so simply type them in accurately and press enter. 

```
sudo ssh osmc@IP_ADDRESS
```

2. Go to the /opt folder:

```
cd /opt
```

3. Download the official script (hint: if prompted, use the osmc password):

```
sudo wget https://nzbget.net/download/nzbget-latest-bin-linux.run
```

Wait for the download to finish.

4. Run the script (hint: when prompted, use the osmc password): 

```
sudo sh nzbget-latest-bin-linux.run
```

5. Now the resulting nzbget folder is owned by root and we need to change that. First, check it by running: 

```
ls -la
```

If you try to do what the script tells you now (nzbget -s), you will get a permissions error. We need to change the owner to osmc: 

```
sudo chown -R osmc nzbget
```

6. Now try running this command and it should work ok (no errors):

```
nzbget/nzbget -s
```

7. Now that we know nzbget can start fine, we need to add it to autostart. There are a few ways to do it on Linux, we are going to use 'systemd'.

```
sudo nano /etc/systemd/system/nzbget.service
```

A built-in editor (nano) will open, copy the following and paste: 

```
[Unit]
Description=NZBGet Daemon
After=network.target

[Service]
User=osmc
Group=osmc
Type=forking
ExecStart=/opt/nzbget/nzbget -c /opt/nzbget/nzbget.conf -D
ExecStop=/opt/nzbget/nzbget -Q
ExecReload=/opt/nzbget/nzbget -O
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Now we need to save the contents of the file (press Ctrl + O and then Enter) and exit (press Ctrl + X)

And finally: 

```
sudo systemctl enable nzbget.service
```



