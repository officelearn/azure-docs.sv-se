---
title: Aktivera och Visa diagnostikloggar för Azure Data Lake Analytics
description: Information om hur du ställer in och få åtkomst till diagnostikloggar för Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616523"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Åtkomst till diagnostikloggar för Azure Data Lake Analytics

Diagnostisk loggning kan du samla in granskningshistorik för dataåtkomst. De här loggarna ger information som:

* En lista över användare som har öppnat data.
* Hur ofta data används.
* Hur mycket data lagras i kontot.

## <a name="enable-logging"></a>Aktivera loggning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Öppna ditt Data Lake Analytics-konto och välj **diagnostikloggar** från den __övervakaren__ avsnittet. Välj sedan __slå på diagnostik__.

    ![Slå på diagnostik för att samla in granskning och begära loggar](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Från __diagnostikinställningar__, ange en __namn__ för den här loggningskonfiguration och välj sedan loggningsalternativen.

    ![Slå på diagnostik för att samla in granskning och begära loggar](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "aktivera diagnostikloggar")

   * Du kan välja att store/bearbeta data på tre olika sätt.

     * Välj __arkivet till ett lagringskonto__ att lagra loggar i ett Azure storage-konto. Använd det här alternativet om du vill arkivera data. Om du väljer det här alternativet måste du ange ett Azure storage-konto för att spara loggarna till.

     * Välj **Stream till en Händelsehubb** till stream loggdata till en Azure-Händelsehubb. Använd det här alternativet om du har en underordnad process-pipelinen som analyserar inkommande loggar i realtid. Om du väljer det här alternativet måste du ange information för Azure-Händelsehubben som du vill använda.

     * Välj __skicka till Log Analytics__ att skicka data till Azure Monitor-tjänsten. Använd det här alternativet om du vill använda Azure Monitor-loggar att samla in och analysera loggar.
   * Ange om du vill hämta granskningsloggar eller begära loggar, eller bådadera.  En Begärandelogg samlar in varje API-begäran. En granskningslogg som registrerar alla åtgärder som utlöses av den API-begäran.

   * För __arkivet till ett lagringskonto__, ange hur många dagar att behålla data.

   * Klicka på __Spara__.

        > [!NOTE]
        > Du måste välja __arkivet till ett lagringskonto__, __Stream till en Händelsehubb__ eller __skicka till Log Analytics__ innan du klickar på den __spara__ knappen.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Använda Azure Storage-kontot som innehåller loggdata

1. Öppna Azure Storage-kontot som används för Data Lake Analytics för loggning för att visa blobbehållare som innehåller loggningsdata, och klicka sedan på __Blobar__.

   * Behållaren **insights loggar granskning** innehåller granskningsloggar.
   * Behållaren **insights-logs-begäranden** innehåller loggarna som begäran.

2. Loggfilerna lagras i behållare, under följande filstruktur:

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
   > Den `##` posterna i sökvägen innehåller år, månad, dag och timme som loggen skapades. Data Lake Analytics skapar en fil varje timme, så `m=` alltid innehåller ett värde av `00`.

    Exempelvis kan den fullständiga sökvägen till en granskningslogg:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    På samma sätt kan det vara den fullständiga sökvägen till en Begärandelogg:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Log-struktur

Gransknings- och begäran loggarna finns i ett strukturerat JSON-format.

### <a name="request-logs"></a>Begära loggar

Här är en exempel-post i loggen för JSON-formaterad begäran. Varje blob har en rotobjektet kallas **poster** som innehåller en matris med objekt i loggen.

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

#### <a name="request-log-schema"></a>Begäran log schema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |String |Loggen tidsstämpel (i UTC) |
| resourceId |String |Identifierare för den resurs som åtgärden tog placera på |
| category |String |Loggkategori. Till exempel **begäranden**. |
| operationName |String |Namnet på åtgärden som loggas. Till exempel GetAggregatedJobHistory. |
| resultType |String |Status för åtgärden, till exempel 200. |
| callerIpAddress |String |IP-adressen för klienten som gör begäran |
| correlationId |String |Identifierare för loggen. Det här värdet kan användas för att gruppera en uppsättning relaterade poster. |
| identity |Object |Den identitet som genereras i loggen |
| properties |JSON |Finns i nästa avsnitt (begäran log egenskaper schema) för information |

#### <a name="request-log-properties-schema"></a>Schemat för begäran log-egenskaper

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| HttpMethod |String |HTTP-metoden används för åtgärden. Till exempel få. |
| Sökväg |String |Sökvägen åtgärden utfördes på |
| RequestContentLength |int |Innehållslängd HTTP-förfrågan |
| ClientRequestId |String |Det ID som unikt identifierar den här begäran |
| StartTime |String |Den tidpunkt då servern tog emot begäran |
| EndTime |String |Den tid då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar

Här är en exempel-post i JSON-formaterade granskningsloggen. Varje blob har en rotobjektet kallas **poster** som innehåller en matris med objekt i loggen.

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
| time |String |Loggen tidsstämpel (i UTC) |
| resourceId |String |Identifierare för den resurs som åtgärden tog placera på |
| category |String |Loggkategori. Till exempel **Audit**. |
| operationName |String |Namnet på åtgärden som loggas. Till exempel JobSubmitted. |
| resultType |String |En understatus för jobbets status (operationName). |
| resultSignature |String |Mer information om jobbets status (operationName). |
| identity |String |Användaren som begärde igen. Till exempel susan@contoso.com. |
| properties |JSON |Finns i nästa avsnitt (Granska loggen egenskaper schema) för information |

> [!NOTE]
> **resultType** och **resultSignature** innehåller information om resultatet av en åtgärd och bara innehålla ett värde om en åtgärd har slutförts. Till exempel som endast innehåller ett värde när **operationName** innehåller ett värde av **JobStarted** eller **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Granska loggen egenskaper schema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| JobId |String |Det ID som jobbet har tilldelats |
| Jobbnamn |String |Det namn som har angetts för jobbet |
| JobRunTime |String |Körningen används för att behandla uppgiften |
| SubmitTime |String |Tiden (i UTC) som jobbet har skickats |
| StartTime |String |Den tid som jobbet började köras efter bidrag (i UTC) |
| EndTime |String |Den tid som jobbet avslutades |
| Parallellitet |String |Antalet Data Lake Analytics-enheter som begärdes för jobbets ärendet |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, och **parallellitet** innehåller information om en åtgärd. Dessa poster endast innehåller ett värde om som åtgärden har startats eller har slutförts. Till exempel **SubmitTime** endast innehåller ett värde efter **operationName** har värdet **JobSubmitted**.

## <a name="process-the-log-data"></a>Bearbeta loggdata

Azure Data Lake Analytics innehåller ett exempel på hur du bearbetar och analysera loggdata. Du hittar exemplet i [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
