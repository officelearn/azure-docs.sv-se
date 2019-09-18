---
layout: LandingPage
description: Lär dig hur du felsöker distributioner av virtuella datorer.
title: Felsökning för dokumentation om Azure Virtual Machines | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: d7ceb3acb1d2e3d174f3b665ec6210d3ddac9970
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059154"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Felsöka virtuella Azure-datorer

## <a name="tools-for-troubleshooting"></a>Verktyg för felsökning

- [Seriekonsol](serial-console-windows.md)
- [Startdiagnostik](boot-diagnostics.md)
- [Virtuell Windows-dator: Koppla OS-disken till en annan virtuell dator för felsökning](troubleshoot-recovery-disks-portal-windows.md)
- [Virtuell Linux-dator: Koppla OS-disken till en annan virtuell dator för felsökning](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Jag kan inte ansluta till den virtuella datorn

### <a name="windows"></a>Windows

**Gemensam lösning**

- [Återställa RDP](reset-rdp.md)
- [Felsökning av RDP](troubleshoot-rdp-connection.md)
- [Detaljerad felsökning av RDP](detailed-troubleshoot-rdp.md)

**RDP-fel**

- [Ingen licensserver](troubleshoot-rdp-no-license-server.md)
- [Interna ](Troubleshoot-rdp-internal-error.md)
- [autentiseringsfel](troubleshoot-authentication-error-rdp-vm.md)
- [Felsöka specifika fel](troubleshoot-specific-rdp-errors.md)

**Fel vid start av virtuell dator**

* [BitLocker-startfel](troubleshoot-bitlocker-boot-error.md) 
* [”Kontrollerar filsystem” visas i Windows vid start](troubleshoot-check-disk-boot-error.md)
* [Fel som visar en blå kraschskärm](troubleshoot-common-blue-screen-error.md)
* [Uppstarten av den virtuella datorn fastnar i läget ”Förbereder Windows”](troubleshoot-vm-boot-configure-update.md)
* [Felmeddelandet "KRITISKT TJÄNSTFEL" visas på blå skärm](troubleshoot-critical-service-failed-boot-error.md)
* [Problem med omstartsloop](troubleshoot-reboot-loop.md)
* [Uppstarten av den virtuella datorn fastnar i Windows-uppdateringen](troubleshoot-stuck-updating-boot-error.md)
* [Den virtuella datorn startar i felsäkert läge](troubleshoot-rdp-safe-mode.md)

**Övrigt**
- [Återställa VM-lösenordet för den virtuella Windows-datorn offline](reset-local-password-without-agent.md)
- [Återställa NIC efter felaktig konfiguration](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Felsökning av SSH](troubleshoot-ssh-connection.md)
- [Detaljerad felsökning av SSH](detailed-troubleshoot-ssh-connection.md)
- [Vanliga felmeddelanden](error-messages.md)
- [Återställa VM-lösenordet för den virtuella Linux-datorn offline](reset-password.md)

## <a name="vm-deployment-issues"></a>Distributionsproblem för virtuell dator

- [Allokeringsfel](allocation-failure.md)
- Distribuera om en VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Felsöka distributioner
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Enhetsnamnen har ändrats](troubleshoot-device-names-problems.md)
- [Installera agent för virtuell Windows-dator offline](install-vm-agent-offline.md)
- [Starta om eller ändra storlek på en virtuell dator](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Prestandaproblem med virtuell dator
- [Prestandaproblem med virtuella datorer](performance-diagnostics.md)
- Windows
    - [Använda PerfInsights](how-to-use-perfinsights.md)
    - [Tillägg för prestandadiagnostik](performance-diagnostics-vm-extension.md)
- Linux
    - [Använda PerfInsights](how-to-use-perfinsights-linux.md)