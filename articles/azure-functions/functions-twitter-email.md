---
title: Skapa en funktion som kan integreras med Azure Logikappar | Microsoft Docs
description: "Skapa en funktion som kan integreras med Azure Logikappar och kognitiva Azure-tjänster för att kategorisera sentiment tweets och skicka meddelanden när sentiment är dålig."
services: functions, logic-apps, cognitive-services
keywords: workflow, cloud apps, cloud services, business processes, system integration, enterprise application integration, EAI
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 910077645b521d4cd303d39f543cf155161a31c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Skapa en funktion som kan integreras med Azure Logikappar

Azure Functions kan integreras med Azure Logikappar i Logic Apps Designer. Den här integreringen kan du använda datorkraft av funktioner i orkestreringarna med andra Azure och tjänster från tredje part. 

Den här kursen visar hur du använder funktioner med Logic Apps och kognitiva Microsoft-tjänster i Azure för att analysera sentiment från Twitter meddelandena. En funktion för HTTP-utlöses kategoriserar tweets som gröna, gula och röda baserat på sentiment poäng. Ett e-postmeddelande skickas när dålig sentiment har identifierats. 

![bild två första stegen i logik App Designer-appen](media/functions-twitter-email/designer1.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett kognitiva Services-konto.
> * Skapa en funktion som kategoriserar tweet sentiment.
> * Skapa en logikapp som ansluter till Twitter.
> * Lägg till sentiment identifiering logikappen. 
> * Ansluta logikappen till funktionen.
> * Skicka ett e-post baserat på svar från funktionen.

## <a name="prerequisites"></a>Krav

+ En aktiv [Twitter](https://twitter.com/) konto. 
+ En [Outlook.com](https://outlook.com/) konto (för att skicka meddelanden).
+ För det här avsnittet förutsätts de resurser som skapades i avsnittet [Create your first function from the Azure portal](functions-create-first-azure-function.md) (Skapa din första funktion i Azure Portal).  
Om du inte redan gjort det, kan du slutföra de här stegen nu för att skapa funktionsapp.

## <a name="create-a-cognitive-services-account"></a>Skapa en kognitiva Services-konto

Ett kognitiva Services-konto krävs för att identifiera sentiment av tweets som övervakas.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

3. Klicka på **Data + analys** > **kognitiva Services**. Använd sedan inställningarna som anges i tabellen, accepterar villkoren och kontrollera **fäst på instrumentpanelen**.

    ![Skapa kognitiva kontosida](media/functions-twitter-email/cog_svcs_account.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                                        |
    | --- | --- | --- |
    | **Namn** | MyCognitiveServicesAccnt | Välj ett unikt namn. |
    | **API-typ** | API för textanalys | API som används för att analysera text.  |
    | **Plats** | Västra USA | För närvarande endast **västra USA** är tillgänglig för textanalys. |
    | **prisnivå** | F0 | Börja med den lägsta nivån. Om du kör out-of-anrop, skala till en högre nivå.|
    | **Resursgrupp** | myResourceGroup | Använd samma resursgrupp för alla tjänster i den här självstudiekursen.|

4. Klicka på **skapa** att skapa kontot. Klicka på din nya kognitiva tjänster konto fäst på instrumentpanelen när kontot skapas. 

5. I kontot, klickar du på **nycklar**, och sedan kopiera värdet för **nyckel 1** och spara den. Du kan använda denna nyckel för att ansluta logikappen till kognitiva Services-konto. 
 
    ![Nycklar](media/functions-twitter-email/keys.png)

## <a name="create-the-function"></a>Skapa funktionen

Functions erbjuder ett bra sätt att avlasta bearbetningen aktiviteter i ett arbetsflöde för logic apps. Den här kursen används en HTTP-utlöses funktion att bearbeta tweet sentiment resultat från kognitiva tjänster och returnera ett kategorivärde.  

1. Expandera din funktionsapp, klicka på den  **+**  knappen bredvid **funktioner**, klicka på den **HTTPTrigger** mall. Typen `CategorizeSentiment` för funktionen **namn** och på **skapa**.

    ![Funktionen appar bladet funktioner +](media/functions-twitter-email/add_fun.png)

2. Ersätt innehållet i filen run.csx med följande kod och klicka sedan på **spara**:

    ```c#
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    Den här Funktionskoden returnerar en färgkategori baserat på sentiment poängsättningen togs emot i begäran. 

3. Testa funktionen genom att klicka på **testa** längst till höger att expandera fliken Test. Ange ett värde för `0.2` för den **Begärandetext**, och klicka sedan på **kör**. Ett värde av **RED** returneras i brödtexten i svaret. 

    ![Testa funktionen i Azure-portalen](./media/functions-twitter-email/test.png)

Nu har du en funktion som kategoriserar sentiment resultat. Därefter kan du skapa en logikapp som integrerar din funktion med Twitter och kognitiva Services-konton. 

## <a name="create-a-logic-app"></a>Skapa en logikapp   

1. I Azure-portalen klickar du på den **ny** knapp hittades i det övre vänstra hörnet i Azure-portalen.

2. Klicka på **företagsintegration** > **Logikapp**. Använd sedan inställningarna som anges i tabellen, kontrollera **fäst på instrumentpanelen**, och klicka på **skapa**.
 
4. Skriv en **namn** som `TweetSentiment`, använda inställningar som anges i tabellen, accepterar villkoren och kontrollera **fäst på instrumentpanelen**.

    ![Skapa logikappen i Azure-portalen](./media/functions-twitter-email/new_logicApp.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                                        |
    | ----------------- | ------------ | ------------- |
    | **Namn** | TweetSentiment | Välj ett lämpligt namn för din app. |
    | **Resursgrupp** | myResourceGroup | Välj samma befintlig resursgrupp som innan. |
    | **Plats** | Östra USA | Välj en plats nära dig. |    

4. Välj **fäst på instrumentpanelen**, och klicka sedan på **skapa** att skapa din logikapp. 

5. När appen har skapats klickar du på den nya logikappen fäst på instrumentpanelen. Sedan i Logic Apps Designer rulla nedåt och klicka på den **tom Logikapp** mall. 

    ![Tom mall för Logic Apps](media/functions-twitter-email/blank.png)

Du kan nu använda Logic Apps Designer för att lägga till tjänster och utlösare i din app.

## <a name="connect-to-twitter"></a>Ansluta till Twitter

Först skapa en anslutning till Twitter-konto. Logikappen avsöker för tweets som utlöser app att köras.

1. I designer klickar du på den **Twitter** tjänst och klicka på den **när en ny tweet skickas** utlösare. Logga in med Twitter-konto och auktorisera Logic Apps att använda ditt konto.

2. Använda Twitter inställningarna som anges i tabellen. 

    ![Twitter connector-inställningar](media/functions-twitter-email/azure_tweet.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                                        |
    | ----------------- | ------------ | ------------- |
    | **Söktext** | #Azure | Använd en hashtaggar är populärt att generera nya tweets i det valda intervallet. När det är för populära med hjälp av den kostnadsfria nivån och din hashtaggar, kan du snabbt använda in transaktioner i kognitiva Services-konto. |
    | **Frekvens** | Minut | Frekvens enhet som används för avsökning av Twitter.  |
    | **Intervall** | 15 | Tid mellan Twitter begäranden i frekvens enheter. |

3.  Klicka på **spara** att ansluta till ditt Twitter-konto. 

Appen är nu ansluten till Twitter. Därefter ansluta till text analytics identifiera sentiment av insamlade tweets.

## <a name="add-sentiment-detection"></a>Lägg till sentiment identifiering

1. Klicka på **nytt steg**, och sedan **lägga till en åtgärd**.

    ![Nytt steg och Lägg till en åtgärd](media/functions-twitter-email/new_step.png)

2. I **välja en åtgärd**, klickar du på **textanalys**, och klicka sedan på den **identifiera sentiment** åtgärd.

    ![Identifiera Sentiment](media/functions-twitter-email/detect_sent.png)

3. Ange ett anslutningsnamn `MyCognitiveServicesConnection`, klistra in nyckeln för dina kognitiva tjänster konto som du sparade och klicka på **skapa**.  

4. Klicka på **Text för att analysera** > **Twittra text**, och klicka sedan på **spara**.  

    ![Identifiera Sentiment](media/functions-twitter-email/ds_tta.png)

Du kan lägga till en anslutning till din funktion som förbrukar sentiment poäng utdata sentiment identifiering är konfigurerat.

## <a name="connect-sentiment-output-to-your-function"></a>Ansluta sentiment utdata till din funktion

1. I Logic Apps Designer klickar du på **nytt steg** > **lägga till en åtgärd**, och klicka sedan på **Azure Functions**. 

2. Klicka på **välja en Azure-funktion**, Välj den **CategorizeSentiment** funktionen som du skapade tidigare.  

    ![Azure funktionsrutan visar Välj en Azure-funktion](media/functions-twitter-email/choose_fun.png)

3. I **brödtext i begäran**, klickar du på **poäng** och sedan **spara**.

    ![Poäng](media/functions-twitter-email/trigger_score.png)

Nu kan utlöses din funktion när en sentiment poäng skickas från logikappen. En färgkodade kategori returneras till logikappen av funktionen. Sedan lägger du till ett e-postmeddelande som skickas när sentiment värdet **RED** returneras från funktionen. 

## <a name="add-email-notifications"></a>Lägg till e-postaviseringar

Den sista delen av arbetsflödet är att utlösa ett e-postmeddelande när sentiment beräknas som _RED_. Det här avsnittet använder en Outlook.com-koppling. Du kan utföra liknande steg för att använda en Gmail eller Office 365 Outlook-koppling.   

1. I Logic Apps Designer klickar du på **nytt steg** > **Lägg till ett villkor**. 

2. Klicka på **väljer ett värde**, klicka på **brödtext**. Välj **är lika med**, klickar du på **väljer ett värde** och skriv `RED`, och klicka på **spara**. 

    ![Lägga till ett villkor i logikappen.](media/functions-twitter-email/condition.png)

3. I **om Ja, gör INGENTING**, klickar du på **lägga till en åtgärd**, söka efter `outlook.com`, klickar du på **skickar ett e-**, och logga in på Outlook.com-konto.
    
    ![Välj en åtgärd för villkoret.](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Om du inte har en Outlook.com-konto, kan du välja en annan koppling, t.ex Gmail eller Office 365 Outlook

4. I den **skickar ett e-** åtgärd, Använd e-postinställningar som angetts i tabellen. 

    ![Konfigurera e-postmeddelandet skickas en e-åtgärd.](media/functions-twitter-email/sendEmail.png)

    | Inställning      |  Föreslaget värde   | Beskrivning  |
    | ----------------- | ------------ | ------------- |
    | **Till** | Skriv din e-postadress | E-postadressen som tar emot ett meddelande. |
    | **Ämne** | Negativa tweets sentiment upptäcktes  | Ämnesraden för e-postmeddelande.  |
    | **Brödtext** | Tweet text, plats | Klicka på den **Twittra text** och **plats** parametrar. |

5.  Klicka på **Spara**.

Arbetsflödet har slutförts kan du aktivera logikappen och finns funktionen på arbetet.

## <a name="test-the-workflow"></a>Testa arbetsflödet

1. I logik App Designer klickar du på **kör** starta appen.

2. Klicka i den vänstra kolumnen **översikt** att se status för logikappen. 
 
    ![Logik app Körstatus](media/functions-twitter-email/over1.png)

3. (Valfritt) Klicka på Kör visas detaljerad information om körningen.

4. Gå till din funktion, visa loggfilerna och kontrollera att sentiment värden tagits emot och bearbetas.
 
    ![Visa funktionsloggar](media/functions-twitter-email/sent.png)

5. Du får ett e-postmeddelande när potentiellt negativa sentiment har identifierats. Om du inte har fått ett e-postmeddelande, kan du ändra Funktionskoden för att returnera RED varje gång:

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    När du har kontrollerat e-postmeddelanden, ändra tillbaka till den ursprungliga koden:

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > När du har slutfört den här kursen ska du inaktivera logikappen. Genom att inaktivera appen undviker du att debiteras för körningar och använder upp transaktioner i kognitiva Services-konto.

Nu har du sett hur enkelt det är att integrera funktioner i ett arbetsflöde för Logic Apps.

## <a name="disable-the-logic-app"></a>Inaktivera logikappen

Om du vill inaktivera logikappen **översikt** och klicka sedan på **inaktivera** överst på skärmen. Detta stoppar logikappen köra och medför kostnader utan att ta bort appen. 

![Funktionsloggar](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett kognitiva Services-konto.
> * Skapa en funktion som kategoriserar tweet sentiment.
> * Skapa en logikapp som ansluter till Twitter.
> * Lägg till sentiment identifiering logikappen. 
> * Ansluta logikappen till funktionen.
> * Skicka ett e-post baserat på svar från funktionen.

Gå vidare till nästa kurs att lära dig hur du skapar ett serverlösa API för din funktion.

> [!div class="nextstepaction"] 
> [Skapa ett API utan server med Azure Functions](functions-create-serverless-api.md)

Läs mer om Logic Apps i [Azure Logikappar](../logic-apps/logic-apps-what-are-logic-apps.md).

