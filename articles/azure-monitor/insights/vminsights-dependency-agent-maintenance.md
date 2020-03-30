---
title: Uppgradera Azure Monitor för VMs Dependency Agent
description: I den här artikeln beskrivs hur du uppgraderar Azure Monitor för VMs Dependency agent med kommandorad, installationsguide och andra metoder.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480767"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Uppgradera Azure Monitor för VMs Dependency Agent

Efter den första distributionen av Azure Monitor för VMs Dependency agent, uppdateringar släpps som inkluderar buggfixar eller stöd för nya funktioner eller funktioner.  Den här artikeln hjälper dig att förstå de metoder som finns och hur du utför uppgraderingen manuellt eller genom automatisering.

## <a name="upgrade-options"></a>Alternativ för uppgradering 

Beroendeagenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på distributionsscenariot och miljön som datorn körs i. Följande metoder kan användas för att uppgradera agenten.

|Miljö |Installationsmetod |Uppgraderingsmetod |
|------------|--------------------|---------------|
|Azure VM | VM-tillägg för [beroendeagent](../../virtual-machines/extensions/agent-dependency-windows.md) för Windows och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agenten uppgraderas automatiskt som standard om du inte har konfigurerat mallen Azure Resource Manager för att välja bort det genom att ange egenskapen *autoUpgradeMinorVersion* till **false**. Uppgraderingen för mindre version där automatisk uppgradering är inaktiverad, och en större version uppgradering följa samma metod - avinstallera och installera om tillägget. |
| Anpassade Azure VM-avbildningar | Manuell installation av beroendeagent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommandoraden som kör Windows-installationspaketet eller Linux självextraherande och installerbara skalskriptpaket.|
| Virtuella datorer som inte är azure | Manuell installation av beroendeagent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommandoraden som kör Windows-installationspaketet eller Linux självextraherande och installerbara skalskriptpaket. |

## <a name="upgrade-windows-agent"></a>Uppgradera Windows-agent 

Om du vill uppdatera agenten på en Virtuell Windows-dator till den senaste versionen som inte är installerad med vm-tillägget Beroendeagent kör du antingen från kommandotolken, skriptet eller någon annan automatiseringslösning eller med installationsguiden för InstallDependencyAgent-Windows.exe.  

Du kan ladda ner den senaste versionen av Windows-agenten [härifrån](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Använda installationsguiden

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Starta installationsguiden genom att köra **InstallDependencyAgent-Windows.exe.**
   
3. Följ **guiden Konfigurera beroendeagent** för att avinstallera den tidigare versionen av beroendeagenten och installera sedan den senaste versionen.


### <a name="from-the-command-line"></a>Från kommandoraden

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Kör följande kommando.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Parametern `/RebootMode=manual` förhindrar att uppgraderingen automatiskt startar om datorn om vissa processer använder filer från den tidigare versionen och har ett lås på dem. 

3. Kontrollera detaljerad inställningsinformation för `install.log` att bekräfta att uppgraderingen lyckades. Loggkatalogen är *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Uppgradera Linux-agent 

Uppgradering från tidigare versioner av beroendeagenten på Linux stöds och utförs efter samma kommando som en ny installation.

Du kan ladda ner den senaste versionen av Windows-agenten [härifrån](https://aka.ms/dependencyagentlinux).

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Kör följande kommando`sh InstallDependencyAgent-Linux64.bin -s`som rot . 

Om beroendeagenten inte startar kontrollerar du om det finns detaljerad felinformation i loggarna. På Linux-agenter är loggkatalogen */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Nästa steg

Om du vill sluta övervaka dina virtuella datorer under en viss tid eller helt ta bort Azure Monitor för virtuella datorer läser du [Inaktivera övervakning av dina virtuella datorer i Azure Monitor för virtuella datorer](vminsights-optout.md).
