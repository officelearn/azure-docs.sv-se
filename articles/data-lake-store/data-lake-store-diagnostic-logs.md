---
title: Visa diagnostikloggar för Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Information om hur du konfigurerar och få åtkomst till diagnostikloggar för Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 91cbebecc227d24337b2d1b421dd1611bf0fac46
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44390804"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Åtkomst till diagnostikloggar för Azure Data Lake Storage Gen1
Lär dig att aktivera loggning för ditt konto i Azure Data Lake Storage Gen1 och visa loggar som samlats in för ditt konto.

Organisationer kan aktivera Diagnostisk loggning för sitt konto för Azure Data Lake Storage Gen1 att samla in granskningshistorik för dataåtkomst som ger information, till exempel listan över användare som har åtkomst till data, hur ofta data används, hur mycket data lagras i den konto, osv. När aktiverad, loggas diagnostik och/eller begäranden efter bästa förmåga. Både förfrågningar och diagnostik loggposter skapas endast om det finns förfrågningar mot tjänsteslutpunkt.

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage Gen1 konto**. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Aktivera Diagnostisk loggning för ditt Data Lake Storage Gen1-konto
1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Öppna ditt Data Lake Storage Gen1-konto och dina Data Lake Storage Gen1-kontobladet klickar du på **diagnostikinställningar**.
3. I den **diagnostikinställningar** bladet klickar du på **slå på diagnostik**.

    ![Aktivera Diagnostisk loggning](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "aktivera diagnostikloggar")

3. I den **diagnostikinställningar** bladet gör följande ändringar för att konfigurera Diagnostisk loggning.
   
    ![Aktivera Diagnostisk loggning](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "aktivera diagnostikloggar")
   
   * För **namn**, ange ett värde för diagnostiklogg konfigurationen.
   * Du kan välja att store/bearbeta data på olika sätt.
     
        * Välj alternativet för att **arkivet till ett lagringskonto** att lagra loggar till ett Azure Storage-konto. Du kan använda det här alternativet om du vill arkivera data som ska bearbetas av batch vid ett senare tillfälle. Om du väljer det här alternativet måste du ange ett Azure Storage-konto för att spara loggarna till.
        
        * Välj alternativet för att **Stream till en händelsehubb** till stream loggdata till en Azure-Händelsehubb. Du kommer troligen använda det här alternativet om du har en underordnad process-pipelinen för att analysera inkommande loggar i realtid. Om du väljer det här alternativet måste du ange information för Azure-Händelsehubben som du vill använda.

        * Välj alternativet för att **skicka till Log Analytics** du använder Azure Log Analytics-tjänsten för att analysera den genererade loggdata. Om du väljer det här alternativet måste du ange information för Log Analytics-arbetsytan som du skulle göra logganalyser utför. Se [visa eller analysera data som samlas in med Log Analytics-loggsökning](../log-analytics/log-analytics-tutorial-viewdata.md) mer information om hur du använder Log Analytics.
     
   * Ange om du vill hämta granskningsloggar eller begära loggar, eller bådadera.
   * Ange antalet dagar som data måste bibehållas. Kvarhållning gäller endast om du använder Azure storage-konto för att arkivera loggdata.
   * Klicka på **Spara**.

När du har aktiverat diagnostikinställningar, kan du titta på loggarna i den **diagnostikloggar** fliken.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Visa diagnostikloggar för Data Lake Storage Gen1-konto
Det finns två sätt att visa loggdata för ditt Data Lake Storage Gen1-konto.

* Data Lake Storage Gen1 Se kontoinställningar
* Från Azure Storage-konto där data lagras

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Med hjälp av vyn Data Lake Storage Gen1 inställningar
1. Från ditt konto för Data Lake Storage Gen1 **inställningar** bladet klickar du på **diagnostikloggar**.
   
    ![Visa diagnostikloggar](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visa diagnostikloggar") 
2. I den **diagnostikloggar** bladet som du bör se loggarna efter **granskningsloggar** och **begära loggar**.
   
   * Begära loggar avbilda varje API-begäran som görs på Data Lake Storage Gen1-konto.
   * Granskningsloggar liknar begära loggar, men ger mycket mer detaljer för de åtgärder som utförs på Data Lake Storage Gen1-konto. Ett API-anrop för uppladdning i begäran loggar kan exempelvis resultera i flera ”tilläggsåtgärder” i granskningsloggarna.
3. För att hämta loggarna, klickar du på den **hämta** länken mot varje loggpost.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Från Azure Storage-kontot innehåller som loggdata
1. Öppna bladet Azure Storage-konto som är associerade med Data Lake Storage Gen1 för loggning och sedan klickar du på Blobbar. Den **Blobtjänst** bladet visar två behållare.
   
    ![Visa Diagnostisk loggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visa diagnostikloggar")
   
   * Behållaren **insights loggar granskning** innehåller granskningsloggar.
   * Behållaren **insights-logs-begäranden** innehåller loggarna som begäran.
2. Loggfilerna lagras i de här behållarna under följande struktur.
   
    ![Visa Diagnostisk loggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visa diagnostikloggar")
   
    Exempelvis kan den fullständiga sökvägen till en granskningslogg `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    På samma sätt kan den fullständiga sökvägen till en Begärandelogg `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Förstå strukturen för loggdata
Gransknings- och begäran loggarna är i JSON-format. I det här avsnittet ska vi titta på strukturen för JSON för begäran och granskningsloggar.

### <a name="request-logs"></a>Begära loggar
Här är en exempel-post i loggen för JSON-formaterad begäran. Varje blob har en rotobjektet kallas **poster** som innehåller en matris med objekt i loggen.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
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

#### <a name="request-log-schema"></a>Begäran log schema
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| tid |Sträng |Loggen tidsstämpel (i UTC) |
| resourceId |Sträng |ID för den resurs som åtgärden tog placera på |
| category |Sträng |Loggkategori. Till exempel **begäranden**. |
| operationName |Sträng |Namnet på åtgärden som loggas. Till exempel getfilestatus. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| callerIpAddress |Sträng |IP-adressen för klienten som gör begäran |
| correlationId |Sträng |ID för loggen som kan används för att gruppera en uppsättning relaterade loggposter |
| identity |Objekt |Den identitet som genereras i loggen |
| properties |JSON |Se nedan för information |

#### <a name="request-log-properties-schema"></a>Schemat för begäran log-egenskaper
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| HttpMethod |Sträng |HTTP-metoden används för åtgärden. Till exempel få. |
| Sökväg |Sträng |Sökvägen åtgärden utfördes på |
| RequestContentLength |int |Innehållslängd HTTP-förfrågan |
| clientRequestId |Sträng |Det ID som unikt identifierar den här begäran |
| startTime |Sträng |Den tidpunkt då servern tog emot begäran |
| endTime |Sträng |Den tid då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar
Här är en exempel-post i JSON-formaterade granskningsloggen. Varje blob har en rotobjektet kallas **poster** som innehåller en matris med log-objekt

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Granska loggen schema
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| tid |Sträng |Loggen tidsstämpel (i UTC) |
| resourceId |Sträng |ID för den resurs som åtgärden tog placera på |
| category |Sträng |Loggkategori. Till exempel **Audit**. |
| operationName |Sträng |Namnet på åtgärden som loggas. Till exempel getfilestatus. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| resultSignature |Sträng |Mer information om åtgärden. |
| correlationId |Sträng |ID för loggen som kan används för att gruppera en uppsättning relaterade loggposter |
| identity |Objekt |Den identitet som genereras i loggen |
| properties |JSON |Se nedan för information |

#### <a name="audit-log-properties-schema"></a>Granska loggen egenskaper schema
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| StreamName |Sträng |Sökvägen åtgärden utfördes på |

## <a name="samples-to-process-the-log-data"></a>Exempel för att bearbeta loggdata
När du skickar loggar från Azure Data Lake Storage Gen1 till Azure Log Analytics (se [visa eller analysera data som samlas in med Log Analytics-loggsökning](../log-analytics/log-analytics-tutorial-viewdata.md) mer information om hur du använder Log Analytics), följande fråga returnerar en tabell som innehåller en lista användarens visningsnamn, tidpunkten för händelserna och antalet händelser för tid för händelse tillsammans med ett visuellt diagram. Du kan enkelt ändra för att visa GUID för användare eller andra attribut:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 innehåller ett exempel på hur du bearbetar och analysera loggdata. Du hittar exemplet i [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)

