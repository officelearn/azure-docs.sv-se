---
title: "Kör Azure Functions med Azure Stream Analytics-jobb | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Functions som utdatamottagare till Stream Analytics-jobb."
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
ms.openlocfilehash: ab095827dc9dbfee19284abfbac353b16d3239a7
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Kör Azure Functions med Azure Stream Analytics-jobb 
 
> [!IMPORTANT]
> Den här funktionen är i förhandsgranskningen.

Du kan köra Azure Functions med Azure Stream Analytics genom att konfigurera funktioner som en av utdata sänkor till Stream Analytics-jobbet. Functions är en händelsedriven, beräkning på begäran-upplevelse som gör att du kan implementera kod som utlöses av händelser i Azure eller tjänster från tredje part. Den här möjligheten för funktioner att svara på utlösare gör det fysiska utdata till Stream Analytics-jobb.

Stream Analytics anropar funktioner via HTTP-utlösare. Utdataadapter funktioner tillåter användare att ansluta funktioner till Stream Analytics, så att händelserna kan utlösas baserat på Stream Analytics-frågor. 

Den här kursen visar hur du ansluter Stream Analytics till [Azure Redis-Cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md), med hjälp av [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurera ett Stream Analytics-jobb för att köra en funktion 

Det här avsnittet beskrivs hur du konfigurerar en Stream Analytics-jobbet om du vill köra en funktion som skriver data till Azure Redis-Cache. Stream Analytics-jobbet läser händelser från Azure Event Hubs och kör en fråga som anropar funktionen. Den här funktionen läser data från Stream Analytics-jobbet och skriver den till Azure Redis-Cache.

![Diagram som visar relationerna mellan Azure-tjänster](./media/stream-analytics-with-azure-functions/image1.png)

Följande steg krävs för att uppnå den här uppgiften:
* [Skapa ett Stream Analytics-jobb med Händelsehubbar som indata](#create-stream-analytics-job-with-event-hub-as-input)  
* [Skapa en instans av Azure Redis-Cache](#create-an-azure-redis-cache)  
* [Skapa en funktion i Azure-funktioner som kan skriva data till Azure Redis-Cache](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Uppdatera Stream Analytics-jobb med hjälp av funktionen som utdata](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Kontrollera Azure Redis-Cache för resultat](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Skapa ett Stream Analytics-jobb med Händelsehubbar som indata

Följ den [att upptäcka bedrägerier realtid](stream-analytics-real-time-fraud-detection.md) självstudiekursen för att skapa en händelsehubb, starta händelsen generator programmet och skapa ett Stream Analytics-jobb. (Hoppa över steg för att skapa frågan och utdata. Läs följande avsnitt för att konfigurera funktioner utdata.)

## <a name="create-an-azure-redis-cache-instance"></a>Skapa en instans av Azure Redis-Cache

1. Skapa en cache i Azure Redis-Cache med hjälp av stegen som beskrivs i [skapa ett cacheminne](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. När du har skapat cachen under **inställningar**väljer **åtkomstnycklar**. Anteckna den **primära anslutningssträngen**.

   ![Skärmbild av Azure Redis-Cache-anslutningssträng](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Skapa en funktion i Azure-funktioner som kan skriva data till Azure Redis-Cache

1. Finns det [skapa en funktionsapp](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) i dokumentationen för funktioner. Detta vägleder dig igenom hur du skapar en funktionsapp och en [HTTP-utlösta funktion i Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function), med hjälp av CSharp-språk.  

2. Bläddra till den **run.csx** funktion. Uppdatera den med följande kod. (Ersätt ”\<anslutningssträngen redis-cache här\>” med den primära Azure Redis-Cache-anslutningssträng som du hämtade i föregående avsnitt.)  

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

   När Stream Analytics tar emot ”HTTP-begäran entiteten för stor” undantag från funktionen, minskar storleken på batchar som skickas till funktioner. I din funktion att använda följande kod för att kontrollera att Stream Analytics inte skicka stora batchar. Se till att de maximala batch antal och storlek värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics-portalen.

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Skapa en JSON-fil med namnet i en textredigerare önskat **project.json**. Använd följande kod och spara den på den lokala datorn. Den här filen innehåller NuGet paketberoenden som krävs av C#-funktionen.  
   
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
 
4. Gå tillbaka till Azure-portalen. Från den **plattformsfunktioner** Bläddra till funktionen. Under **utvecklingsverktyg**väljer **App Service Editor**. 
 
   ![Skärmbild av App Service Editor](./media/stream-analytics-with-azure-functions/image3.png)

5. I App Service redigeraren, högerklicka på din rotkatalog och ladda upp den **project.json** fil. När överföringen är klar, uppdatera sidan. Du bör nu se en automatiskt genererade-fil som heter **project.lock.json**. Filen automatiskt genererade innehåller referenser till DLL-filer som anges i filen project.json.  

   ![Skärmbild av App Service Editor](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Uppdatera Stream Analytics-jobb med hjälp av funktionen som utdata

1. Öppna Stream Analytics-jobb på Azure-portalen.  

2. Bläddra till din funktion och markera **översikt** > **utdata** > **Lägg till**. Om du vill lägga till en ny utdata, Välj **Azure-funktion** för sink-alternativet. Kortet för utdata av nya funktioner är tillgängliga med följande egenskaper:  

   |**Egenskapsnamn**|**Beskrivning**|
   |---|---|
   |Kolumnalias| Ett användarvänligt namn som du använder i jobbfråga till referens utdata. |
   |Importera alternativet| Du kan använda funktionen från aktuell prenumeration, eller ange inställningarna manuellt om funktionen finns i en annan prenumeration. |
   |Funktionsapp| Namnet på appen funktioner. |
   |Funktion| Namnet på funktionen i appen funktioner (namnet på din run.csx funktion).|
   |Maximal batchstorlek|Anger den maximala storleken för varje batch för utdata som skickas till funktionen. Det här värdet är som standard, 256 KB.|
   |Maxantal för Batch|Anger det maximala antalet händelser i varje batch som skickas till funktionen. Standardvärdet är 100. Den här egenskapen är valfri.|
   |Nyckel|Kan du använda en funktion från en annan prenumeration. Ange värdet för nyckeln för att komma åt din funktion. Den här egenskapen är valfri.|

3. Ange ett namn för utdataalias. I den här självstudiekursen kommer vi namnet **saop1** (du kan använda alla valfritt namn). Fyll i annan information.  

4. Öppna Stream Analytics-jobbet och uppdatera frågan till följande. (Se till att ersätta texten ”saop1” om du har gett utdatamottagaren på olika sätt.)  

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

5. Starta programmet telcodatagen.exe genom att köra följande kommando på kommandoraden (Använd formatet `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000.2 2**
    
6.  Starta Stream Analytics-jobbet.

## <a name="check-azure-redis-cache-for-results"></a>Kontrollera Azure Redis-Cache för resultat

1. Bläddra till den Azure-portalen och hitta din Azure Redis-Cache. Välj **konsolen**.  

2. Använd [Redis cache kommandon](https://redis.io/commands) att kontrollera att dina data är i Redis-cache. (Har formatet för kommandot Get {nyckeln}.) Exempel:

   **Hämta ”12/19/2017 21:32:24-123414732”**

   Det här kommandot ska skrivas ut värdet för den angivna nyckeln:

   ![Skärmbild av Azure Redis-Cache-utdata](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Kända problem

I Azure-portalen när du försöker återställa Max Batch-storlek / Max Batchantal värde till tomt (standard), värdet ändras tillbaka till det tidigare angivna värdet på Spara. Manuellt ange standardvärden för de här fälten i det här fallet.

