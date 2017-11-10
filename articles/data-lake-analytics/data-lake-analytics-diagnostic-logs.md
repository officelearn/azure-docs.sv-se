---
title: "Visa diagnostikloggar för Azure Data Lake Analytics | Microsoft Docs"
description: "Lär dig att konfigurera och komma åt diagnostikloggarna för Azure Data Lake analytics "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/08/2017
ms.author: larryfr
ms.openlocfilehash: 5bab7a0646d34de3b6d71370a0fa4216845ee6a2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Åtkomst till diagnostikloggarna för Azure Data Lake Analytics

Diagnostikloggning kan du samla in data granskningsspår från filåtkomstförsök. Dessa loggar finns information som:

* En lista över användare som har öppnat data.
* Hur ofta data används.
* Hur mycket data som lagras på kontot.

## <a name="enable-logging"></a>Aktivera loggning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Öppna Data Lake Analytics-kontot och markera **diagnostikloggar** från den __övervakaren__ avsnitt. Välj därefter __aktivera diagnostiken__.

    ![Aktivera diagnostik för att samla in granskning och begära loggar](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Från __diagnostikinställningarna__, ange en __namn__ för loggningskonfiguration och välj sedan alternativ.

    ![Aktivera diagnostik för att samla in granskning och begära loggar](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "aktivera diagnostikloggar")

   * Du kan välja att lagra/bearbeta data i tre olika sätt.

     * Välj __arkivet till ett lagringskonto__ att lagra loggfiler i ett Azure storage-konto. Använd det här alternativet om du vill arkivera informationen. Om du väljer det här alternativet måste du ange ett Azure storage-konto om du vill spara loggfilerna för att.

     * Välj **dataströmmen till en Händelsehubb** dataströmmen logga data till en Azure-Händelsehubb. Använd det här alternativet om du har en pipeline för nedströms bearbetning som analysera inkommande loggarna i realtid. Om du väljer det här alternativet måste du ange detaljer för Azure-Händelsehubb som du vill använda.

     * Välj __skicka till logganalys__ att skicka data till Log Analytics-tjänsten. Använd det här alternativet om du vill använda Log Analytics för att samla in och analysera loggfiler.
   * Ange om du vill hämta granskningsloggar eller begäran loggar eller båda.  En Begärandelogg samlar in varje API-begäran. En granskningslogg registrerar alla åtgärder som utlöses av denna API-begäran.

   * För __arkivet till ett lagringskonto__, ange antalet dagar att behålla data.

   * Klicka på __Spara__.

        > [!NOTE]
        > Du måste välja antingen __arkivet till ett lagringskonto__, __dataströmmen till en Händelsehubb__ eller __skicka till logganalys__ innan du klickar på den __spara__ knappen.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Använd Azure Storage-konto som innehåller loggdata

1. Öppna Azure Storage-kontot som används för Data Lake Analytics för loggning för att visa blobbbehållare som innehåller loggningsdata, och klicka sedan på __Blobbar__.

   * Behållaren **insikter loggar granskning** innehåller granskningsloggarna.
   * Behållaren **insikter loggar begäran** innehåller loggarna begäran.

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
   > Den `##` posterna i sökvägen innehåller år, månad, dag och timme där loggen har skapats. Data Lake Analytics skapar en fil varje timme så `m=` alltid innehåller värdet `00`.

    Den fullständiga sökvägen till en granskningslogg kan vara ett exempel:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    På liknande sätt kan det vara den fullständiga sökvägen till en Begärandelogg:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Loggen struktur

Granskning och begäran loggarna finns i ett strukturerat JSON-format.

### <a name="request-logs"></a>Begäran loggar

Här är ett exempel i JSON-formaterad begäran-loggen. Varje blobb har en rotobjektet kallas **poster** som innehåller en matris med objekt.

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

#### <a name="request-log-schema"></a>Schemat för begäran-logg

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |Sträng |Loggen tidsstämpel (i UTC) |
| resourceId |Sträng |Identifierare för den resurs som åtgärden tog placera på |
| category |Sträng |Log-kategori. Till exempel **begäranden**. |
| operationName |Sträng |Namnet på åtgärden som är inloggad. Till exempel GetAggregatedJobHistory. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| callerIpAddress |Sträng |IP-adressen för den klient som begäran |
| correlationId |Sträng |Identifierare för loggen. Det här värdet kan användas för att gruppera en uppsättning relaterade loggposter. |
| identity |Objekt |Den identitet som skapar loggen |
| properties |JSON |Finns i nästa avsnitt (begäran loggen egenskaper schema) mer information |

#### <a name="request-log-properties-schema"></a>Begäran loggen egenskaper schema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| HttpMethod |Sträng |HTTP-metoden används för åtgärden. Till exempel få. |
| Sökväg |Sträng |Sökvägen åtgärden utfördes på |
| RequestContentLength |int |Den maximala längden för HTTP-begäran |
| ClientRequestId |Sträng |Det ID som unikt identifierar den här begäran |
| StartTime |Sträng |Den tidpunkt då servern tog emot begäran |
| Sluttid |Sträng |Den tid då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar

Här är ett exempel i JSON-formaterad granskningsloggen. Varje blobb har en rotobjektet kallas **poster** som innehåller en matris med objekt.

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

#### <a name="audit-log-schema"></a>Granska loggen schema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |Sträng |Loggen tidsstämpel (i UTC) |
| resourceId |Sträng |Identifierare för den resurs som åtgärden tog placera på |
| category |Sträng |Log-kategori. Till exempel **Audit**. |
| operationName |Sträng |Namnet på åtgärden som är inloggad. Till exempel JobSubmitted. |
| resultType |Sträng |En substatus för jobbstatus (operationName). |
| resultSignature |Sträng |Ytterligare information om jobbets status (operationName). |
| identity |Sträng |Användaren som begärde igen. Till exempel susan@contoso.com. |
| properties |JSON |Finns i nästa avsnitt (Granska loggen egenskaper schema) mer information |

> [!NOTE]
> **resultType** och **resultSignature** innehåller information om resultatet av en åtgärd och bara innehålla ett värde om en åtgärd har slutförts. Till exempel som endast innehåller ett värde när **operationName** innehåller värdet **JobStarted** eller **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Granska loggen egenskaper schema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| JobId |Sträng |Det ID som tilldelats jobbet |
| Jobbnamn |Sträng |Namnet som angavs för jobbet |
| JobRunTime |Sträng |Körningsmiljön som används för att bearbeta i jobbet |
| SubmitTime |Sträng |Den tid (i UTC) då jobbet skickades |
| StartTime |Sträng |Den tid som jobbet började köras efter att (i UTC) |
| Sluttid |Sträng |Den tid som jobbet avslutades |
| Parallellitet |Sträng |Antalet Data Lake Analytics-enheter som krävs för det här jobbet under överföring |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, och **parallellitet** innehåller information om en åtgärd. Dessa poster endast innehåller ett värde om som åtgärden har startats eller slutförts. Till exempel **SubmitTime** endast innehåller ett värde efter **operationName** har värdet **JobSubmitted**.

## <a name="process-the-log-data"></a>Bearbeta loggdata

Azure Data Lake Analytics innehåller ett exempel att bearbeta och analysera loggdata. Du kan hitta exempel på [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
