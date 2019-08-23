---
title: Aktivera Azure Monitor for VMs (för hands version) – Översikt | Microsoft Docs
description: Lär dig hur du distribuerar och konfigurerar Azure Monitor for VMs. Ta reda på system kraven.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: f395ba5d63463aa177b453d187d025a4461eff28
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905578"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Aktivera Azure Monitor for VMs (för hands version) – Översikt

Den här artikeln innehåller en översikt över tillgängliga alternativ för att konfigurera Azure Monitor for VMs. Använd Azure Monitor for VMs för att övervaka hälso tillstånd och prestanda. Identifiera program beroenden som körs på virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer, lokala virtuella datorer eller virtuella datorer som finns i en annan moln miljö.  

Så här konfigurerar du Azure Monitor for VMs:

* Aktivera en enskild virtuell Azure-dator eller skalnings uppsättning för virtuella datorer genom att välja insikter **(för hands version)** direkt från den virtuella datorn eller skalnings uppsättningen för virtuella datorer.
* Aktivera två eller flera virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy. Den här metoden säkerställer att de nödvändiga beroendena är installerade och korrekt konfigurerade för befintliga och nya virtuella datorer och skalnings uppsättningar. Icke-kompatibla virtuella datorer och skalnings uppsättningar rapporteras, så du kan välja om du vill aktivera dem och åtgärda dem.
* Aktivera två eller flera virtuella Azure-datorer eller virtuella datorn skalningsuppsättningar över en viss prenumeration eller resursgrupp med hjälp av PowerShell.
* Aktivera Azure Monitor for VMs för att övervaka virtuella datorer eller fysiska datorer som finns i företagets nätverk eller i annan moln miljö.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du förstår informationen i följande avsnitt. 

>[!NOTE]
>Följande information som beskrivs i det här avsnittet gäller även för den [tjänstkarta lösningen](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor for VMs stöder en Log Analytics arbets yta i följande regioner:

- Västra centrala USA
- USA, västra 2<sup>1</sup>
- East US
- Centrala Kanada
- Storbritannien, södra
- Västra Europa
- Sydostasien
- Östra Australien<sup>1</sup>
- Australien, sydöstra<sup>1</sup>

<sup>1</sup> den här regionen för tillfället stöder inte funktionen Health i Azure Monitor för virtuella datorer.

>[!NOTE]
>Du kan distribuera virtuella Azure-datorer från vilken region som helst. De här virtuella datorerna är inte begränsade till de regioner som stöds av Log Analytics-arbetsytan.
>

Om du inte har en arbets yta kan du skapa en med hjälp av någon av följande resurser:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Du kan också skapa en arbets yta när du aktiverar övervakning för en enskild virtuell Azure-dator eller skalnings uppsättning för virtuella datorer i Azure Portal.

Om du vill konfigurera ett i skala-scenario som använder Azure Policy, Azure PowerShell eller Azure Resource Manager mallar, i din Log Analytics arbets yta:

* Installera ServiceMap och InfrastructureInsights lösningar. Du kan slutföra installationen med hjälp av en angiven Azure Resource Manager mall. Eller Välj **Konfigurera arbets yta**på fliken **Kom igång** .
* Konfigurera Log Analytics-arbetsytan för att samla in prestandaräknare.

Använd någon av följande metoder för att konfigurera din arbets yta för scenariot för vid skalning:

* Använd [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* På sidan Azure Monitor for VMs [**princip täckning**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) väljer du **Konfigurera arbets yta**. 

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows-och Linux-operativsystem som Azure Monitor for VMs stöder. Senare i det här avsnittet finns en fullständig lista med information om de viktigaste och lägre Linux OS-versionerna och de kernel-versioner som stöds.

|Operativsystemversion |Prestanda |Kartor |Hälsa |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|Windows 10-1803 | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14,04, 16,04, 18,04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9,4 | X<sup>1</sup> | | X |

<sup>1</sup> the prestanda funktion i Azure Monitor för virtuella datorer är tillgängliga från Azure Monitor. Den är inte tillgänglig direkt från den vänstra rutan i den virtuella Azure-datorn.

>[!NOTE]
>Hälso funktionen i Azure Monitor for VMs stöder inte kapslad [virtualisering](../../virtual-machines/windows/nested-virtualization.md) i en virtuell Azure-dator.
>

>[!NOTE]
>I Linux-operativ systemet:
> - Endast standardversioner och SMP Linux-kernelversioner stöds.
> - Avvikande kernel versioner, t.ex. (Physical Address tillägget) och Xen, inte stöds för alla Linux-distribution. Till exempel ett system med release-sträng med *2.6.16.21-0.8-xen* stöds inte.
> - Anpassade kärnor, inklusive omkompileringar av standard kärnor, stöds inte.
> - CentOSPlus-kärnan stöds.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernel-version |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operativsystemversion | Kernel-version |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | Kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |
|12 SP4 | 4.4. * |
|12 SP4 | Azure-justerad kernel |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency-agent

Kart funktionen i Azure Monitor for VMs hämtar data från Microsoft-beroende agenten. Beroendeagenten är beroende av Log Analytics-agenten ansluter till Log Analytics. Datorn måste ha Log Analytics-agenten installerad och konfigurerad med beroende agenten.

Oavsett om du aktiverar Azure Monitor for VMs för en enskild virtuell Azure-dator eller om du använder distributions metoden för vid-skala, använder du tillägget Azure VM Dependency agent för att installera agenten som en del av upplevelsen.

>[!NOTE]
>Följande information som beskrivs i det här avsnittet gäller även för den [tjänstkarta lösningen](service-map.md).  

I en hybrid miljö kan du ladda ned och installera beroende agenten manuellt. Om dina virtuella datorer finns utanför Azure använder du en automatiserad distributions metod.

I följande tabell beskrivs de anslutna källor som har stöd för funktionen kartan i en hybridmiljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Tillsammans med [Log Analytics agent för Windows](../../azure-monitor/platform/log-analytics-agent.md), behöver Windows-agenter beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| Linux-agenter | Ja | Tillsammans med [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md)behöver Linux-agenterna beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Nej | |

Du kan ladda ned beroende agenten från följande platser:

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.1 | FCF9C1D9B20AD414051B49EE79144E595CCC411EB6D444D6D5B5A7B1874DCDEC |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Om du vill aktivera och komma åt funktionerna i Azure Monitor for VMs måste du ha rollen *Log Analytics deltagare* . Om du vill visa prestanda-, hälso-och kart data måste du ha rollen *övervaknings läsare* för den virtuella Azure-datorn. Log Analytics-arbetsytan måste konfigureras för Azure Monitor för virtuella datorer.

Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Så här aktiverar du Azure Monitor for VMs (för hands version)

Aktivera Azure Monitor for VMs med någon av de metoder som beskrivs i den här tabellen:

| Distributions tillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Enskild virtuell Azure-dator eller skalnings uppsättning för virtuell dator | [Aktivera från den virtuella datorn](vminsights-enable-single-vm.md) | Du kan aktivera en enskild virtuell Azure-dator genom att välja insikter **(för hands version)** direkt från den virtuella datorn eller skalnings uppsättningen för virtuella datorer. |
| Flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer | [Aktivera via Azure Policy](vminsights-enable-at-scale-policy.md) | Du kan aktivera flera virtuella Azure-datorer med hjälp av Azure Policy och tillgängliga princip definitioner. |
| Flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer | [Aktivera via Azure PowerShell eller Azure Resource Manager mallar](vminsights-enable-at-scale-powershell.md) | Du kan aktivera flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer i en angiven prenumeration eller resurs grupp med hjälp av Azure PowerShell eller Azure Resource Manager mallar. |
| Hybridmoln | [Aktivera för Hybrid miljön](vminsights-enable-hybrid-cloud.md) | Du kan distribuera till virtuella datorer eller fysiska datorer som finns i ditt data Center eller i andra moln miljöer. |

## <a name="performance-counters-enabled"></a>Prestandaräknare som aktiverats 

Azure Monitor for VMs konfigurerar en Log Analytics arbets yta för att samla in de prestanda räknare som används. I följande tabeller visas de objekt och räknare som samlas in var 60: e sekund.

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

|Objektnamn |Namn på räknare |
|------------|-------------|
|Logisk disk |Ledigt utrymme i procent |
|Logisk disk |Genomsn. S/diskläsning |
|Logisk disk |Genomsn. S/disköverföring |
|Logisk disk |Genomsn. S/diskskrivning |
|Logisk disk |Disk byte/sek |
|Logisk disk |Disk – lästa byte/sek |
|Logisk disk |Diskläsningar/sek |
|Logisk disk |Disköverföringar/sek |
|Logisk disk |Disk – skrivna byte/sek |
|Logisk disk |Diskskrivningar/sek |
|Logisk disk |Ledigt utrymme i MB |
|Minne |Tillgängliga megabyte |
|Nätverkskort |Mottagna byte/sek |
|Nätverkskort |Skickade byte/sek |
|Processor |Tid i procent för processor |

### <a name="linux-performance-counters"></a>Prestandaräknare för Linux

|Objektnamn |Namn på räknare |
|------------|-------------|
|Logisk Disk |Använt utrymme i procent |
|Logisk Disk |Disk – lästa byte/sek |
|Logisk Disk |Diskläsningar/sek |
|Logisk Disk |Disköverföringar/sek |
|Logisk Disk |Disk – skrivna byte/sek |
|Logisk Disk |Diskskrivningar/sek |
|Logisk Disk |Ledigt utrymme i MB |
|Logisk Disk |Logisk Disk byte/sek |
|Minne |Tillgängligt minne i megabyte |
|Nätverk |Totalt antal byte mottaget |
|Nätverk |Totalt antal överförda byte |
|Processor |Tid i procent för processor |

## <a name="management-packs"></a>Hanteringspaket

När Azure Monitor for VMs har Aktiver ATS och kon figurer ATS med en Log Analytics arbets yta vidarebefordras ett hanterings paket till alla Windows-datorer som rapporterar till arbets ytan. Om du har [integrerat din System Center Operations Manager hanterings grupp](../../azure-monitor/platform/om-agents.md) med arbets ytan Log Analytics distribueras tjänstkarta hanterings paketet från hanterings gruppen till Windows-datorer som rapporterar till hanterings gruppen.  

Hanterings paketet heter *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Den skrivs till%program%\Microsoft Monitoring Agent\Agent\Health service State\Management Packs \ Folder. Datakällan som hanteringspaketet använder är % Program files%\Microsoft övervakning Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata

Microsoft samlar automatiskt in användnings- och data via din användning av Azure Monitor-tjänsten. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet. 

För att tillhandahålla exakta och effektiva fel söknings funktioner innehåller kart funktionen information om konfigurationen av program varan. Datan innehåller information, till exempel operativ system och version, IP-adress, DNS-namn och arbets Stations namn. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu när du har aktiverat övervakning av den virtuella datorn är övervaknings informationen tillgänglig för analys i Azure Monitor for VMs.

## <a name="next-steps"></a>Nästa steg

Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md). Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
