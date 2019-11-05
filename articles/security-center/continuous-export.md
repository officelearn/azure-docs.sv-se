---
title: Exportera Azure Security Center aviseringar och rekommendationer till Siem | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar kontinuerlig export av säkerhets aviseringar och rekommendationer till Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cd26ed446ce676bcec85d8e413d3ec37ac236869
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522000"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Exportera säkerhets aviseringar och rekommendationer (för hands version)

Azure Security Center genererar detaljerade säkerhets aviseringar och rekommendationer. Du kan visa dessa i portalen eller med programmerings verktyg. Du kan också behöva exportera den här informationen eller skicka den till andra övervaknings verktyg i din miljö. 

I den här artikeln beskrivs en uppsättning (för hands versions verktyg) som gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

Med de här verktygen kan du:

* Generera detaljerade rapporter som CSV
* Exportera till Log Analytics arbets ytor
* Exportera till Azure Event Hubs (för integreringar med Siem från tredje part)

## <a name="setting-up-a-continuous-export"></a>Konfigurera en löpande export

1. Klicka på **prissättning & inställningar**från Security Center marginal List.

1. Välj den prenumeration som du vill konfigurera data exporten för.
    
1. Välj **kontinuerlig export (för hands version)** på sidan Inställningar för den prenumerationen.

    [![export alternativ i Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Här ser du export alternativen. Det finns en flik för varje tillgängligt export mål. 

1. Välj den datatyp som du vill exportera och välj bland filtren för varje typ (till exempel endast exportera aviseringar med hög allvarlighets grad).

1. I området "Exportera mål" väljer du var du vill spara data. Data kan sparas i ett mål för en annan prenumeration (till exempel på en central Event Hub-instans eller en central Log Analytics-arbetsyta).

1. Klicka på **Spara**.

## <a name="continuous-export-through-azure-event-hubs"></a>Kontinuerlig export via Azure Event Hubs  

> [!NOTE]
> Den mest effektiva metoden att strömma övervaknings data till externa verktyg i de flesta fall är att använda Azure Event Hubs. [Den här artikeln](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) innehåller en kort beskrivning av hur du kan strömma övervaknings data från olika källor till en Event Hub och länkar till detaljerad vägledning.

> [!NOTE]
> Om du tidigare har exporterat Security Center aviseringar till en SIEM med hjälp av Azure aktivitets logg ersätter proceduren nedan metoden.

### <a name="to-integrate-with-a-siem"></a>Integrera med en SIEM 

När du har konfigurerat den löpande exporten av dina valda Security Center data till Azure Event Hubs kan du konfigurera lämplig anslutning på din SIEM genom att följa anvisningarna nedan.

Följ de anvisningar som är relevanta för din SIEM från [den här sidan](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) och Använd relevant koppling:

* **Splunk** – Använd [Azure Monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM-QRadar** – Använd [en manuellt konfigurerad logg källa](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** – Använd [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Om du använder **Azure Sentinel**använder du den interna Azure Security Center aviserings [Data Connector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) som erbjuds där.

Om du vill flytta de kontinuerligt exporterade data automatiskt från den konfigurerade Händelsehubben till Azure Datautforskaren, använder du anvisningarna i mata in [data från händelsehubben till azure datautforskaren](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-log-analytics-workspace"></a>Löpande export till Log Analytics arbets yta

För att kunna exportera till Log Analytics arbets ytan måste du ha Security Center kostnads fri eller standard Log Analytics-nivå som är aktive rad på din arbets yta. Om du använder Azure Portal aktive ras lösningen för Security Center kostnads fri nivå automatiskt när du aktiverar kontinuerlig export. Men om du konfigurerar dina inställningar för kontinuerlig export program mässigt måste du manuellt välja den kostnads fria eller standard pris nivån för den nödvändiga arbets ytan från **pris & inställningar**.  

Säkerhets aviseringar och rekommendationer lagras i tabellerna *SecurityAlert* respektive *SecurityRecommendations* . Namnet på Log Analytics-lösningen som innehåller dessa tabeller beror på om du är på nivån kostnads fri eller standard (se [prissättning](security-center-pricing.md)): säkerhet eller SecurityCenterFree.

![\* SecurityAlert *-tabellen i Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

## <a name="manual-one-time-export-of-security-alerts"></a>Manuell export av säkerhets aviseringar

Om du vill ladda ned en CSV-rapport för aviseringar eller rekommendationer öppnar du sidan **säkerhets aviseringar** eller **rekommendationer** och klickar på **Hämta CSV-rapport (för hands version)** .

[![Hämta aviserings data som en CSV-fil](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Dessa rapporter innehåller aviseringar och rekommendationer för resurser från de valda prenumerationerna i katalog-och prenumerations filtret i Azure-portalen: ![filtret för att välja katalog + prenumeration](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar kontinuerliga exporter av dina rekommendationer och aviseringar. Du har också lärt dig hur du hämtar dina aviserings data som en CSV-fil. 

Information om relaterade material finns i följande dokumentation: 

- [Dokumentation om Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentation om Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentation om Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)