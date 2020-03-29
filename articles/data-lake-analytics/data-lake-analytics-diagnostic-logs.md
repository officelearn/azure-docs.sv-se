---
title: Aktivera och visa diagnostikloggar för Azure Data Lake Analytics
description: Förstå hur du konfigurerar och får åtkomst till diagnostikloggar för Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60616523"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Åtkomst till diagnostikloggar för Azure Data Lake Analytics

Med diagnostikloggning kan du samla in granskningsspår för dataåtkomst. Dessa loggar innehåller information som:

* En lista över användare som har åtkomst till data.
* Hur ofta data används.
* Hur mycket data lagras i kontot.

## <a name="enable-logging"></a>Aktivera loggning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Öppna ditt DataSjöanalyskonto och välj **Diagnostikloggar** i avsnittet __Bildskärm.__ Välj sedan __Aktivera diagnostik__.

    ![Aktivera diagnostik för att samla in gransknings- och begärandeloggar](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Ange ett __namn__ för den här loggningskonfigurationen i __diagnostikinställningarna__och välj sedan loggningsalternativ.

    ![Aktivera diagnostik för att samla in gransknings- och begärandeloggar](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Aktivera diagnostikloggar")

   * Du kan välja att lagra/bearbeta data på tre olika sätt.

     * Välj __Arkiv till ett lagringskonto__ för att lagra loggar i ett Azure-lagringskonto. Använd det här alternativet om du vill arkivera data. Om du väljer det här alternativet måste du ange ett Azure-lagringskonto för att spara loggarna till.

     * Välj **Stream till en händelsehubb** om du vill strömma loggdata till en Azure Event Hub. Använd det här alternativet om du har en pipeline för bearbetning nedströms som analyserar inkommande loggar i realtid. Om du väljer det här alternativet måste du ange information om den Azure Event Hub som du vill använda.

     * Välj __Skicka till Logganalys om__ du vill skicka data till Azure Monitor-tjänsten. Använd det här alternativet om du vill använda Azure Monitor-loggar för att samla in och analysera loggar.
   * Ange om du vill hämta granskningsloggar eller begärandeloggar eller båda.  En begäran logg fångar varje API-begäran. En granskningslogg registrerar alla åtgärder som utlöses av den API-begäran.

   * För __Arkiv till ett lagringskonto__anger du hur många dagar som ska lagras.

   * Klicka på __Spara__.

        > [!NOTE]
        > Du måste antingen välja __Arkiv till ett lagringskonto__, __Strömma till en händelsehubb__ eller __Skicka till Logganalys__ innan du klickar på __knappen Spara.__

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Använda Azure Storage-kontot som innehåller loggdata

1. Om du vill visa blob-behållarna som innehåller loggningsdata öppnar du Azure Storage-kontot som används för DataSjöanalys för loggning och klickar sedan på __Blobbar__.

   * Behållaren **insights-logs-audit** innehåller granskningsloggarna.
   * Behållaren **insights-logs-requests** innehåller begärandeloggarna.

2. I behållarna lagras loggarna under följande filstruktur:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > Posterna `##` i sökvägen innehåller det år, månad, dag och timme då loggen skapades. DataSjöanalys skapar en fil varje `m=` timme, så `00`innehåller alltid värdet .

    Som ett exempel kan den fullständiga sökvägen till en granskningslogg vara:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    På samma sätt kan den fullständiga sökvägen till en begärandelogg vara:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Loggstruktur

Gransknings- och begärandeloggarna är i ett strukturerat JSON-format.

### <a name="request-logs"></a>Begär loggar

Här är en exempelpost i JSON-formaterad begäran logg. Varje blob har ett rotobjekt som kallas **poster** som innehåller en matris med loggobjekt.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Begär loggschema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |String |Tidsstämpeln (i UTC) för loggen |
| resourceId |String |Identifieraren för den resurs som operationen ägde rum på |
| category |String |Loggkategorin. Till exempel **begäranden**. |
| operationName |String |Namnet på den åtgärd som loggas. Till exempel GetAggregatedJobHistory. |
| resultType |String |Status för operationen, till exempel 200. |
| callerIpAddress |String |IP-adressen för den klient som gör begäran |
| correlationId |String |Identifieraren för loggen. Det här värdet kan användas för att gruppera en uppsättning relaterade loggtransaktioner. |
| identity |Objekt |Identiteten som genererade loggen |
| properties |JSON |Mer information finns i nästa avsnitt (schema för egenskaper för begäran om loggegenskaper) |

#### <a name="request-log-properties-schema"></a>Schema för begär loggegenskaper

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| Mer från HttpMethod |String |DEN HTTP-metod som används för åtgärden. Till exempel GET. |
| Sökväg |String |Den sökväg som operationen utfördes på |
| RequestContentLength |int |Innehållslängden för HTTP-begäran |
| KlientRequestId |String |Identifieraren som unikt identifierar den här begäran |
| StartTime |String |Den tidpunkt då servern tog emot begäran |
| EndTime |String |Den tidpunkt då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar

Här är en exempelpost i JSON-formaterad granskningslogg. Varje blob har ett rotobjekt som kallas **poster** som innehåller en matris med loggobjekt.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schema för spårningslogg

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |String |Tidsstämpeln (i UTC) för loggen |
| resourceId |String |Identifieraren för den resurs som operationen ägde rum på |
| category |String |Loggkategorin. Till exempel **Granska**. |
| operationName |String |Namnet på den åtgärd som loggas. Till exempel JobSubmitted. |
| resultType |String |En understatus för jobbstatusen (operationName). |
| resultSignature |String |Ytterligare information om jobbstatus (operationName). |
| identity |String |Användaren som begärde åtgärden. Till exempel susan@contoso.com. |
| properties |JSON |Mer information finns i nästa avsnitt (schema för granskningsloggegenskaper) |

> [!NOTE]
> **resultType** och **resultSignature** ger information om resultatet av en åtgärd och innehåller endast ett värde om en åtgärd har slutförts. De innehåller till exempel bara ett värde när **operationName** innehåller värdet **JobStarted** eller **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schema för egenskapsschema för granskningslogg

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| JobId |String |ID:et som tilldelats jobbet |
| Jobbnamn |String |Namnet som angavs för jobbet |
| JobbRunTime |String |Den körning som används för att bearbeta jobbet |
| SkickaTid |String |Den tid (i UTC) som jobbet skickades |
| StartTime |String |Den tid jobbet började köras efter inlämning (i UTC) |
| EndTime |String |Tiden då jobbet avslutades |
| Parallellitet |String |Antalet DataSjöanalysenheter som begärts för det här jobbet under inlämning |

> [!NOTE]
> **SubmitTime**, **StartTime,** **EndTime**och **Parallelism** ger information om en åtgärd. Dessa transaktioner innehåller bara ett värde om åtgärden har startats eller slutförts. **SubmitTime** innehåller till exempel bara ett värde efter **att operationName** har värdet **JobSubmitted**.

## <a name="process-the-log-data"></a>Bearbeta loggdata

Azure Data Lake Analytics innehåller ett exempel på hur du bearbetar och analyserar loggdata. Du hittar provet [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)på .

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
