---
title: Så här uppgraderar du den Azure Monitor for VMs beroende agenten | Microsoft Docs
description: Den här artikeln beskriver hur du uppgraderar Azure Monitor for VMs beroende agent med hjälp av kommando raden, installations guiden och andra metoder.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 548a578365b03162396fb8618718ab1e7ce5b081
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400803"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Så här uppgraderar du den Azure Monitor for VMs beroende agenten

Efter den första distributionen av den Azure Monitor for VMs beroende agenten släpps uppdateringar som innehåller fel korrigeringar eller stöd för nya funktioner eller funktioner.  Den här artikeln hjälper dig att förstå vilka metoder som är tillgängliga och hur du utför uppgraderingen manuellt eller genom automatisering.

## <a name="upgrade-options"></a>Uppgraderings alternativ 

Beroende agenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på distributions scenariot och miljön som datorn körs i. Följande metoder kan användas för att uppgradera agenten.

|Miljö |Installationsmetod |Uppgraderings metod |
|------------|--------------------|---------------|
|Azure VM | Beroende agent VM-tillägg för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agenten uppgraderas automatiskt som standard om du inte har konfigurerat din Azure Resource Manager-mall för att välja att ange egenskapen *aktiverat autoupgrademinorversion* till **false**. Uppgradera till en lägre version där automatisk uppgradering är inaktive rad och en större versions uppgradering följer samma metod-Uninstall och installera om tillägget. |
| Anpassade Azure VM-avbildningar | Manuell installation av beroende agent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommando raden som kör Windows Installer-paketet eller Linux-paketet för självextraherande och installerbara gränssnitt.|
| Icke-virtuella datorer i Azure | Manuell installation av beroende agent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommando raden som kör Windows Installer-paketet eller Linux-paketet för självextraherande och installerbara gränssnitt. |

## <a name="upgrade-windows-agent"></a>Uppgradera Windows-agent 

Om du vill uppdatera agenten på en virtuell Windows-dator till den senaste versionen som inte är installerad med hjälp av tillägget för den virtuella datorns beroende agent, kan du antingen köra från kommando tolken, skriptet eller någon annan Automation-lösning eller genom att använda installations guiden för InstallDependencyAgent-Windows. exe.  

Du kan hämta den senaste versionen av Windows-agenten [här](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Använda installations guiden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör **InstallDependencyAgent-Windows. exe** för att starta installations guiden.

3. I dialog rutan **Dependency agent 9.9.1-installation** klickar **du på jag accepterar** att godkänna licens avtalet.

5. I dialog rutan **Dependency agent avinstallation av 9.9.0** klickar du på **Nästa**. Status sidan visar förloppet för avinstallationen av den tidigare versionen.

6. I dialog rutan **Dependency agent avinstallation av 9.9.0** klickar du på **Avinstallera** för att fortsätta med avinstallationen av den tidigare versionen från den sökväg som anges i dialog rutan. 

7. I dialog rutan **Dependency agent 9.9.0 Uninstall** visas avinstallations förloppet och när det är klart visas sidan **Slutför Dependency agent avinstallation** . Klicka på **Slutför**.

8. Installations förloppet visas i dialog rutan **Dependency agent installations 9.9.1** . När sidan **slutför Dependency agent avinstallation** visas klickar du på **Slutför**. 

### <a name="from-the-command-line"></a>Från kommando raden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör följande kommando.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Parametern `/RebootMode=manual` förhindrar att datorn startas om automatiskt om vissa processer använder filer från den tidigare versionen och låser sig dem. 

3. För att bekräfta att uppgraderingen lyckades, kontrol lera `install.log` för detaljerad installations information. Loggkatalogen är *%Programfiles%\Microsoft beroende Agent\logs*.

## <a name="upgrade-linux-agent"></a>Uppgradera Linux-agenten 

Uppgradering från tidigare versioner av beroende agenten på Linux stöds och utförs efter samma kommando som en ny installation.

Du kan hämta den senaste versionen av Windows-agenten [här](https://aka.ms/dependencyagentlinux).

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör följande kommando som rot`sh InstallDependencyAgent-Linux64.bin -s`. 

Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Linux-agenter till loggkatalogen är */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Nästa steg

Om du vill stoppa övervakningen av dina virtuella datorer under en viss tids period eller ta bort Azure Monitor for VMs helt, se [inaktivera övervakning av dina virtuella datorer i Azure Monitor for VMS](vminsights-optout.md).
