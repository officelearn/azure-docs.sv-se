---
title: Importera data till analyser i Azure Application Insights | Microsoft Docs
description: Importera statiska data att ansluta med app telemetri eller importera en separat dataström frågan med Analytics.
services: application-insights
keywords: Öppna schema, import av data
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 688d620e19a8a6f536d134d9c4d7c837ec06bbdc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293629"
---
# <a name="import-data-into-analytics"></a>Importera data till Analytics

Importera inga tabelldata i [Analytics](app-insights-analytics.md), antingen för att ansluta till den med [Programinsikter](app-insights-overview.md) telemetri från din app, eller så att du kan analysera dem som en separat dataström. Analytics är ett kraftfullt frågespråk som är väl lämpade för analys av stora volymer tidsstämplad dataströmmar telemetri.

Du kan importera data till Analytics med hjälp av ditt eget schema. Det behöver inte använda standard Application Insights-scheman som begäran eller trace.

Du kan importera JSON eller DSV (avgränsare med kommaavgränsade värden - kommatecken eller semikolon fliken) filer.

Det finns tre situationer som du importerar till Analytics kan vara användbar:

* **Anslut med app telemetri.** Exempelvis kan du importera en tabell som mappar URL: er från webbplatsen till mer lättläst rubriker. Du kan skapa en instrumentpanel diagramrapport som visar de tio populäraste sidorna på webbplatsen i analyser. Det kan nu visa sidnamn i stället för URL: er.
* **Korrelera programmets telemetri** med andra källor, till exempel nätverkstrafik, server-data eller CDN loggfiler.
* **Gäller Analytics en separat dataström.** Application Insights Analytics är ett kraftfullt verktyg som fungerar bra med sparse, tidsstämplad dataströmmar - mycket bättre än SQL i många fall. Om du har en sådan dataström från någon annan källa kan analysera du dem med Analytics.

Det är enkelt att skicka data till datakällan. 

1. (En gång) Definiera schemat för dina data i en-datakälla.
2. (Med jämna mellanrum) Ladda upp data till Azure-lagring och anropa REST-API för att meddela oss att nya data väntar införandet. Data är tillgängliga för frågan i Analytics inom några minuter.

Frekvensen av överföringen har definierats av dig och hur snabbt vill du att dina data ska vara tillgängliga för frågor. Det är mer effektivt att överföra data i större segment, men inte är större än 1GB.

> [!NOTE]
> *Har många datakällor för att analysera?* [*Överväg att använda* logstash *att leverera data till Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Innan du börjar

Du behöver:

1. En Application Insights-resurs i Microsoft Azure.

 * Om du vill analysera dina data separat från andra telemetri [skapar en ny Application Insights-resurs](app-insights-create-new-resource.md).
 * Om du ansluter eller jämförelse av dina data med telemetri från en app som redan har konfigurerats med Application Insights, kan du använda resursen för appen.
 * Medarbetare eller ägare åtkomst till resursen.
 
2. Azure-lagring. Du överför till Azure-lagring och Analytics hämtar dina data därifrån. 

 * Vi rekommenderar att du skapar ett dedikerat storage-konto för din BLOB. Det tar längre tid för våra processer att läsa din blobbar om dina blobbar som delas med andra processer.


## <a name="define-your-schema"></a>Definiera schemat

Innan du kan importera data, måste du definiera en *datakällan* som anger schemat för dina data.
Du kan ha upp till 50 datakällor i en enda Application Insights-resurs

1. Starta guiden datakälla. Använd knappen ”Lägg till ny datakälla”. Du kan också - klicka på inställningsknappen i övre högra hörnet och välj ”datakällor” i listrutan.

    ![Lägg till ny datakälla](./media/app-insights-analytics-import/add-new-data-source.png)

    Ange ett namn för den nya datakällan.

2. Definiera formatet för de filer som du kommer att överföra.

    Du kan definiera formatet manuellt eller ladda upp en exempelfil.

    Om data finns i CSV-format, kan den första raden i exempel vara kolumnrubrikerna. Du kan ändra fältnamn i nästa steg.

    Exemplet ska innehålla minst 10 rader eller poster av data.

    Kolumnen eller fältnamn bör ha alfanumeriskt namn (utan blanksteg eller skiljetecken).

    ![Överför en exempelfil](./media/app-insights-analytics-import/sample-data-file.png)


3. Granska det schema som du har gjort i guiden. Om den härleda typer från ett exempel kan du behöva justera antydda typer av kolumnerna.

    ![Granska antydda schemat](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Valfritt.) Överför schemadefinition. Se nedanstående format.

 * Välj en tidsstämpel. Alla data i Analytics måste ha något tidsstämpelsfält. Det måste vara av typen `datetime`, men det behöver inte ha namnet ”tidsstämpel'. Om dina data har en kolumn som innehåller datum och tid med ISO-format, väljer du det som kolumn för tidsstämpel. Annars väljer du ”som data anlänt” och importen lägger till något tidsstämpelsfält.

5. Skapa datakällan.

### <a name="schema-definition-file-format"></a>Schemaformat för paketdefinitionsfiler

Du kan läsa in schemadefinitionen från en fil i stället för att redigera schemat i Användargränssnittet. Formatet schema definition är följande: 

Avgränsad format 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

JSON-format 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Varje kolumn identifieras av plats, namn och typen. 

* Plats – är för avgränsad filformatet den positionen för det mappade värdet. JSON-format är jpath av mappade nyckeln.
* Namn – visningsnamnet för kolumnen.
* Typ – datatypen för kolumnen.
 
Om en exempeldata användes och filformatet avgränsas schemadefinitionen Mappa alla kolumner och lägga till nya kolumner i slutet. 

JSON tillåter delvis mappning av data, därför schemadefinitionen för JSON-formatet saknar att mappa alla nycklar som finns i en exempeldata. Det kan också mappa kolumner som inte är en del av exempeldata. 

## <a name="import-data"></a>Importera data

För att importera data och överföra den till Azure storage, skapa en snabbtangent för den och göra ett REST API-anrop.

![Lägg till ny datakälla](./media/app-insights-analytics-import/analytics-upload-process.png)

Du kan utföra följande process manuellt eller konfigurera ett automatiserat system för att göra det med jämna mellanrum. Du måste följa de här stegen för varje datablock som du vill importera.

1. Överföra data till [Azure-blobblagring](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Blobbar kan vara en storlek på upp till 1GB okomprimerade. Stora blobbar MB hundratals lämpar sig ur prestandaperspektiv.
 * Du kan komprimera den med Gzip att förbättra tid och svarstid för att data ska vara tillgängliga för frågan. Använd den `.gz` filnamnstillägg.
 * Det är bäst att använda ett separat lagringskonto för detta ändamål för att undvika anrop från olika tjänster sänka prestanda.
 * När du skickar data i hög frekvens rekommenderas några sekunders att använda mer än ett lagringskonto av prestandaskäl.

 
2. [Skapa en nyckel för signatur för delad åtkomst för blobben](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Nyckeln bör ha en utgångsperiod på en dag och ge läsbehörighet.
3. Skapa ett REST-anrop för att meddela Application Insights som väntar på data.

 * Slutpunkt: `https://dc.services.visualstudio.com/v2/track`
 * HTTP-metod: POST
 * Nyttolasten:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Platshållarna är:

* `Blob URI with Shared Access Key`: Du får detta från proceduren för att skapa en nyckel. Det är specifika för blob.
* `Schema ID`: Schemat ID genereras för din definierat schema. Informationen i det här blob bör passa schemat.
* `DateTime`: Den tid då begäran har skickats, UTC. Vi godkänna dessa format: ISO8601 (som ”2016-01-01 13:45:01”); Rfc822 (”ons, 14 Dec 16 14:57:01 + 0000”); RFC850 (”onsdag, 14-Dec-16 14:57:00 UTC”); RFC1123 (”ons, 14 Dec 2016 14:57:00 + 0000”).
* `Instrumentation key` i Application Insights-resurs.

Data är tillgängliga i Analytics efter några minuter.

## <a name="error-responses"></a>Felsvar

* **400 Felaktig begäran**: Anger att nyttolasten i begäran är ogiltig. Kontrollera:
 * Rätt instrumentation nyckel.
 * Giltig tid-värde. Det bör vara nu i UTC-tid.
 * JSON av händelsen överensstämmer med schemat.
* **403 Nekad**: blob som du har skickat är inte tillgänglig. Se till att den delade åtkomstnyckeln är giltigt och inte har gått ut.
* **Det gick inte att hitta 404**:
 * Blobben finns inte.
 * Målentiteten är felaktigt.

Mer detaljerad information finns i svarsmeddelandet för fel.


## <a name="sample-code"></a>Exempelkod

Den här koden används den [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet-paketet.

### <a name="classes"></a>Klasser

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>För in data

Använd den här koden för varje blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Nästa steg

* [Genomgång av Log Analytics-frågespråket](app-insights-analytics-tour.md)
* Om du använder Logstash kan använda den [Logstash plugin-programmet för att skicka data till Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
