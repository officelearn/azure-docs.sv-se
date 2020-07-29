---
title: Aktivera Azure Monitor for VMs översikt
description: Lär dig hur du distribuerar och konfigurerar Azure Monitor for VMs. Ta reda på system kraven.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 96783955eac6ade90a155236891307720616ed20
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323952"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Aktivera Azure Monitor for VMs översikt

Den här artikeln innehåller en översikt över tillgängliga alternativ för att aktivera Azure Monitor for VMs övervaka hälso tillstånd och prestanda för följande:

- Virtuella Azure-datorer 
- Azure Virtual Machine Scale Sets
- Virtuella hybrid datorer som är anslutna till Azure Arc
- Lokala virtuella datorer
- Virtuella datorer som finns i en annan moln miljö.  

Så här konfigurerar du Azure Monitor for VMs:

* Aktivera en enskild virtuell Azure-dator, Azure-VMSS eller Azure Arc-dator genom att välja **insikter** direkt från menyn i Azure Portal.
* Aktivera flera virtuella Azure-datorer, Azure-VMSS eller Azure Arc-datorer med hjälp av Azure Policy. Den här metoden säkerställer att de nödvändiga beroendena är installerade och korrekt konfigurerade för befintliga och nya virtuella datorer och skalnings uppsättningar. Icke-kompatibla virtuella datorer och skalnings uppsättningar rapporteras, så du kan välja om du vill aktivera dem och åtgärda dem.
* Aktivera flera virtuella Azure-datorer, virtuella Azure Arc-datorer, Azure-VMSS eller Azure Arc-datorer i en viss prenumeration eller resurs grupp med hjälp av PowerShell.
* Aktivera Azure Monitor for VMs för att övervaka virtuella datorer eller fysiska datorer som finns i företagets nätverk eller i annan moln miljö.

## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att du förstår informationen i följande avsnitt. 

>[!NOTE]
>Följande information som beskrivs i det här avsnittet gäller även för den [tjänstkarta lösningen](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor for VMs stöder en Log Analytics arbets yta i följande regioner:

- USA, västra centrala
- USA, västra
- USA, västra 2
- USA, södra centrala
- East US
- USA, östra 2
- Central US
- USA, norra centrala
- US Gov AZ
- US Gov va
- Kanada, centrala
- Storbritannien, södra
- Norra Europa
- Europa, västra
- Asien, östra
- Sydostasien
- Indien, centrala
- Japan, östra
- Australien, östra
- Australien, sydöstra

>[!NOTE]
>Du kan övervaka virtuella Azure-datorer i vilken region som helst. De virtuella datorerna är inte begränsade till de regioner som stöds av Log Analytics arbets ytan.
>

Om du inte har en Log Analytics arbets yta kan du skapa en med hjälp av en av resurserna:
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure-portalen](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Virtuell Azure-dator
- Skalnings uppsättning för virtuella Azure-datorer
- Hybrid virtuell dator som är ansluten till Azure-bågen

## <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows-och Linux-operativsystem som Azure Monitor for VMs stöder. Senare i det här avsnittet finns en fullständig lista med information om de viktigaste och lägre Linux OS-versionerna och de kernel-versioner som stöds.

|OS-version |Prestanda |Kartor |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8,1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9,4, 8 | X<sup>1</sup> | |

<sup>1</sup> prestanda funktionen i Azure Monitor for VMS är bara tillgänglig från Azure Monitor. Den är inte tillgänglig direkt från den vänstra rutan i den virtuella Azure-datorn.

>[!NOTE]
>I Linux-operativ systemet:
> - Endast standardversioner och SMP Linux-kernelversioner stöds.
> - Icke-standardutgåvor av kernel, till exempel PAE (Physical Address Extension) och Xen, stöds inte för någon Linux-distribution. Till exempel stöds inte ett system med versions strängen *2.6.16.21-0,8-xen* .
> - Anpassade kärnor, inklusive omkompileringar av standard kärnor, stöds inte.
> - CentOSPlus-kärnan stöds.
> - Linux-kärnan måste korrigeras för SPECTRE-problemet. Kontakta din leverantör av Linux-distribution för mer information.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS-version | Kernelversion |
|:--|:--|
| 7,6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS-version | Kernelversion |
|:--|:--|
| 6,10 | 2.6.32-754 |
| 6,9 | 2.6.32 – 696 |

#### <a name="centosplus"></a>CentOSPlus

| OS-version | Kernelversion |
|:--|:--|
| 6,10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6,9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS-version | Kernelversion |
|:--|:--|
| 18,04 | 5.3.0 – 1020<br>5,0 (inkluderar Azure-justerad kernel)<br>4,18* <br> 4,15* |
| 16.04.3 | 4,15. * |
| 16,04 | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS-version | Kernelversion |
|:--|:--|
|12 SP4 | 4,12. * (innehåller Azure-justerad kernel) |
|12 SP3 | 4,4. * |
|12 SP2 | 4,4. * |

#### <a name="debian"></a>Debian 

| OS-version | Kernelversion |
|:--|:--|
| 9 | 4,9 | 

## <a name="supported-azure-arc-machines"></a>Azure Arc-datorer som stöds
Azure Monitor for VMs är tillgängligt för Azure Arc-aktiverade servrar i regioner där Arc-tillägget är tillgängligt. Du måste köra version 0,9 eller senare av Arc-agenten.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Tillsammans med [Log Analytics agent för Windows](../platform/log-analytics-agent.md), behöver Windows-agenter beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| Linux-agenter | Ja | Tillsammans med [Log Analytics-agenten för Linux](../platform/log-analytics-agent.md)behöver Linux-agenterna beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Nej | |

## <a name="agents"></a>Agenter
Azure Monitor for VMs kräver att följande två agenter installeras på varje virtuell dator eller skalnings uppsättning för virtuella datorer som ska övervakas. Att installera dessa agenter och ansluta dem till arbets ytan är det enda kravet att publicera resursen.

- [Log Analytics agent](../platform/log-analytics-agent.md). Samlar in händelser och prestanda data från den virtuella datorn eller skalnings uppsättningen för den virtuella datorn och levererar den till Log Analytics arbets ytan. Distributions metoder för Log Analytics-agenten på Azure-resurser använder VM-tillägget för [Windows](../../virtual-machines/extensions/oms-windows.md) och [Linux](../../virtual-machines/extensions/oms-linux.md).
- Beroende agent. Samlar in identifierade data om processer som körs på den virtuella datorn och externa process beroenden, som används av [Map-funktionen i Azure Monitor for VMS](vminsights-maps.md). Beroende agenten använder den Log Analytics agenten för att leverera data till Azure Monitor. Distributions metoder för beroende agenten på Azure-resurser använder VM-tillägget för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Log Analytics agenten är samma agent som används av System Center Operations Manager. Azure Monitor for VMs kan övervaka agenter som också övervakas av Operations Manager om de är direkt anslutna och du installerar beroende agenten på dem. Agenter som är anslutna till Azure Monitor via en anslutning till en [hanterings grupp](../tform/../platform/om-agents.md) kan inte övervakas av Azure Monitor for VMS.

Följande är flera metoder för att distribuera dessa agenter. 

| Metod | Beskrivning |
|:---|:---|
| [Azure-portalen](vminsights-enable-single-vm.md) | Installera båda agenterna på en enskild virtuell dator, skalnings uppsättning för virtuella datorer eller hybrid virtuella datorer som är anslutna till Azure-bågen. |
| [Mallar för Resurshanteraren](vminsights-enable-powershell.md) | Installera båda agenterna med någon av de metoder som stöds för att distribuera en Resource Manager-mall, inklusive CLI och PowerShell. |
| [Azure Policy](vminsights-enable-at-scale-policy.md) | Tilldela Azure Policy initiativ för att automatiskt installera agenterna när en virtuell dator eller skalnings uppsättning för virtuell dator skapas. |
| [Manuell installation](vminsights-enable-hybrid-cloud.md) | Installera agenterna i gäst operativ systemet på datorer som ligger utanför Azure, inklusive i ditt data Center eller i andra moln miljöer. |




## <a name="management-packs"></a>Hanteringspaket
När en Log Analytics arbets yta har kon figurer ATS för Azure Monitor for VMs vidarebefordras två hanterings paket till alla Windows-datorer som är anslutna till den arbets ytan. Hanterings paketen heter *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* och *Microsoft. IntelligencePacks. VMInsights* och skrivs till *%program%\Microsoft Monitoring Agent\Agent\Health service State\Management Packs \* . 

Data källan som används av *ApplicationDependencyMonitor* -hanterings paketet är **% Program Files%\Microsoft Monitoring Agent\Agent\Health service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Data källan som används av *VMInsights* -hanterings paketet är *% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnostik-och användnings data

Microsoft samlar automatiskt in användnings-och prestanda data via din användning av tjänsten Azure Monitor. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet. 

För att tillhandahålla exakta och effektiva fel söknings funktioner innehåller kart funktionen information om konfigurationen av program varan. Datan innehåller information, till exempel operativ system och version, IP-adress, DNS-namn och arbets Stations namn. Microsoft samlar inte in namn, adresser eller annan kontakt information.

Mer information om insamling och användning av data finns i [sekretess policy för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du använder funktionen för prestanda övervakning finns i [visa Azure Monitor for VMS prestanda](vminsights-performance.md). Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).

