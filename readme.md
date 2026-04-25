
### Thermal design

Based on other users who have dissembled the D8U, the CPU cooler that’s been installed is a HP Probook compatible cooler, part no.s 905706-001, 912932, and L52034-001 – this was confirmed by a user on Discord dissembling their unit and viewing the QR codes on the cooler.

![[Pasted image 20260425161149.png]]

This cooling solution is designed to be used with CPUs with 15–45W, whereas the Intel 125H can reach 60–80W.

The D8U by default shipped with the BIOS settings having the Power Limit 1 set to 45W and Power Limit 2 set to 93W – values that are completely incompatible with the CPU cooler.

The lack of appropriate CPU cooling is amplified by the fact that the D8U models are not getting enough fresh air into the CPU compartment, something that multiple users have proven is an issue by using laptop fans to push air into the CPU compartment, this results in an immediate drop in temperature and fan speed.

There is ample room within the D8U chassis for intake fans to be installed from the factory:
![[Pasted image 20260425160952.png]]
![[Pasted image 20260425161008.png]]

The fan module that Zettlab has chosen to implement also does not follow standard design practices. Most PWM fans follow 0-255 for their settings, the Zettlab module uses 0-183 and is not plug and play with third-party operating systems. If a user wants to install TrueNAS, Unraid, Ubuntu or anything else they need to compile the driver for the fan module and then use a script to convert the 0-183 range into a standard 0-225 range for fan control software to work as Zettlab have not provided a fan driver.

#### Zettlab claims vs real world testing

##### Zettlab data:
On the discord server Zettlab have shown this thermal data citing that the D8U units have no thermal throttling problems
![[zl-thermals-1.png]]

![[zl-thermals-2.png]]

##### Real world user data:

It should be noted in every single test, real world data shows the CPU is up to 30c higher than Zettlab's claimed thermal results and in most testing is causing the CPU to thermal throttle while the CPU fan is running significantly faster

![[Pasted image 20260425155522.png]]

![[Pasted image 20260425155428.png]]
![[Pasted image 20260425155434.png]]![[Pasted image 20260425155439.png]]

![[Pasted image 20260425155447.png]]

#### Conclusions:
From a hardware perspective; Zettlab have chosen a CPU cooler that is not appropriate for the Intel Ultra 5 125H. Additionally the CPU blower does not receive enough fresh air due to the lack of any active air intakes 

Additional concerns can be drawn, with multiple possible answers; 
Zettlab has inaccuracies with their testing methodology and the resulting data is not accurate to real word usage.
OR
Customers are receiving units that are not configured in the same way as Zettlab's internal testing units are.


### Hard Drive Backplane

The HDD backplane that Zettlab have implemented into the D8U is extremely flawed and has already caused irreparable damage to several users high capacity drives.

Zettlab have opted to put all 8 HDDs on a single PCIe 4x connector, without providing any auxiliary power to the hard drive backplane and are relying solely on the PCIe slot for power delivery.

![[Pasted image 20260425161921.png]]

**Zettlab have claimed that they're using a custom design for the PCIe slot**

![[Pasted image 20260425162746.png]]

![[Pasted image 20260425162809.png|697]]

![[Pasted image 20260425162830.png]]

It should be noted that the power limitation is not a protocol or interface restriction, it is a mechanical and electrical limitation of the PCIe connector itself, and Zettlab are using the PCIe connector for power delivery.

##### PCIe 4x Power 

A PCIe 4x slot, regardless of claims, is designed to to deliver a maximum of **25W**
Looking at an average Seagate enterprise drive power consumption and then multiplying by 8

|            | **Idle W** | **Max Load W** |
| ---------- | ---------- | -------------- |
| Single HDD | 5.3        | 9.4            |
| 8 HDDs     | 42.4       | 75.2           |
The idle power draw alone is nearly double what a PCIe 4x slot is rated for. This lack of appropriate power delivery causes all HDDs connected to the backplane to disconnect and reconnect as they're losing their electrical signal.
This can be seen here on all 8 drives:

**Disk A:**
![[disk-a.png]]

**Disk B:**
![[disk-b.png]]

**Disk C:**
![[disk-c.png]]

**Disk D:**
![[disk-d.png]]

**Disk E:**
![[disk-e.png]]

**Disk F:**
![[disk-f.png]]

**Disk G:**
![[disk-g.png]]

**Disk H:**
![[disk-h.png]]

The constant fluctuations in power causing to the HDDs to constantly disconnect and reconnect leads to irreparable data loss and file corruption and potentially HDD destruction:

**Capture of a RAIDZ1  pool running on Unraid. 278,600 checksum errors:**
![[Pasted image 20260425170054.png]]

**This lack of consistent power delivery has already destroyed a customer hard drive:**

![[Pasted image 20260425165552.png]]


##### SATA Speeds

Zettlab's backplane additionally does not provide the full SATA3 speeds to all 8 of the HDDs connected to it
Two HDDs are running at SATA2 speeds of 3.0Gbps

Here is the pertinent information from the previous screenshots:
```
Disk A - SATA controller: ASMedia ASM1166 - SATA link up 6.0 Gbps
Disk B - SATA controller: ASMedia ASM1166 - SATA link up 6.0 Gbps
Disk C - SATA controller: ASMedia ASM1166 - SATA link up 6.0 Gbps
Disk D - SATA controller: ASMedia ASM1166 - SATA link up 6.0 Gbps
Disk E - SATA controller: ASMedia ASM1166 - SATA link up 6.0 Gbps
Disk F - SATA controller: ASMedia ASM1166 - SATA link up 6.0 Gbps
Disk G - SATA controller: Intel 7e63      - SATA link up 3.0 Gbps
Disk H - SATA controller: Intel 7e63      - SATA link up 3.0 Gbps
```

The Intel 7e63 SATA controller does support SATA3 speeds, however due to the limitations of the PCIe 4x connector Zettlab are using these speeds are never reached as there is not enough bandwidth available.

This difference in SATA connection speeds limits any RAID operations to the SATA2 speeds of the drives connected to the Intel 7e63 - effectively halving what should be available to users.

#### Conclusions:
The Zettlab D8U is not safe to run with 8 HDDs installed into it. The inadequate power delivery system will cause data corruption, and eventually cause damage to any HDD that is constantly disconnecting and reconnecting. 

With how far out of specification the PCIe connector is being used, it's not unreasonable to conclude the device is a potential fire risk.

It is reasonable to assume these electrical concerns also apply to the D6U models based on Zettlab's comments:

![[Pasted image 20260425172230.png]]

Running less than 3 HDDs should not exceed the limitations of the PCIe 4x slot and ideally prevent damage to user hardware and data.

### Final Thoughts

The Zettlab NAS range has hardware design choices that lead to a user experience that isn't close to what was originally pitched during the Kickstarter campaign.

The D8U in particular suffers the most from these hardware design choices and has already shown to be damaging user HDDs to the point of complete failure.

Unfortunately due the nature of these hardware concerns, an end user is not capable of rectifying them as they are all internal components that would require disassembly to access. and not something covered by the Kickstarter warranty

**Kickstarter Warranty**
![[Pasted image 20260425173706.png]]

My personal recommendation is that any D8U unit should be shut down for the time being to avoid any more potential damage to user HDDs and data.


