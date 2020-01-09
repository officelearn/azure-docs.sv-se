---
title: Real tids analys av Twitter-sentiment med Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder Stream Analytics för analys av Twitter-sentiment i real tid. Steg-för-steg-anvisningar från Event-generering till data på en Live-instrumentpanel.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: f3ab21d55b7d59bb58760bfba452b4ea2d103496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369906"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Real tids analys av Twitter-sentiment i Azure Stream Analytics

Lär dig hur du skapar en sentiment analys lösning för Azures analys av sociala medier genom att ta real tids händelser i real tid till Azure Event Hubs. Skriv sedan en Azure Stream Analytics fråga för att analysera data och lagra resultatet för senare användning eller skapa en [Power BI](https://powerbi.com/) instrument panel för att ge insikter i real tid.

Med hjälp av verktyg för sociala media Analytics kan organisationer förstå trender i ämnen. Avsnitt om trender är ämnen och Attitudes som har stora mängder inlägg på sociala medier. Sentiment-analys, som också kallas för att avhjälpa *utvinning*, använder verktyg för sociala medier för att fastställa Attitudes till en produkt eller idé. 

Real tids analys av Twitter-trender är ett bra exempel på ett analys verktyg eftersom prenumerations modellen hashtagg gör att du kan lyssna på vissa nyckelord (hashtagg) och utveckla sentiment analys av flödet.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: sentiment analys av sociala medier i real tid

Ett företag som har en nyhets medie webbplats är intresse rad av att få en fördel av sina konkurrenter genom att ha plats innehåll som är direkt relevant för läsarna. Företaget använder analys av sociala medier för ämnen som är relevanta för läsare genom sentiment analys av Twitter-data i real tid.

För att kunna identifiera trender i ämnen i real tid på Twitter behöver företaget real tids analys av tweet-volymen och sentiment för viktiga ämnen.

## <a name="prerequisites"></a>Krav
I den här instruktions guiden använder du ett klient program som ansluter till Twitter och letar efter Tweets som har vissa hashtagg (som du kan ställa in). Om du vill köra programmet och analysera tweets med Azure streaming Analytics måste du ha följande:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Twitter](https://twitter.com) -konto.
* TwitterWPFClient-programmet som läser Twitter-flödet. Hämta det här programmet genom att ladda ned [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) -filen från GitHub och sedan packa upp paketet i en mapp på datorn. Om du vill se käll koden och köra programmet i en fel sökare kan du hämta käll koden från [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Skapa en Event Hub för indata från strömmande analys

Exempel programmet genererar händelser och skickar dem till en Azure Event Hub. Azure Event Hub är den bästa metoden för händelse inmatning för Stream Analytics. Mer information finns i [Azure Event Hubs-dokumentationen](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Skapa ett namn område för händelse hubb och händelsehubben
Skapa ett namn område för Event Hub och Lägg sedan till en händelsehubben till det namn området. Namn områden för Event Hub används för att logiskt gruppera relaterade händelse buss instanser. 

1. Logga in på Azure Portal och klicka på **skapa en resurs** > **Sakernas Internet** > **Event Hub**. 

2. På bladet **skapa namn område** anger du ett namn områdes namn som `<yourname>-socialtwitter-eh-ns`. Du kan använda namn områdets namn, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
3. Välj en prenumeration och skapa eller Välj en resurs grupp och klicka sedan på **skapa**. 

    ![Skapa ett namnområde för en händelsehubb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. När namn rymden har distribuerats hittar du namn området för händelsehubben i listan med Azure-resurser. 

5. Klicka på det nya namn området och på bladet namnrymd klickar du på **+&nbsp;Event Hub**. 

    ![Knappen Lägg till Händelsehubben för att skapa en ny händelsehubben](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Namnge den nya händelsehubben `socialtwitter-eh`. Du kan använda ett annat namn. Om du gör det, gör du en anteckning om det, eftersom du behöver namnet senare. Du behöver inte ange andra alternativ för händelsehubben.

    ![Blad för att skapa en ny händelsehubben](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klicka på **Skapa**.


### <a name="grant-access-to-the-event-hub"></a>Bevilja åtkomst till händelsehubben

Innan en process kan skicka data till en Event Hub måste händelsehubben ha en princip som tillåter lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1.  Klicka på **Event Hubs** på bladet händelse namn område och klicka sedan på namnet på din nya händelsehubben.

2.  På bladet Event Hub klickar du på **principer för delad åtkomst** och klickar sedan på **+&nbsp;Lägg till**.

    >[!NOTE]
    >Se till att du arbetar med händelsehubben, inte Event Hub-namnområdet.

3.  Lägg till en princip med namnet `socialtwitter-access` och välj **Hantera**i **anspråk**.

    ![Blad för att skapa en ny åtkomst princip för händelsehubben](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klicka på **Skapa**.

5.  När principen har distribuerats klickar du på den i listan över principer för delad åtkomst.

6.  Leta upp rutan med etiketten **ANSLUTNINGS sträng – primär nyckel** och klicka på knappen Kopiera bredvid anslutnings strängen. 
    
    ![Kopierar den primära anslutnings Strängs nyckeln från åtkomst principen](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutnings strängen för nästa avsnitt när du har gjort små ändringar i den.

    Anslutnings strängen ser ut så här:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observera att anslutnings strängen innehåller flera nyckel/värde-par, åtskiljda med semikolon: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`och `EntityPath`.  

    > [!NOTE]
    > Av säkerhets nivå har delar av anslutnings strängen i exemplet tagits bort.

8.  Ta bort `EntityPath`-paret från anslutnings strängen i text redigeraren (Glöm inte att ta bort det semikolon som föregår det). När du är klar ser anslutnings strängen ut så här:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurera och starta Twitter-klient programmet
Klient programmet hämtar tweet-händelser direkt från Twitter. För att göra det måste du ha behörighet att anropa Twitter streaming-API: erna. Om du vill konfigurera den behörigheten skapar du ett program i Twitter, som genererar unika autentiseringsuppgifter (till exempel en OAuth-token). Sedan kan du konfigurera klient programmet att använda dessa autentiseringsuppgifter när det gör API-anrop. 

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du inte redan har ett Twitter-program som du kan använda för den här instruktions guiden kan du skapa ett. Du måste redan ha ett Twitter-konto.

> [!NOTE]
> Den exakta processen i Twitter för att skapa ett program och hämta nycklar, hemligheter och token kan ändras. Om dessa anvisningar inte matchar det du ser på Twitter-webbplatsen, se dokumentationen om Twitter-utvecklare.

1. I en webbläsare går du till [Twitter för utvecklare](https://developer.twitter.com/en/apps)och väljer **skapa en app**. Du kan se ett meddelande om att du måste använda för ett Twitter-konto för utvecklare. Det är kostnads fritt att göra det och när ditt program har godkänts bör du se ett bekräftelse meddelande. Det kan ta flera dagar att godkännas för ett Developer-konto.

   ![Konto bekräftelse för Twitter-utvecklare](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Konto bekräftelse för Twitter-utvecklare")

   ![Information om Twitter-program](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Information om Twitter-program")

2. På sidan **Skapa ett program** anger du information om den nya appen. Välj sedan **Create your Twitter application** (Skapa ditt Twitter-program).

   ![Information om Twitter-program](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Information om Twitter-program")

3. På sidan program väljer du fliken **nycklar och tokens** och kopierar värdena för klient-API- **nyckel** och **hemlig nyckel för konsument-API**. Välj också **skapa** under **åtkomst-token och åtkomst till token Secret** för att generera åtkomsttoken. Kopiera värdena för **åtkomsttoken** och **åtkomsttokenhemligheten**.

    ![Information om Twitter-program](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Information om Twitter-program")

Spara de värden som du hämtade för Twitter-programmet. Du behöver värdena senare i instruktionen How-to.

>[!NOTE]
>Nycklarna och hemligheterna för Twitter-programmet ger åtkomst till ditt Twitter-konto. Behandla denna information som känslig, på samma sätt som du gör ditt Twitter-lösenord. Bädda till exempel inte in den här informationen i ett program som du ger andra. 


### <a name="configure-the-client-application"></a>Konfigurera klient programmet
Vi har skapat ett klient program som ansluter till Twitter-data med [Twitter: s strömnings-API: er](https://dev.twitter.com/streaming/overview) för att samla in tweet-händelser om en specifik uppsättning ämnen. Programmet använder verktyget [Sentiment140](http://help.sentiment140.com/) med öppen källkod som tilldelar följande sentiment-värde till varje tweet:

* 0 = negativ
* 2 = neutral
* 4 = positiv

När tweet-händelserna har tilldelats ett sentiment-värde flyttas de till händelsehubben som du skapade tidigare.

Innan programmet körs krävs viss information från dig, t. ex. Twitter-nycklar och Event Hub-anslutningssträngen. Du kan ange konfigurations information på följande sätt:

* Kör programmet och Använd sedan programmets användar gränssnitt för att ange nycklar, hemligheter och anslutnings sträng. Om du gör detta används konfigurations informationen för den aktuella sessionen, men den sparas inte.
* Redigera programmets. config-fil och ange värdena där. Den här metoden har kvar konfigurations informationen, men det innebär också att denna potentiellt känsliga information lagras som oformaterad text på din dator.

Följande procedur dokument beskriver båda metoderna. 

1. Se till att du har laddat ned och zippa upp [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) -programmet som anges i kraven.

2. Om du vill ange värden vid kör tid (och endast för den aktuella sessionen) kör du `TwitterWPFClient.exe` programmet. Ange följande värden när programmet efterfrågar:

    * Twitter-konsument nyckeln (API-nyckel).
    * Twitter-konsument hemlighet (API-hemlighet).
    * Twitter-åtkomsttoken.
    * Hemligheten för Twitter-åtkomsttoken.
    * Den anslutnings Strängs information som du sparade tidigare. Kontrol lera att du använder anslutnings strängen som du tog bort `EntityPath` nyckel-värde-paret från.
    * De Twitter-nyckelord som du vill fastställa sentiment för.

   ![TwitterWpfClient-program som körs, visar dolda inställningar](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Om du vill ange värden permanent använder du en text redigerare för att öppna filen TwitterWpfClient. exe. config. Gör sedan följande i `<appSettings>`-elementet:

   * Ange `oauth_consumer_key` till Twitter-konsument nyckeln (API-nyckel). 
   * Ange `oauth_consumer_secret` till Twitter-konsument hemligheten (API-hemlighet).
   * Ange `oauth_token` till Twitter-åtkomsttoken.
   * Ange `oauth_token_secret` till hemligheten för Twitter-åtkomsttoken.

     Gör följande ändringar senare i `<appSettings>`-elementet:

   * Ange `EventHubName` till Event Hub-namnet (det vill säga till värdet för enhetens sökväg).
   * Ange `EventHubNameConnectionString` till anslutnings strängen. Kontrol lera att du använder anslutnings strängen som du tog bort `EntityPath` nyckel-värde-paret från.

     Avsnittet `<appSettings>` ser ut som i följande exempel. (För klarhet och säkerhet har vi omslutit några rader och tagit bort några tecken.)

     ![TwitterWpfClient program konfigurations fil i en text redigerare som visar Twitter-nycklar och hemligheter och information om anslutnings strängen för händelsehubben](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Om du inte redan har startat programmet kör du TwitterWpfClient. exe nu. 

5. Klicka på den gröna Start knappen för att samla in sociala sentiment. Du ser tweet-händelser med **CreatedAt**-, **topic**-och **SentimentScore** -värden som skickas till händelsehubben.

    ![TwitterWpfClient-program som körs och visar en lista över tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Om du ser fel och du inte ser en ström med Tweets som visas i den nedre delen av fönstret, dubbelklickar du på nycklarna och hemligheterna. Kontrol lera också anslutnings strängen (se till att den inte innehåller `EntityPath` nyckel och värde.)


## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu när tweet-händelser strömmas i real tid från Twitter, kan du konfigurera ett Stream Analytics jobb för att analysera händelserna i real tid.

1. Klicka på **skapa en resurs** > **Sakernas Internet** > **Stream Analytics jobb**i Azure Portal.

2. Namnge jobbet `socialtwitter-sa-job` och ange en prenumeration, en resurs grupp och en plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betalar för att överföra data mellan regioner.

    ![Skapa ett nytt Stream Analytics jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klicka på **Skapa**.

    Jobbet skapas och portalen visar jobb information.


## <a name="specify-the-job-input"></a>Ange jobb ingången

1. Klicka på **indata**under **jobb sto pol Ogin** på bladet jobb i Stream Analytics jobb. 

2. I bladet **indata** klickar du på **+&nbsp;Lägg till** och fyller sedan i bladet med följande värden:

   * **Indataområde**: använd namnet `TwitterStream`. Om du använder ett annat namn ska du anteckna det eftersom du behöver det senare.
   * **Typ av källa**: Välj **data ström**.
   * **Källa**: Välj **Event Hub**.
   * **Import alternativ**: Välj **Använd händelsehubben från den aktuella prenumerationen**. 
   * **Service Bus-namnrymd**: Välj den Event Hub-namnrymd som du skapade tidigare (`<yourname>-socialtwitter-eh-ns`).
   * **Event Hub**: Välj den händelsehubben som du skapade tidigare (`socialtwitter-eh`).
   * **Princip namn för Event Hub**: Välj den åtkomst princip som du skapade tidigare (`socialtwitter-access`).

     ![Skapa ny indata för strömnings analys jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klicka på **Skapa**.


## <a name="specify-the-job-query"></a>Ange jobb frågan

Stream Analytics stöder en enkel, deklarativ frågeplan som beskriver transformeringar. Mer information om språket finns i [språk referens för Azure Stream Analytics frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Den här instruktions guiden hjälper dig att skapa och testa flera frågor över Twitter-data.

Om du vill jämföra antalet omnämnanden mellan ämnen kan du använda ett [rullande-fönster](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) för att få antalet omnämnanden efter var femte sekund.

1. Stäng bladet **indata** om du inte redan gjort det.

2. I **översikts** bladet klickar du på **Redigera fråga** nära det övre högra hörnet i rutan fråga. Azure visar de indata och utdata som har kon figurer ATS för jobbet och gör att du kan skapa en fråga som gör att du kan omvandla indataströmmen när den skickas till utdata.

3. Kontrol lera att TwitterWpfClient-programmet körs. 

3. På bladet **fråga** klickar du på punkterna bredvid `TwitterStream` indata och väljer sedan **exempel data från indata**.

    ![Meny alternativ för att använda exempel data för jobb posten streaming Analytics med "exempel data från indata" valt](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Då öppnas ett blad där du kan ange hur mycket exempel data som ska hämtas, vilket definieras i hur lång tid det tar att läsa indataströmmen.

4. Ange **minuter** till 3 och klicka sedan på **OK**. 
    
    ![Alternativ för att sampla in indataströmmen med "3 minuter" valt.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure-exempel 3 minuter är värda data från indataströmmen och meddelar dig när exempel data är klara. (Det här tar en kort stund.) 

    Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempel data och du måste skapa en ny uppsättning exempel data. 

5. Ändra frågan i kod redigeraren till följande:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Om du inte använde `TwitterStream` som alias för inmatade värden, måste du ange ditt alias för `TwitterStream` i frågan.  

    Den här frågan använder **tidsstämpeln med** nyckelord för att ange ett tidsstämpelfält i nytto lasten som ska användas i den temporala beräkningen. Om det här fältet inte anges utförs fönster åtgärden med hjälp av den tid som varje händelse har anlänt till händelsehubben. Läs mer i avsnittet "ankomst tid jämfört med program tid" i [referens för Stream Analytics frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    Den här frågan kommer även åt en tidstämpel för slutet av varje fönster med hjälp av egenskapen **system. timestamp** .

5. Klicka på **testa**. Frågan körs mot de data som du har samplat.
    
6. Klicka på **Spara**. Detta sparar frågan som en del av det strömmande Analytics-jobbet. (Vi sparar inte exempel data.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentera med olika fält från data strömmen 

I följande tabell visas de fält som är en del av JSON-strömmande data. Experimentera gärna med Frågeredigeraren.

|JSON-egenskap | Definition|
|--- | ---|
|CreatedAt | Tiden då tweeten skapades|
|Ämne | Avsnittet som matchar det angivna nyckelordet|
|SentimentScore | Sentiment-poängen från Sentiment140|
|Skapa | Twitter-referensen som skickade tweeten|
|Text | Den fullständiga delen av tweeten|


## <a name="create-an-output-sink"></a>Skapa en utgående mottagare

Nu har du definierat en händelse ström, en Event Hub-indata för inmatnings händelser och en fråga för att utföra en omvandling över strömmen. Det sista steget är att definiera en utgående mottagare för jobbet.  

I den här instruktions guiden skriver du de aggregerade tweet-händelserna från jobb frågan till Azure Blob Storage.  Du kan också skicka resultaten till Azure SQL Database, Azure Table Storage, Event Hubs eller Power BI, beroende på dina program behov.

## <a name="specify-the-job-output"></a>Ange jobbets utdata

1. Klicka på rutan **utdata** i avsnittet **jobb sto pol Ogin** . 

2. **Klicka på** **+&nbsp; Läggtillpåbladetutdataochfyllsedani** bladet med följande värden:

   * **Utdata-alias**: använd namnet `TwitterStream-Output`. 
   * **Mottagare**: Välj **Blob Storage**.
   * **Import alternativ**: Välj **Använd Blob Storage från aktuell prenumeration**.
   * **Lagringskonto**. Välj **skapa ett nytt lagrings konto.**
   * **Lagrings konto** (andra rutan). Ange `YOURNAMEsa`, där `YOURNAME` är ditt namn eller en annan unik sträng. Namnet kan använda bara gemena bokstäver och siffror det måste vara unikt i Azure. 
   * **Container**. Ange `socialtwitter`.
     Lagrings kontots namn och behållar namn används tillsammans för att tillhandahålla en URI för Blob Storage, så här: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Bladet "nytt utdata" för Stream Analytics jobb](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klicka på **Skapa**. 

    Azure skapar lagrings kontot och genererar en nyckel automatiskt. 

5. Stäng bladet **utdata** . 


## <a name="start-the-job"></a>Starta jobbet

Du har angett jobbets indata, fråga och utdata. Du är redo att starta Stream Analytics jobbet.

1. Kontrol lera att TwitterWpfClient-programmet körs. 

2. Klicka på **Start**på bladet jobb.

    ![Starta Stream Analytics jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. På bladet **starta jobb** , för **Start tid för jobb**, väljer du **nu** och klickar sedan på **Starta**. 

    ![Bladet "starta jobb" för Stream Analytics jobbet](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure meddelar dig när jobbet har startat och på bladet jobb visas status som **körs**.

    ![Jobb som körs](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Visa utdata för sentiment-analys

När jobbet har startat och bearbetar Twitter-dataströmmen i real tid kan du visa resultatet för sentiment-analys.

Du kan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) för att Visa jobbets utdata i real tid. Härifrån kan du använda [Power BI](https://powerbi.com/) för att utöka ditt program så att det innehåller en anpassad instrument panel som det som visas på följande skärm bild:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Skapa en annan fråga för att identifiera trend ämnen

En annan fråga som du kan använda för att förstå Twitter-sentiment baseras på ett [glidande fönster](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Om du vill identifiera trender kan du leta efter avsnitt som överskrider ett tröskelvärde för omnämnanden under en angiven tids period.

I den här instruktionen kan du söka efter ämnen som nämns fler än 20 gånger under de senaste 5 sekunderna.

1. På bladet jobb klickar du på **stoppa** för att stoppa jobbet. 

2. I avsnittet **jobb sto pol Ogin** klickar du på rutan **fråga** . 

3. Ändra frågan till följande:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klicka på **Spara**.

5. Kontrol lera att TwitterWpfClient-programmet körs. 

6. Klicka på **Starta** för att starta om jobbet med den nya frågan.


## <a name="get-support"></a>Få support
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
