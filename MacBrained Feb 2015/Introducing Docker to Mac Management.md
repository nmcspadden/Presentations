# Introducing Docker to Mac Management

### Nick McSpadden<br>Client Systems Manager<br>Schools of the Sacred Heart


---

# What the heck is Docker?

### [https://www.docker.com/whatisdocker/]()

![inline fill](docker.com.png)

---

# Simple explanation:
##A wrapper around a tiny sandboxed Linux environment.

##Docker containers are designed to run a single service or application.

---

![left fit](dockervsvms.png)

# Thinner than running a new VM for a service

---

![right fit](dockerrepos.png)

# Examples:

* A database - Postgres, MySQL
* Webserver
* Puppet server
* Wordpress blog
* Node.js

  
  
[https://www.docker.com/resources/usecases/]()

---

# But this is all Linux based! How does this involve Macs? I have OS X Server!

![inline](server.app.tiff)

---

![right fit](server.app_services.png)

# Look at Server.app.

Not many services that are truly unique to OS X:

* Caching Service (arguable vs. squid)
* Messages (no one uses this)
* Time Machine Server (no one uses this)
* Xcode (don't, it'll hurt)
* OpenDirectory (not common in enterprise)
* Xsan (wait, this still exists?)

---

![right fit](server.app_services.png)

* CalDAV
* NFS, SMB, Netatalk (lol)
* Mail (duh)
* Mobile Device Management
* Enterprise backup (Code42?)
* VPN via firewall appliances
* Apache / nginx
* Git
* dhcpd
* bind
* BSDPy

---

# You can run lots of neat Mac management tools without ever needing OS X Server, or OS X.

## And if you can do it in Linux...

---

# ...you can do it in Docker!

![inline](dockerlogo.jpg)

---

# Time for demo.