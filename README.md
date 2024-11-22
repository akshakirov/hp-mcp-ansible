# This playbook install necessary tools for HPE Proliant servers. 

``` ansible-playbook --diff --inventory hosts --private-key ~/.ssh/id_rsa --user ansible  hp-mcp-playbook.yml --limit  proliant-servers --diff ```

## hp-health provides tools:
 * hpasmcli
 * hpbootcfg
 * hplog
 * hpuid

```
 hpasmcli -s "SHOW SERVER"
 hpasmcli -s "SHOW DIMM" | grep -E "Module|Size|Status"
 hpasmcli -s "SHOW TEMP; SHOW FAN"
 hpasmcli -s "SHOW IML" | more
 hplog -t
 hplog -f
 hplog -p
```



## Using the `ssacli` tool

**Command short and long names**
All commands have a short name to reduce the length of the total input provided to the `ssacli` tool. You can use short or long name. Here is a list of all commands and their long and short names:

* chassisname = ch
* controller = ctrl
* logicaldrive = ld
* physicaldrive = pd
* drivewritecache = dwc
* licensekey = lk

You can specify disks also as:

* A range of drives (bay 1 to 3): 1I:1:1-1I:1:3
* Drives that are unassigned: allunassigned

## Command examples
Here are few examples of  `ssacli` commands that you can use to diagnose and manage your HP Smart Storage Controller.

**Show available controllers**

	ssacli ctrl all show

**Show controllers status**

	ssacli ctrl all show status

**Show detailed controllers information**

	ssacli ctrl all show detail

**Show controllers configuration**

	ssacli ctrl all show config

**Rescan for new devices**
Useful after swapping disks in bays, etc...
	
	ssacli rescan

**Show all physical disks (or their status) (controller slot 0)**

	ssacli ctrl slot=0 pd all show
	ssacli ctrl slot=0 pd all show status

**Show all physical disks detailed information (controller slot 0)**
	
	ssacli ctrl slot=0 pd all show detail

**Show logical drives (or their status) (controller slot 0, all or specific logical drive(s))**

	ssacli ctrl slot=0 ld all show
	ssacli ctrl slot=0 ld all show status
	
	ssacli ctrl slot=0 ld 1 show
	ssacli ctrl slot=0 ld 1 show status

**Show detailed logical drives information (controller slot 0, all or specific logical drive(s))**

	ssacli ctrl slot=0 ld all show detail
	ssacli ctrl slot=0 ld 1 show detail

**Show array information (controller slot 0, array A)**	

	ssacli ctrl slot=0 array a show

**Show array status (controller slot 0, all arrays)**	

	ssacli ctrl slot=0 array all show status

**Create new RAID 0 logical drive (controller slot 0, disk in port 1I:box 1:bay 1)**

	ssacli ctrl slot=0 create type=ld drives=1I:1:1 raid=0

**Create new RAID 1 logical drive (controller slot 0, disks in port 1I:box 1:bay 1 and 2)**
	
	ssacli ctrl slot=0 create type=ld drives=1I:1:1,1I:1:2 raid=1

**Create new RAID 5 logical drive  (controller slot 0, diks in port 1I:box 1:bay 1 to 4)**

	ssacli ctrl slot=0 create type=ld drives=1I:1:1-1I:1:4 raid=5

**Delete logical drive (controller slot 0, logical drive 1)**

	ssacli ctrl slot=0 ld 1 delete

**Add new physical disks to logical drive (controller slot 0, logical drive 1, disks in port 1I:box 1:bay 6 and 7)**

	ssacli ctrl slot=0 ld 2 add drives=1I:1:6,1I:1:7

**Add spare disks (controller slot 0, logical drive 1, array A, disks in port 1I:box 1:bay 6 and 7)**

	ssacli ctrl slot=0 array a add spares=1I:1:6,1I:1:7

**Add global spare disks (controller slot 0, logical drive 1, all arrays, disks in port 1I:box 1:bay 6 and 7)**

	ssacli ctrl slot=0 array all add spares=1I:1:6,1I:1:7

**Turn on/off blink logical drive LED (controller slot 0, logical drive 1)**

	ssacli ctrl slot=0 ld 1 modify led=on
	ssacli ctrl slot=0 ld 1 modify led=off

**Turn on/off blink physical disk LED (controller slot 0, physical disk port 1I:box 1:bay 1)**  
	
	ssacli ctrl slot=0 pd 1I:1:1 modify led=on
	ssacli ctrl slot=0 pd 1I:1:1 modify led=off

**Modify smart array cache read and write ratio (controller slot 0, cacheratio 80% read/20% write)**  

	ssacli ctrl slot=0 modify cacheratio=80/20

**Show physical drive write cache status (controller slot 0)** 

	ssacli ctrl slot=0 modify dwc=?

**Enable/disable physical drive write cache (controller slot 0)** 
Important: Because physical drive write cache is not battery-backed, you could lose data if a power failure occurs during a write process. To minimize this possibility, use a backup power supply.

	ssacli ctrl slot=0 modify dwc=enable
	ssacli ctrl slot=0 modify dwc=disable

**Show status of smart array write cache when no battery is present (no-battery write cache option, controller slot 0)**

	ssacli ctrl slot=0 modify nbwc=?

**Enable/disable smart array write cache when no battery is present (no-battery write cache option, controller slot 0)**

	ssacli ctrl slot=0 modify nbwc=enable
	ssacli ctrl slot=0 modify nbwc=disable

**Enable/disable smart array cache for certain Logical Volume (controller slot 0, logical drive 1)**
	
	ssacli ctrl slot=0 ld 1 modify arrayaccelerator=enable
	ssacli ctrl slot=0 ld 1 modify arrayaccelerator=disable

**Enable/disable SSD Smart Path (controller slot 0, array A)**
	
	ssacli ctrl slot=0 array a modify ssdsmartpath=enable
	ssacli ctrl slot=0 array a modify ssdsmartpath=disable

**Show spare activation mode**

	ssacli ctrl slot=0 modify spareactivationmode=?

**Set spare activation mode**

	ssacli ctrl slot=0 modify spareactivationmode=predictive
	ssacli ctrl slot=0 modify spareactivationmode=failure

**Show rebuild priority**
	
	ssacli ctrl slot=0 modify rp=?
	
**Modify rebuild priority**

	ssacli ctrl slot=0 modify rp=low
	ssacli ctrl slot=0 modify rp=medium
	ssacli ctrl slot=0 modify rp=mediumhigh
	ssacli ctrl slot=0 modify rp=high

**Erase Physical Drive (controller slot 0, physical disk port 1I:box 1:bay 1)**  

	ssacli ctrl slot=0 pd 1I:1:1 modify erase

## Examples
```ssacli ctrl slot=0 ld all show detail```

Smart Array P420i in Slot 0 (Embedded)

   Array A

      Logical Drive: 1
         Size: 838.33 GB
         Fault Tolerance: 1
         Heads: 255
         Sectors Per Track: 32
         Cylinders: 65535
         Strip Size: 256 KB
         Full Stripe Size: 256 KB
         Status: OK
         Unrecoverable Media Errors: None
         Caching:  Enabled
         Unique Identifier: 600508B1001CD326DDB697BAB5900BC9
         Disk Name: /dev/sda 
         Mount Points: None
         Boot Volume: Primary and Secondary
         Logical Drive Label: SAS-R1-900
         Mirror Group 1:
            physicaldrive 1I:1:1 (port 1I:box 1:bay 1, SAS HDD, 900 GB, OK)
         Mirror Group 2:
            physicaldrive 1I:1:2 (port 1I:box 1:bay 2, SAS HDD, 900 GB, OK)
         Drive Type: Data
         LD Acceleration Method: Controller Cache


   Array B

      Logical Drive: 3
         Size: 931.48 GB
         Fault Tolerance: 0
         Heads: 255
         Sectors Per Track: 32
         Cylinders: 65535
         Strip Size: 256 KB
         Full Stripe Size: 256 KB
         Status: OK
         Caching:  Enabled
         Unique Identifier: 600508B1001C794A1E4BCEBA3072A0C0
         Disk Name: /dev/sdc 
         Mount Points: 931.4 GiB Partition   1 /mnt/pve/local-backup
         Disk Partition Information
            Partition   1: Basic, 931.4 GiB, /mnt/pve/local-backup
         Logical Drive Label: SATA-1TB
         Drive Type: Data
         LD Acceleration Method: Controller Cache


```ssacli ctrl all show config```
   Array A (SAS, Unused Space: 0  MB)

      logicaldrive 1 (838.33 GB, RAID 1, OK)

      physicaldrive 1I:1:1 (port 1I:box 1:bay 1, SAS HDD, 900 GB, OK)
      physicaldrive 1I:1:2 (port 1I:box 1:bay 2, SAS HDD, 900 GB, OK)

   Array B (SATA, Unused Space: 0  MB)

      logicaldrive 3 (931.48 GB, RAID 0, OK)

      physicaldrive 2I:1:5 (port 2I:box 1:bay 5, SATA HDD, 1 TB, OK)

   Unassigned

      physicaldrive 1I:1:3 (port 1I:box 1:bay 3, SATA HDD, 500 GB, OK)
      physicaldrive 1I:1:4 (port 1I:box 1:bay 4, SATA HDD, 500 GB, OK)
      physicaldrive 2I:1:7 (port 2I:box 1:bay 7, SATA HDD, 1 TB, OK)


```ssacli ctrl slot=0 ld 555 delete```



