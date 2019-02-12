---
title: Skapa en datapipeline med Azure Azure Monitor Data Collector API | Microsoft Docs
description: Du kan använda Azure Monitor HTTP Data Collector API för att lägga till POST JSON-data till Log Analytics-arbetsytan från valfri klient som kan anropa REST-API. Den här artikeln beskriver hur du överför data som lagras i filer på ett automatiserat sätt.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.openlocfilehash: d2736e397827373949da1634a99056420dc13b8a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003864"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Skapa en datapipeline med Data Collector API

Den [Azure Monitor Data Collector API](data-collector-api.md) kan du importera den anpassade loggdata till en Log Analytics-arbetsyta i Azure Monitor. De enda kraven är att data att JSON-formaterade och dela upp till 30 MB eller mindre segment. Det här är en helt flexibla mekanism som kan anslutas till på många sätt: från data som skickas direkt från ditt program, till oneoff adhoc överför. Den här artikeln beskriver vissa startpunkter för ett vanligt scenario: behovet av att ladda upp data som lagras i filer på basis av regelbundna, automatiserade. Medan pipelinen visas här kan inte de flesta högpresterande eller annars optimerade, som den är avsedd att fungera som en startpunkt för att skapa en pipeline för produktion på egen hand.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Exempel-problem
Under resten av den här artikeln ska vi granska data om sidvisningar i Application Insights. I vår hypotetiska scenario som vi vill att korrelera geografisk information som samlas in som standard av Application Insights SDK till anpassade data som innehåller befolkningen i alla länder i världen, med målet att identifiera där ska vi ägna mest pengar. 

Vi använder en allmän datakälla som den [Avregistrera världen Population prospekt](https://esa.un.org/unpd/wpp/) för detta ändamål. Data har följande enkla schema:

![Exempel enkel schema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

I vårt exempel antar vi att vi överför en ny fil med det senaste året data så fort de blir tillgängliga.

## <a name="general-design"></a>Allmän design
Vi använder en klassiska ETL-type-logik för att utforma vår pipeline. Arkitekturen ser ut på följande sätt:

![Data collection pipeline-arkitektur](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Den här artikeln inte beskriver hur du skapar data eller [överföra den till ett Azure Blob Storage-konto](../../storage/blobs/storage-upload-process-images.md). I stället över vi flödet så fort en ny fil laddas upp till blob. Härifrån:

1. En process som identifierar att nya data har överförts.  Vårt exempel använder en [Azure Logic App](../../logic-apps/logic-apps-overview.md), som har en utlösare för att identifiera nya data som överförs till en blob.

2. En processor läser dessa nya data och konverterar den till JSON, formatet som krävs av Azure Monitor i det här exemplet, använder vi en [Azure Function](../../azure-functions/functions-overview.md) som ett enkelt, kostnadseffektivt sätt att köra vår kod för bearbetning. Funktionen har startats av samma Logikapp som vi använde för att identifiera en nya data.

3. Slutligen när JSON-objekt är tillgängliga kan skickas till Azure Monitor. Samma Logikappen skickar data till Azure Monitor med den inbyggda datainsamlare Log Analytics-aktivitet.

Även om detaljerade installationen av blob-lagring, Logikapp eller ett Azure-funktion inte beskrivs i den här artikeln, finns detaljerade instruktioner på den specifika produkter sidor.

För att övervaka denna pipeline, vi kan använda Application Insights för att övervaka vårt Azure-funktion [information här](../../azure-functions/functions-monitoring.md), och Azure Monitor för att övervaka vårt Logikapp [information här](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Att konfigurera pipelinen
Om du vill ange pipelinen, se till att du har din blob-behållare som har skapats och konfigurerats. På samma sätt kan du se till att där du vill skicka data till Log Analytics-arbetsytan har skapats.

## <a name="ingesting-json-data"></a>Mata in JSON-data
Mata in JSON-data är enkelt med Logic Apps och eftersom ingen transformation måste äga rum, vi omge hela pipelinen i en enkel Logikapp. När både blob-behållaren och Log Analytics-arbetsytan har konfigurerats kan du skapa en ny Logikapp och konfigurera den på följande sätt:

![Exempel på Logic apps arbetsflöde](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Spara Logikappen och fortsätta att testa den.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Mata in XML, CSV eller andra format av data
Logic Apps idag har inte inbyggda funktioner för att enkelt kunna omvandla XML, CSV eller andra typer till JSON-format. Vi måste därför utföra den här omvandlingen med hjälp av något annat sätt. I den här artikeln använder vi beräkning utan Server-funktionerna i Azure Functions som ett väldigt lätt och kostnaden eget sätt med detta. 

I det här exemplet vi parsa en CSV-fil, men alla andra filtyper kan bearbetas på samma sätt. Ändra bara deserialisering av delen av Azure-funktion för att återspegla rätt logiken för din typ av specifika data.

1.  Skapa en ny Azure-funktion med hjälp av funktionen runtime v1 och förbrukningsbaserad när du uppmanas till detta.  Välj den **HTTP-utlösare** mall som är riktad mot C# som en startpunkt som konfigurerar din bindningar som vi behöver. 
2.  Från den **visa filer** fliken i den högra rutan, skapa en ny fil med namnet **project.json** och klistra in följande kod från NuGet-paket som vi använder:

    ![Azure Functions-exempelprojekt](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Växla till **run.csx** från det högra fönstret och Ersätt standardkoden med följande. 

    >[!NOTE]
    >Du måste ersätta registrera modellen (klassen ”PopulationRecord”) med ditt eget dataschema för ditt projekt.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Spara din funktion.
5. Testa funktionen för att kontrollera koden fungerar som den ska. Växla till den **testa** fliken i den högra rutan, konfigurera testet på följande sätt. Placera en länk till en blob med exempeldata i den **Begärandetext** textrutan. När du klickar på **kör**, du bör se JSON-utdata i den **utdata** box:

    ![Funktionsappar testa kod](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nu måste vi gå tillbaka och ändra Logikappen vi igång med att skapa tidigare om du vill ta med data som matas in och konverteras till JSON-format.  Med hjälp av Vydesigner, konfigurera enligt följande och spara din Logikapp:

![Logic Apps arbetsflöde komplett exempel](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testa pipeline
Nu kan du ladda upp en ny fil till blob som tidigare har konfigurerat och har den övervakas av din Logikapp. Du bör snart, finns i en ny instans av Logikappen sätter igång, kalla in till din Azure-funktion och sedan skicka data till Azure Monitor. 

>[!NOTE]
>Det kan ta upp till 30 minuter innan data visas i Azure Monitor första gången du skickar en ny datatyp.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korrelering med andra data i Log Analytics och Application Insights
Kör följande fråga för att slutföra vårt mål att korrelera data om sidvisningar Application Insights med ifyllning av data som vi inhämtas från våra anpassad datakälla från din Application Insights Analytics-fönstret eller Log Analytics-arbetsyta:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Utdata bör visa två data nu anslutna källor.  

![Korrelera kopplas från data i ett exempel för search-resultat](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Förslag på förbättringar för en pipeline för produktion
Den här artikeln visas en fungerande prototyp, logiken bakom som kan tillämpas gentemot en SANT lösning med hög kvalitet. För en hög kvalitet lösning rekommenderas följande förbättringar:

* Lägg till felhantering och logik för omprövning i din Logikapp och fungerar.
* Lägg till logik för att säkerställa att 30MB/enskild Log Analytics inmatning API-anrop gränsen inte överskrids. Dela upp data i mindre segment om det behövs.
* Konfigurera en princip för Rensa på blob storage. När du har skickats till Log Analytics-arbetsytan om du vill hålla rådata som är tillgängliga för arkivering, finns det ingen anledning att fortsätta lagring av den. 
* Kontrollera övervakning är aktiverad över fullständig pipeline, lägger till punkter för spårning och aviseringar efter behov.
* Utnyttja källkontroll för att hantera koden för din funktion och en Logikapp.
* Se till att en princip för hantering av rätt ändra följs, så att om schemat ändras funktionen och Logic Apps ändras.
* Om du överför flera olika datatyper, särskilja dem till enskilda mappar i blobbehållaren och skapa logik för att fläkt logiken ut baserat på datatypen. 


## <a name="next-steps"></a>Nästa steg
Läs mer om den [Data Collector API](data-collector-api.md) att skriva data till Log Analytics-arbetsytan från valfri REST API-klient.
