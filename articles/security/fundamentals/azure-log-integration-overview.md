---
title: Integrera loggar från Azure-resurser med dina SIEM-system | Microsoft Docs
description: Lär dig mer om Azure Log Integration, dess viktiga funktioner och hur det fungerar.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9ba4a64268bcc57f04e92be83edb2ac71f18bcaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727597"
---
# <a name="introduction-to-azure-log-integration"></a>Introduktion till Azure Log Integration

>[!IMPORTANT]
> Funktionen Azure logg integrering kommer att föråldras med 06/15/2019. AzLog hämtningar inaktiverades den 27 juni 2018. Information om vad du kan göra när du flyttar framåt finns i [använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integration gjorts tillgängligt för att förenkla uppgiften att integrera Azure-loggar med ditt lokala säkerhets informations-och händelse hanterings system (SIEM).

 Den rekommenderade metoden för att integrera Azure-loggar är att använda SIEM-leverantörens anslutningar. Azure Monitor ger möjlighet att strömma loggarna till Event Hub och SIEM-leverantörer kan skriva anslutningar för att ytterligare integrera loggar från händelsehubben till SIEM.  En beskrivning av hur detta fungerar finns i anvisningarna i övervaka data [Ströms övervakning för data händelse nav](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). Artikeln innehåller också en lista över Siem för vilka direkta Azure-kopplingar redan är tillgängliga.  

> [!IMPORTANT]
> Om ditt primära intresse samlar in loggar för virtuella datorer inkluderar de flesta SIEM-leverantörer det här alternativet i sin lösning. Att använda SIEM-leverantörens anslutning är alltid det bästa alternativet.

Dokumentationen till Azure Log Integration-funktionen underhålls fortfarande tills funktionen är föråldrad.

Läs vidare om du vill veta mer om den Azure Log Integration funktionen:

Azure Log Integration samlar in Windows-händelser från Windows Loggboken-loggar, [Azure-aktivitets loggar](/azure/azure-monitor/platform/activity-logs-overview), [Azure Security Center aviseringar](/azure/security-center/security-center-intro)och [Azure-diagnostik loggar](/azure/azure-monitor/platform/diagnostic-logs-overview) från Azure-resurser. Integreringen hjälper din SIEM-lösning att tillhandahålla en enhetlig instrument panel för alla dina till gångar, oavsett om den är lokal eller i molnet. Du kan använda en instrument panel för att ta emot, aggregera, korrelera och analysera aviseringar för säkerhets händelser.

> [!NOTE]
> För närvarande stöder Azure Log Integration endast Azures kommersiella och Azure Government moln. Andra moln stöds inte.

![Azure Log Integration processen](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>Vilka loggar kan jag integrera?

Azure ger omfattande loggning för varje Azure-tjänst. Loggarna representerar tre typer av loggar:

* **Kontroll-/hanterings loggar**: Ge insyn i [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) åtgärder för att skapa, uppdatera och ta bort. En Azure aktivitets logg är ett exempel på den här typen av logg.
* **Data Plans loggar**: Ge insyn i händelser som aktive ras när du använder en Azure-resurs. Ett exempel på den här typen av logg är Windows-Loggboken **system**-, **säkerhets**-och **program** kanaler i en virtuell Windows-dator. Ett annat exempel är Azure-diagnostik loggning, som du konfigurerar genom Azure Monitor.
* **Bearbetade händelser**: Tillhandahåll analyserad händelse-och aviserings information som bearbetas åt dig. Ett exempel på den här typen av händelse är Azure Security Center aviseringar. Azure Security Center bearbetar och analyserar din prenumeration för att tillhandahålla aviseringar som är relevanta för din aktuella säkerhets position.

Azure Log Integration stöder ArcSight, QRadar och Splunk. Kontakta din SIEM-leverantör för att bedöma om leverantören har en inbyggd koppling. Använd inte Azure Log Integration om en inbyggd koppling är tillgänglig.

Om inga andra alternativ är tillgängliga kan du överväga att använda Azure Log Integration. Följande tabell innehåller våra rekommendationer:

|SIEM | Kunden använder redan Azure log Integrator | Kunden undersöker SIEM-integrerings alternativ|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Börja migrera till [Azure Monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/). | Använd [Splunk-anslutningen](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrera till eller börja använda QRadar-anslutningen som dokumenteras i det sista avsnittet i [strömma Azure-övervaknings data till en händelsehubben för användning av ett externt verktyg](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). | Använd QRadar-anslutningen som dokumenteras i det sista avsnittet i [Stream Azure Monitoring data till en Event Hub för användning av ett externt verktyg](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). |
|**ArcSight** | Fortsätt att använda Azure logg Integrator tills en anslutning är tillgänglig och migrera sedan till den anslutningsbaserade lösningen.  | Överväg att använda Azure Monitor loggar som ett alternativ. Publicera inte till Azure Log Integration om du inte är villig att gå igenom migreringsprocessen när anslutningen blir tillgänglig. |

> [!NOTE]
> Även om Azure Log Integration är en kostnads fri lösning finns det kostnader för Azure Storage som är kopplade till lagring av logg fils information.

Om du behöver hjälp kan du skapa en [support förfrågan](/azure/azure-supportability/how-to-create-azure-support-request). För tjänsten väljer du **logg integrering**.

## <a name="next-steps"></a>Nästa steg

Den här artikeln introducerar dig för Azure Log Integration. Mer information om Azure Log Integration och vilka typer av loggar som stöds finns i följande artiklar:

* [Kom igång med Azure log integration](azure-log-integration-get-started.md). Den här självstudien vägleder dig genom installationen av Azure Log Integration. Det beskriver också hur du integrerar loggar från Windows Azure-diagnostik (WAD)-lagring, Azure-aktivitets loggar, Azure Security Center aviseringar och Azure Active Directory gransknings loggar.
* Vanliga [frågor och svar om Azure log integration vanliga frågor och svar](azure-log-integration-faq.md). Vanliga frågor och svar om Azure Log Integration.
* Lär dig mer om att [strömma Azures övervaknings data till en Event Hub för användning med ett externt verktyg](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs).

