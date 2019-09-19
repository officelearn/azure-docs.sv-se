---
title: Övervaka Azure Datautforskaren-inmatnings åtgärder med hjälp av diagnostikloggar
description: Lär dig hur du konfigurerar diagnostikloggar för Azure Datautforskaren för att övervaka inmatnings åtgärder.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 7d0fec56791c0d3e7ae60d78da83cf286532b9ab
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124011"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Övervaka Azure Datautforskaren-inmatnings åtgärder med diagnostikloggar (för hands version)

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i en tabell i en databas så att du kan köra frågor mot den. [Azure Monitor diagnostikloggar](/azure/azure-monitor/platform/diagnostic-logs-overview) ger information om hur Azure-resurser fungerar. Azure Datautforskaren använder diagnostikloggar för insikter om inläsningar och fel. Du kan exportera åtgärds loggar till Azure Storage, Event Hub eller Log Analytics för att övervaka inmatnings status. Loggar från Azure Storage och Azure Event Hub kan dirigeras till en tabell i Azure Datautforskaren-klustret för ytterligare analys.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/).
* Skapa ett [kluster och en databas](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Konfigurera diagnostikloggar för ett Azure Datautforskaren-kluster

Diagnostikloggar kan användas för att konfigurera samlingen av följande logg data:
* Slutförda inmatnings åtgärder: De här loggarna innehåller information om slutförda inmatnings åtgärder.
* Misslyckade inmatnings åtgärder: Dessa loggar innehåller detaljerad information om misslyckade inmatnings åtgärder, inklusive fel information. 

Data arkiveras sedan i ett lagrings konto, strömmas till en Händelsehubben eller skickas till Log Analytics enligt dina specifikationer.

### <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Gör så här för att aktivera diagnostikloggar:

1. I [Azure Portal](https://portal.azure.com)väljer du den Azure datautforskaren kluster resurs som du vill övervaka.
1. Under **övervakning**väljer **diagnostikinställningar**.
  
    ![Lägg till diagnostikloggar](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Välj **Lägg till diagnostisk inställning**.
1. I fönstret **diagnostikinställningar** :
 
    ![Konfiguration av diagnostikinställningar](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Välj **namn** för den diagnostiska inställningen.
    1. Välj ett eller flera mål: ett lagrings konto, en Event Hub-eller Log Analytics.
    1. Välj loggar som ska samlas in `SucceededIngestion` : `FailedIngestion`eller.
    1. Välj [mått](using-metrics.md) som ska samlas in (valfritt).   
    1. Välj **Spara** för att spara de nya inställningarna och måtten för diagnostikloggar.
    1. Skapa en **ny supportbegäran** i Azure Portal för att begära aktivering av diagnostikloggar.

Nya inställningar kommer att ställas in på några minuter. Loggarna visas sedan i det konfigurerade lagrings målet (lagrings konto, Event Hub eller Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Diagnostikloggar schema

Alla [Azure Monitor diagnostikloggar delar ett gemensamt schema på högsta nivån](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Datautforskaren har unika egenskaper för sina egna händelser. Alla loggar lagras i JSON-format.

### <a name="ingestion-logs-schema"></a>Schema för inmatnings loggar

Log JSON-strängar innehåller element som anges i följande tabell:

|Name               |Beskrivning
|---                |---
|time               |Tid för rapporten
|resourceId         |Resurs-ID för Azure Resource Manager
|operationName      |Åtgärdens namn: UTFORSKAREN. KUSTO/KLUSTER/INTAG/ÅTGÄRD
|operationVersion   |Schema version: "1,0" 
|category           |Åtgärdens kategori. `SucceededIngestion`eller `FailedIngestion`. Egenskaperna skiljer sig åt för [åtgärden lyckades](#successful-ingestion-operation-log) eller [misslyckades](#failed-ingestion-operation-log).
|properties         |Detaljerad information om åtgärden.

#### <a name="successful-ingestion-operation-log"></a>Åtgärds loggen har slutförts

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
**Egenskaper för en lyckad Operations Diagnostic-logg**

|Name               |Beskrivning
|---                |---
|succeededOn        |Tid för slut för ande av inmatning
|operationId        |ID för inmatnings åtgärd i Azure Datautforskaren
|database           |Namnet på mål databasen
|table              |Namn på mål tabellen
|ingestionSourceId  |ID för data källan för inmatning
|ingestionSourcePath|Sökväg till inmatnings data källan eller BLOB-URI
|rootActivityId     |Aktivitets-ID

#### <a name="failed-ingestion-operation-log"></a>Åtgärds logg för misslyckad inmatning

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

**Egenskaper för en misslyckad Operations Diagnostic-logg**

|Name               |Beskrivning
|---                |---
|failedOn           |Tid för slut för ande av inmatning
|operationId        |ID för inmatnings åtgärd i Azure Datautforskaren
|database           |Namnet på mål databasen
|table              |Namn på mål tabellen
|ingestionSourceId  |ID för data källan för inmatning
|ingestionSourcePath|Sökväg till inmatnings data källan eller BLOB-URI
|rootActivityId     |Aktivitets-ID
|details            |Detaljerad beskrivning av felet och fel meddelandet
|errorCode          |Felkod 
|failureStatus      |`Permanent`eller `Transient`. Det kan lyckas att försöka igen med ett tillfälligt fel.
|originatesFromUpdatePolicy|Sant om det uppstår ett problem med en uppdaterings princip
|shouldRetry        |Sant om återförsök kan lyckas

## <a name="next-steps"></a>Nästa steg

[Använd mått för att övervaka kluster hälsa](using-metrics.md)
