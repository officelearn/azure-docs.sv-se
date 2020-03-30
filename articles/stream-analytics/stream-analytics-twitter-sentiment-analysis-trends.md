---
title: Twitter-sentimentanalys i realtid med Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder Stream Analytics för sentimentanalys i realtid. Steg-för-steg-vägledning från händelsegenerering till data på en live-instrumentpanel.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240291"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Attitydanalys för Twitter i realtid i Azure Stream Analytics

I den här artikeln får du lära dig hur du skapar en lösning för analys av känslor i sociala medier genom att föra in Twitter-händelser i realtid i Azure Event Hubs. Du skriver en Azure Stream Analytics-fråga för att analysera data och lagra resultaten för senare användning eller skapa en [Power BI-instrumentpanel](https://powerbi.com/) för att ge insikter i realtid.

Analysverktyg för sociala medier hjälper organisationer att förstå populära ämnen. Populära ämnen är ämnen och attityder som har en stor mängd inlägg på sociala medier. Sentimentanalys, som också kallas *opinionsutveckling*, använder sociala medier analysverktyg för att avgöra attityder till en produkt eller idé. 

Twitter trendanalys i realtid är ett bra exempel på ett analysverktyg eftersom hashtag-prenumerationsmodellen gör att du kan lyssna på specifika sökord (hashtags) och utveckla sentimentanalys av flödet.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: Sociala medier sentimentanalys i realtid

Ett företag som har en nyhetsmedia webbplats är intresserad av att få en fördel gentemot sina konkurrenter genom featuring webbplatsinnehåll som är omedelbart relevant för sina läsare. Företaget använder sociala medier analys om ämnen som är relevanta för läsarna genom att göra realtid känslor analys av Twitter data.

För att identifiera populära ämnen i realtid på Twitter behöver företaget analyser i realtid om tweetvolymen och sentimentet för viktiga ämnen.

## <a name="prerequisites"></a>Krav

I den här guiden använder du ett klientprogram som ansluter till Twitter och letar efter tweets som har vissa hashtaggar (som du kan ställa in). Om du vill köra programmet och analysera tweets med Azure Streaming Analytics måste du ha följande:

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Ett [Twitter](https://twitter.com) Twitter-konto.

* Den TwitterClientCore ansökan, som läser Twitter foder. För att få denna ansökan, ladda ner [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Installera [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) version 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Skapa en händelsehubb för direktuppspelningsinmatning

Exempelprogrammet genererar händelser och skickar dem till en Azure-händelsehubb. Azure Event Hubs är den metod för händelsematning för Stream Analytics. Mer information finns i [dokumentationen till Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Skapa ett namnområde för händelsehubben och händelsehubben
I det här avsnittet skapar du ett namnområde för händelsehubben och lägger till en händelsehubb i namnområdet. Namnområden för händelsehubben används för att logiskt gruppera relaterade händelsebussinstanser. 

1. Logga in på Azure-portalen och välj **Skapa en resurs**. Sedan. sök efter **händelsehubbar** och välj **Skapa**.

2. Ange ett namnområdesnamn på sidan **Skapa namnområde.** Du kan använda vilket namn som helst för namnområdet, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
3. Välj en prisnivå och prenumeration och skapa eller välj en resursgrupp. Välj sedan en plats och välj **Skapa**. 
 
4. När namnområdet har distribuerats är du tillbaka till resursgruppen och hittar namnområdet för händelsehubben i listan över Azure-resurser. 

5. Välj ** + &nbsp;Event Hub**i det nya namnområdet . 

6. Namnge den nya händelsehubben *socialtwitter-eh*. Du kan använda ett annat namn. Om du gör det, anteckna det, eftersom du behöver namnet senare. Du behöver inte ange några andra alternativ för händelsehubben.
 
7. Välj **Skapa**.

### <a name="grant-access-to-the-event-hub"></a>Bevilja åtkomst till händelsehubben

Innan en process kan skicka data till en händelsehubb behöver händelsehubben en princip som ger åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1.  Välj **Event Hubs**i avsnittet **Entiteter** till vänster om namnområdet för händelsehubbar. Välj sedan den händelsehubb som du just skapade.

2.  Välj Principer för delad åtkomst under **Inställningar**i **navigeringsfältet** till vänster.

    >[!NOTE]
    >Det finns ett alternativ för principer för delad åtkomst under för namnområdet för händelsehubben och för händelsehubben. Kontrollera att du arbetar i kontexten för händelsehubben, inte det övergripande namnområdet för händelsehubben.

3.  På sidan för åtkomstprincip väljer du **+ Lägg till**. Ange sedan *socialtwitter-access* för **principnamnet** och markera kryssrutan **Hantera.**
 
4.  Välj **Skapa**.

5.  När principen har distribuerats väljer du principen i listan över principer för delad åtkomst.

6.  Leta reda på rutan Med namnet **Primärnyckel för anslutningssträng** och välj kopieringsknappen bredvid anslutningssträngen.
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen för nästa avsnitt när du har gjort några små redigeringar.

   Anslutningssträngen ser ut så här:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Observera att anslutningssträngen innehåller flera nyckelvärdespar, avgränsade `Endpoint` `SharedAccessKeyName`med `SharedAccessKey`semikolon: , , och `EntityPath`.  

   > [!NOTE]
   > Av säkerhetsskäl har delar av anslutningssträngen i exemplet tagits bort.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurera och starta Twitter-klientprogrammet

Klienten ansökan får tweet händelser direkt från Twitter. För att göra det, det måste tillstånd att ringa Twitter Streaming API: er. Om du vill konfigurera den behörigheten skapar du ett program på Twitter, som genererar unika autentiseringsuppgifter (till exempel en OAuth-token). Du kan sedan konfigurera klientprogrammet så att de använder dessa autentiseringsuppgifter när det gör API-anrop. 

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du inte redan har en Twitter program som du kan använda för denna how-to guide, kan du skapa en. Du måste redan ha ett Twitter-konto.

> [!NOTE]
> Den exakta processen på Twitter för att skapa ett program och få nycklar, hemligheter och token kan ändras. Om dessa instruktioner inte matchar det du ser på Twitter-webbplatsen läser du dokumentationen till Utvecklaren på Twitter.

1. Från en webbläsare går du till [Twitter för utvecklare](https://developer.twitter.com/en/apps), skapar ett utvecklarkonto och väljer Skapa en **app**. Du kan se ett meddelande om att du måste ansöka om ett Twitter-utvecklarkonto. Känn dig fri att göra det, och efter att din ansökan har godkänts, bör du se en bekräftelse via e-post. Det kan ta flera dagar att godkännas för ett utvecklarkonto.

   ![Twitter ansökan detaljer](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter ansökan detaljer")

2. På sidan **Skapa ett program** anger du information om den nya appen. Välj sedan **Create your Twitter application** (Skapa ditt Twitter-program).

   ![Twitter ansökan detaljer](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter ansökan detaljer")

3. På programsidan väljer du fliken Nycklar och token och **kopierar** värdena för **konsument-API-nyckel** och **hemlig konsument-API-nyckel**. Välj också **Skapa** under **Åtkomsttoken och Åtkomsttokenhemlig** för att generera åtkomsttoken. Kopiera värdena för **åtkomsttoken** och **åtkomsttokenhemligheten**.

   Spara de värden som du hämtade för Twitter-programmet. Du behöver värdena senare.

> [!NOTE]
> Nycklarna och hemligheterna för Twitter-programmet ger tillgång till ditt Twitter-konto. Behandla denna information som känslig, samma som du gör ditt Twitter-lösenord. Bädda till exempel inte in den här informationen i ett program som du ger till andra. 

### <a name="configure-the-client-application"></a>Konfigurera klientprogrammet

Vi har skapat ett klientprogram som ansluter till Twitter-data med [Twitters streaming-API:er](https://dev.twitter.com/streaming/overview) för att samla in tweethändelser om en viss uppsättning ämnen.

Innan programmet körs kräver det viss information från dig, till exempel Twitter-tangenterna och anslutningssträngen för händelsehubben.

1. Se till att du har laddat ner [TwitterClientCore-programmet,](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) enligt vad som anges i förutsättningarna.

2. Öppna *filen App.config* med hjälp av en textredigerare. Gör följande ändringar `<appSettings>` i elementet:

   * Ställ `oauth_consumer_key` in på Twitter Consumer Key (API-nyckel). 
   * Ställ `oauth_consumer_secret` in på Twitter Consumer Secret (API hemlig nyckel).
   * Ställ `oauth_token` in på Twitter Access-token.
   * Ställ `oauth_token_secret` in på Twitter Access token hemlighet.
   * Ställ `EventHubNameConnectionString` in på anslutningssträngen.
   * Ange `EventHubName` till händelsenavnamnet (det villikans värde för entitetssökvägen).

3. Öppna kommandoraden och navigera till katalogen där din TwitterClientCore-app finns. Använd kommandot `dotnet build` för att skapa projektet. Använd sedan `dotnet run` kommandot för att köra appen. Appen skickar tweets till din eventhub.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu när tweet händelser strömmas i realtid från Twitter, kan du ställa in en Stream Analytics jobb för att analysera dessa händelser i realtid.

1. I Azure-portalen navigerar du till din resursgrupp och väljer **+ Lägg till**. Sök sedan efter **Stream Analytics-jobb** och välj **Skapa**.

2. Namnge projektet `socialtwitter-sa-job` och ange en prenumeration, resursgrupp och plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betalar för att överföra data mellan regioner.

3. Välj **Skapa**. Navigera sedan till jobbet när distributionen är klar.

## <a name="specify-the-job-input"></a>Ange jobbindata

1. I ditt Stream Analytics-jobb väljer du **Indata** på den vänstra menyn under **Jobbtopologi**.

2.  > Välj ** + &nbsp;Lägg till händelsehubben för indataström**.**Event Hub** Fyll i det **nya inmatningsformuläret** med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias| *TwitterStream (På andra)* | Ange ett alias för indata. |
   |Prenumeration  | \<Din prenumeration\> |  Ange den prenumeration som du vill använda. |
   |Namnområde för händelsehubb | *asa-twitter-eventhub* |
   |Namn på händelsehubb | *socialtwitter-eh* | Välj *Använd befintlig*. Välj sedan den eventhubben som du skapade.|
   |Typ av händelsekomprimering| Gzip | Datakomprimeringstypen.|

   Lämna de återstående standardvärdena och välj **Spara**.

## <a name="specify-the-job-query"></a>Ange jobbfrågan

Stream Analytics stöder en enkel, deklarativ frågemodell som beskriver omvandlingar. Mer information om språket finns i Språkreferens för [Azure Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Den här hjälpguiden hjälper dig att skapa och testa flera frågor via Twitter-data.

Om du vill jämföra antalet omnämnanden mellan ämnen kan du använda ett [tumlande fönster](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) för att få antalet omnämnanden per ämne var femte sekund.

1. Välj **Redigera fråga** längst upp till höger i rutan Fråga i **projektöversikten.** Azure listar de indata och utdata som är konfigurerade för jobbet och låter dig skapa en fråga för att omvandla indataströmmen när den skickas till utdata.

2. Ändra frågan i frågeredigeraren till följande:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Händelsedata från meddelandena ska visas i **förhandsgranskningsfönstret för indata** under frågan. Se till att **vyn** är inställd på **JSON**. Om du inte ser några data kontrollerar du att datageneratorn skickar händelser till händelsehubben och att du har valt **GZip** som komprimeringstyp för indata.

4. Välj **Testfråga** och lägg märke till resultaten i fönstret **Testresultat** under frågan.

5. Ändra frågan i kodredigeraren till följande och välj **Testfråga:**

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Den här frågan returnerar alla tweets som innehåller nyckelordet *Azure*.

## <a name="create-an-output-sink"></a>Skapa en utdatamottagare

Du har nu definierat en händelseström, en händelsehubbinmatning till inmatning av händelser och en fråga för att utföra en omvandling över strömmen. Det sista steget är att definiera en utdatamottagare för jobbet.  

I den här programguiden skriver du de aggregerade tweethändelserna från jobbfrågan till Azure Blob-lagring.  Du kan också skicka dina resultat till Azure SQL Database, Azure Table Storage, Event Hubs eller Power BI, beroende på dina programbehov.

## <a name="specify-the-job-output"></a>Ange projektutdata

1. Under avsnittet **Jobbtopologi** på menyn till vänster väljer du **Utdata**. 

2. På sidan **Utdata** ** + &nbsp;** klickar du på Lägg till och **Blob storage/Data Lake Storage Gen2:**

   * **Utdataalias:** `TwitterStream-Output`Använd namnet . 
   * **Importalternativ:** Välj **Välj lagringsutrymme från dina prenumerationer**.
   * **Lagringskonto**. Välj ditt lagringskonto.
   * **Behållare**. Välj **Skapa** nytt `socialtwitter`och ange .
   
4. Välj **Spara**.   

## <a name="start-the-job"></a>Starta jobbet

En jobbinmatning, fråga och utdata anges. Du är redo att starta Stream Analytics-jobbet.

1. Kontrollera att TwitterClientCore-programmet körs. 

2. Välj **Start**i projektöversikten .

3. Välj **Nu** för starttid för **jobbutdata**på sidan **Startjobb** och välj sedan **Start**.

## <a name="get-support"></a>Få support
Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
