---
title: Integrera loggar från Azure-resurser med din SIEM-system | Microsoft Docs
description: Läs mer om Azure Log Integration, de viktigaste funktionerna och hur det fungerar.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: c644d81b647fc1151b10a94fb636e3eeaba1ce5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478312"
---
# <a name="introduction-to-azure-log-integration"></a>Introduktion till Azure-Loggintegrering

>[!IMPORTANT]
> Funktionen Azure Log integration upphör att gälla genom 06/01/2019. AzLog hämtningar har inaktiverats på den 27 juni 2018. För information om vad du gör Flytta framåt granska inlägget [Använd Azure monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integration har gjorts tillgängliga för att förenkla uppgiften att integrera Azure loggar med din lokala säkerhetsinformation och händelsehantering (SIEM) system.

 Den rekommenderade metoden för integrerande Azure loggar är att använda din SIEM-leverantör kopplingar. Azure Monitor innehåller möjligheten att strömma loggar till event hubs och SIEM-leverantörer kan skriva kopplingar för att ytterligare integrera loggar från händelsehubben i SIEM.  En beskrivning av hur det fungerar, följer du anvisningarna i [övervakaren stream övervakning för data händelsehubbar](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Dessutom anges SIEMs som direkt Azure anslutningsapparna finns redan.  

> [!IMPORTANT]
> Om din primära intresse samlar in virtuella datorer, loggar, inkludera detta alternativ i de flesta SIEM-leverantörer i sin lösning. Med hjälp av SIEM leverantörens connector är alltid bra alternativ.

Dokumentation om Azure Log Integration funktionen underhålls fortfarande tills funktionen är inaktuell.

Läs vidare om du vill veta mer om Azure Log Integration-funktionen:

Azure Log Integration samlar in Windows-händelser från Windows Loggboken, [Azure-aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md), [aviseringar i Azure Security Center](../security-center/security-center-intro.md), och [Azure Diagnostics-loggar](../azure-monitor/platform/diagnostic-logs-overview.md) från Azure-resurser. Integrering hjälper din SIEM-lösning som ger en enhetlig instrumentpanel för alla dina tillgångar, om den lokala eller i molnet. Du kan använda en instrumentpanel för att ta emot, aggregera, korrelera och analysera aviseringar för säkerhetshändelser.

> [!NOTE]
> Azure Log Integration stöder för närvarande endast kommersiella Azure och Azure Government-moln. Andra moln stöds inte.

![Azure Log Integration-processen][1]

## <a name="what-logs-can-i-integrate"></a>Vilka loggar kan integrera?

Azure ger utförlig loggning för varje Azure-tjänsten. Loggarna representerar tre loggtyper:

* **Kontroll och hantering loggar**: Ger bättre inblick i de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) skapa, uppdatera och ta bort åtgärder. En Azure-aktivitetsloggen är ett exempel på den här typen av loggen.
* **Data-dataplaner loggar**: Ge dig information om händelser som är när du använder en Azure-resurs. Ett exempel på den här typen av loggen är Windows Loggboken **System**, **Security**, och **program** kanaler på en Windows-dator. Ett annat exempel är Azure Diagnostics-loggning, vilket du kan konfigurera via Azure Monitor.
* **Bearbetade händelser**: Ange analyserade händelse- och aviseringsinformation som bearbetas åt dig. Ett exempel på den här typen av händelse är Azure Security Center-aviseringar. Azure Security Center bearbetar och analyserar din prenumeration för att ange aviseringar som är relevanta för din aktuella säkerhetsstatus.

Azure Log Integration stöder ArcSight, QRadar och Splunk. Kontrollera med din SIEM-leverantör för att bedöma huruvida leverantören har en inbyggd anslutning. Använd inte Azure Log Integration om en inbyggd anslutning är tillgänglig.

Om inga andra alternativ är tillgängliga kan du använda Azure Log Integration. Följande tabell innehåller våra rekommendationer:

|SIEM | Kunden använder redan Azure log integrator | Kunden undersöker alternativ för SIEM-integrering|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Börja migrera till den [Azure Monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/). | Använd den [Splunk connector](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrera till eller komma igång med QRadar anslutningen som beskrivs i det sista avsnittet i [Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). | Använda anslutningstjänsten QRadar som beskrivs i det sista avsnittet i [Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Fortsätta att använda Azure log integrator tills en anslutning är tillgänglig och migrera sedan till den connector-baserad lösningen.  | Överväg att använda Azure Monitor-loggar som ett alternativ. Inte publicera till Azure Log Integration såvida du inte vill gå igenom migreringsprocessen när anslutningen blir tillgänglig. |

> [!NOTE]
> Azure Log Integration är en kostnadsfri lösning, men det finns kostnader för Azure storage som är associerade med log fillagring för information.

Om du behöver hjälp kan du skapa en [supportförfrågan](../azure-supportability/how-to-create-azure-support-request.md). Tjänsten, Välj **Loggintegrering**.

## <a name="next-steps"></a>Nästa steg

Den här artikeln har du introducerats till Azure Log Integration. Mer information om Azure Log Integration och vilka loggtyper som stöds finns i följande artiklar:

* [Kom igång med Azure Log Integration](security-azure-log-integration-get-started.md). Den här självstudien vägleder dig genom installationen av Azure Log Integration. Det beskriver också hur du integrerar loggar från Windows Azure Diagnostics SÄKERHETSSPECIFIKA storage, Azure-aktivitetsloggar, Azure Security Center-aviseringar och granskningsloggar i Azure Active Directory.
* [Vanliga frågor (och svar FAQ) om Azure Log Integration](security-azure-log-integration-faq.md). HÄR får du svar på vanliga frågor om Azure Log Integration.
* Mer information om hur du [strömma Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
