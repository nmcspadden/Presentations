build-lists: true
footer: MacBrained, May 2016 
slidenumbers: false

# Automating Imaging Creation at Facebook 
## Heavy Edition

#### Nick McSpadden, Milestone @ Facebook

---

# How We Do It:

AutoDMG - the base image  
<br>
<br>
Munki - software deployment  
<br>
<br>
Chef - config management  

---
# Creating The Image: AutoDMG

AutoDMG gives us the base OS, and Apple updates.
<br>

It also supports templates that allow arbitrary packages to be added.
<br>
It can work entirely via CLI, no GUI interaction required.

---

# Software Deployment: Munki

We specify what software goes on the machine, Munki downloads and installs it.
<br>

To simplify matters, all machines get the same basic set of software regardless of eventual destination.
<br>

The Munki manifest has a list of all items that will be installed, on a remote server.

---

# Configuration Management: Chef

Chef applies all of our management, policies and configurations. 
<br>

Chef needs to run first to lay down the policies (including binding to directory services so a user can log in).
<br>

Until Chef completes its first two runs, the machine is incomplete.

---

# We can query Munki for the list of software it thinks should be installed.

# To do that, we can use Munki code directly in Python.

---

# Adding `munkilib` to your Python code:

```
import sys
with open('/private/etc/paths.d/munki', 'rb') as f:
  munkipath = f.read().strip()
sys.path.append(os.path.join(munkipath, 'munkilib'))
try:
  from munkicommon import pref, getsha256hash
  import updatecheck
  from fetch import (getURLitemBasename, getResourceIfChangedAtomically,
                     MunkiDownloadError, writeCachedChecksum,
                     getxattr, XATTR_SHA)
  import keychain
except ImportError as err:
  print "Something went wrong! %s" % err
```

---

# We can use Munki code to download these items from the repo:
```
# These are necessary to populate the globals used in updatecheck
keychain_obj = keychain.MunkiKeychain()
manifestpath = updatecheck.getPrimaryManifest(manifest)
updatecheck.getPrimaryManifestCatalogs(.manifest)
updatecheck.getCatalogs([catalog])
# Populate the lists
installinfo = {}
installinfo['processed_installs'] = []
installinfo['processed_uninstalls'] = []
installinfo['managed_updates'] = []
installinfo['optional_installs'] = []
installinfo['managed_installs'] = []
installinfo['removals'] = []
updatecheck.processManifestForKey(manifestpath, 'managed_installs',
                                  installinfo)
# installinfo['managed_installs'] now contains a list of all managed_installs
```

---

# For simple packages, we can download them, and add these to the AutoDMG template.

---

# For "complex" or non-standard items (like profiles), we can pre-download them and place them in the Munki cache.

Why should Munki spend time downloading items directly after imaging?

That time can be eliminated significantly by precaching them into `/Library/Managed Installs/Cache/`.

---

# Exceptions & Additions

We use a JSON file containing a list of exceptions and additions to be cached/added:

```
{
  "additions_list": [  
    # These get downloaded & added directly to the AutoDMG template
    "https://munki/munki_repo/pkgs/apps/microsoft/office2016/Microsoft_Office_2016_15.22.0_160506_Installer-15.22.0.pkg",
    "https://munki/munki_repo/pkgs/profiles/Office2016-SuppressFirstRun-1.2.mobileconfig"
  ],
  "exceptions_list": [ 
    # These are Munki items that should be installed by Munki bootstrapping
    "BomgarClient",
    "Computrace",
    "MicrosoftOffice2016_Serializer"
  ]
}
```

---

# Additions
These packages get added to the AutoDMG template - either a local file path, or available by URL.

```
{
  "additions_list": [  
    # These get downloaded & added directly to the AutoDMG template
    "https://munki/munki_repo/pkgs/apps/microsoft/office2016/Microsoft_Office_2016_15.22.0_160506_Installer-15.22.0.pkg",
    "https://munki/munki_repo/pkgs/profiles/Office2016-SuppressFirstRun-1.2.mobileconfig"
  ],
```

---

# Exceptions
Due to issues with these packages' scripts, don't attempt to install them in the image. 
<br>

Instead place them directly into the Munki cache folder, so that Munki installs them during bootstrapping.

```
  "exceptions_list": [ 
    # These are Munki items that should be installed by Munki bootstrapping
    "BomgarClient",
    "Computrace",
    "MicrosoftOffice2016_Serializer"
  ]
}
```

---

# Some other things we want to consider:

Munki caches the icons of all the items in Managed Software Center.

Chef caches all of the cookbooks it runs locally.

We want to preload special configs and our Mac management codebase.

We want to avoid downloads or dependencies on external sources as much as possible.

---

# The Workflow:

---

# 1

## Query list of Munki items to be installed.


---

# 2

## Download all Munki items to be installed into a local download folder.

---

# 3

## If it's a normal package, add it to AutoDMG template.

## If it's a "different" item, move it to a staging Munki cache folder.

---

# 4

## Package up all of the Munki icons, add to AutoDMG template.

---

# 5

## Package up all of the Chef cache, add to AutoDMG template.

---

# 6

## Package up the packages we want to preinstall.

---

# 7

## Package up the preloaded Munki cache.

---

# 8

## Package up the Chef package, so it doesn't require download on firstboot.

---

# 9

## Add any additional packages we've specified.

---

# 10

## Update the AutoDMG [`UpdateProfiles.plist` template](https://github.com/MagerValp/AutoDMGUpdateProfiles/blob/master/UpdateProfiles.plist).

```
  <key>Profiles</key>
  <dict>
    <key>10.10.5-14F27</key>
    <array>
      <string>RemoteDesktopClient</string>
      <string>iTunes</string>
      <string>SafariYo</string>
      <string>SecurityYo</string>
    </array>
    <key>10.11.5-15F34</key>
    <array>
      <string>iTunes</string>
    </array>
```

---

# 11

## Build the image from our template

---

# 12

## 