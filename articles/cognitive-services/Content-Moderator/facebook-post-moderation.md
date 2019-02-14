---
title: 'Självstudier: Innehållsmoderering på Facebook – Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: I självstudien lär du dig att använda maskininlärningsbaserade Content Moderator till att moderera Facebook-inlägg och kommentarer.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 86c89164e3ccd5bf5df303b98cf6d336f3916e2b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878077"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Självstudier: Innehållsmoderering på Facebook – Azure Content Moderator

I självstudien lär du dig att använda maskininlärningsbaserade Content Moderator till att moderera Facebook-inlägg och kommentarer.

Den här självstudien vägleder dig genom följande steg:

1. Skapa ett Content Moderator-team.
2. Skapa Azure Functions som lyssnar efter HTTP-händelser från Content Moderator och Facebook.
3. Skapa en Facebook-sida och app, samt ansluta dem till Content Moderator.

När vi är klara skickar Facebook innehåll som publicerats av besökare till Content Moderator. Baserat på dina tröskelvärden för matchning publicerar Content Moderator antingen innehållet eller skapar granskningar inom granskningsverktyget. 

Nedanstående bild visar komponenterna i lösningen.

![Moderering av Facebook-inlägg](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Skapa ett Content Moderator-team

Gå till snabbstarten [Try Content Moderator on the web](quick-start.md) (Testa Content Moderator på webben) för att registrera dig för Content Moderator och skapa ett team.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurera ett arbetsflöde för bildändring (tröskelvärde)

På sidan [Arbetsflöden](review-tool-user-guide/workflows.md) kan du konfigurera ett anpassat bildarbetsflöde (tröskelvärde). Notera arbetsflödets **namn**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurera ett arbetsflöde för textändring (tröskelvärde)

Med liknande åtgärder som på sidan [Arbetsflöden](review-tool-user-guide/workflows.md) kan du konfigurera ett anpassat tröskelvärde och arbetsflöde för texten. Notera arbetsflödets **namn**.

![Konfigurera textarbetsflöden](images/text-workflow-configure.PNG)

Testa ditt arbetsflöde med hjälp av knappen ”Kör arbetsflöde”.

![Testa textarbetsflödet](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Skapa Azure Functions

Logga in på [Azure-hanteringsportalen](https://portal.azure.com/) för att skapa dina Azure Functions. Följ de här stegen:

1. Skapa en Azure-funktionsapp enligt beskrivningen på sidan [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Öppna den nya funktionsappen.
3. I appen går du till **Plattformsfunktioner -> Programinställningar**
4. Definiera följande [programinställningar](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **cm: Region** är regionens namn (utan blanksteg).
> Exempelvis **westeurope** i stället för Västeuropa och **westcentralus** i stället för västra centrala USA osv.
>

| Programinställning | Beskrivning   | 
| -------------------- |-------------|
| cm:TeamId   | Ditt team-ID för Content Moderator  | 
| cm:SubscriptionKey | Prenumerationsnyckeln för Content Moderator – Se [Autentiseringsuppgifter](review-tool-user-guide/credentials.md) | 
| cm:Region | Regionnamnet i Content Moderator utan blanksteg. Se föregående kommentar. |
| cm:ImageWorkflow | Namnet på arbetsflödet som ska köras på bilderna |
| cm:TextWorkflow | Namnet på arbetsflödet som ska köras på texten |
| cm:CallbackEndpoint | URL:en för den CMListener-funktionsapp som du skapar senare i den här guiden |
| fb:VerificationToken | Hemlig token, som även används till att prenumerera på händelser i Facebook-feeden |
| fb:PageAccessToken | Din åtkomsttoken för Facebooks Graph API upphör inte att gälla och kan dölja/ta bort inlägg åt dig. |

5. Skapa en ny funktion för **HttpTrigger-CSharp** med namnet **FBListener**. Den här funktionen tar emot händelser från Facebook. Skapa funktionen genom att följa dessa steg:

    1. Behåll sidan [Azure Functions Creation](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) (Skapa Azure Functions) öppen som referens.
    2. Klicka på **+** Lägg till för att skapa en ny funktion.
    3. I stället för de inbyggda mallarna väljer du alternativet **Get started on your own/custom function** (Kom igång med en egen/anpassad funktion).
    4. Klicka på panelen **HttpTrigger-CSharp**.
    5. Ange namnet **FBListener**. Fältet **Auktorisationsnivå** ska vara inställt på **Funktion**.
    6. Klicka på **Skapa**.
    7. Ersätt innehållet i **run.csx** med innehållet från [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Skapa en ny funktion för **HttpTrigger-CSharp** med namnet **CMListener**. Den här funktionen tar emot händelser från Content Moderator. Skapa funktionen genom att följa de här stegen.

    1. Behåll sidan [Azure Functions Creation](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) (Skapa Azure Functions) öppen som referens.
    2. Klicka på **+** Lägg till för att skapa en ny funktion.
    3. I stället för de inbyggda mallarna väljer du alternativet **Get started on your own/custom function** (Kom igång med en egen/anpassad funktion).
    4. Klicka på panelen **HttpTrigger-CSharp**
    5. Ange namnet **CMListener**. Fältet **Auktorisationsnivå** ska vara inställt på **Funktion**.
    6. Klicka på **Skapa**.
    7. Ersätt innehållet i **run.csx** med innehållet från [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sidan och appen
1. Skapa en Facebook-app.

    1. Gå till [Facebooks webbplats för utvecklare](https://developers.facebook.com/)
    2. Klicka på **Mina appar**.
    3. Lägg till en ny app.
    4. Välj **WebHooks -> Kom igång**
    5. Välj **Sida -> Subscribe to this topic** (Prenumerera på det här ämnet)
    6. Ange **FBListener Url** som motringnings-URL och den **verifieringstoken** som du konfigurerade i **Funktionsappinställningar**
    7. När prenumerationen är klar bläddrar du i flödet och väljer **prenumerera**.

2. Skapa en Facebook-sida.

    1. Gå till [Facebook](https://www.facebook.com/bookmarks/pages) och skapa en **ny Facebook-sida**.
    2. Ge Facebook-appen åtkomst till sidan genom att följa dessa steg:
        1. Gå till [Graph API Explorer](https://developers.facebook.com/tools/explorer/) (Graph API-utforskaren).
        2. Välj **Program**.
        3. Välj **Page Access Token** (Åtkomsttoken för sida), skicka en **Get**-begäran.
        4. Klicka på **Sid-ID** i svaret.
        5. Lägg nu till **/subscribed_apps** i URL:en och skicka en **Get**-begäran (tomt svar).
        6. Skicka en **Post**-begäran. Du får svaret **success: true**.

3. Skapa en åtkomsttoken för Graph API som inte upphör att gälla.

    1. Gå till [Graph API Explorer](https://developers.facebook.com/tools/explorer/) (Graph API-utforskaren).
    2. Välj alternativet**Program**.
    3. Välj alternativet **Get User Access Token** (Hämta åtkomsttoken för användare).
    4. Under **Välj behörigheter** väljer du **manage_pages** och **publish_pages**.
    5. Vi använder **åtkomsttoken** (kortlivad token) i nästa steg.

4. Vi använder Postman för kommande steg.

    1. Öppna **Postman** (eller hämta den [här](https://www.getpostman.com/)).
    2. Importera följande två filer:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Uppdatera följande miljövariabler:
    
    | Nyckel | Värde   | 
    | -------------------- |-------------|
    | appId   | Infoga din Facebook-appidentifierare här  | 
    | appSecret | Infoga din Facebook-apphemlighet här | 
    | short_lived_token | Infoga den kortlivade åtkomsttoken som du skapade i föregående steg |
    4. Kör nu de 3 API:er som visas i samlingen: 
        1. Välj **Generate Long-Lived Access Token** (Generera långlivad åtkomsttoken) och klicka på **Skicka**.
        2. Välj **Hämta användar-ID** och klicka på **Skicka**.
        3. Välj **Get Permanent Page Access Token** (Hämta permanent åtkomsttoken för sida) och klicka på **Skicka**.
    5. Kopiera värdet **access_token** från svaret och tilldela det till appinställningen **fb:PageAccessToken**.

Klart!

Lösningen skickar alla bilder och texter som publiceras på din Facebook-sida till Content Moderator. De arbetsflöden som du konfigurerade tidigare anropas. Det innehåll som inte uppfyller de kriterier som definierats i arbetsflödena leder till granskningar i granskningsverktyget. Resten av innehållet publiceras.

## <a name="license"></a>Licens

Alla SDK:er i Microsoft Cognitive Services och exempel är licensierade med MIT-licensen. Mer information finns i [LICENS](https://microsoft.mit-license.org/).

## <a name="next-steps"></a>Nästa steg

1. [Titta på en demo (video)](https://channel9.msdn.com/Events/Build/2017/T6033) av den här lösningen från Microsoft Build 2017.
1. [Facebook-exemplet på GitHub](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
