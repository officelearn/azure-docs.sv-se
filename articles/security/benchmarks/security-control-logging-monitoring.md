---
title: Azure säkerhets kontroll – loggning och övervakning
description: Loggning och övervakning av säkerhets kontroller
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ceb82bf729470f9996bc9ecc766329a0d1242ec4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930033"
---
# <a name="security-control-logging-and-monitoring"></a>Säkerhets kontroll: loggning och övervakning

Säkerhets loggning och övervakning fokuserar på aktiviteter som rör aktivering, hämtning och lagring av gransknings loggar för Azure-tjänster.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft hanterar tids källor för Azure-resurser, men du har möjlighet att hantera tidssynkroniserings inställningarna för dina beräknings resurser.

Så här konfigurerar du tidssynkronisering för Azure Compute-resurser:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.2 | 6,5, 6,6 | Kund |

Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. Publicera Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Samla in plattforms loggar och-mått med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Så här kommer du igång med Azure Monitor och SIEM-integrering från tredje part:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.3 | 6,2, 6,3 | Kund |

Aktivera diagnostiska inställningar på Azure-resurser för åtkomst till gransknings-, säkerhets-och diagnostikloggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

Samla in plattforms loggar och-mått med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Förstå loggning och olika logg typer i Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.4 | 6,2, 6,3 | Kund |

Om beräknings resursen ägs av Microsoft ansvarar Microsoft för att övervaka den. Om beräknings resursen ägs av din organisation är det ditt ansvar att övervaka den. Du kan använda Azure Security Center för att övervaka operativ systemet. Data som samlas in av Security Center från operativ systemet innehåller OS-typ och version, OS (Windows-händelseloggen), processer som körs, dator namn, IP-adresser och inloggad användare. Log Analytics agent samlar även in krasch dum par-filer.

Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Förstå Azure Security Center data insamling:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.5 | 6.4 | Kund |

I Azure Monitor anger Log Analytics du arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

Så här ställer du in logg lagrings parametrar för Log Analytics arbets ytor:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2,6 | 6.7 | Kund |

Analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part. 

Publicera Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Förstå Log Analytics arbets yta:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.7 | 6.8 | Kund |

Använd Azure Security Center med Log Analytics arbets yta för övervakning och aviseringar om avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel.

Publicera Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Hantera aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Så här aviserar du om Log Analytics-loggdata:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.8 | 8,6 | Kund |

Aktivera insamling av händelse samling för program mot skadlig kod för Azure Virtual Machines och Cloud Services.

Så här konfigurerar du Microsoft Antimalware för Virtual Machines:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Så här konfigurerar du Microsoft Antimalware för Cloud Services:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Förstå Microsofts program mot skadlig kod:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.9 | 8.7 | Kund |

Implementera en lösning från tredje part för DNS-loggning.

## <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 2.1 | 8.8 | Kund |

Konfigurera konsol loggning och PowerShell-avskrift manuellt på varje nod-basis.

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhets kontroll: [identitet och Access Control](security-control-identity-access-control.md)
