---
title: Övervaka inmatningsåtgärder för Azure Data Explorer med hjälp av diagnostikloggar
description: Lär dig hur du konfigurerar diagnostikloggar för Azure Data Explorer för att övervaka inmatningsåtgärder.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277430"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Övervaka inmatningsåtgärder för Azure Data Explorer med hjälp av diagnostikloggar (förhandsversion)

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan kan du ange (läsa in) data i en tabell i en databas så att du kan köra frågor mot den. [Azure Monitor-diagnostikloggar](/azure/azure-monitor/platform/diagnostic-logs-overview) tillhandahåller data om driften av Azure-resurser. Azure Data Explorer använder diagnostikloggar för insikter om inmatningsframgångar och fel. Du kan exportera åtgärdsloggar till Azure Storage, Event Hub eller Log Analytics för att övervaka inmatningsstatus. Loggar från Azure Storage och Azure Event Hub kan dirigeras till en tabell i Azure Data Explorer-klustret för vidare analys.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).
* Skapa ett [kluster och en databas](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Konfigurera diagnostikloggar för ett Azure Data Explorer-kluster

Diagnostikloggar kan användas för att konfigurera insamlingen av följande loggdata:
* Lyckade inmatningsåtgärder: Dessa loggar har information om slutförda inmatningsåtgärder.
* Misslyckade inmatningsåtgärder: Dessa loggar har detaljerad information om misslyckade inmatningsåtgärder inklusive felinformation. 

Data arkiveras sedan i ett lagringskonto, strömmas till en eventhubb eller skickas till Log Analytics enligt dina specifikationer.

### <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Så här aktiverar du diagnostikloggar:

1. I [Azure-portalen](https://portal.azure.com)väljer du den Azure Data Explorer-klusterresurs som du vill övervaka.
1. Under **Övervakning** väljer du **Diagnostikinställningar**.
  
    ![Lägga till diagnostikloggar](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Välj **Lägg till diagnostikinställning**.
1. I fönstret **Diagnostikinställningar:**
 
    ![Konfiguration av inställningar för diagnostik](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Välj **Namn** för diagnostikinställningen.
    1. Välj ett eller flera mål: ett lagringskonto, händelsehubb eller logganalys.
    1. Välj loggar som ska `SucceededIngestion` samlas `FailedIngestion`in: eller .
    1. Välj [mått som](using-metrics.md#supported-azure-data-explorer-metrics) ska samlas in (valfritt).  
    1. Välj **Spara** om du vill spara de nya diagnostiklogginställningarna och måtten.
    1. Skapa en **ny supportbegäran** i Azure-portalen för att begära aktivering av diagnostikloggar.

Nya inställningar ställs in om några minuter. Loggar visas sedan i det konfigurerade arkiveringsmålet (lagringskonto, händelsehubben eller logganalys). 

## <a name="diagnostic-logs-schema"></a>Schema för diagnostikloggar

Alla [Azure Monitor-diagnostikloggar delar ett gemensamt schema på den högsta nivån](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Data Explorer har unika egenskaper för sina egna händelser. Alla loggar lagras i ett JSON-format.

### <a name="ingestion-logs-schema"></a>Schema för inmatningsloggar

LoggJSON-strängar innehåller element som visas i följande tabell:

|Namn               |Beskrivning
|---                |---
|time               |Tid för rapporten
|resourceId         |Resurs-ID för Azure Resource Manager
|operationName      |Namnet på åtgärden: 'MICROSOFT. KUSTO/KLUSTER/INTAG/ÅTGÄRD"
|operationVersion   |Schemaversion: '1.0' 
|category           |Kategori för operationen. `SucceededIngestion` eller `FailedIngestion`. Egenskaper skiljer sig åt för [lyckad åtgärd](#successful-ingestion-operation-log) eller [misslyckad åtgärd](#failed-ingestion-operation-log).
|properties         |Detaljerad information om operationen.

#### <a name="successful-ingestion-operation-log"></a>Åtgärdslogg för inmatning

**Exempel:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Egenskaper för en lyckad åtgärdsdiagnostisk logg**

|Namn               |Beskrivning
|---                |---
|lyckadesOn        |Tid för slutförande av intag
|operationId        |Åtgärds-ID för inmatning av Azure Data Explorer
|databas           |Namn på måldatabasen
|tabell              |Namn på måltabellen
|intagSourceId  |ID för datakällan för inmatning
|intagKällaPath|Sökväg till datakällan för inmatningsdata eller blob URI
|rootActivityId     |Aktivitets-ID

#### <a name="failed-ingestion-operation-log"></a>Åtgärdsloggen för inmatning misslyckades

**Exempel:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Egenskaper för en diagnosk för en misslyckad åtgärdsdiagnostik**

|Namn               |Beskrivning
|---                |---
|failedOn           |Tid för slutförande av intag
|operationId        |Åtgärds-ID för inmatning av Azure Data Explorer
|databas           |Namn på måldatabasen
|tabell              |Namn på måltabellen
|intagSourceId  |ID för datakällan för inmatning
|intagKällaPath|Sökväg till datakällan för inmatningsdata eller blob URI
|rootActivityId     |Aktivitets-ID
|Detaljer            |Detaljerad beskrivning av fel- och felmeddelandet
|errorCode          |Felkod 
|felStatus      |`Permanent` eller `Transient`. Ett nytt försök av ett tillfälligt fel kan lyckas.
|kommer frånUpdatePolicy|Sant om fel kommer från en uppdateringsprincip
|börFörsök        |Sant om ett nytt försök kan lyckas

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Övr och frågar övervakningsdata i Azure Data Explorer](ingest-data-no-code.md)
* [Använda mått för att övervaka hälsotillstånd hos kluster](using-metrics.md)

