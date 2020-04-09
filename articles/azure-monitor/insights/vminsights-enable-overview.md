---
title: Aktivera Azure Monitor för virtuella datorer översikt
description: Lär dig hur du distribuerar och konfigurerar Azure Monitor för virtuella datorer. Ta reda på systemkraven.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982318"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Aktivera Azure Monitor för virtuella datorer översikt

Den här artikeln innehåller en översikt över de alternativ som finns tillgängliga för att aktivera Azure Monitor för virtuella datorer på dina virtuella datorer för att övervaka hälsa och prestanda. Identifiera programberoenden som körs på virtuella Azure-datorer (virtuella datorer) och skaluppsättningar för virtuella datorer, lokala virtuella datorer eller virtuella datorer som finns i en annan molnmiljö.  

Så här konfigurerar du Azure Monitor för virtuella datorer:

* Aktivera en enda Azure VM- eller virtuell datorskalauppsättning genom att välja **Insikter** direkt från den virtuella datorn eller skalningsuppsättningen för virtuella datorer.
* Aktivera två eller flera virtuella Azure-datorer och skalningsuppsättningar för virtuella datorer med hjälp av Azure Policy. Den här metoden säkerställer att på befintliga och nya virtuella datorer och skalningsuppsättningar installeras och konfigureras de nödvändiga beroendena. Icke-kompatibla virtuella datorer och skalningsuppsättningar rapporteras, så du kan bestämma om du vill aktivera dem och åtgärda dem.
* Aktivera två eller flera Azure-virtuella datorer eller skalningsuppsättningar för virtuella datorer över en angiven prenumeration eller resursgrupp med hjälp av PowerShell.
* Aktivera Azure Monitor för virtuella datorer för att övervaka virtuella datorer eller fysiska datorer som finns i företagets nätverk eller annan molnmiljö.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du förstå informationen i följande avsnitt. 

>[!NOTE]
>Följande information som beskrivs i det här avsnittet gäller även för [servicekartlösningen](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor för virtuella datorer stöder en Log Analytics-arbetsyta i följande regioner:

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
>Du kan övervaka virtuella Azure-datorer i valfri region. Själva virtuella datorer är inte begränsade till de regioner som stöds av log analytics-arbetsytan.
>

Om du inte har en Log Analytics-arbetsyta kan du skapa en med hjälp av en av resurserna:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure-portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Du kan också skapa en arbetsyta medan du aktiverar övervakning för en enda Azure VM- eller virtuell datorskala som anges i Azure-portalen.

Så här konfigurerar du ett scenario i skala som använder Azure Policy-, Azure PowerShell- eller Azure Resource Manager-mallar på arbetsytan Log Analytics:

* Installera *Lösningarna ServiceMap* och *InfrastructureInsights.* Du kan slutföra den här installationen med hjälp av en medföljande Azure Resource Manager-mall. Eller på fliken **Kom igång** i Azure-portalen väljer du **Konfigurera arbetsyta**.
* Konfigurera arbetsytan Log Analytics för att samla in prestandaräknare.

Om du vill konfigurera arbetsytan för scenariot i skala använder du någon av följande metoder:

* Använd [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* På sidan Azure Monitor för [**virtuella datorers principtäckning**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) väljer du **Konfigurera arbetsyta**. 

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows- och Linux-operativsystem som Azure Monitor för virtuella datorer stöder. Senare i det här avsnittet hittar du en fullständig lista som beskriver huvud- och mindre Linux OS-utgåvan och kärnversionerna som stöds.

|Operativsystemversion |Prestanda |Kartor |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> Prestandafunktionen i Azure Monitor för virtuella datorer är endast tillgänglig från Azure Monitor. Den är inte tillgänglig direkt från den vänstra rutan i Den virtuella Azure-datorn.

>[!NOTE]
>I Operativsystemet Linux:
> - Endast standardversioner och SMP Linux-kernelversioner stöds.
> - Icke-standardiserade kärnversioner, till exempel PAE (Physical Address Extension) och Xen, stöds inte för någon Linux-distribution. Ett system med utgivningssträngen *2.6.16.21-0.8-xen* stöds till exempel inte.
> - Anpassade kärnor, inklusive omkompileringar av standardkärnor, stöds inte.
> - CentOSPlus-kärnan stöds.
> - Linux-kärnan måste korrigeras för Spectre-sårbarheten. Kontakta din Linux-distributionsleverantör för mer information.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernelversion |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operativsystemversion | Kernelversion |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus (centOSPlus)

| Operativsystemversion | Kernelversion |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operativsystemversion | Kernelversion |
|:--|:--|
| 18.04 | 5.0 (inkluderar Azure-trimmad kärna)<br>4,18*<br>4,15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Företagsserver

| Operativsystemversion | Kernelversion |
|:--|:--|
|12 SP4 (på andra) | 4.12.* (inkluderar Azure-trimmad kärna) |
|12 SP3 (PÅ ANDRA) | 4.4.* |
|12 SP2 (S) | 4.4.* |

#### <a name="debian"></a>Debian 

| Operativsystemversion | Kernelversion |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsofts beroendeagent

Kartfunktionen i Azure Monitor för virtuella datorer hämtar sina data från Microsoft Dependency Agent. Beroendeagenten förlitar sig på Log Analytics-agenten för sin anslutning till Log Analytics. Så ditt system måste ha Log Analytics-agenten installerad och konfigurerad med beroendeagenten.

Oavsett om du aktiverar Azure Monitor för virtuella datorer för en enda Azure-virtuell dator eller om du använder distributionsmetoden i skala använder du Azure VM Dependency Agent-tillägget för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) eller [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) för att installera agenten som en del av upplevelsen.

>[!NOTE]
>Följande information som beskrivs i det här avsnittet gäller även för [servicekartlösningen](service-map.md).  

I en hybridmiljö kan du hämta och installera beroendeagenten manuellt eller använda en automatiserad metod.

I följande tabell beskrivs de anslutna källor som kartfunktionen stöder i en hybridmiljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Tillsammans med [Log Analytics-agenten för Windows](../../azure-monitor/platform/log-analytics-agent.md)behöver Windows-agenter beroendeagenten. Mer information finns i [operativsystem som stöds](#supported-operating-systems). |
| Linux-agenter | Ja | Tillsammans med [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md)behöver Linux-agenter beroendeagenten. Mer information finns i [operativsystem som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Inga | |

Du kan hämta beroendeagenten från följande platser:

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54AA7F0B |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Om du vill aktivera och komma åt funktionerna i Azure Monitor för virtuella datorer måste du ha rollen *Log Analytics-deltagare.* Om du vill visa prestanda, hälsa och kartdata måste du ha *rollen övervakningsläsare* för Den virtuella Azure-datorn. Log Analytics-arbetsytan måste konfigureras för Azure Monitor för virtuella datorer.

Mer information om hur du styr åtkomsten till en Log Analytics-arbetsyta finns i [Hantera arbetsytor](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor för virtuella datorer

Aktivera Azure Monitor för virtuella datorer med någon av de metoder som beskrivs i den här tabellen:

| Driftsättningstillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Skala uppsättning för en enda Virtuell virtuell dator eller virtuell dator | [Aktivera från den virtuella datorn](vminsights-enable-single-vm.md) | Du kan aktivera en enda Azure-virtuell dator genom att välja **insikter** direkt från den virtuella datorn eller skalningsuppsättningen för virtuella datorer. |
| Flera Azure-virtuella datorer eller skaluppsättningar för virtuella datorer | [Aktivera via Azure-princip](vminsights-enable-at-scale-policy.md) | Du kan aktivera flera virtuella Azure-datorer med hjälp av Azure-princip och tillgängliga principdefinitioner. |
| Flera Azure-virtuella datorer eller skaluppsättningar för virtuella datorer | [Aktivera via Azure PowerShell- eller Azure Resource Manager-mallar](vminsights-enable-at-scale-powershell.md) | Du kan aktivera flera Azure-virtuella datorer eller skalningsuppsättningar för virtuella datorer över en angiven prenumeration eller resursgrupp med hjälp av Azure PowerShell- eller Azure Resource Manager-mallar. |
| Hybridmoln | [Aktivera för hybridmiljön](vminsights-enable-hybrid-cloud.md) | Du kan distribuera till virtuella datorer eller fysiska datorer som finns i ditt datacenter eller andra molnmiljöer. |

## <a name="management-packs"></a>Hanteringspaket

När Azure Monitor för virtuella datorer är aktiverat och konfigurerat med en Log Analytics-arbetsyta vidarebefordras ett hanteringspaket till alla Windows-datorer som rapporterar till den arbetsytan. Om du har [integrerat hanteringsgruppen för System Center Operations Manager](../../azure-monitor/platform/om-agents.md) med arbetsytan Log Analytics distribueras hanteringspaketet för Service Map från hanteringsgruppen till windows-datorer som rapporterar till hanteringsgruppen.  

Hanteringspaketet heter *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Dess skriftliga till `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` mappen. Den datakälla som hanteringspaketet använder är `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata

Microsoft samlar automatiskt in användnings- och prestandadata med hjälp av Azure Monitor-tjänsten. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet. 

För att tillhandahålla korrekta och effektiva felsökningsfunktioner innehåller kartfunktionen data om programvarans konfiguration. Informationen innehåller information som operativsystem och version, IP-adress, DNS-namn och arbetsstationsnamn. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning av data finns i [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu när du har aktiverat övervakning för din virtuella dator är övervakningsinformation tillgänglig för analys i Azure Monitor för virtuella datorer.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder funktionen Prestandaövervakning finns i [Visa Azure Monitor för virtuella datorers prestanda](vminsights-performance.md). Information om hur du visar identifierade programberoenden finns i [Visa Azure Monitor för virtuella datorer Karta](vminsights-maps.md).
