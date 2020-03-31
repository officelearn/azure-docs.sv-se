---
title: Använda datainsamlare-API för att skapa en datapipeline
description: Du kan använda AZURE Monitor HTTP Data Collector API för att lägga till POST JSON-data i Log Analytics-arbetsytan från alla klienter som kan anropa REST API. I den här artikeln beskrivs hur du laddar upp data som lagras i filer på ett automatiserat sätt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 96c64f6a0167b678f14bf0199069ecd6b4c8d57a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055118"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Skapa en datapipeline med DATA Collector API

[Azure Monitor Data Collector API](data-collector-api.md) kan du importera alla anpassade loggdata till en Log Analytics-arbetsyta i Azure Monitor. De enda kraven är att data ska JSON-formateras och delas upp i 30 MB eller mindre segment. Detta är en helt flexibel mekanism som kan anslutas till på många sätt: från data som skickas direkt från din ansökan, till enstaka adhoc uppladdningar. Den här artikeln kommer att beskriva några startpunkter för ett gemensamt scenario: behovet av att ladda upp data som lagras i filer på en regelbunden, automatiserad basis. Även om rörledningen som presenteras här inte kommer att vara den mest högpresterande eller på annat sätt optimerad, är den avsedd att fungera som en utgångspunkt för att bygga en egen produktionspipeline.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Exempel på problem
För resten av den här artikeln kommer vi att granska sidvisningsdata i Application Insights. I vårt hypotetiska scenario vill vi korrelera geografisk information som samlas in som standard av Application Insights SDK till anpassade data som innehåller befolkningen i varje land/region i världen, med målet att identifiera var vi ska spendera mest marknadsföringsdollar. 

Vi använder en offentlig datakälla som [FN:s världsbefolkningsutsikter](https://esa.un.org/unpd/wpp/) för detta ändamål. Data kommer att ha följande enkla schema:

![Exempel på enkelt schema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

I vårt exempel förutsätter vi att vi kommer att ladda upp en ny fil med det senaste årets data så snart den blir tillgänglig.

## <a name="general-design"></a>Allmän design
Vi använder en klassisk ETL-typ logik för att utforma vår pipeline. Arkitekturen kommer att se ut så här:

![Pipelinearkitektur för datainsamling](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Den här artikeln täcker inte hur du skapar data eller [överför dem till ett Azure Blob Storage-konto](../../storage/blobs/storage-upload-process-images.md). Snarare tar vi upp flödet så fort en ny fil laddas upp till blob. Härifrån:

1. En process identifierar att nya data har överförts.  I vårt exempel används en [Azure Logic App](../../logic-apps/logic-apps-overview.md), som har en tillgänglig utlösare för att identifiera nya data som överförs till en blob.

2. En processor läser dessa nya data och konverterar dem till JSON, det format som krävs av Azure Monitor I det här exemplet använder vi en [Azure-funktion](../../azure-functions/functions-overview.md) som ett lätt och kostnadseffektivt sätt att köra vår bearbetningskod. Funktionen sparkas av samma Logic App som vi använde för att identifiera en ny data.

3. Slutligen, när JSON-objektet är tillgängligt, skickas det till Azure Monitor. Samma Logic App skickar data till Azure Monitor med hjälp av den inbyggda logganalysdatainsamlareaktiviteten.

Även om den detaljerade inställningen för blob-lagring, Logic App eller Azure-funktion inte beskrivs i den här artikeln, finns detaljerade instruktioner tillgängliga på de specifika produkternas sidor.

För att övervaka den här pipelinen använder vi Application Insights för att övervaka vår Azure-funktionsinformation [här](../../azure-functions/functions-monitoring.md)och Azure Monitor för att övervaka våra [Logic App-detaljer här](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Ställa in pipelinen
Om du vill ange pipelinen kontrollerar du först att du har skapat och konfigurerat blob-behållaren. Se också till att arbetsytan Log Analytics där du vill skicka data till skapas.

## <a name="ingesting-json-data"></a>Intag av JSON-data
Intag av JSON-data är trivialt med Logic Apps, och eftersom ingen omvandling behöver ske kan vi innesluta hela pipelinen i en enda Logic App. När både blob-behållaren och log analytics-arbetsytan har konfigurerats skapar du en ny Logikapp och konfigurerar den på följande sätt:

![Exempel på arbetsflöde för logikappar](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Spara logikappen och fortsätt med att testa den.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Intag av XML, CSV eller andra dataformat
Logic Apps idag har inte inbyggda funktioner för att enkelt omvandla XML, CSV eller andra typer till JSON-format. Därför måste vi använda ett annat sätt för att slutföra denna omvandling. I den här artikeln använder vi de serverlösa beräkningsfunktionerna i Azure Functions som ett mycket lätt och kostnadseffektivt sätt att göra det. 

I det här exemplet tolkar vi en CSV-fil, men alla andra filtyper kan bearbetas på samma sätt. Ändra helt enkelt den avserialiserande delen av Azure-funktionen för att återspegla rätt logik för din specifika datatyp.

1.  Skapa en ny Azure-funktion med hjälp av funktionskörningen v1 och förbrukningsbaserad när du uppmanas.  Välj **http-utlösarmallen** som är inriktad på C# som en utgångspunkt som konfigurerar dina bindningar som vi behöver. 
2.  På fliken **Visa filer** i den högra rutan skapar du en ny fil som heter **project.json** och klistrar in följande kod från NuGet-paket som vi använder:

    ![Exempelprojekt för Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. Växla till **run.csx** från den högra rutan och ersätt standardkoden med följande. 

    >[!NOTE]
    >För projektet måste du ersätta postmodellen (klassen "PopulationRecord" med ditt eget dataschema.
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
5. Testa funktionen för att kontrollera att koden fungerar som den ska. Växla till fliken **Testa** i den högra rutan och konfigurera testet på följande sätt. Placera en länk till en blob med exempeldata i **brödtextrutan Begäran.** När du har klickat på **Kör**bör du se JSON-utdata i rutan **Utdata:**

    ![Testkod för funktionsappar](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nu måste vi gå tillbaka och ändra Logic App vi började bygga tidigare för att inkludera de data som intas och konverteras till JSON-format.  Konfigurera enligt följande med View Designer och spara logikappen:

![Exempel på slutfört arbetsflöde för Logic Apps-arbetsflöde](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testa rörledningen
Nu kan du ladda upp en ny fil till bloben som konfigurerats tidigare och få den övervakad av din Logic App. Snart bör du se en ny instans av Logic App kick off, ropa till din Azure-funktion och sedan skicka data till Azure Monitor. 

>[!NOTE]
>Det kan ta upp till 30 minuter innan data visas i Azure Monitor första gången du skickar en ny datatyp.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korrelera med andra data i Logganalys och Application Insights
Om du vill slutföra vårt mål att korrelera sidvydata för Application Insights med de populationsdata som vi har intagit från vår anpassade datakälla kör du följande fråga från antingen fönstret Application Insights Analytics eller Log Analytics-arbetsytan:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Utdata ska visa de två datakällor som nu är anslutna.  

![Korrelera osammanhängande data i ett sökresultatexempel](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Föreslagna förbättringar för en produktionspipeline
Denna artikel presenterade en fungerande prototyp, logiken bakom som kan tillämpas mot en sann produktion-kvalitet lösning. För en sådan lösning av produktionskvalitet rekommenderas följande förbättringar:

* Lägg till logikhantering och återförsökslogik i logikappen och funktionen.
* Lägg till logik för att säkerställa att 30 MB/enkel inloggnings-API-anfallsgräns inte överskrids. Dela upp data i mindre segment om det behövs.
* Konfigurera en rensningsprincip för blob-lagringen. När du har skickat till log analytics-arbetsytan, om du inte vill hålla rådata tillgängliga för arkivering, finns det ingen anledning att fortsätta lagra den. 
* Kontrollera att övervakning är aktiverad över hela pipelinen, lägga till spårningspunkter och varningar efter behov.
* Utnyttja källkontrollen för att hantera koden för din funktion och Logic App.
* Kontrollera att en korrekt ändringshanteringsprincip följs, så att om schemat ändras ändras funktionen och Logic Apps i enlighet med detta.
* Om du laddar upp flera olika datatyper, segregera dem i enskilda mappar i blob-behållaren och skapa logik för att fläkta ut logiken baserat på datatypen. 


## <a name="next-steps"></a>Nästa steg
Läs mer om [datainsamlare-API:et](data-collector-api.md) för att skriva data till Log Analytics-arbetsytan från alla REST API-klienter.
