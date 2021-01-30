# botnet-fishbowl
Use random bots scanning the internet to seed a Conways Game of Life running on a small LCD on my desk

Conways Game of Life tends to reach a state where it becomes stable, but it certainly really fun to look at. The general gist of this project is very simple: Use bots scanning the internet to re-seed the game state.

The code is basically as follows regardless of platform:
* Bind to TCP port 6666
* Initialize GOL with random seed
* LOOP
* * IF: we have new data on port 6666, re-seed gamestate
* * ELSE: Animate and process the next step of GOL

Included in this repo is:
* A golang version which can be compiled for any computer/rasp pi. (.go)
* A arduino version intended for an ESP32 M5STACK (.ino)
* A micropython version intended for and ESP32 M5STACK (.mpy) which still needs the networking figured out.

Basic compilation instructions are included in subfolders.

---

"Cool, it binds to port 6666. How do I get actual bots to send traffic?"
* Port forwarding and Tunneling.

Spin up a small linux VM with a public IP on a cloud provider (which bots will inevitably scan)

I'm a big fan of autossh (https://linux.die.net/man/1/autossh) which can be used as follows to forwawrd traffic for port 6666 on the cloud VM to port 6666 on you local machine through an SSH reverse tunnel.

```
/usr/bin/autossh -M 0 -N -o "ServerAliveInterval 15" -o "ServerAliveCountMax 3" -o "ConnectTimeout 10" -o "ExitOnForwardFailure yes" -i /home/user/.ssh/private_rsa -p 22 users@<public-IP> -R 6666:localhost:6666
```

"But bots don't typically scan port 6666!"

Some do, but you likely want to pick up traffic from commonly scanned ports like 21 (FTP), 139 (SMB), 3389 (RDP) etc...

Use socat (https://linux.die.net/man/1/socat)

```
socat TCP-LISTEN:3389,fork TCP:localhost:6666
```

Traffic will come in on port 3389 of the cloud VM and be redirected to localhost port 6666 on the cloud VM. If your SSH reverse tunnel listed above is listening on port 6666, it will be routed over the tunnel, into your home network, and effect the game state of the LCD on your desk.


Between these two tools, you can connect pretty much anything.