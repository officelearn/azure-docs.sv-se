---
title: Så här uppgraderar du den Azure Monitor for VMs beroende agenten | Microsoft Docs
description: Den här artikeln beskriver hur du uppgraderar Azure Monitor for VMs beroende agent med hjälp av kommando raden, installations guiden och andra metoder.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: c98c48a4494ac37ef4868c44d4a7adacfd0d48da
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662441"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Så här uppgraderar du den Azure Monitor for VMs beroende agenten

Efter den första distributionen av den Azure Monitor for VMs beroende agenten släpps uppdateringar som innehåller fel korrigeringar eller stöd för nya funktioner eller funktioner.  Den här artikeln hjälper dig att förstå vilka metoder som är tillgängliga och hur du utför uppgraderingen manuellt eller genom automatisering.

## <a name="upgrade-options"></a>Uppgraderings alternativ 

Beroende agenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på distributions scenariot och miljön som datorn körs i. Följande metoder kan användas för att uppgradera agenten.

|Miljö |Installations metod |Uppgraderingsmetod |
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
   
3. Använd installations guiden för **Dependency agent** för att avinstallera den tidigare versionen av beroende agenten och installera sedan den senaste versionen.


### <a name="from-the-command-line"></a>Från kommando raden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör följande kommando.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Parametern `/RebootMode=manual` förhindrar att datorn startas om automatiskt om vissa processer använder filer från den tidigare versionen och låser sig dem. 

3. För att bekräfta att uppgraderingen lyckades, kontrol lera `install.log` för detaljerad installations information. Logg katalogen är en *%program%\Microsoft-Agent\logs*.

## <a name="upgrade-linux-agent"></a>Uppgradera Linux-agenten 

Uppgradering från tidigare versioner av beroende agenten på Linux stöds och utförs efter samma kommando som en ny installation.

Du kan hämta den senaste versionen av Windows-agenten [här](https://aka.ms/dependencyagentlinux).

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör följande kommando som rot`sh InstallDependencyAgent-Linux64.bin -s`. 

Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. I Linux-agenter är logg katalogen */var/opt/Microsoft/Dependency-Agent/log*. 

## <a name="next-steps"></a>Nästa steg

Om du vill stoppa övervakningen av dina virtuella datorer under en viss tids period eller ta bort Azure Monitor for VMs helt, se [inaktivera övervakning av dina virtuella datorer i Azure Monitor for VMS](vminsights-optout.md).
