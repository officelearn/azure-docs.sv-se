---
title: Exportera Azure Security Center aviseringar och rekommendationer till Siem | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar kontinuerlig export av säkerhets aviseringar och rekommendationer till Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: cf8fdd8d91c035d374277c4752fb761c0c4e72c7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905623"
---
# <a name="export-security-alerts-and-recommendations"></a>Exportera säkerhetsaviseringar och rekommendationer

Azure Security Center genererar detaljerade säkerhets aviseringar och rekommendationer. Du kan visa dem i portalen eller med programmerings verktyg. Du kan också behöva exportera den här informationen eller skicka den till andra övervaknings verktyg i din miljö. 

I den här artikeln beskrivs de verktyg som du kan använda för att exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

Med de här verktygen kan du:

* Exportera kontinuerligt till Log Analytics arbets ytor
* Exportera kontinuerligt till Azure Event Hubs (för integreringar med Siem från tredje part)
* Exportera till CSV (en tid)



## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|**Rollen säkerhets administratör** i resurs gruppen (eller **ägaren**)<br>Måste också ha Skriv behörighet för mål resursen|
|Moln|![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![Yes](./media/icons/yes-icon.png) Kina, gov (till Event Hub), andra gov|
|||



## <a name="set-up-a-continuous-export"></a>Konfigurera en löpande export

Stegen nedan är nödvändiga om du konfigurerar en kontinuerlig export till Log Analytics arbets yta eller Azure Event Hubs.

1. Välj **pris & inställningar**från Security Center marginal List.

1. Välj den prenumeration som du vill konfigurera data exporten för.
    
1. Från List rutan på sidan Inställningar för den prenumerationen väljer du **löpande export**.

    [ ![ Exportera alternativ i Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) här visas export alternativen. Det finns en flik för varje tillgängligt export mål. 

1. Välj den datatyp som du vill exportera och välj bland filtren för varje typ (till exempel endast exportera aviseringar med hög allvarlighets grad).

1. Om ditt val till exempel innehåller någon av dessa fyra rekommendationer, kan du inkludera resultaten av sårbarhets utvärderingen tillsammans med dem:

    - Avgöranden för sårbarhets bedömning på SQL-databaser bör åtgärdas
    - Utvärderings resultat av säkerhets risker på dina SQL-servrar på datorer bör åtgärdas (för hands version)
    - Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas (drivs av Qualys)
    - Säkerhets risker på dina virtuella datorer bör åtgärdas

    Om du vill inkludera undersöknings resultaten med dessa rekommendationer aktiverar du alternativet **Inkludera säkerhets resultat** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Ta med säkerhets brister växla i kontinuerlig export konfiguration" :::


1. I området "Exportera mål" väljer du var du vill spara data. Data kan sparas i ett mål för en annan prenumeration (till exempel på en central Event Hub-instans eller en central Log Analytics-arbetsyta).

1. Välj **Spara**.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Konfigurera kontinuerlig export via REST API

Funktionen för kontinuerlig export kan konfigureras och hanteras via Azure Security Center [automations API](https://docs.microsoft.com/rest/api/securitycenter/automations). Använd detta API för att skapa eller uppdatera automatiseringar för export till någon av följande möjliga destinationer:

- Azure Event Hub
- Log Analytics-arbetsyta
- Azure Logic Apps 

API: et tillhandahåller ytterligare funktioner som inte är tillgängliga från Azure Portal, till exempel:

* **Större volym** – API: et gör att du kan skapa flera export konfigurationer på en enda prenumeration. Sidan för **kontinuerlig export** i Security Centers Portal gränssnitt stöder bara en export konfiguration per prenumeration.

* **Ytterligare funktioner** – API: et erbjuder ytterligare parametrar som inte visas i användar gränssnittet. Du kan till exempel lägga till taggar till din Automation-resurs och definiera din export baserat på en bredare uppsättning aviserings-och rekommendations egenskaper än de som erbjuds på sidan för **kontinuerlig export** i Security Center användarens Portal gränssnitt.

* **Mer fokuserat omfång** – API: et ger en mer detaljerad nivå för omfattningen av dina export konfigurationer. När du definierar en export med API: et kan du göra det på resurs grupps nivå. Om du använder sidan för **kontinuerlig export** i Security Center användarens Portal gränssnitt måste du definiera den på prenumerations nivå.

    > [!TIP]
    > Om du har konfigurerat flera export konfigurationer med hjälp av API: et, eller om du har använt endast API-parametrar, kommer dessa extra funktioner inte att visas i Security Center användar gränssnittet. I stället är det en banderoll som informerar dig om att andra konfigurationer finns.

Läs mer om automations-API: et i [REST API-dokumentationen](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>Konfigurera SIEM-integrering via Azure Event Hubs

Azure Event Hubs är en bra lösning för program mässigt som förbrukar alla strömmande data. För Azure Security Center aviseringar och rekommendationer är det det bästa sättet att integrera med en SIEM från tredje part.

> [!NOTE]
> Den mest effektiva metoden att strömma övervaknings data till externa verktyg i de flesta fall är att använda Azure Event Hubs. [Den här artikeln](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) innehåller en kort beskrivning av hur du kan strömma övervaknings data från olika källor till en Event Hub och länkar till detaljerad vägledning.

> [!NOTE]
> Om du tidigare har exporterat Security Center aviseringar till en SIEM med hjälp av Azure aktivitets logg ersätter proceduren nedan metoden.

Om du vill visa händelse scheman för de exporterade data typerna kan du gå till händelse [scheman för Event Hub](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integrera med en SIEM 

När du har konfigurerat den löpande exporten av dina valda Security Center data till Azure Event Hubs kan du konfigurera lämplig anslutning för din SIEM:

* **Azure Sentinel** – Använd den interna Azure Security Center aviserings [Data Connector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) som erbjuds där.
* **Splunk** – Använd [Azure Monitor-tillägget för Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM-QRadar** – Använd [en manuellt konfigurerad logg källa](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – Använd [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Om du vill flytta de kontinuerligt exporterade data automatiskt från den konfigurerade Händelsehubben till Azure Datautforskaren, använder du anvisningarna i mata in [data från händelsehubben till azure datautforskaren](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Löpande export till en Log Analytics-arbetsyta

Om du vill analysera Azure Security Center data i en Log Analytics arbets yta eller använda Azure-aviseringar tillsammans med Security Center konfigurerar du löpande export till din Log Analytics-arbetsyta.

Om du vill exportera till en Log Analytics arbets yta måste du ha Security Center Log Analytics-lösningar som är aktiverade på din arbets yta. Om du använder Azure Portal aktive ras Security Centers kostnads fri lösning automatiskt när du aktiverar kontinuerlig export. Men om du konfigurerar dina inställningar för kontinuerlig export program mässigt måste du aktivera eller inaktivera Azure Defender manuellt från sidan **pris & inställningar** .

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabeller och scheman

Säkerhets aviseringar och rekommendationer lagras i tabellerna *SecurityAlert* respektive *SecurityRecommendations* . Namnet på Log Analytics-lösningen som innehåller dessa tabeller beror på om du har aktiverat Azure Defender: Security (' Säkerhet och granskning ') eller SecurityCenterFree.

![* SecurityAlert *-tabellen i Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Om du vill visa händelse scheman för de exporterade data typerna går du till [Log Analytics tabell scheman](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Visa exporterade säkerhets aviseringar och rekommendationer i Azure Monitor

I vissa fall kan du välja att visa de exporterade säkerhets aviseringarna och/eller rekommendationerna i [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor ger en enhetlig aviserings upplevelse för en rad olika Azure-aviseringar, inklusive diagnostisk logg, mått aviseringar och anpassade aviseringar baserat på frågor från Log Analytics-arbetsyta.

Om du vill visa aviseringar och rekommendationer från Security Center i Azure Monitor konfigurerar du en varnings regel baserat på Log Analytics frågor (logg avisering):

1. På sidan **aviseringar** för Azure Monitor väljer du **ny aviserings regel**.

    ![Azure Monitor sidan aviseringar](./media/continuous-export/azure-monitor-alerts.png)

1. På sidan Skapa regel konfigurerar du din nya regel (på samma sätt som du konfigurerar en [logg varnings regel i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * För **resurs**väljer du den Log Analytics arbets yta som du exporterade säkerhets aviseringar och rekommendationer till.

    * För **villkor**väljer du **anpassad loggs ökning**. På sidan som visas konfigurerar du frågan, lookback perioden och frekvens perioden. I Sök frågan kan du skriva *SecurityAlert* eller *SecurityRecommendation* för att fråga data typerna som Security Center kontinuerligt exportera till när du aktiverar funktionen för kontinuerlig export till Log Analytics. 
    
    * Du kan också konfigurera den [Åtgärds grupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) som du vill utlösa. Åtgärds grupper kan utlösa e-post som skickas, ITSM biljetter, Webhooks och mycket annat.
    ![Azure Monitor varnings regel](./media/continuous-export/azure-monitor-alert-rule.png)

Nu visas nya Azure Security Center aviseringar eller rekommendationer (beroende på din konfiguration) i Azure Monitor aviseringar, med automatisk utlösning av en åtgärds grupp (om det finns).

## <a name="manual-one-time-export-of-security-alerts"></a>Manuell export av säkerhets aviseringar

Om du vill ladda ned en CSV-rapport för aviseringar eller rekommendationer öppnar du sidan **säkerhets aviseringar** eller **rekommendationer** och väljer knappen **Hämta CSV-rapport** .

[![Hämta aviserings data som en CSV-fil](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> De här rapporterna innehåller aviseringar och rekommendationer för resurser från de för tillfället valda prenumerationerna.



## <a name="faq---continuous-export"></a>Vanliga frågor och svar – löpande export

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Vilka kostnader ingår i export av data?

Det kostar inget att aktivera en löpande export. Kostnader kan tillkomma för inmatning och lagring av data i din Log Analytics arbets yta, beroende på din konfiguration där. 

Läs mer om [priser för Log Analytics-arbetsyta](https://azure.microsoft.com/pricing/details/monitor/).

Läs mer om [priser för Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar kontinuerliga exporter av dina rekommendationer och aviseringar. Du har också lärt dig hur du hämtar dina aviserings data som en CSV-fil. 

Information om relaterade material finns i följande dokumentation: 

- [Dokumentation om Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentation om Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentation för Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Scheman för arbets flödes automatisering och kontinuerlig export av data typer](https://aka.ms/ASCAutomationSchemas)
