---
title: Visa diagnostikloggar för Azure Data Lake Storage Gen1 | Microsoft-dokument
description: 'Förstå hur du konfigurerar och får åtkomst till diagnostikloggar för Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878753"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Komma åt diagnostikloggar för Azure Data Lake Storage Gen1
Lär dig att aktivera diagnostikloggning för ditt Azure Data Lake Storage Gen1-konto och hur du visar loggarna som samlats in för ditt konto.

Organisationer kan aktivera diagnostikloggning för sitt Azure Data Lake Storage Gen1-konto för att samla in granskningsspår för dataåtkomst som innehåller information som en lista över användare som använder data, hur ofta data används, hur mycket data som lagras i kontot osv. När diagnostiken och/eller begäranden är aktiverade loggas den på bästa sätt. Loggposter för både begäranden och diagnostik skapas endast om det finns begäranden som görs mot tjänstslutpunkten.

## <a name="prerequisites"></a>Krav
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage Gen1-konto**. Följ instruktionerna på [Kom igång med Azure Data Lake Storage Gen1 med Hjälp av Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Aktivera diagnostikloggning för ditt Data Lake Storage Gen1-konto
1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Öppna ditt Data Lake Storage Gen1-konto och klicka på **Diagnostikinställningar**från kontobladet Data Lake Storage Gen1.
3. Klicka **på Aktivera diagnostik**i bladet **Diagnostikinställningar** .

    ![Aktivera diagnostisk loggning](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Aktivera diagnostikloggar")

3. I **diagnostikinställningsbladet** gör du följande ändringar för att konfigurera diagnostikloggning.
   
    ![Aktivera diagnostisk loggning](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Aktivera diagnostikloggar")
   
   * För **Namn**anger du ett värde för diagnostikloggkonfigurationen.
   * Du kan välja att lagra/bearbeta data på olika sätt.
     
        * Välj alternativet att **arkivera till ett lagringskonto** för att lagra loggar till ett Azure Storage-konto. Du kan använda det här alternativet om du vill arkivera de data som ska batchbehandlas vid ett senare tillfälle. Om du väljer det här alternativet måste du ange ett Azure Storage-konto för att spara loggarna till.
        
        * Välj alternativet Att **strömma till en händelsehubb** för att strömma loggdata till en Azure Event Hub. Troligtvis kommer du att använda det här alternativet om du har en nedströms bearbetning pipeline för att analysera inkommande loggar i realtid. Om du väljer det här alternativet måste du ange information om den Azure Event Hub som du vill använda.

        * Välj alternativet **Att skicka till Logganalys för** att använda Azure Monitor-tjänsten för att analysera genererade loggdata. Om du väljer det här alternativet måste du ange information om arbetsytan Log Analytics som du skulle använda analys av utföra logg. Se [Visa eller analysera data som samlats in med Azure Monitor-loggar söker efter](../azure-monitor/learn/tutorial-viewdata.md) information om hur du använder Azure Monitor-loggar.
     
   * Ange om du vill hämta granskningsloggar eller begärandeloggar eller båda.
   * Ange hur många dagar data måste lagras. Kvarhållning är endast tillämpligt om du använder Azure-lagringskonto för att arkivera loggdata.
   * Klicka på **Spara**.

När du har aktiverat diagnostikinställningar kan du titta på loggarna på fliken **Diagnostikloggar.**

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Visa diagnostikloggar för ditt Data Lake Storage Gen1-konto
Det finns två sätt att visa loggdata för ditt Data Lake Storage Gen1-konto.

* Från kontoinställningsvyn Data Lake Storage Gen1
* Från Azure Storage-kontot där data lagras

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Använda vyn Inställningar för datasjölagring gen1
1. Klicka på **Diagnostikloggar**i **bladet** DataSjölagringsgenm1.
   
    ![Visa diagnostikloggar](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visa diagnostikloggar") 
2. I bladet **Diagnostikloggar** bör du se de loggar som kategoriseras av **granskningsloggar** och **begärandeloggar**.
   
   * Begäran loggar fånga varje API-begäran som gjorts på Data Lake Storage Gen1 konto.
   * Granskningsloggar liknar begäran loggar men ger en mycket mer detaljerad uppdelning av de åtgärder som utförs på Data Lake Storage Gen1-kontot. Ett enda api-anrop för överföring i begäran kan till exempel resultera i flera tilläggsåtgärder i granskningsloggarna.
3. Om du vill hämta loggarna klickar du på länken **Hämta** mot varje loggpost.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Från Azure Storage-kontot som innehåller loggdata
1. Öppna azure storage-kontobladet som är associerat med Data Lake Storage Gen1 för loggning och klicka sedan på Blobbar. **Blob-servicebladet** listar två behållare.
   
    ![Visa diagnostikloggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visa diagnostikloggar")
   
   * Behållaren **insights-logs-audit** innehåller granskningsloggarna.
   * Behållaren **insights-logs-requests** innehåller begärandeloggarna.
2. I dessa behållare lagras loggarna under följande struktur.
   
    ![Visa diagnostikloggning](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visa diagnostikloggar")
   
    Som ett exempel kan den fullständiga sökvägen till en granskningslogg`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    På samma sätt kan den fullständiga sökvägen till en begärandelogg`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Förstå strukturen på loggdata
Gransknings- och begärandeloggarna är i JSON-format. I det här avsnittet tittar vi på JSON:s struktur för begäran och granskningsloggar.

### <a name="request-logs"></a>Begär loggar
Här är en exempelpost i JSON-formaterad begäran logg. Varje blob har ett rotobjekt som kallas **poster** som innehåller en matris med loggobjekt.

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

#### <a name="request-log-schema"></a>Begär loggschema
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |String |Tidsstämpeln (i UTC) för loggen |
| resourceId |String |ID:et för den resurs som åtgärden genomfördes på |
| category |String |Loggkategorin. Till exempel **begäranden**. |
| operationName |String |Namnet på den åtgärd som loggas. Till exempel getfilestatus. |
| resultType |String |Status för operationen, till exempel 200. |
| callerIpAddress |String |IP-adressen för den klient som gör begäran |
| correlationId |String |ID:t för loggen som kan användas för att gruppera en uppsättning relaterade loggposter |
| identity |Objekt |Identiteten som genererade loggen |
| properties |JSON |Se nedan för mer information |

#### <a name="request-log-properties-schema"></a>Schema för begär loggegenskaper
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| Mer från HttpMethod |String |DEN HTTP-metod som används för åtgärden. Till exempel GET. |
| Sökväg |String |Den sökväg som operationen utfördes på |
| RequestContentLength |int |Innehållslängden för HTTP-begäran |
| KlientRequestId |String |ID:et som unikt identifierar den här begäran |
| StartTime |String |Den tidpunkt då servern tog emot begäran |
| EndTime |String |Den tidpunkt då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar
Här är en exempelpost i JSON-formaterad granskningslogg. Varje blob har ett rotobjekt som kallas **poster** som innehåller en matris med loggobjekt

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

#### <a name="audit-log-schema"></a>Schema för spårningslogg
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |String |Tidsstämpeln (i UTC) för loggen |
| resourceId |String |ID:et för den resurs som åtgärden genomfördes på |
| category |String |Loggkategorin. Till exempel **Granska**. |
| operationName |String |Namnet på den åtgärd som loggas. Till exempel getfilestatus. |
| resultType |String |Status för operationen, till exempel 200. |
| resultSignature |String |Ytterligare information om operationen. |
| correlationId |String |ID:t för loggen som kan användas för att gruppera en uppsättning relaterade loggposter |
| identity |Objekt |Identiteten som genererade loggen |
| properties |JSON |Se nedan för mer information |

#### <a name="audit-log-properties-schema"></a>Schema för egenskapsschema för granskningslogg
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| StreamName (StreamName) |String |Den sökväg som operationen utfördes på |

## <a name="samples-to-process-the-log-data"></a>Exempel för att bearbeta loggdata
När du skickar loggar från Azure Data Lake Storage Gen1 till Azure Monitor-loggar (se [Visa eller analysera data som samlats in med Azure Monitor-loggar söker efter](../azure-monitor/learn/tutorial-viewdata.md) information om hur du använder Azure Monitor-loggar), returnerar följande fråga en tabell som innehåller en lista över användarvisningsnamn, tiden för händelserna och antalet händelser för tiden för händelsen tillsammans med ett visuellt diagram. Det kan enkelt ändras för att visa användaren GUID eller andra attribut:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 innehåller ett exempel på hur du bearbetar och analyserar loggdata. Du hittar provet [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)på . 

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)

