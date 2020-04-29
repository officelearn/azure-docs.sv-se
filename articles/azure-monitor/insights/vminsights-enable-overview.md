---
title: Aktivera Azure Monitor for VMs översikt
description: Lär dig hur du distribuerar och konfigurerar Azure Monitor for VMs. Ta reda på system kraven.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982318"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Aktivera Azure Monitor for VMs översikt

Den här artikeln innehåller en översikt över tillgängliga alternativ för att aktivera Azure Monitor for VMs på dina virtuella datorer för att övervaka hälso tillstånd och prestanda. Identifiera program beroenden som körs på virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer, lokala virtuella datorer eller virtuella datorer som finns i en annan moln miljö.  

Så här konfigurerar du Azure Monitor for VMs:

* Aktivera en enskild virtuell Azure-dator eller skalnings uppsättning för virtuella datorer genom att välja **insikter** direkt från den virtuella datorn eller skalnings uppsättningen för virtuella datorer.
* Aktivera två eller flera virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy. Den här metoden säkerställer att de nödvändiga beroendena är installerade och korrekt konfigurerade för befintliga och nya virtuella datorer och skalnings uppsättningar. Icke-kompatibla virtuella datorer och skalnings uppsättningar rapporteras, så du kan välja om du vill aktivera dem och åtgärda dem.
* Aktivera två eller flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer i en angiven prenumeration eller resurs grupp med hjälp av PowerShell.
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
- USA, östra
- USA, östra 2
- USA, centrala
- USA, norra centrala
- Kanada, centrala
- Storbritannien, södra
- Europa, norra
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
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Du kan också skapa en arbets yta när du aktiverar övervakning för en enskild virtuell Azure-dator eller skalnings uppsättning för virtuella datorer i Azure Portal.

Om du vill konfigurera ett i skala-scenario som använder Azure Policy, Azure PowerShell eller Azure Resource Manager mallar, i din Log Analytics arbets yta:

* Installera *ServiceMap* -och *InfrastructureInsights* -lösningarna. Du kan slutföra installationen med hjälp av en angiven Azure Resource Manager mall. Eller på fliken **Kom igång** i Azure Portal väljer du **Konfigurera arbets yta**.
* Konfigurera Log Analytics arbets ytan för att samla in prestanda räknare.

Använd någon av följande metoder för att konfigurera din arbets yta för scenariot för vid skalning:

* Använd [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* På sidan Azure Monitor for VMs [**princip täckning**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) väljer du **Konfigurera arbets yta**. 

### <a name="supported-operating-systems"></a>Operativsystem som stöds

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
| 18,04 | 5,0 (inkluderar Azure-justerad kernel)<br>4,18*<br>4,15* |
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

### <a name="the-microsoft-dependency-agent"></a>Microsofts beroende agent

Kart funktionen i Azure Monitor for VMs hämtar data från Microsoft-beroende agenten. Beroende agenten använder den Log Analytics agenten för att ansluta till Log Analytics. Datorn måste ha Log Analytics-agenten installerad och konfigurerad med beroende agenten.

Oavsett om du aktiverar Azure Monitor for VMs för en enskild virtuell Azure-dator eller om du använder distributions metoden vid vid skala, använder du tillägget Azure VM Dependency agent för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) eller [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) för att installera agenten som en del av upplevelsen.

>[!NOTE]
>Följande information som beskrivs i det här avsnittet gäller även för den [tjänstkarta lösningen](service-map.md).  

I en hybrid miljö kan du ladda ned och installera beroende agenten manuellt eller med en automatiserad metod.

I följande tabell beskrivs de anslutna källor som kart funktionen stöder i en hybrid miljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Tillsammans med [Log Analytics agent för Windows](../../azure-monitor/platform/log-analytics-agent.md), behöver Windows-agenter beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| Linux-agenter | Ja | Tillsammans med [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md)behöver Linux-agenterna beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Nej | |

Du kan ladda ned beroende agenten från följande platser:

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54AA7F0B |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Om du vill aktivera och komma åt funktionerna i Azure Monitor for VMs måste du ha rollen *Log Analytics deltagare* . Om du vill visa prestanda-, hälso-och kart data måste du ha rollen *övervaknings läsare* för den virtuella Azure-datorn. Arbets ytan Log Analytics måste konfigureras för Azure Monitor for VMs.

Mer information om hur du styr åtkomsten till en Log Analytics arbets yta finns i [hantera arbets ytor](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Så här aktiverar du Azure Monitor for VMs

Aktivera Azure Monitor for VMs med någon av de metoder som beskrivs i den här tabellen:

| Distributions tillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Enskild virtuell Azure-dator eller skalnings uppsättning för virtuell dator | [Aktivera från den virtuella datorn](vminsights-enable-single-vm.md) | Du kan aktivera en enskild virtuell Azure-dator genom att välja **insikter** direkt från den virtuella datorn eller skalnings uppsättningen för virtuella datorer. |
| Flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer | [Aktivera via Azure Policy](vminsights-enable-at-scale-policy.md) | Du kan aktivera flera virtuella Azure-datorer med hjälp av Azure Policy och tillgängliga princip definitioner. |
| Flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer | [Aktivera via Azure PowerShell eller Azure Resource Manager mallar](vminsights-enable-at-scale-powershell.md) | Du kan aktivera flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer i en angiven prenumeration eller resurs grupp med hjälp av Azure PowerShell eller Azure Resource Manager mallar. |
| Hybridmoln | [Aktivera för Hybrid miljön](vminsights-enable-hybrid-cloud.md) | Du kan distribuera till virtuella datorer eller fysiska datorer som finns i ditt data Center eller i andra moln miljöer. |

## <a name="management-packs"></a>Hanteringspaket

När Azure Monitor for VMs har Aktiver ATS och kon figurer ATS med en Log Analytics arbets yta vidarebefordras ett hanterings paket till alla Windows-datorer som rapporterar till arbets ytan. Om du har [integrerat din System Center Operations Manager hanterings grupp](../../azure-monitor/platform/om-agents.md) med arbets ytan Log Analytics distribueras tjänstkarta hanterings paketet från hanterings gruppen till Windows-datorer som rapporterar till hanterings gruppen.  

Hanterings paketet heter *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Den skrivs till `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` mappen. Data källan som hanterings paketet använder är `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Diagnostik-och användnings data

Microsoft samlar automatiskt in användnings-och prestanda data via din användning av tjänsten Azure Monitor. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet. 

För att tillhandahålla exakta och effektiva fel söknings funktioner innehåller kart funktionen information om konfigurationen av program varan. Datan innehåller information, till exempel operativ system och version, IP-adress, DNS-namn och arbets Stations namn. Microsoft samlar inte in namn, adresser eller annan kontakt information.

Mer information om insamling och användning av data finns i [sekretess policy för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu när du har aktiverat övervakning av den virtuella datorn är övervaknings informationen tillgänglig för analys i Azure Monitor for VMs.

## <a name="next-steps"></a>Nästa steg

Information om hur du använder funktionen för prestanda övervakning finns i [visa Azure Monitor for VMS prestanda](vminsights-performance.md). Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).
