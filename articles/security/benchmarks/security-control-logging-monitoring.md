---
title: Azure Security Control - Loggning och övervakning
description: Loggning och övervakning av säkerhetskontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545508"
---
# <a name="security-control-logging-and-monitoring"></a>Säkerhetskontroll: Loggning och övervakning

Säkerhetsloggning och övervakning fokuserar på aktiviteter som är relaterade till att aktivera, hämta och lagra granskningsloggar för Azure-tjänster.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft underhåller tidskällor för Azure-resurser, men du har möjlighet att hantera tidssynkroniseringsinställningarna för dina beräkningsresurser.

Konfigurera tidssynkronisering för Azure-beräkningsresurser:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.2 | 6.5, 6.6 | Kund |

Inta loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av slutpunktsenheter, nätverksresurser och andra säkerhetssystem. I Azure Monitor använder du Log Analytics Workspace(s) för att fråga och utföra analyser och använda Azure Storage-konton för långsiktig/arkiveringslagring.

Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM. Så här går du ombord på Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Så här samlar du in plattformsloggar och mått med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Samla in interna värdloggar för Virtuella Azure-datorer med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Så här kommer du igång med Azure Monitor och SIEM-integrering från tredje part:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.3 | 6.2, 6.3 | Kund |

Aktivera diagnostikinställningar på Azure-resurser för åtkomst till gransknings-, säkerhets- och diagnostikloggar. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element.

Så här samlar du in plattformsloggar och mått med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Förstå loggning och olika loggtyper i Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.4 | 6.2, 6.3 | Kund |

Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för att övervaka den. Om beräkningsresursen ägs av din organisation är det ditt ansvar att övervaka den. Du kan använda Azure Security Center för att övervaka operativsystemet. Data som samlas in av Security Center från operativsystemet inkluderar OS-typ och version, OS-loggar(Windows händelseloggar), processer som körs, datornamn, IP-adresser och inloggade användare. Log Analytics Agent samlar också in kraschdumpfiler.

Samla in interna värdloggar för Virtuella Azure-datorer med Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Förstå datainsamlingen i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.5 | 6.4 | Kund |

I Azure Monitor anger du lagringsperioden för Logganalysarbetsyta enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig/arkiveringslagring.

Så här anger du loggkvarhållningsparametrar för Logganalysarbetsytor:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2,6 | 6.7 | Kund |

Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat. Använd Azure Monitor's Log Analytics Workspace för att granska loggar och utföra frågor om loggdata.

Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM. 

Så här går du ombord på Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Förstå logganalys arbetsyta:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.7 | 6.8 | Kund |

Använd Azure Security Center med Log Analytics Workspace för övervakning och avisering om avvikande aktivitet som finns i säkerhetsloggar och händelser.

Alternativt kan du aktivera och ombord data till Azure Sentinel.

Så här går du ombord på Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Hantera aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Så här varnar du för logganalysloggdata:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.8 | 8.6 | Kund |

Aktivera händelsesamling mot skadlig kod för virtuella Azure-datorer och molntjänster.

Konfigurera Microsoft Antimalware för virtuella datorer:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Konfigurera Microsoft Antimalware för molntjänster:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Förstå Microsoft Antimalware:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.9 | 8.7 | Kund |

Implementera en tredjepartslösning för DNS-loggning.

## <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 2.1 | 8.8 | Kund |

Konfigurera konsolloggning och PowerShell-transkription manuellt per nod.

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhetskontroll: [Identitets- och åtkomstkontroll](security-control-identity-access-control.md)
