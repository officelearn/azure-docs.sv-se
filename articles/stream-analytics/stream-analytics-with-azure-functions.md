---
title: "Kör Azure Functions med Azure Stream Analytics-jobb | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure-funktion som utdatamottagare till dataströmmen analytiska jobb."
keywords: "data som utdata, strömmande data, Azure-funktion"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: adc147fc9f47e78cc0a2fcaf53f064bcfa5eee2c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Kör Azure Functions med Azure Stream Analytics-jobb 
 
> [!IMPORTANT]
> Den här funktionen är i förhandsgranskningen.

Du kan köra Azure Functions med Azure Stream Analytics genom att konfigurera Azure Functions som en av utdata sänkor till Stream Analytics-jobbet. Azure-funktionen är en händelse som drivs, beräkning på begäran-upplevelse som gör att du kan implementera kod som utlöses av händelser i Azure eller tjänster från tredje part. Den här möjligheten för Azure-funktion för att svara på utlösare blir en naturlig utdata för Azure Stream Analytics-jobbet.

Azure Stream Analytics anropar Azure-funktion via HTTP-utlösare. Azure-funktion utdataadapter tillåter användare att ansluta Azure Functions till Stream Analytics så att händelserna kan utlösas baserat på Stream Analytics-frågor. 

Den här kursen visar hur du ansluter Azure Stream Analytics till [Azure Redis-Cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) med [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-stream-analytics-job-to-run-an-azure-function"></a>Konfigurera Stream Analytics-jobbet om du vill köra en Azure-funktion 

Det här avsnittet beskrivs hur du konfigurerar en Stream Analytics-jobbet om du vill köra en Azure-funktion som skriver data till Azure Redis-Cache. Stream Analytics-jobbet läser händelser från Event Hub, kör en fråga som anropar funktionen Azure. Den här funktionen Azure läser data från Stream Analytics-jobbet och skriver den till Azure Redis-Cache.

![Bild att illustrera kursen](./media/stream-analytics-with-azure-functions/image1.png)

Följande steg krävs för att uppnå den här uppgiften:
* [Skapa Stream Analytics-jobbet med Event Hub som indata.](#create-stream-analytics-job-with-event-hub-as-input)  
* [Skapa ett Azure Redis-Cache.](#create-an-azure-redis-cache)  
* [Skapa en Azure-funktion som kan skriva data till Redis-Cache.](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Uppdatera dataströmmen analytiska jobbet med Azure-funktion som utdata.](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Kontrollera Redis-Cache för resultat.](#check-redis-cache-for-results)  

## <a name="create-stream-analytics-job-with-event-hub-as-input"></a>Skapa Stream Analytics-jobbet med Event Hub som indata

Följ den [att upptäcka bedrägerier realtid](stream-analytics-real-time-fraud-detection.md) självstudiekursen för att skapa en händelsehubb, starta händelsen generator programmet och skapa ett Azure Stream Analytics (hoppa över stegen för att skapa frågan och utdata, i stället ska du konfigurera en Azure Functions utdata i nästa avsnitt.)

## <a name="create-an-azure-redis-cache"></a>Skapa en Azure Redis Cache

1. Skapa ett Azure Redis-Cache med hjälp av stegen som beskrivs i [skapa ett cacheminne](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) i Redis-Cache-artikeln.  

2. När Redis-Cache skapas, går du till skapade cachen > **åtkomstnycklar** > och anteckna den **primära anslutningssträngen**.

   ![Redis-Cache-anslutningssträng](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-an-azure-function-that-can-write-data-to-the-redis-cache"></a>Skapa en Azure-funktion som kan skriva data till Redis-Cache

1. Använd den [skapa en funktionsapp](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) i Azure Functions-dokumentationen för att skapa en App för Azure-funktionen och en [HTTP-utlösta Azure-funktion](../azure-functions/functions-create-first-azure-function.md#create-function) (även kallat Webhook) med hjälp av **CSharp** språk.  

2. Navigera till den **run.csx** fungerar och uppdatera den med följande kod (Ersätt den ”\<anslutningssträngen redis-cache här\>” text med primära anslutningssträngen för Redis-Cache som du hämtade i föregående avsnitt):  

   ```c#
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
}    

   ```

   När Azure Stream Analytics tar emot 413 (http-begäran entiteten för stort) undantag från Azure-funktion, minskar storleken på batchar skickar den till Azure Functions. I din Azure-funktionen att använda följande kod för att kontrollera att Azure Stream Analytics inte skicka stora batchar. Se till att de maximala batch antal och storlek värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics-portalen.

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Skapa en JSON-fil med namnet i en textredigerare önskat **project.json** med följande kod och spara den på den lokala datorn. Den här filen innehåller NuGet paketberoenden som krävs av c#-funktionen.  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. Gå tillbaka till Azure-portalen > Gå till din Azure-funktion > från den **plattformsfunktioner** fliken > klickar du på **App Service Editor** som finns under **utvecklingsverktyg**. 
 
   ![App service editor skärmen](./media/stream-analytics-with-azure-functions/image3.png)

5. I App Service Editor högerklickar du på din rotkatalog och ladda upp den **project.json** fil. När överföringen är klar, uppdatera sidan, du bör nu se en automatiskt genererade-fil som heter **project.lock.json**.  Filen automatiskt genererade innehåller referenser till DLL-filer som anges i filen Project.json.  

   ![Överför project.json-fil](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytic-job-with-azure-function-as-output"></a>Uppdatera dataströmmen analytiska jobbet med Azure-funktion som utdata

1. Öppna din Azure Stream Analytics-jobb på Azure-portalen.  

2. Navigera till din Azure-funktion > **översikt** > **utdata** > **Lägg till** nya utdata > Välj **Azure-funktion** för Sink-alternativet. Det nya Azure-funktion utdata kortet är tillgänglig med följande egenskaper:  

   |**Egenskapsnamn**|**Beskrivning**|
   |---|---|
   |Kolumnalias| Ett användarvänligt namn som du använder i jobbfråga till referens utdata. |
   |Importera alternativet| Du kan använda azure funktionen från aktuell prenumeration eller ange inställningarna manuellt om funktionen finns i andra prenumerationer. |
   |Funktionsapp| Namnet på din Funktionsapp i Azure. |
   |Funktion| Namnet på funktionen i din App (namnet på din run.csx funktion) för funktionen.|
   |Maximal batchstorlek|Den här egenskapen används för att ange den maximala storleken för varje batch för utdata som skickas till din Azure-funktion. Det här värdet är som standard, 256 KB.|
   |Maxantal för Batch|Den här egenskapen kan du ange det maximala antalet händelser i varje batch som skickas till Azure-funktion. Värdet för antal för högsta batch är 100. Den här egenskapen är valfri.|
   |Nyckel|Den här egenskapen kan du använda en Azure-funktion från en annan prenumeration. Ange värdet för nyckeln för att komma åt din funktion. Den här egenskapen är valfri.|

3. Ange ett namn för utdataalias. I den här självstudiekursen kommer vi namnet **saop1** (du kan använda andra namnet på ditt val) och Fyll i annan information.  

4. Öppna Stream Analytics-jobbet och uppdatera frågan till följande (se till att ersätta texten ”saop1” om du har gett utdatamottagaren annorlunda):  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Starta programmet telcodatagen.exe genom att köra följande kommando på kommandoraden (kommandot tar format - `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`)  
   
   **telcodatagen.exe 1000.2 2**
    
6.  Starta Stream Analytics-jobbet.

## <a name="check-redis-cache-for-results"></a>Kontrollera Redis-Cache för resultat

1. Navigera till den Azure-portalen och hitta Redis-Cache > Välj **konsolen**.  

2. Använd [Redis cache kommandon](https://redis.io/commands) att kontrollera att dina data är i Redis-cache. (Kommandot tar format-Get {nyckeln}). Exempel:

   **Hämta ”12/19/2017 21:32:24-123414732”**

   Det här kommandot ska skrivas ut värdet för den angivna nyckeln:

   ![Redis-Cache-utdata](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Kända problem

* I Azure-portalen när du försöker återställa Max Batch-storlek / Max Batchantal värde till empty(default), ändras tillbaka till det tidigare angivna värdet på Spara. Avsiktligt ange standardvärden för de här fälten i det här fallet.

## <a name="next-steps"></a>Nästa steg
