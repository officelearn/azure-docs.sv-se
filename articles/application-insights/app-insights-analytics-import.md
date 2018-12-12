---
title: Importera dina data till Analytics i Azure Application Insights | Microsoft Docs
description: Importera statiska data ska kopplas till apptelemetri eller importera en separat dataström till frågan med Analytics.
services: application-insights
keywords: Öppna schema, importera data
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: mbullwin
ms.openlocfilehash: 7160232cf511226b26c15e6476ff75fcdf5ad33e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866608"
---
# <a name="import-data-into-analytics"></a>Importera data till Analytics

Importera alla tabelldata i [Analytics](app-insights-analytics.md), antingen för att ansluta till den med [Application Insights](app-insights-overview.md) telemetri från din app, eller så att du kan analysera dem som en separat dataström. Analytics är ett kraftfullt frågespråk lämpade för att analysera stora volymer tidsstämplad strömmar av telemetri.
Du kan importera data till Analytics med hjälp av ditt eget schema. Det behöver inte använda standard Application Insights-scheman som begäran eller trace.

Du kan importera JSON eller DSV (avgränsare med kommaavgränsade värden - kommatecken, semikolon eller flik) filer.

> [!IMPORTANT]
> Den här artikeln har **inaktuell**. Den rekommenderade metoden för att hämta data till Log Analytics sker via det [Log Analytics API för datainsamling.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)

Det finns tre situationer som kan importeras till Analytics vara användbar:

* **Träffa apptelemetri.** Exempelvis kan importera du en tabell som mappar URL: er från din webbplats till lättare att läsa rubriker. I Analytics, kan du skapa en instrumentpanel diagrammet rapport som visar de 10 populäraste sidorna på webbplatsen. Det kan nu visa titlarna sidan i stället för URL: er.
* **Korrelera din programtelemetri** med andra källor, till exempel nätverkstrafik, server-data eller CDN loggfiler.
* **Avser en separat stream Analytics.** Application Insights Analytics är ett kraftfullt verktyg som fungerar bra med null-optimerade, tidsstämplad strömmar - mycket bättre än SQL i många fall. Om du har en sådan ström från någon annan källa, kan du analysera den med Analytics.

Det är enkelt att skicka data till datakällan. 

1. (En gång) Definiera schemat för dina data i en-datakälla.
2. (Med jämna mellanrum) Ladda upp data till Azure storage och anropa REST-API för att meddela oss att nya data väntar på inmatning. Data är tillgängliga för frågor i Analytics inom några minuter.

Frekvensen av överföringen har definierats av dig och hur snabbt vill du att dina data ska vara tillgängliga för frågor. Det är mer effektivt att ladda upp data i större segment, men inte är större än 1GB.

> [!NOTE]
> *Massor av datakällor för att analysera?* [*Överväg att använda* logstash *kan leverera dina data till Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Innan du börjar

Du behöver:

1. En Application Insights-resurs i Microsoft Azure.

 * Om du vill analysera dina data separat från andra telemetri, [skapa en ny Application Insights-resurs](app-insights-create-new-resource.md).
 * Om du ansluter till eller Jämför dina data med telemetri från en app som redan har konfigurerats med Application Insights, kan du använda resursen för appen.
 * Deltagare eller ägare åtkomst till resursen.
 
2. Azure storage. Du överför till Azure storage och Analytics hämtar dina data där. 

 * Vi rekommenderar att du skapar en dedikerad storage-konto för din BLOB. Om dina blobar ska delas med andra processer, tar det längre tid för vår processer att läsa dina blobar.


## <a name="define-your-schema"></a>Definiera ditt schema

Innan du kan importera data, måste du definiera en *datakällan,* som anger schemat för dina data.
Du kan ha upp till 50 datakällor i en enda Application Insights-resurs

1. Starta guiden datakälla. Använd knappen ”Lägg till ny datakälla”. Du kan också - klicka på knappen i övre högra hörnet och välj ”datakällor” i listrutan.

    ![Lägg till ny datakälla](./media/app-insights-analytics-import/add-new-data-source.png)

    Ange ett namn för den nya datakällan.

2. Definiera formatet för de filer som du kommer att överföra.

    Du kan definiera formatet manuellt eller ladda upp en exempelfil.

    Om data är i CSV-format, kan den första raden i exemplet vara kolumnrubriker. Du kan ändra namnen på i nästa steg.

    Exemplet bör innehålla minst 10 rader eller poster av data.

    Kolumnen eller fältnamn bör ha alfanumeriskt namn (utan blanksteg eller skiljetecken).

    ![Ladda upp en exempelfil](./media/app-insights-analytics-import/sample-data-file.png)


3. Granska det schema som varit i guiden. Om den härleds typer från ett exempel kan du behöva justera antydda typer av kolumnerna.

    ![Granska antydda schemat](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Valfritt.) Ladda upp en schemadefinition. Se ha nedanstående format.

 * Välj en tidsstämpel. Alla data i Analytics måste ha något tidsstämpelsfält. Det måste vara av typen `datetime`, men det behöver inte ha namnet ”timestamp”. Om dina data har en kolumn som innehåller ett datum och tid i ISO-format, väljer du det som kolumn för tidsstämpel. Annars väljer du ”som data anlänt” och importen läggs ett tidsstämpelsfält.

5. Skapa datakällan.

### <a name="schema-definition-file-format"></a>Schemaformat för paketdefinitionsfiler

Du kan läsa in schemadefinitionen från en fil i stället för att redigera schemat i Användargränssnittet. Formatet schemat definition är följande: 

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
 
Varje kolumn har identifierats av plats, namn och typ.

* Plats – är för avgränsad fil formatera den positionen för det mappade värdet. JSON-format är det jpath den mappade nyckeln.
* Namn – visningsnamnet för kolumnen.
* Typ - datatypen för kolumnen.

> [!NOTE]
> Om exempeldata har använts och formatet är avgränsat, schemadefinitionen Mappa alla kolumner och lägga till nya datakolumner i slutet.
> 
> JSON kan delvis mappning av data, därför schemadefinitionen med JSON-format behöver inte mappa alla nycklar som finns i exempeldata. Det kan också mappa kolumner som inte är en del av exempeldata. 

## <a name="import-data"></a>Importera data

För att importera data, överföra den till Azure storage, skapa en åtkomstnyckel för den och gör sedan ett REST API-anrop.

![Lägg till ny datakälla](./media/app-insights-analytics-import/analytics-upload-process.png)

Du kan utföra följande process manuellt eller konfigurera automatiserade system att göra det med jämna mellanrum. Du måste följa de här stegen för varje datablock som du vill importera.

1. Ladda upp data till [Azure blobblagring](../storage/blobs/storage-quickstart-blobs-dotnet.md). 

 * Blobar kan vara en storlek på upp till 1GB okomprimerad. Stora blobbar hundratals MB är idealiska ur prestandaperspektiv.
 * Du kan komprimera den med Gzip att förbättra tid och svarstid för data som ska vara tillgängliga för frågor. Använd den `.gz` filnamnstillägg.
 * Det är bäst att använda ett separat lagringskonto för detta ändamål, för att undvika anrop från olika tjänster långsammare prestanda.
 * När du skickar data i hög frekvens, rekommenderas några sekunders att använda mer än ett lagringskonto av prestandaskäl.

 
2. [Skapa en nyckel för signatur för delad åtkomst för blobben](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Nyckeln bör ha en förfalloperiod på en dag och ger läsåtkomst.
3. Gör ett REST-anrop för att meddela Application Insights som väntar på data.

 * Slutpunkt: `https://dc.services.visualstudio.com/v2/track`
 * HTTP-metod: INLÄGG
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
* `Schema ID`: Det schema-ID som genererades för din definierat schema. Data i den här bloben ska följa schemat.
* `DateTime`: Den tid då begäran har skickats, UTC. Vi accepterar dessa format: ISO8601 (som ”2016-01-01 13:45:01”); Rfc822 (”Wed, 14 Dec 16 14:57:01 + 0000”); RFC850 (”onsdag, 14-Dec-16 14:57:00 UTC”); RFC1123 (”Wed den 14 december 2016 14:57:00 + 0000”).
* `Instrumentation key` för Application Insights-resursen.

Data är tillgängliga i Analytics efter ett par minuter.

## <a name="error-responses"></a>Felsvar

* **400 Felaktig begäran**: Anger att nyttolasten i begäran är ogiltig. Kontrollera:
 * Rätt instrumenteringsnyckeln.
 * Giltigt tidsvärde. Det bör vara nu i UTC-tid.
 * JSON händelsens överensstämmer med schemat.
* **403 Åtkomst nekas**: blob som du har skickat är inte tillgänglig. Se till att den delade åtkomstnyckeln är giltigt och inte har upphört att gälla.
* **404 hittades inte**:
 * Blobben finns inte.
 * Målentiteten är fel.

Mer detaljerad information finns i felmeddelandet svar.


## <a name="sample-code"></a>Exempelkod

Den här koden använder den [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet-paketet.

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

### <a name="ingest-data"></a>Mata in data

Använd den här koden för varje blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Nästa steg

* [Genomgång av Log Analytics-frågespråket](../azure-monitor/log-query/get-started-portal.md)
* Om du använder Logstash kan använda den [Logstash plugin-programmet att skicka data till Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
