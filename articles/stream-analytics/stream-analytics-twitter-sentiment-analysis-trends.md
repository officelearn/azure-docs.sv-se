---
title: I realtid Twitter-attitydanalys med Azure Stream Analytics
description: Den här artikeln beskriver hur du använder Stream Analytics för attitydanalys för Twitter i realtid. Stegvisa anvisningar från händelsegenerering till data på en live-instrumentpanel.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: abb2a89f41340e8e2e26fa36cc20b790341618d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60763365"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>I realtid Twitter-attitydanalys i Azure Stream Analytics

> [!IMPORTANT] 
> Skapa för Twitter-program inte längre är tillgänglig via [apps.twitter.com](https://apps.twitter.com/). Den här självstudiekursen är håller på att uppdateras för att inkludera den nya Twitter-API.

Lär dig hur du skapar en lösning för sentiment för analys av sociala medier genom att föra händelser i realtid Twitter till Azure Event Hubs. Du kan sedan skriva en Azure Stream Analytics-fråga för att analysera data och antingen lagrar resultatet för senare använda eller använda en instrumentpanel och [Power BI](https://powerbi.com/) att ge insikter i realtid.

Analysverktyg för sociala medier hjälpa företag att förstå populära ämnen. Populära ämnen är ämnen och attityder som har ett stort antal inlägg i sociala medier. Attitydanalys, även kallat *åsikt utvinning*, använder sociala media analytics-verktyg för att avgöra attityder mot en produkt, idé och så vidare. 

Trendanalys för Twitter i realtid är ett bra exempel på ett analysverktyg, eftersom prenumerationsmodell hashtagg kan du lyssna på specifika nyckelord (hashtaggar) och utveckla känsloanalys av flödet.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: Analys av sociala medier åsikter i realtid

Ett företag som har en nyhetsmedier webbplats är intresserad av att få en fördel jämfört med konkurrenterna med webbplatsens innehåll som är relevant omedelbart till dess läsare. Företaget använder analys av sociala medier om ämnen som är relevanta för läsare genom att göra känsloanalys av Twitter-data i realtid.

Företaget måste analys i realtid om tweet volym och sentiment för viktiga ämnen för att identifiera populära ämnen i realtid på Twitter. Med andra ord är behovet av en sentiment analysis-Analysmotorn som baseras på den här sociala medier feed.

## <a name="prerequisites"></a>Nödvändiga komponenter
I den här självstudien använder du ett klientprogram som ansluter till Twitter och söker efter tweets som innehåller vissa hashtaggar (som du kan ställa in). För att köra programmet och analysera tweets med hjälp av Azure Streaming Analytics, måste du ha följande:

* En Azure-prenumeration
* Ett Twitter-konto 
* Ett Twitter-program och [OAuth-åtkomsttoken](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) för programmet. Vi tillhandahåller övergripande instruktioner för hur du skapar en Twitter-programmet senare.
* TwitterWPFClient programmet, vilket läser Twitter-flöde. För att få det här programmet kan hämta den [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) från GitHub och packa upp paketet till en mapp på datorn. Om du vill se source code och kör programmet med en felsökare, kan du hämta källkoden från [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Skapa en händelsehubb för Streaming Analytics-indata

Exempelprogrammet som genererar händelser och skickar dem till en Azure-händelsehubb. Azure event hubs är den bästa metoden för händelsepåfyllning för Stream Analytics. Mer information finns i den [dokumentation om Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Skapa ett namnområde för händelsehubb och en händelsehubb
I den här proceduren måste du först skapa ett händelsehubbnamnområde och sedan du lägga till en händelsehubb i namnområdet. Händelsehubbnamnområden som används för att gruppera relaterade event bus instanser. 

1. Logga in på Azure-portalen och klicka på **skapa en resurs** > **Internet of Things** > **Event Hub**. 

2. I den **skapa namnområde** bladet, ange ett namn för namnområdet som `<yourname>-socialtwitter-eh-ns`. Du kan använda ett namn för namnområdet, men namnet måste vara giltig för en URL och det måste vara unikt i Azure. 
    
3. Välj en prenumeration och skapa eller välj en resursgrupp och sedan klicka på **skapa**. 

    ![Skapa ett namnområde för en händelsehubb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. När namnområdet har slutfört distributionen, hitta händelsehubbens namnområde i din lista över Azure-resurser. 

5. Klicka på det nya namnområdet och i namnområdesbladet klickar du på  **+ &nbsp;Event Hub**. 

    ![Lägg till Event Hub-knappen för att skapa en ny händelsehubb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Namnge den nya händelsehubben `socialtwitter-eh`. Du kan använda ett annat namn. Om du gör notera av det, eftersom du behöver namnet senare. Du behöver inte ange några andra alternativ för event hub.

    ![Bladet för att skapa en ny händelsehubb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klicka på **Skapa**.


### <a name="grant-access-to-the-event-hub"></a>Bevilja åtkomst till händelsehubben

Innan en process kan skicka data till en händelsehubb, måste event hub ha en princip som tillåter lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1.  På namnområdesbladet händelse klickar du på **Händelsehubbar** och klicka sedan på namnet på din nya event hub.

2.  I event hub-blad klickar du på **principer för delad åtkomst** och klicka sedan på  **+ &nbsp;Lägg till**.

    >[!NOTE]
    >Kontrollera att du arbetar med event hub inte händelsehubbens namnområde.

3.  Lägg till en princip med namnet `socialtwitter-access` och för **anspråk**väljer **hantera**.

    ![Bladet för att skapa en ny åtkomstprincip för event hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klicka på **Skapa**.

5.  När principen har distribuerats, klickar du på den i listan över principer för delad åtkomst.

6.  I listrutan **ANSLUTNINGSSTRÄNG – primär nyckel** och klicka på kopieringsknappen bredvid anslutningssträngen. 
    
    ![Kopiera den primära anslutning strängnyckeln från åtkomstprincipen](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen för nästa avsnitt när du har gjort mindre ändringar till den.

    Anslutningssträngen ser ut så här:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observera att anslutningssträngen innehåller flera nyckel / värde-par, avgränsade med semikolon: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, och `EntityPath`.  

    > [!NOTE]
    > Delar av strängen som i exemplet för säkerhet, har tagits bort.

8.  I redigeraren, ta bort den `EntityPath` par från anslutningssträngen (Glöm inte att ta bort semikolonet som föregår det). När du är klar anslutningssträngen ser ut så här:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurera och starta Twitter-klientprogram
Klientprogrammet får tweet händelser direkt från Twitter. För att kunna göra det, måste den behörighet att anropa den Twitter Streaming API: er. Om du vill konfigurera behörighet, skapa ett program i Twitter, vilket genererar unika autentiseringsuppgifter (t.ex en OAuth-token). Du kan sedan konfigurera klientprogrammet använder dessa autentiseringsuppgifter när den gör API-anrop. 

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du inte redan har ett Twitter-program som du kan använda för den här självstudiekursen kan du skapa en. Du måste redan ha ett Twitter-konto.

> [!NOTE]
> Exakta processen i Twitter för att skapa ett program och komma på nycklar, hemligheter och token kan ändras. Om de här anvisningarna inte matchar vad som visas på webbplatsen Twitter, finns i dokumentationen för utvecklare av Twitter.

1. Gå till den [Twitter application management-sidan](https://apps.twitter.com/). 

2. Skapa ett nytt program. 

   * Ange en giltig URL för webbplats-URL. Det behöver inte vara en live-webbplats. (Du kan inte ange bara `localhost`.)
   * Lämna återanrop fältet tomt. Klientprogram som du använder för den här självstudien kräver inte återanrop.

     ![Skapa ett program i Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Du kan också ändra programmets behörigheter till skrivskyddat läge.

4. När programmet har skapats går du till den **nycklar och åtkomsttoken** sidan.

5. Klicka på knappen för att generera en åtkomst-token och åtkomst-token secret.

Behåll den här informationen till hands eftersom du behöver i nästa procedur.

>[!NOTE]
>Nycklar och hemligheter för Twitter-programmet ger åtkomst till ditt Twitter-konto. Hantera den här informationen som känsliga samma som du gör ditt Twitter-lösenord. Till exempel inte bädda in den här informationen i ett program som du ge. 


### <a name="configure-the-client-application"></a>Konfigurera klientprogrammet
Vi har skapat ett klientprogram som ansluter till Twitter-data med [Twitter's Streaming API: er](https://dev.twitter.com/streaming/overview) att samla in händelser för tweets om en specifik uppsättning ämnen. Programmet använder den [Sentiment140](http://help.sentiment140.com/) verktyg med öppen källkod, vilket innebär att varje tweet tilldelas följande sentiment värde:

* 0 = negativt
* 2 = neutral
* 4 = positivt

När tweet-händelser har tilldelats ett värde för sentiment, skickas de till händelsehubben som du skapade tidigare.

Innan programmet körs, kräver viss information från dig, som Twitter-nycklar och event hub-anslutningssträngen. Du kan ange konfigurationsinformation på följande sätt:

* Kör programmet och sedan använda programmets användargränssnitt för att ange nycklar, hemligheter och anslutningssträngen. Om du gör detta konfigurationsinformationen används för den aktuella sessionen, men den sparas inte.
* Redigera programmets .config-filen och ange värden för det. Den här metoden kvarstår konfigurationsinformation, men det innebär också att potentiellt känslig information lagras i klartext på datorn.

Följande procedur dokument båda metoderna. 

1. Kontrollera att du har hämtat och packat den [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) program, som anges i förutsättningarna.

2. Att ställa in värden under körning och endast för den aktuella sessionen, kör den `TwitterWPFClient.exe` program. När programmet ber om det, anger du följande värden:

    * Twitter använda nyckeln (API-nyckel).
    * Konsumenthemligheten Twitter (API-hemlighet).
    * Twitter-åtkomsttoken.
    * Den Twitter Åtkomsttokenhemligheten.
    * Informationen i anslutningssträngen som du sparade tidigare. Se till att du använder anslutningssträngen som du har tagit bort den `EntityPath` nyckel / värde-par från.
    * Twitter-nyckelord som du vill fastställa sentiment för.

   ![TwitterWpfClient program som körs, visa dolda inställningar](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Använd en textredigerare för att ange värden för ett beständigt sätt för att öppna filen TwitterWpfClient.exe.config. I den `<appSettings>` element, göra detta:

   * Ange `oauth_consumer_key` till Twitter konsumentnyckel (API-nyckel). 
   * Ange `oauth_consumer_secret` till Twitter-Konsumenthemligheten (API-hemlighet).
   * Ange `oauth_token` till Twitter-åtkomsttoken.
   * Ange `oauth_token_secret` till Twitter Åtkomsttokenhemligheten.

     Senare i den `<appSettings>` element, gör dessa ändringar:

   * Ange `EventHubName` till event hub-namn (det vill säga till värdet för entitetssökväg).
   * Ange `EventHubNameConnectionString` på anslutningssträngen. Se till att du använder anslutningssträngen som du har tagit bort den `EntityPath` nyckel / värde-par från.

     Den `<appSettings>` avsnittet ser ut som i följande exempel. (För tydlighetens skull och säkerhet, vi omslutna vissa rader och ta bort vissa tecken.)

     ![TwitterWpfClient programkonfigurationsfilen i en textredigerare, som visar Twitter-nycklar och hemligheter och informationen i anslutningssträngen i event hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Om du inte har redan startat programmet, kör du TwitterWpfClient.exe nu. 

5. Klicka på den gröna start-knappen för att samla in åsikter i sociala medier. Du ser Tweet händelser med den **CreatedAt**, **avsnittet**, och **SentimentScore** värden som skickas till din event hub.

    ![TwitterWpfClient program som körs, som visar en lista över tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Om du får felmeddelanden och du inte ser en dataström med tweets som visas i den nedre delen av fönstret, kontrollera nycklar och hemligheter. Även kontrollera anslutningssträngen (se till att den inte innehåller den `EntityPath` nyckel och värde.)


## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu när tweet händelser strömning i realtid från Twitter, kan du ställa in ett Stream Analytics-jobb att analysera dessa händelser i realtid.

1. I Azure-portalen klickar du på **skapa en resurs** > **Internet of Things** > **Stream Analytics-jobbet**.

2. Namnge jobbet `socialtwitter-sa-job` och ange en prenumeration, resursgrupp och plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte behöver betala att överföra data mellan regioner.

    ![Skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klicka på **Skapa**.

    Jobbet har skapats och i portalen visas jobbinformation.


## <a name="specify-the-job-input"></a>Ange jobbindata

1. I ditt Stream Analytics-jobb under **Jobbtopologi** mitt jobb-bladet klickar du på **indata**. 

2. I den **indata** bladet klickar du på  **+ &nbsp;Lägg till** och Fyll i bladet med följande värden:

   * **Indataalias**: Använd namnet `TwitterStream`. Om du använder ett annat namn, notera den eftersom du behöver den senare.
   * **Datakällans typ**: Välj **dataströmmen**.
   * **Källa**: Välj **händelsehubb**.
   * **Importalternativ**: Välj **Använd händelsehubb från aktuell prenumeration**. 
   * **Service bus-namnområde**: Välj händelsehubbens namnområde som du skapade tidigare (`<yourname>-socialtwitter-eh-ns`).
   * **Händelsehubb**: Välj händelsehubben som du skapade tidigare (`socialtwitter-eh`).
   * **Namn på händelsehubbsprincipen**: Välj den åtkomstprincip som du skapade tidigare (`socialtwitter-access`).

     ![Skapa nya indata för Streaming Analytics-jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klicka på **Skapa**.


## <a name="specify-the-job-query"></a>Ange frågan som jobb

Stream Analytics stöder en enkel, deklarativ frågemodell som beskriver transformationer. Mer information om språk finns i [Frågespråksreferens för Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Den här kursen hjälper dig att skapa och testa flera frågor över Twitter-data.

Om du vill jämföra antalet omnämnanden bland ämnen, som du kan använda en [rullande fönster](https://msdn.microsoft.com/library/azure/dn835055.aspx) att hämta antal omnämnanden i avsnittet var femte sekund.

1. Stäng den **indata** bladet om du inte redan har gjort.

2. I den **översikt** bladet klickar du på **Redigera fråga** överst höger om rutan fråga. Azure listar indata och utdata som är konfigurerade för jobbet, och kan du skapa en fråga som låter dig omvandla Indataströmmen som skickas till utdata.

3. Se till att TwitterWpfClient programmet körs. 

3. I den **fråga** bladet klickar du på punkterna bredvid den `TwitterStream` indata och väljer sedan **exempeldata från indata**.

    ![Menyn Alternativ för att använda exempeldata för posten Streaming Analytics-jobb med ”exempeldata från indata” valt](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Då öppnas ett blad där du kan ange hur mycket exempeldata för att få kan definieras när det gäller hur lång tid för att läsa Indataströmmen.

4. Ange **minuter** till 3 och klicka sedan på **OK**. 
    
    ![Alternativ för sampling Indataströmmen, med ”3 minuter” som valts.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure-exempel 3 minuter leverantör av data från Indataströmmen och meddelar dig när exempeldata är klara. (Detta tar en liten stund.) 

    Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempeldata och du måste skapa en ny uppsättning exempeldata. 

5. Ändra frågan i kodredigeraren till följande:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Om inte använde `TwitterStream` som alias för indata, ersätter du ditt alias för `TwitterStream` i frågan.  

    Den här frågan använder den **TIMESTAMP BY** nyckelord att ange ett tidsstämpelsfält i nyttolasten som ska användas i den temporala beräkningen. Om det här fältet har inte angetts utförs fönsterhantering åtgärden med hjälp av den tid som varje händelse som anlänt på händelsehubben. Mer information finns i avsnittet ”ankomsttid vs programmet tid” i [referens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Den här frågan också har åtkomst till en tidsstämpel för slutet av varje fönster med hjälp av den **System.Timestamp** egenskapen.

5. Klicka på **Test**. Frågan körs mot de data som du samplas.
    
6. Klicka på **Spara**. Detta sparar frågan som en del av Streaming Analytics-jobbet. (Det inte att spara exempeldata.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentera med olika fält från strömmen 

I följande tabell visas de fält som ingår i JSON strömmande data. Passa på att experimentera i frågeredigeraren.

|JSON-egenskap | Definition|
|--- | ---|
|CreatedAt | Den tidpunkt då tweeten skapades|
|Avsnitt | Ämnet som matchar det angivna nyckelordet|
|SentimentScore | Sentimentresultatet från Sentiment140|
|Författare | Twitter-referens som skickats tweeten|
|Text | Fullständig brödtexten i tweeten|


## <a name="create-an-output-sink"></a>Skapa en utdatamottagare

Du har nu definierat en händelseström, en händelsehubb som indata för att mata in händelser och en fråga för att utföra en omvandling dataströmmen. Det sista steget är att definiera en utdatamottagare för jobbet.  

I den här självstudien får skriva du aggregerade tweet-händelser från jobbet frågan till Azure Blob storage.  Du kan också skicka resultaten till Azure SQL Database, Azure Table storage, Event Hubs eller Power BI, beroende på ditt program behöver.

## <a name="specify-the-job-output"></a>Ange jobbutdata

1. I den **Jobbtopologi** klickar du på den **utdata** box. 

2. I den **utdata** bladet klickar du på  **+ &nbsp;Lägg till** och Fyll i bladet med följande värden:

   * **Utdataalias**: Använd namnet `TwitterStream-Output`. 
   * **Mottagare**: Välj **Blob-lagring**.
   * **Importalternativ**: Välj **använda blob storage från aktuell prenumeration**.
   * **Storage-konto**. Välj **skapa ett nytt lagringskonto.**
   * **Storage-konto** (andra rutan). Ange `YOURNAMEsa`, där `YOURNAME` är ditt namn eller en annan unik sträng. Namnet kan använda bara gemena bokstäver och siffror det måste vara unikt i Azure. 
   * **Behållaren**. Ange `socialtwitter`.
     Lagringskontonamn och namn på behållare används tillsammans för att tillhandahålla en URI för blob-lagring, så här: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![”Ny utdata” bladet för Stream Analytics-jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klicka på **Skapa**. 

    Azure skapar storage-konto och genererar en nyckel automatiskt. 

5. Stäng den **utdata** bladet. 


## <a name="start-the-job"></a>Starta jobbet

Jobbindata-, fråge- och utdata har angetts. Du är redo att starta Stream Analytics-jobbet.

1. Se till att TwitterWpfClient programmet körs. 

2. Jobb-bladet klickar du på **starta**.

    ![Starta Stream Analytics-jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. I den **startjobb** bladet för **starttid för jobbutdata**väljer **nu** och klicka sedan på **starta**. 

    ![”Starta jobbet” bladet för Stream Analytics-jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure meddelar dig när jobbet har startat och bladet jobb visas status som **kör**.

    ![Jobb som körs](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Visa utdata för attitydanalys

När jobbet har startats och bearbetar realtidsströmmar för Twitter, kan du visa utdata för attitydanalys.

Du kan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) att visa din jobbutdata i realtid. Härifrån kan du använda [Power BI](https://powerbi.com/) att utöka ditt program att inkludera anpassad instrumentpanel som den som visas i följande skärmbild:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Skapa en annan fråga för att identifiera populära ämnen

En annan fråga som du kan använda för att förstå Twitter-åsikter baseras på en [glidande fönstret](https://msdn.microsoft.com/library/azure/dn835051.aspx). För att identifiera populära ämnen leta du efter ämnen överskrider ett tröskelvärde för omnämnanden i en angiven tidsperiod.

För den här självstudiekursen, kan du söka efter avsnitt som nämns fler än 20 gånger i de senaste 5 sekunderna.

1. Jobb-bladet klickar du på **stoppa** att stoppa jobbet. 

2. I den **Jobbtopologi** klickar du på den **fråga** box. 

3. Ändra frågan så här:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klicka på **Spara**.

5. Se till att TwitterWpfClient programmet körs. 

6. Klicka på **starta** att starta om jobbet med hjälp av den nya frågan.


## <a name="get-support"></a>Få support
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
