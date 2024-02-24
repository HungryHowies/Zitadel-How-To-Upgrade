# Zitadel-How-To-Upgrade

# Overview


The following example shows how to upgrade a minor version of Zitadel (i.e, v2.42.10 to v.2.44.2). 
This instance does have a systemd service created. More information about this setup can be found [here](https://github.com/HungryHowies/How-To-Build-Zitadel-from-Source) 

# Upgrade Zitadel

The following release are shown here https://github.com/zitadel/zitadel/releases 

Steps: 

Navigate to home directory.

``` 
cd /home
```

Download the new version.

```
wget https://github.com/zitadel/zitadel/archive/refs/tags/v2.44.2.tar.gz
```

Unzip Source.

```
sudo tar -xvf  v2.44.2.tar.gz
```

Go into Zitadel old version directory.

```
cd /home/zitadel-2.44.2/
```

Depending on what files were modified they need to be copied over to Zitadel current version. Ensure to check new release information before copying over the new files, this could create issues.

Example:

```
cp console/src/index.html  /home/zitadel-2.44.2/console/src/index.html 
```

# Edit Makefile

The Makefile should be located in Zitadel's home directory for the new build (/home/zitadel-2.44.2).

```
vi /home/zitadel-2.44.2/Makefile
```

Match the package version download, edit the following line. This will be shown in the console when finished.

```
now := $(shell date --rfc-3339=seconds | sed 's/ /T/')
VERSION ?= v2.44.2                                 <---------- HERE
```

Run make.

```
make
```

Stop Zitadel service.
```
systemctl stop zitadel

```

Copy new Zitadel version executable made to the working directory. You can either remove the old Zitadel executable or copy over the old one.

```
cp  /home/zitadel-2.44.2/zitadel /usr/local/bin/zitadel
```

# Update tables

Change directory.

```
cd /usr/local/bin
```

Execute folowwing command. This may take a few minutes.

Execute with the "Steps" file.

```
zitadel setup  --init-projections=true   --config defaults.yaml --steps steps.yaml --masterkey "MasterkeyNeedsToHave32Characters"  --tlsMode external
```

Without the "Steps" file.

```
zitadel setup  --init-projections=true   --config defaults.yaml  --masterkey "MasterkeyNeedsToHave32Characters"  --tlsMode external
```


Start Zitadel service. 

```
systemctl start zitadel
```

Console should show new version.

 ![image](https://github.com/HungryHowies/Zitadel-How-To-Upgrade/assets/22652276/ee93a4f3-7783-4b69-aab0-88d083f07daa)


NOTE: Another option is at the botton of the  defaults.yaml file this can be set  to true.

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
