---
title: Visa diagnostikloggar för Azure Data Lake Store | Microsoft Docs
description: 'Lär dig att konfigurera och komma åt diagnostikloggarna för Azure Data Lake Store '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a5cdc67a138e2316c2e87a72371a6df527cc36ac
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Åtkomst till diagnostikloggarna för Azure Data Lake Store
Lär dig att aktivera loggning för ditt Data Lake Store-konto och visa loggar som samlats in för ditt konto.

Organisationer kan aktivera diagnostikloggning för sina Azure Data Lake Store-konto att samla in data granskningsspår från filåtkomstförsök som innehåller information, till exempel listan över användare som har åtkomst till data, hur ofta data används hur mycket data som lagras i konto, osv. När aktiverad loggas diagnostik och/eller begäranden för bästa prestanda. Både begäranden och diagnostik loggposter skapas bara om det finns begäranden som görs mot tjänstslutpunkten.

## <a name="prerequisites"></a>Krav
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Store-konto**. Följ instruktionerna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Aktivera diagnostikloggning för ditt Data Lake Store-konto
1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Öppna Data Lake Store-konto och ditt Data Lake Store-kontoblad klickar du på **diagnostikloggar**.
3. I den **diagnostik loggar** bladet, klickar du på **aktivera diagnostiken**.

    ![Aktivera diagnostikloggning](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "aktivera diagnostikloggar")

3. I den **diagnostiska** bladet gör följande ändringar för att konfigurera diagnostikloggning.
   
    ![Aktivera diagnostikloggning](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "aktivera diagnostikloggar")
   
   * För **namn**, ange ett värde för konfigurationen av diagnostiska loggen.
   * Du kan välja att lagra/bearbeta data på olika sätt.
     
        * Välj alternativet för att **arkivet till ett lagringskonto** att lagra loggfiler till ett Azure Storage-konto. Du använder det här alternativet om du vill arkivera de data som ska bearbetas i batch vid ett senare tillfälle. Om du väljer det här alternativet måste du ange ett Azure Storage-konto om du vill spara loggfilerna för att.
        
        * Välj alternativet för att **dataströmmen till en händelsehubb** dataströmmen logga data till en Azure-Händelsehubb. Du kommer troligen använda det här alternativet om du har en bearbetningen nedströms rörledning för att analysera inkommande loggar i realtid. Om du väljer det här alternativet måste du ange detaljer för Azure-Händelsehubb som du vill använda.

        * Välj alternativet för att **skicka till logganalys** att använda Azure Log Analytics-tjänsten för att analysera den genererade loggdata. Om du väljer det här alternativet måste du ange information för logganalys-arbetsytan som du skulle använda utför logganalysen. Se [vy eller analysera data som samlas in med logganalys loggen search](../log-analytics/log-analytics-tutorial-viewdata.md) mer information om hur du använder logganalys.
     
   * Ange om du vill hämta granskningsloggar eller begäran loggar eller båda.
   * Ange antalet dagar som data måste behållas. Kvarhållning gäller endast om du använder Azure storage-konto för att arkivera loggdata.
   * Klicka på **Spara**.

När du har aktiverat diagnostikinställningar, kan du titta på loggarna i den **diagnostikloggar** fliken.

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Visa diagnostikloggar för ditt Data Lake Store-konto
Det finns två sätt att visa loggdata för ditt Data Lake Store-konto.

* Inställningar för Visa från Data Lake Store-konto
* Från Azure Storage-konto där data lagras

### <a name="using-the-data-lake-store-settings-view"></a>I vyn inställningar för Data Lake Store
1. Från ditt Data Lake Store-konto **inställningar** bladet, klickar du på **diagnostikloggar**.
   
    ![Visa diagnostikloggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visa diagnostikloggar") 
2. I den **diagnostikloggar** bladet bör du se loggar kategoriserade efter **granskningsloggar** och **begära loggar**.
   
   * Begäran loggar avbilda alla API-begäranden som görs på Data Lake Store-konto.
   * Granskningsloggar liknar begära loggar, men ger en mycket mer detaljerad analys av åtgärder som utförs på Data Lake Store-konto. En enskild överföring API-anrop i begäran loggar kan exempelvis resultera i flera ”tilläggsåtgärder” i granskningsloggarna.
3. Hämta loggarna genom att klicka på den **hämta** länken mot varje loggpost.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Från Azure Storage-konto innehåller som loggdata
1. Öppna bladet Azure Storage-konto som är associerade med Data Lake Store loggning och klicka på Blobbar. Den **Blob-tjänst** bladet visar två behållare.
   
    ![Visa diagnostikloggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visa diagnostikloggar")
   
   * Behållaren **insikter loggar granskning** innehåller granskningsloggarna.
   * Behållaren **insikter loggar begäran** innehåller loggarna begäran.
2. Loggfilerna lagras i dessa behållare under följande struktur.
   
    ![Visa diagnostikloggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visa diagnostikloggar")
   
    Exempelvis kan vara den fullständiga sökvägen till en granskningslogg `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    På liknande sätt kan den fullständiga sökvägen till en loggen vara `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Förstå loggdata
Granskning och begäran loggarna finns i en JSON-format. I det här avsnittet ska vi titta på JSON struktur för begäran och granskningsloggar.

### <a name="request-logs"></a>Begäran loggar
Här är ett exempel i JSON-formaterad begäran-loggen. Varje blobb har en rotobjektet kallas **poster** som innehåller en matris med objekt.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schemat för begäran-logg
| namn | Typ | Beskrivning |
| --- | --- | --- |
| tid |Sträng |Loggen tidsstämpel (i UTC) |
| resourceId |Sträng |ID för den resurs som åtgärden tog placera på |
| category |Sträng |Log-kategori. Till exempel **begäranden**. |
| operationName |Sträng |Namnet på åtgärden som är inloggad. Till exempel getfilestatus. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| callerIpAddress |Sträng |IP-adressen för den klient som begäran |
| correlationId |Sträng |ID för den logg som kan används för att gruppera en uppsättning relaterade loggposter |
| identity |Objekt |Den identitet som skapar loggen |
| properties |JSON |Mer information finns nedan |

#### <a name="request-log-properties-schema"></a>Begäran loggen egenskaper schema
| namn | Typ | Beskrivning |
| --- | --- | --- |
| HttpMethod |Sträng |HTTP-metoden används för åtgärden. Till exempel få. |
| Sökväg |Sträng |Sökvägen åtgärden utfördes på |
| RequestContentLength |int |Den maximala längden för HTTP-begäran |
| ClientRequestId |Sträng |Det ID som unikt identifierar den här begäran |
| StartTime |Sträng |Den tidpunkt då servern tog emot begäran |
| Sluttid |Sträng |Den tid då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar
Här är ett exempel i JSON-formaterad granskningsloggen. Varje blobb har en rotobjektet kallas **poster** som innehåller en uppsättning objekt

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Granska loggen schema
| namn | Typ | Beskrivning |
| --- | --- | --- |
| tid |Sträng |Loggen tidsstämpel (i UTC) |
| resourceId |Sträng |ID för den resurs som åtgärden tog placera på |
| category |Sträng |Log-kategori. Till exempel **Audit**. |
| operationName |Sträng |Namnet på åtgärden som är inloggad. Till exempel getfilestatus. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| resultSignature |Sträng |Ytterligare information om åtgärden. |
| correlationId |Sträng |ID för den logg som kan används för att gruppera en uppsättning relaterade loggposter |
| identity |Objekt |Den identitet som skapar loggen |
| properties |JSON |Mer information finns nedan |

#### <a name="audit-log-properties-schema"></a>Granska loggen egenskaper schema
| namn | Typ | Beskrivning |
| --- | --- | --- |
| StreamName |Sträng |Sökvägen åtgärden utfördes på |

## <a name="samples-to-process-the-log-data"></a>Exempel för att bearbeta loggdata
När du skickar loggar från Azure Data Lake Store till Azure logganalys (finns [visa och analysera data som samlas in med logganalys loggen search](../log-analytics/log-analytics-tutorial-viewdata.md) mer information om hur du använder logganalys), följande fråga returnerar en tabell som innehåller en lista över användare Visa namn, tiden för händelser och antalet händelser för tid för händelsen tillsammans med ett visual diagram. Enkelt kan ändras om du vill visa GUID för användare eller andra attribut:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Store ger ett exempel att bearbeta och analysera loggdata. Du kan hitta exempel på [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)

