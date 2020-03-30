---
title: Exportera Azure Security Center-aviseringar och rekommendationer till SIEMs | Microsoft-dokument
description: I den här artikeln beskrivs hur du ställer in kontinuerlig export av säkerhetsaviseringar och rekommendationer till SIEMs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158985"
---
# <a name="export-security-alerts-and-recommendations"></a>Exportera säkerhetsaviseringar och rekommendationer

Azure Security Center genererar detaljerade säkerhetsaviseringar och rekommendationer. Du kan visa dem i portalen eller via programmatiska verktyg. Du kan också behöva exportera den här informationen eller skicka den till andra övervakningsverktyg i din miljö. 

I den här artikeln beskrivs den uppsättning verktyg som gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller kontinuerligt.

Med hjälp av dessa verktyg kan du:

* Exportera kontinuerligt till Log Analytics-arbetsytor
* Exportera kontinuerligt till Azure Event Hubs (för integreringar med SIE-moduler från tredje part)
* Exportera till CSV (en gång)


## <a name="setting-up-a-continuous-export"></a>Ställa in en kontinuerlig export

Stegen nedan är nödvändiga oavsett om du konfigurerar en kontinuerlig export till Log Analytics-arbetsytan eller Azure Event Hubs.

1. Välj **Pris & inställningar i**Säkerhetscenters sidofält .

1. Välj den specifika prenumeration som du vill konfigurera dataexporten för.
    
1. Välj Kontinuerlig export på sidan **Inställningars**sidofält för den prenumerationen .

    [Exportera alternativ i Azure Security Center ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Här ser du exportalternativen. Det finns en flik för varje tillgängligt exportmål. 

1. Välj den datatyp som du vill exportera och välj bland filtren för varje typ (exportera till exempel endast varningar för hög allvarlighetsgrad).

1. Välj var du vill att data ska sparas i området Exportera mål. Data kan sparas i ett mål på en annan prenumeration (till exempel på en Central Event Hub-instans eller en central Log Analytics-arbetsyta).

1. Klicka på **Spara**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Konfigurera SIEM-integrering via Azure Event Hubs

Azure Event Hubs är en bra lösning för att programmässigt konsumera alla strömmande data. För Azure Security Center-aviseringar och rekommendationer är det det bästa sättet att integrera med en SIEM från tredje part.

> [!NOTE]
> Den mest effektiva metoden för att strömma övervakningsdata till externa verktyg i de flesta fall är att använda Azure Event Hubs. [Den här artikeln](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) innehåller en kort beskrivning av hur du kan strömma övervakningsdata från olika källor till en eventhubb och länkar till detaljerad vägledning.

> [!NOTE]
> Om du tidigare exporterade Security Center-aviseringar till en SIEM med hjälp av Azure Activity log ersätter proceduren nedan den metoden.

Om du vill visa händelsescheman för de exporterade datatyperna besöker du [händelsehubbens händelsescheman](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Att integrera med en SIEM 

När du har konfigurerat kontinuerlig export av dina valda Security Center-data till Azure Event Hubs kan du konfigurera lämplig anslutningspunkt för din SIEM:

* **Azure Sentinel** - Använd den inbyggda Azure Security Center-aviseringar [dataapp som](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) erbjuds där.
* **Splunk** - Använd [Azure Monitor-tillägget för Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Använd [en manuellt konfigurerad loggkälla](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – Använd [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Om du vill flytta kontinuerligt exporterade data automatiskt från den konfigurerade händelsehubben till Azure Data Explorer använder du instruktionerna i [Ingest-data från Event Hub till Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Kontinuerlig export till en Log Analytics-arbetsyta

Om du vill analysera Azure Security Center-data i en Log Analytics-arbetsyta eller använda Azure-aviseringar tillsammans med Security Center, konfigurera kontinuerlig export till din Log Analytics-arbetsyta.

Om du vill exportera till en Log Analytics-arbetsyta måste security centers Log Analytics-lösningar vara aktiverade på arbetsytan. Om du använder Azure-portalen aktiveras Security Centers kostnadsfria nivålösning automatiskt när du aktiverar kontinuerlig export. Om du konfigurerar om dina kontinuerliga exportinställningar programmässigt måste du dock manuellt välja den kostnadsfria eller standardiserade prisnivån för den arbetsyta som krävs i inställningar för & för **prissättning**.  

### <a name="log-analytics-tables-and-schemas"></a>Logganalystabeller och scheman

Säkerhetsaviseringar och rekommendationer lagras i tabellerna *SecurityAlert* respektive *SecurityRecommendations.* Namnet på Log Analytics-lösningen som innehåller dessa tabeller beror på om du är på den kostnadsfria nivån eller standardnivån (se [prissättning](security-center-pricing.md)): Säkerhet("Säkerhet och granskning") eller SecurityCenterFree.

![Tabellen *SecurityAlert* i Logganalys](./media/continuous-export/log-analytics-securityalert-solution.png)

Om du vill visa händelsescheman för de exporterade datatyperna går du till [registerschemana log analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Visa exporterade säkerhetsaviseringar och rekommendationer i Azure Monitor

I vissa fall kan du välja att visa de exporterade säkerhetsaviseringarna och/eller rekommendationerna i [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor ger en enhetlig aviseringsupplevelse för en mängd olika Azure-aviseringar, inklusive diagnostiklogg, måttaviseringar och anpassade aviseringar baserat på logganalysarbetsytafrågor.

Om du vill visa aviseringar och rekommendationer från Security Center i Azure Monitor konfigurerar du en aviseringsregel baserat på Logganalysfrågor (loggavisering):

1. Klicka på **Ny aviseringsregel**på sidan **Aviseringar i** Azure Monitor .

    ![Sidan Aviseringar i Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Konfigurera den nya regeln på sidan Skapa regel (på samma sätt som du konfigurerar en [loggaviseringsregel i Azure Monitor):](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

    * För **Resurs**väljer du arbetsytan Log Analytics som du exporterade säkerhetsaviseringar och rekommendationer till.

    * För **Villkor**väljer du **Anpassad loggsökning**. På sidan som visas konfigurerar du frågan, tillbakablicksperioden och frekvensperioden. I sökfrågan kan du skriva *SecurityAlert* eller *SecurityRecommendation* för att fråga de datatyper som Security Center kontinuerligt exporterar till när du aktiverar funktionen Kontinuerlig export till Log Analytics. 
    
    * Du kan också konfigurera den [åtgärdsgrupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) som du vill utlösa. Åtgärdsgrupper kan utlösa e-postsändning, ITSM-biljetter, WebHooks med mera.
    ![Aviseringsregel för Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Nu visas nya Aviseringar eller rekommendationer från Azure Security Center (beroende på din konfiguration) i Azure Monitor-aviseringar, med automatisk utlösande av en åtgärdsgrupp (om sådan finns).

## <a name="manual-one-time-export-of-security-alerts"></a>Manuell engångsexport av säkerhetsvarningar

Om du vill hämta en CSV-rapport för aviseringar eller rekommendationer öppnar du sidan **Säkerhetsvarningar** eller **rekommendationer** och klickar på knappen **Hämta CSV-rapport.**

[![Hämta aviseringar data som en CSV-fil](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Dessa rapporter innehåller aviseringar och rekommendationer för resurser från de aktuella valda prenumerationerna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar kontinuerlig export av dina rekommendationer och aviseringar. Du har också lärt dig hur du hämtar dina aviseringar data som en CSV-fil. 

För relaterat material, se följande dokumentation: 

- [Dokumentation för Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentation om Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentation om Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Scheman för automatisering av arbetsflöde och kontinuerlig exportdatatyper](https://aka.ms/ASCAutomationSchemas)
