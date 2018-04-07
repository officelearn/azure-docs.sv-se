---
title: Realtid Twitter-sentiment analys med Azure Stream Analytics
description: Den här artikeln beskriver hur du använder Stream Analytics för analys av realtidsskyddet Twitter-sentiment. Stegvisa anvisningar från händelse genereras till data på en levande instrumentpanel.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 4c77c8a7209825477929ddc0997b75ace5fe04a1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Realtid Twitter-sentiment analys i Azure Stream Analytics

Lär dig hur du skapar en lösning för analys av sentiment för analys av sociala medier genom att ta realtid Twitter-händelser i Händelsehubbar i Azure. Du kan sedan skriva en Azure Stream Analytics-fråga för att analysera data och antingen lagra resultaten för senare använda eller använda en instrumentpanel och [Power BI](https://powerbi.com/) till ger inblick i realtid.

Verktyg för sociala media analytics hjälper företag att förstå trender avsnitt. Trender avsnitt är ämnen och attityder som har en stor volym med inlägg i sociala medier. Sentiment analys, som också kallas *åsikt utvinningsmodellen*, använder sociala media analytics verktyg för att avgöra attityder mot en produkt, idé och så vidare. 

Trendanalys för realtid Twitter är ett bra exempel på ett webbanalysverktyg för eftersom hashtaggar prenumeration modellen gör det möjligt att lyssna på specifika nyckelord (hash-taggar) och utveckla sentiment analys av feeden.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: Sociala medier sentiment analys i realtid

Ett företag som har en webbplats för Nyheter media är intresserad av att få flera fördelar jämfört med dess konkurrenter med innehåll som är direkt relevant för dess läsare. Företaget använder analys av sociala medier på ämnen som är relevanta för läsare genom att göra realtid sentiment analys av Twitter-data.

Företaget måste analys i realtid om tweet volym och sentiment för viktiga ämnen för att identifiera trender avsnitt i realtid på Twitter. Med andra ord är behovet av en sentiment analys analytics-motor som är baserad på den här sociala medier feed.

## <a name="prerequisites"></a>Förutsättningar
I den här kursen använder du ett klientprogram som ansluter till Twitter och söker efter tweets som har vissa hash-taggar (som du kan ange). För att köra programmet och analysera tweets med hjälp av Azure Streaming Analytics, måste du ha följande:

* En Azure-prenumeration
* Ett Twitter-konto 
* Ett Twitter-program och [OAuth-åtkomsttoken](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) för programmet. Vi ger avancerade anvisningar för hur du skapar en Twitter-programmet senare.
* TwitterWPFClient-program som läser Twitter-flöde. För att få det här programmet kan hämta den [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) från GitHub och packa upp paketet till en mapp på datorn. Om du vill visa källan code och kör programmet i en felsökare kan du få källkod från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Skapa en händelsehubb för Streaming Analytics indata

Exempelprogrammet genererar händelser och skickar dem till en Azure-händelsehubb. Azure event hubs är den bästa metoden för händelsen införandet för Stream Analytics. Mer information finns i [Händelsehubbar i Azure-dokumentationen](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Skapa en event hub namnområde och händelsehubb
I den här proceduren du först skapa en event hub-namnområde och sedan lägga till en händelsehubb i detta namnområde. Event hub namnområden används för att gruppera relaterade händelser-bussen instanser. 

1. Logga in på Azure portal och klicka på **skapar du en resurs** > **Sakernas Internet** > **Händelsehubb**. 

2. I den **skapa namnområdet** bladet, ange ett namn för namnområdet som `<yourname>-socialtwitter-eh-ns`. Du kan använda valfritt namn för namnområdet, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
3. Välj en prenumeration och skapa eller välja en resursgrupp och sedan klicka på **skapa**. 

    ![Skapa ett namnområde för event hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. När namnområdet distribution har slutförts, kan du hitta event hub namnområdet i din lista över Azure-resurser. 

5. Klicka på det nya namnområdet och i bladet namnområdet på  **+ &nbsp;Händelsehubb**. 

    ![Knappen Lägg till Händelsehubben för att skapa en ny händelsehubb ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Namn på ny händelsehubb `socialtwitter-eh`. Du kan använda ett annat namn. Om du vill anteckna, eftersom du måste ha namnet senare. Du behöver inte ange andra alternativ för händelsehubben.

    ![Bladet för att skapa en ny händelsehubb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klicka på **Skapa**.


### <a name="grant-access-to-the-event-hub"></a>Bevilja åtkomst till händelsehubben

Innan en process kan skicka data till en händelsehubb, måste händelsehubben ha en princip som tillåter åtkomstbehörighet. Åtkomstprincipen producerar en anslutningssträng som innehåller auktoriseringsinformation om.

1.  I bladet händelse namnområde klickar du på **Händelsehubbar** och klicka sedan på namnet på den nya händelsehubben.

2.  Klicka i hubbladet händelse **principer för delad åtkomst** och klicka sedan på  **+ &nbsp;Lägg till**.

    >[!NOTE]
    >Kontrollera att du arbetar med händelsehubb, inte event hub namnområde.

3.  Lägg till en princip med namnet `socialtwitter-access` och **anspråk**väljer **hantera**.

    ![Bladet för att skapa en ny händelse hubb princip](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klicka på **Skapa**.

5.  När principen har distribuerats, klickar du på den i listan med principer för delad åtkomst.

6.  I listrutan **sträng-primära ANSLUTNINGSNYCKEL** och klicka på kopieringsknappen bredvid anslutningssträngen. 
    
    ![Kopiera den primära anslutning strängnyckeln från principen för åtkomst](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen för nästa avsnitt, när du har mindre ändringar.

    Anslutningssträngen ser ut så här:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observera att anslutningssträngen innehåller flera nyckel-värdepar, avgränsade med semikolon: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, och `EntityPath`.  

    > [!NOTE]
    > Delar av anslutningssträngen i exemplet för säkerhet, har tagits bort.

8.  I textredigeraren kan ta bort den `EntityPath` par från anslutningssträngen (Glöm inte att ta bort semikolonet som föregår den). När du är klar anslutningssträngen ser ut så här:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurera och starta klientprogrammet Twitter
Klientprogrammet får tweet händelser direkt från Twitter. För att göra så den behöver behörighet att anropa Twitter Streaming API: erna. Om du vill konfigurera behörigheten Skapa ett program i Twitter, vilket genererar unika autentiseringsuppgifter (t.ex en OAuth-token). Du kan sedan konfigurera klientprogrammet använder dessa autentiseringsuppgifter när den gör API-anrop. 

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du inte redan har ett Twitter-program som du kan använda för den här kursen kan skapa du en. Du måste redan ha ett Twitter-konto.

> [!NOTE]
> Exakt processen i Twitter för att skapa ett program och hämta de nycklar och hemligheter token kan ändras. Om dessa instruktioner inte matchar det som visas på Twitter-plats finns i dokumentationen för Twitter-utvecklare.

1. Gå till den [sidan Twitter programhantering](https://apps.twitter.com/). 

2. Skapa ett nytt program. 

    * Ange en giltig URL för webbplats-URL. Det behöver inte vara en live-webbplats. (Du kan inte ange bara `localhost`.)
    * Lämna fältet återanrop tomt. Klientprogrammet som du använder för den här kursen kräver inte återanrop.

    ![Skapa ett program i Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Du kan också ändra programmets behörigheter till skrivskyddat läge.

4. Gå till när programmet har skapats kan den **nycklar och åtkomst-token** sidan.

5. Klicka på knappen för att generera en åtkomst-token och åtkomst-token hemlighet.

Behåll den här informationen praktisk, eftersom du behöver i nästa procedur.

>[!NOTE]
>Nycklar och hemligheter för programmets Twitter ger åtkomst till ditt Twitter-konto. Behandla den här informationen som känsliga på samma sätt som du gör ditt Twitter-lösenord. Till exempel Bädda inte in informationen i ett program som du ger. 


### <a name="configure-the-client-application"></a>Konfigurera klientprogrammet
Vi har skapat ett klientprogram som ansluter till Twitter data med hjälp av [Twitter's Streaming API: er](https://dev.twitter.com/streaming/overview) att samla in händelser för tweet om en specifik uppsättning avsnitt. Programmet använder den [Sentiment140](http://help.sentiment140.com/) öppen källkod verktyg som tilldelas varje tweet sentiment följande värde:

* 0 = negativt
* 2 = neutral
* 4 = positivt

När tweet händelser har tilldelats ett värde för sentiment, är de pushas till händelsehubben som du skapade tidigare.

Innan programmet körs, kräver vissa information från dig, t.ex. Twitter-nycklar och anslutningssträngen event hub. Du kan ange konfigurationsinformation på följande sätt:

* Kör programmet och sedan använda programmets användargränssnitt för att ange de nycklar och hemligheter anslutningssträngen. Om du gör detta konfigurationsinformationen används för den aktuella sessionen, men sparas inte.
* Redigera programmets .config-fil och ange värden för det. Den här metoden kvarstår konfigurationsinformation, men det innebär också att potentiellt känslig information lagras i klartext på datorn.

Följande procedur dokument båda metoderna. 

1. Kontrollera att du har hämtat och uppackade den [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) program enligt kraven.

2. Ange värden vid körning (och endast för den aktuella sessionen) kör den `TwitterWPFClient.exe` program. När du uppmanas av programmet kan du ange följande värden:

    * Twitter konsumentnyckel (API-nyckel).
    * Twitter konsumenten hemligheten (hemliga API).
    * Twitter-åtkomsttoken.
    * Twitter åtkomst-Token hemligheten.
    * Informationen i anslutningssträngen som du sparade tidigare. Kontrollera att du använder anslutningssträngen som du har tagit bort den `EntityPath` nyckel / värde-par från.
    * Twitter-nyckelord som du vill fastställa sentiment för.

   ![TwitterWpfClient programmet körs, visar dolda inställningarna](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Använd en textredigerare för att öppna filen TwitterWpfClient.exe.config för att ange värden för beständigt. I den `<appSettings>` element, göra detta:

    * Ange `oauth_consumer_key` till Twitter konsumentnyckel (API-nyckel). 
    * Ange `oauth_consumer_secret` till Twitter konsumenten hemligheten (hemliga API).
    * Ange `oauth_token` till Twitter-åtkomsttoken.
    * Ange `oauth_token_secret` till Twitter åtkomst-Token hemlighet.

    Senare i den `<appSettings>` element, göra dessa ändringar:

    * Ange `EventHubName` till händelsehubbens namn (det vill säga till värdet för entiteten sökväg).
    * Ange `EventHubNameConnectionString` i anslutningssträngen. Kontrollera att du använder anslutningssträngen som du har tagit bort den `EntityPath` nyckel / värde-par från.

    Den `<appSettings>` avsnitt ser ut som följande exempel. (För tydlighetens skull och säkerhet, vi omsluten några rader och ta bort vissa tecken.)

    ![TwitterWpfClient programkonfigurationsfilen i en textredigerare, visar Twitter-nycklar och hemligheter och event hub informationen i anslutningssträngen](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Om du inte redan startar programmet kan köra TwitterWpfClient.exe. 

5. Klicka på den gröna start-knappen för att samla in sociala sentiment. Du ser Tweet händelser med den **CreatedAt**, **avsnittet**, och **SentimentScore** värden som skickas till din event hub.

    ![TwitterWpfClient program som körs, visar en lista över tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Om du får felmeddelanden och du inte ser en dataström med tweets som visas i den nedre delen av fönstret, kontrollera nycklar och hemligheter. Kontrollera också anslutningssträngen (se till att den inte innehåller den `EntityPath` nyckel och värde.)


## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu när tweet händelser strömning i realtid från Twitter, kan du ställa in ett Stream Analytics-jobb att analysera dessa händelser i realtid.

1. I Azure-portalen klickar du på **skapar du en resurs** > **Sakernas Internet** > **Stream Analytics-jobbet**.

2. Namnge jobbet `socialtwitter-sa-job` och ange en prenumeration, resursgrupp och plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betala för att överföra data mellan regioner.

    ![Skapar ett nytt Stream Analytics-jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klicka på **Skapa**.

    Jobbet har skapats och portalen visar jobbinformation.


## <a name="specify-the-job-input"></a>Ange indata för jobb

1. I Stream Analytics-jobbet under **jobbet topologi** mitt i jobb-bladet klickar du på **indata**. 

2. I den **indata** bladet, klickar du på  **+ &nbsp;Lägg till** och fyll sedan i bladet med dessa värden:

    * **Ett inmatat alias**: Använd namnet `TwitterStream`. Om du använder ett annat namn, notera den eftersom du behöver den senare.
    * **Typ av datakälla**: Välj **dataströmmen**.
    * **Källan**: Välj **händelsehubb**.
    * **Importera alternativet**: Välj **Använd händelsehubb från aktuell prenumeration**. 
    * **Service bus-namnrymd**: Välj händelsen hubb namnområdet som du skapade tidigare (`<yourname>-socialtwitter-eh-ns`).
    * **Händelsehubb**: Välj händelsehubben som du skapade tidigare (`socialtwitter-eh`).
    * **Namnet på händelsehubben princip**: Välj den åtkomstprincip som du skapade tidigare (`socialtwitter-access`).

    ![Skapa nya indata för Streaming Analytics-jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klicka på **Skapa**.


## <a name="specify-the-job-query"></a>Ange frågan som jobb

Stream Analytics stöder en enkel, deklarativ frågemodell som beskriver transformationer. Mer information om språk finns i [referens för Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Den här kursen hjälper dig att skapa och testa flera frågor via Twitter-data.

Om du vill jämföra antalet nämns mellan ämnen som du kan använda en [rullande fönster](https://msdn.microsoft.com/library/azure/dn835055.aspx) att hämta antalet nämns i avsnittet var femte sekund.

1. Stäng den **indata** bladet om du inte redan har gjort.

2. I jobb-bladet klickar du på den **frågan** rutan. Azure visar in- och utgångar som är konfigurerade för jobbet och kan du skapa en fråga som kan du omvandla Indataströmmen som skickas till utdata.

3. Kontrollera att programmet TwitterWpfClient körs. 

3. I den **frågan** bladet, klickar du på punkter bredvid den `TwitterStream` indata och välj sedan **exempeldata från indata**.

    ![Menyn Alternativ exempeldata för posten Streaming Analytics-jobb med ”exempeldata från indata” markerad](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Då öppnas ett blad som du kan ange hur mycket exempeldata som kan definieras enligt hur lång tid för att läsa Indataströmmen.

4. Ange **minuter** till 3 och klicka sedan på **OK**. 
    
    ![Alternativ för provtagning Indataströmmen, med ”3 minuter” markerad.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure exempel 3 minuter kan du se från Indataströmmen och meddelar dig när exempeldata är klar. (Detta tar en stund.) 

    Exempeldata lagras tillfälligt och är tillgänglig när du har frågefönster öppnas. Om du stänger frågefönstret exempeldata ignoreras och du måste skapa en ny uppsättning exempeldata. 

5. Ändra frågan i kodredigeraren för till följande:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Om inte `TwitterStream` som alias för indata ersätta ditt alias för `TwitterStream` i frågan.  

    Den här frågan använder den **TIMESTAMP BY** nyckelord för att ange något tidsstämpelsfält i nyttolasten som ska användas i den temporala beräkningen. Om det här fältet har inte angetts, utförs fönsterhantering åtgärden med hjälp av den tid som varje händelse som anlänt på händelsehubben. Läs mer i avsnittet ”ankomsttid vs programmet tid” i [referens för Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Den här frågan använder också en tidsstämpel för slutet av varje fönster med hjälp av den **System.Timestamp** egenskapen.

5. Klicka på **Test**. Frågan körs mot de data som du tog prov.
    
6. Klicka på **Spara**. Frågan sparas som en del av Streaming Analytics-jobbet. (Den inte spara exempeldata.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentera med olika fält från dataströmmen 

I följande tabell visas de fält som är en del av JSON strömmande data. Du kan experimentera i frågeredigeraren.

|JSON-egenskap | Definition|
|--- | ---|
|CreatedAt | Den tid som tweet skapades|
|Avsnitt | Avsnittet som matchar det angivna nyckelordet|
|SentimentScore | Sentiment poäng från Sentiment140|
|Skapa | Twitter-referensen som skickats av tweet|
|Text | Själva tweet|


## <a name="create-an-output-sink"></a>Skapa utdatamottagare

Du har nu definierat en händelseström, en händelsehubb som indata för att mata in händelser och en fråga för att genomföra en omvandling över dataströmmen. Det sista steget är att definiera utdatamottagare för jobbet.  

I den här självstudiekursen skriva aggregerade tweet händelser från jobbet frågan till Azure Blob storage.  Du kan också push-resultaten till Azure SQL Database, Azure Table storage Event Hubs eller Power BI, beroende på ditt program behöver.

## <a name="specify-the-job-output"></a>Ange jobbutdata

1. I den **jobbet topologi** klickar du på den **utdata** rutan. 

2. I den **utdata** bladet, klickar du på  **+ &nbsp;Lägg till** och fyll sedan i bladet med dessa värden:

    * **Ett utdataalias**: Använd namnet `TwitterStream-Output`. 
    * **Sink**: Välj **Blob storage**.
    * **Importalternativ**: Välj **använda blob storage från aktuell prenumeration**.
    * **Lagringskontot**. Välj **skapa ett nytt lagringskonto.**
    * **Lagringskontot** (andra rutan). Ange `YOURNAMEsa`, där `YOURNAME` är ditt namn eller en annan unik sträng. Namnet kan använda bara gemena bokstäver och siffror, och den måste vara unikt i Azure. 
    * **Behållaren**. Ange `socialtwitter`.
    Lagringskontonamn och behållarnamn används tillsammans för att ge en URI för blobblagring, så här: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![”Nya utdata” bladet för Stream Analytics-jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klicka på **Skapa**. 

    Azure skapar lagringskontot och skapar automatiskt en nyckel. 

5. Stäng den **utdata** bladet. 


## <a name="start-the-job"></a>Starta jobbet

Jobbet indata-, fråge- och utdata har angetts. Du är redo att börja Stream Analytics-jobbet.

1. Kontrollera att programmet TwitterWpfClient körs. 

2. I jobb-bladet klickar du på **starta**.

    ![Starta Stream Analytics-jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. I den **startjobb** bladet för **jobbutdata starttid**väljer **nu** och klicka sedan på **starta**. 

    ![”Starta jobbet” bladet för Stream Analytics-jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure meddelar dig när jobbet har startat och i bladet jobb visas status som **kör**.

    ![Köra jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Visa utdata för sentiment analys

När jobbet har startats och bearbetas i realtid Twitter-dataströmmen kan visa du utdata för sentiment analys.

Du kan använda ett verktyg som [Azure Lagringsutforskaren](https://http://storageexplorer.com/) eller [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) att visa din jobbutdata i realtid. Härifrån kan du använda [Power BI](https://powerbi.com/) att utöka ditt program att inkludera en anpassad instrumentpanel som det visas i följande skärmbild:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Skapa en annan fråga för att identifiera trender avsnitt

En annan fråga som du kan använda för att förstå Twitter-sentiment baseras på en [glidande fönstret](https://msdn.microsoft.com/library/azure/dn835051.aspx). För att identifiera trender avsnitt kan söka du efter avsnitt som går via ett tröskelvärde för nämns i en angiven tidsperiod.

Vid tillämpningen av den här kursen kan du söka efter avsnitt som nämns fler än 20 gånger i de senaste 5 sekunderna.

1. I jobb-bladet klickar du på **stoppa** att stoppa jobbet. 

2. I den **jobbet topologi** klickar du på den **frågan** rutan. 

3. Ändra frågan till följande:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klicka på **Spara**.

5. Kontrollera att programmet TwitterWpfClient körs. 

6. Klicka på **starta** att starta om jobbet med den nya frågan.


## <a name="get-support"></a>Få support
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
