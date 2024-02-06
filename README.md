# Zitadel-How-To-Upgrade


Upgrade Zitadel

Steps: 

Download the new version.

```
wget https://github.com/zitadel/zitadel/archive/refs/tags/v2.42.14.tar.gz
```

Navigate to the old Zitadel version directory.

```
root@zitadel-build:/# cd /home/zitadel-2.42.10/
```

Depending on what files were modified they need to be copied over to Zitadel current version.

Example:
```
cp console/src/index.html  /home/zitadel-2.42.14/console/src/index.html 
```

The Makefile should be located in Zitadel's home directory for the new build (/home/zitadel-2.42.14).


root@zitadel-build:/# vi /home/zitadel-2.42.14/Makefile

Edit the following lines to match the package version download. This will be shown in the console when finished.

now := $(shell date --rfc-3339=seconds | sed 's/ /T/')
VERSION ?= v2.42.14                                 <---------- HERE

Run make.
```
root@zitadel-build:/home/zitadel-2.42.14# make
```

Stop Zitadel service.
```
root@zitadel-build:/# systemctl stop zitadel
```

Copy new Zitadel version executable made to the working directory. You can either remove the old Zitadel executable or copy over the old one.
```
root@zitadel-build:/# cp  /home/zitadel-2.42.11/zitadel /usr/local/bin/zitadel
```
NOTE: botton of the file defaults.yaml I set this to true.
```
# If a new projection is introduced it will be prefilled during the setup process (if enabled)
# This can prevent serving outdated data after a version upgrade, but might require a longer setup / upgrade process:
# https://zitadel.com/docs/self-hosting/manage/updating_scaling
InitProjections:
  Enabled: true  # ZITADEL_INITPROJECTIONS_ENABLED                <-------------- HERE
  RetryFailedAfter: 100ms # ZITADEL_INITPROJECTIONS_RETRYFAILEDAFTER
  MaxFailureCount: 2 # ZITADEL_INITPROJECTIONS_MAXFAILURECOUNT
  BulkLimit: 1000 # ZITADEL_INITPROJECTIONS_BULKLIMIT
```

Update tables.
```
zitadel setup  --init-projections=true   --config defaults.yaml --steps steps.yaml --masterkey "MasterkeyNeedsToHave32Characters"  --tlsMode external
```
Or inable that in zitadel configurtion file.
```
root@zitadel-build:/usr/local/bin/# zitadel setup   --config defaults.yaml --steps steps.yaml --masterkey "MasterkeyNeedsToHave32Characters"  --tlsMode external
```

Start Zitadel service. 

```

root@zitadel-build:/# systemctl start zitadel
```
