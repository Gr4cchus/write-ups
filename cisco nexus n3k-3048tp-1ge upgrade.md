# Updating the n3k-c3048tp-1g
<b>Moving from 6.0(2)U6(5c) -> 7.0(3)I7(8) -> nxos.9.3.8.bin</b> <br />


### Quick note
* Have system images, check hashes
    * 7.x and beyond wont need kick starts 
    * Once on 7.0(3)I7(8) you can compact large images if need be
* SCP is needed to compact unless USB1 storage method is used
* Warnings about fast-reload method
* bios advisory seems to be a non-issue following this upgrade path
* minor very very easy configuration changes were required
* backed up the config but never needed it
* Forums indicate somewhere in 6.x or 7.x default fan speed increases occur. Maybe can be fixed with cisco api's. PSU fans seem to be the loudest.

# Start
Quick braindump.

run `sh ver` to check the system version. Follow <a name="abcd">this document </a>, section "Upgrade From NX-OS 6.0(2)U6(3a) or later to NX-OS 9.3(x)". 
> :warning: Note I use slightly different versions in the upgrade but it worked just fine and got me quickly to where I wanted following the guidelines.

One of the hicups was compacting the 9.3.8 image I had. IIRC `install all nxos USB1:nxos.9.3.8.bin compact` does not actually install it when from `usb1`:, just copies it to `bootflash:`. Then you may run prechecks and install.

> show incompatibility system   
show install all impact

### 7 to 9.3.8
skipping ahead...

After compacting the image and running incompatibility check yields error, the install will fail if not remedied.
```
sw01-c3048# show incompatibility nxos bootflash:nxos.9.3.8.bin 
Checking incompatible configuration(s) 
The following configurations on active are incompatible with  the system image  
1) Service : pltfm_config , Capability : CAP_FEATURE_DME_ENABLED 
Description : DME feature is not supported on this image 
Capability requirement : STRICT 
Enable/Disable command : Please use "no system dme enable" to disable DME feature
```

Run the suggested command
```
sw01-c3048# show incompatibility-all nxos bootflash:nxos.9.3.8.bin

Checking incompatible configuration(s) for vdc 'sw01-c3048':
------------------------------------------------------------
The following configurations on active are incompatible with  the system image  
1) Service : pltfm_config , Capability : CAP_FEATURE_DME_ENABLED 
Description : DME feature is not supported on this image 
Capability requirement : STRICT 
Enable/Disable command : Please use "no system dme enable" to disable DME feature
```

a save and reload will be required based on this output
```
show system config reload-pending
```
`install all nxos bootflash:nxos` and everything should work. Dont forget to delete the old image. :smile:

## Reference Material
everything
https://www.cisco.com/c/en/us/support/switches/nexus-3048-switch/model.html#~tab-documents

software images
https://software.cisco.com/download/home/283970187/type/282088129/release/9.3(8)?i=!pp

software upgrade guide [link text](#abcd)
https://www.cisco.com/c/en/us/support/docs/switches/nexus-3048-switch/216023-nexus-3048-nx-os-software-upgrade-proced.html#upgrade-U6-3a-or-later-to-7x

more information on upgrades process but not entirely needed
https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus3000/sw/upgrade/7_x/b_Cisco_Nexus_3000_Series_NX_OS_Software_Upgrade_and_Downgrade_Release_7_x/b_Cisco_Nexus_3000_Series_NX_OS_Software_Upgrade_and_Downgrade_Release_7_x_newGuide_chapter_01.html?referring_site=RE&pos=2&page=https://www.cisco.com/c/en/us/support/switches/nexus-3048-switch/model.html#~tab-downloads