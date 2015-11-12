---
layout: post
title:  "Upgrading QLogic CNA firmware with ESXCLI"
date:   2015-11-12
---


Upgrading firmware on hardware components can sometimes be a painful exercise, so each possibility making my sysadmin life easier is very welcome. A while ago I needed to upgrade the drivers and firmware of the QLogic BR-1020 and came across the Brocade ESXCLI plug-in. The QLogic BR-1020 is actually a Brocade BR1741M-k, which got rebranded when QLogic acquired Brocade's FC and CNA business. Apparently rebranding the Brocade name to QLogic hasn't been completed, so we are going to upgrade the QLogic with Brocade tools. 

The reason for upgrading is the migration to vSphere 6, the new drivers and firmware include support for the latest version of vSphere. The great thing about the Brocade ESXCLI plug-in is that you can upgrade the firmware from the ESX command line using esxcli. This means no longer attaching a bootable ISO image with the new firmware to your servers and pressing the right keys to boot, at the right moment. When you have tens or hundreds of servers upgrading firmware can become a time-consuming project.

The first step is to download all drivers and tools that we need. Because it's a converged network adapter we need to download drivers for network and FC. Here's the download list:

Item     | Unzipped
-------- | ---
Network drivers | net-bna-3.2.4.0-1OEM.550.0.0.1331820.x86_64.vib
FC drivers | scsi-bfa-3.2.4.0-1OEM.550.0.0.1331820.x86_64.vib
Brocade ESXCLI-plugin | vmware-esx55-provider-brcdprovider.vib
Adapter Firmware | brocade_adapter_boot_fw_v3-2-5-0

I don't want to make this post to lengthy, so I assume you know how to install or update the vib files manually or even better with VMware Update Manager. After this you can follow the next steps to upgrade the firmware.

Unzip and copy the  firmware to a central datastore and open a SSH connection to the ESXi host. Now the ESXCLI-plugin is installed you have extra options with esxcli:

    ~ # esxcli brocade
    Usage: esxcli brocade {cmd} [cmd options]
    
    Available Commands:
      bcu                   Brocade Command Utility commands
      supportsave           Brocade Support Save Utility
      supportshow           Brocade Support Show Utility

And the specific Brocade commands:

    ~ # esxcli brocade bcu
    
    Available command options are:
    
    Common Adapter (HBA/CNA) Commands:
    adapter  auth     bios     boot     debug    diag
    log      pcifn    port
    
    CNA specific Commands:
    dcb      fcoe     phy
    
    FC/FCoE specific Commands:
    fabric   fcdiag   fcpim    lport    qos      ratelim
    rport    trunk    vhba     vport
    
    ETH specific Commands:
    ethboot  vnic

Start the upgrade of the firmware with:

    esxcli brocade bcu --command=”boot --update <file> -a”


When the upgrade is finished you need to reboot your host.
