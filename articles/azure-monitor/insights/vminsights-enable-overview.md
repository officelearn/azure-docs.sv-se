---
title: Aktivera Azure Monitor för översikt över virtuella datorer (förhandsversion) | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar och konfigurerar Azure Monitor för virtuella datorer och nödvändiga systemkraven.
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
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: c84c3eb74d214a5c98aabef7b2e2987dfdf67c0f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472600"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Aktivera Azure Monitor för översikt över virtuella datorer (förhandsversion)

Den här artikeln innehåller en översikt över de tillgängliga alternativ för att konfigurera Azure Monitor för virtuella datorer att övervaka hälsa, prestanda, och identifiera programberoenden som körs på virtuella Azure-datorer och skalningsuppsättningar för virtuella datorer, lokala virtuella datorer eller virtuella datorer finns i en annan molnmiljö.  

Du kan aktivera Azure Monitor för virtuella datorer med hjälp av något av följande metoder:

* Aktivera en enskild Azure-dator eller en skalningsuppsättning för virtuella datorer genom att välja **Insights (förhandsversion)** direkt från skalningsuppsättningen för virtuell dator eller virtuell dator.
* Aktivera två eller flera virtuella Azure-datorer och VM scale sets med hjälp av Azure Policy. Med den här metoden de nödvändiga beroendena för befintliga och nya virtuella datorer och skalningsuppsättningar installeras och konfigureras korrekt. Icke-kompatibla virtuella datorer och skalningsuppsättningar rapporteras så att du kan avgöra om du vill aktivera dem och hur du vill åtgärda dem.
* Aktivera två eller flera virtuella Azure-datorer eller virtuella datorn skalningsuppsättningar över en viss prenumeration eller resursgrupp med hjälp av PowerShell.
* Aktivera för att övervaka virtuella datorer eller fysiska datorer i företagsnätverket eller andra moln.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du kontrollera att du förstår informationen i följande avsnitt.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor för virtuella datorer stöder en Log Analytics-arbetsyta i följande regioner:

- Västra centrala USA
- USA, västra 2<sup>1</sup>
- Östra USA
- Kanada, centrala<sup>1</sup>
- Storbritannien, södra<sup>1</sup>
- Västra Europa
- Sydostasien<sup>1</sup>

<sup>1</sup> den här regionen för tillfället stöder inte funktionen Health i Azure Monitor för virtuella datorer.

>[!NOTE]
>Azure-datorer kan distribueras från alla regioner och är inte begränsad till regionerna som stöds för Log Analytics-arbetsytan.
>

Om du inte har en arbetsyta kan skapa du en med någon av följande metoder:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Om du aktiverar övervakning för en virtuell Azure-dator eller en skalningsuppsättning för virtuella datorer i Azure-portalen, kan du skapa en arbetsyta under den här processen.

För scenariot i skala med Azure Policy, Azure PowerShell eller Azure Resource Manager-mallar, måste din Log Analytics-arbetsyta först följande konfiguration:

* Installera ServiceMap och InfrastructureInsights lösningar. Du kan slutföra den här installationen med hjälp av en Azure Resource Manager-mall som har angetts eller genom att använda den **konfigurera arbetsytan** alternativ finns på den **börjar** fliken.
* Konfigurera Log Analytics-arbetsytan för att samla in prestandaräknare.

För att konfigurera din arbetsyta för scenariot i skala, kan du konfigurera den med hjälp av någon av följande metoder:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Från den **konfigurera arbetsytan** alternativet på Azure Monitor för virtuella datorer [Principtäckning](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) sidan

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows- och Linux-operativsystem som stöds med Azure Monitor för virtuella datorer. En fullständig lista som beskriver den högre och den lägre Linux OS-versionen och kernel-versioner som stöds finns senare i det här avsnittet.

|Operativsystemversion |Prestanda |Kartor |Hälsa |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> the prestanda funktion i Azure Monitor för virtuella datorer är tillgängliga från Azure Monitor. Det är inte tillgängligt när du har åtkomst till den direkt från den vänstra rutan i Azure VM.

>[!NOTE]
>Följande information gäller för att ge stöd för Linux-operativsystem:
> - Endast standardversioner och SMP Linux-kernelversioner stöds.
> - Avvikande kernel versioner, t.ex. (Physical Address tillägget) och Xen, inte stöds för alla Linux-distribution. Till exempel ett system med release-sträng med *2.6.16.21-0.8-xen* stöds inte.
> - Anpassade kernlar, inklusive omkompileringar av kernlar som är standard, stöds inte.
> - CentOSPlus kernel stöds.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernel-version |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operativsystemversion | Kernel-version |
|:--|:--|
| Ubuntu 18.04 | Kernel 4.15. * |
| Ubuntu 16.04.3 | Kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency-agent

I Azure Monitor för virtuella datorer kartan funktionen hämtar data från Microsoft Dependency agenten. Beroendeagenten är beroende av Log Analytics-agenten ansluter till Log Analytics. Därför måste systemet ha Log Analytics-agenten installeras och konfigureras med beroendeagenten.

Oavsett om du aktiverar Azure Monitor för virtuella datorer för en virtuell Azure-dator eller du använda metoden i skala distributionen kan behöva du använda agent-tillägget för Azure VM beroende för att installera agenten som en del av upplevelsen.

I en hybridmiljö kan du hämta och installera beroendeagenten på något av två sätt: manuellt eller genom att använda en automatisk distributionsmetod för virtuella datorer som är värd för platser utanför Azure.

I följande tabell beskrivs de anslutna källor som har stöd för funktionen kartan i en hybridmiljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Förutom den [Log Analytics-agenten för Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-agenter kräver Microsoft Dependency agenten. En fullständig lista över operativsystemversioner, se [operativsystem som stöds](#supported-operating-systems). |
| Linux-agenter | Ja | Förutom den [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md), kräver Microsoft Dependency agenten för Linux-agenter. En fullständig lista över operativsystemversioner, se [operativsystem som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Nej | |

Beroendeagenten kan hämtas från följande platser:

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

För att aktivera och få åtkomst till funktionerna i Azure Monitor för virtuella datorer, måste tilldelas följande roller för åtkomst:

- Om du vill göra det måste du ha den *Log Analytics contributor* roll.

- Om du vill visa prestanda, hälsotillstånd och mappa data, måste du ha den *övervakning läsare* rollen för den virtuella Azure-datorn. Log Analytics-arbetsytan måste konfigureras för Azure Monitor för virtuella datorer.

Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Så här aktiverar du Azure Monitor för virtuella datorer (förhandsversion)

Du kan aktivera Azure Monitor för virtuella datorer med hjälp av någon av följande metoder som beskrivs i följande tabell.

| Distributionstillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Enkel skalningsuppsättning i virtuell Azure-dator eller virtuell dator | [Direkt från den virtuella datorn](vminsights-enable-single-vm.md) | Du kan aktivera en Azure virtuell dator genom att välja **Insights (förhandsversion)** direkt från den virtuella datorn eller den virtuella datorn skalan inställd. |
| Flera virtuella Azure-datorer eller VM-skalningsuppsättningar | [Azure Policy](vminsights-enable-at-scale-policy.md) | Du kan aktivera flera virtuella Azure-datorer med hjälp av Azure Policy och tillgängliga principdefinitioner. |
| Flera virtuella Azure-datorer eller VM-skalningsuppsättningar | [Azure PowerShell eller Azure Resource Manager-mallar](vminsights-enable-at-scale-powershell.md) | Du kan aktivera flera virtuella Azure-datorer eller VM scale sets i en viss prenumeration eller resursgrupp grupp med hjälp av Azure PowerShell eller Azure Resource Manager-mallar. |
| Hybridmoln | [Aktivera för hybridmiljö](vminsights-enable-hybrid-cloud.md) | Du kan distribuera till virtuella datorer eller fysiska datorer som finns i ditt datacenter eller andra miljöer i molnet. |

## <a name="performance-counters-enabled"></a>Prestandaräknare som aktiverats

Azure Monitor för virtuella datorer konfigurerar Log Analytics-arbetsytan för att samla in prestandaräknare som används. I följande tabell visas de objekt och prestandaräknare som samlas in var 60: e sekund.

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

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata

Microsoft samlar automatiskt in användnings- och data via din användning av Azure Monitor-tjänsten. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten för tjänsten. Data från funktionen kartan innehåller information om konfigurationen av din programvara, till exempel operativsystem och version, IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverad för den virtuella datorn bör är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer. Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md). Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
