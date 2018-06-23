---
title: Facebook innehåll avbrottsmoderering med Azure innehåll kontrollant | Microsoft Docs
description: Måttlig Facebook-sidor med machine learning baserat innehåll kontrollant
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351867"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Facebook innehåll avbrottsmoderering med innehåll kontrollant

Vi Lär dig hur du använder machine-learning-baserat innehåll kontrollanten för att måttlig Facebook-inlägg och kommentarer i den här självstudiekursen.

Guiden leder dig genom stegen:

1. Skapa ett team innehåll kontrollanten.
2. Skapa Azure-funktioner som lyssnar efter http-händelser från innehåll kontrollant och Facebook.
3. Skapa en Facebook Page och App och ansluta till innehåll kontrollanten.

När vi har arbetat skickar Facebook innehåll som anslagits av besökare till innehåll kontrollanten. Baserat på tröskelvärdena som matchar dina innehåll kontrollant arbetsflöden publicera innehållet eller skapa granskningar i verktyget granska. 

Följande bild visar byggblocken i lösningen.

![Moderering av Facebook-inlägg](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Skapa ett team av innehåll kontrollant

Referera till den [Quickstart](quick-start.md) sidan om du vill registrera dig för innehåll kontrollant och skapa ett team.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurera bildarbetsflöde avbrottsmoderering (tröskel)

Referera till den [arbetsflöden](review-tool-user-guide/workflows.md) sidan om du vill konfigurera en anpassad avbildning arbetsflödet (tröskelvärde). Observera arbetsflödet **namn**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurera text avbrottsmoderering arbetsflödet (tröskel)

Med åtgärder som den [arbetsflöden](review-tool-user-guide/workflows.md) att konfigurera en egen text tröskelvärde och arbetsflöde. Observera arbetsflödet **namn**.

![Konfigurera arbetsflöde för Text](images/text-workflow-configure.PNG)

Testa arbetsflödet med hjälp av knappen ”Kör arbetsflöde”.

![Testa arbetsflödet Text](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Skapa Azure Functions

Logga in på den [Azure-hanteringsportalen](https://portal.azure.com/) att skapa Azure Functions. Följ de här stegen:

1. Skapa en App för Azure-funktion som visas i den [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) sidan.
2. Öppna den nyligen skapade Funktionsapp.
3. I appen, navigera till **funktioner -> programinställningar**
4. Definiera följande [programinställningar](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> Den **cm: Region** ska vara namnet på regionen (utan blanksteg).
> Till exempel **westeurope**, inte Västeuropa **westcentralus**, inte Väst centrala USA och så vidare.
>

| Appinställningen | Beskrivning   | 
| -------------------- |-------------|
| cm:TeamId   | Ditt innehåll kontrollant TeamId  | 
| cm:SubscriptionKey | Din nyckel för innehåll kontrollant prenumeration - finns [autentiseringsuppgifter](/review-tool-user-guide/credentials.md) | 
| cm:region | Ditt innehåll kontrollant regionnamn, utan utrymmen. Se föregående. |
| cm:ImageWorkflow | Namnet på arbetsflödet ska köras på bilder |
| cm:TextWorkflow | Namnet på arbetsflödet ska köras på Text |
| cm:CallbackEndpoint | URL för CMListener funktionen appen som du skapar senare i den här guiden |
| FB:VerificationToken | Den hemliga token används också för att prenumerera på Facebook feed händelser |
| FB:PageAccessToken | Facebook graph api åtkomst-token upphör inte och kan funktionen Dölj/ta bort tjänster å dina vägnar. |

5. Skapa en ny **HttpTrigger CSharp** funktion med namnet **FBListener**. Den här funktionen tar emot händelser från Facebook. Skapa den här funktionen genom att följa dessa steg:

    1. Behåll den [Azure Functions skapa](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) öppna referens.
    2. Klicka på den **+** Lägg till om du vill skapa nya funktionen.
    3. I stället för de inbyggda mallarna, Välj den **komma igång med din egen anpassade/funktion** alternativet.
    4. Klicka på panelen som säger **HttpTrigger CSharp**.
    5. Ange namnet **FBListener**. Den **åtkomstnivå** fält ska anges **funktionen**.
    6. Klicka på **Skapa**.
    7. Ersätt innehållet i den **run.csx** med innehållet från [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Skapa en ny **HttpTrigger CSharp** funktion med namnet **CMListener**. Den här funktionen tar emot händelser från Facebook. Följ dessa steg om du vill skapa den här funktionen.

    1. Behåll den [Azure Functions skapa](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) öppna referens.
    2. Klicka på den **+** Lägg till om du vill skapa nya funktionen.
    3. I stället för de inbyggda mallarna, Välj den **komma igång med din egen anpassade/funktion** alternativet.
    4. Klicka på panelen som säger **HttpTrigger CSharp**
    5. Ange namnet **CMListener**. Den **åtkomstnivå** fält ska anges **funktionen**.
    6. Klicka på **Skapa**.
    7. Ersätt innehållet i den **run.csx** med innehållet från [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sida och App
1. Skapa en Facebook-App.

    1. Navigera till den [Facebook developer plats](https://developers.facebook.com/)
    2. Klicka på **Mina appar**.
    3. Lägg till en ny App.
    4. Välj **Webhooks -> få igång**
    5. Välj **sida -> prenumerera till det här avsnittet**
    6. Ange den **FBListener Url** som återanrop URL och **verifiera Token** du konfigurerade den **funktionen App-inställningar**
    7. När du prenumererar på, rulla feeden och välj **prenumerera**.

2. Skapa en Facebook-sida.

    1. Gå till [Facebook](https://www.facebook.com/bookmarks/pages) och skapa en **nya Facebook Page**.
    2. Tillåt Facebook-App åtkomst till den här sidan genom att följa dessa steg:
        1. Navigera till den [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Välj **programmet**.
        3. Välj **sidan åtkomst-Token**, skicka ett **hämta** begäran.
        4. Klicka på den **sid-ID** i svaret.
        5. Lägg nu till den **/subscribed_apps** till URL och skicka en **hämta** (tomt svar returnerades) begäran.
        6. Skicka en **Post** begäran. Du får svar som **lyckades: true**.

3. Skapa en obegränsade Graph API-åtkomsttoken.

    1. Navigera till den [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Välj den **programmet** alternativet.
    3. Välj den **får användaren åtkomst-Token** alternativet.
    4. Under den **Välj behörigheter**väljer **manage_pages** och **publish_pages** alternativ.
    5. Vi använder den **åtkomsttoken** (kort livslängd Token) i nästa steg.

4. Vi använder Postman för följande steg.

    1. Öppna **Postman** (eller hämta den [här](https://www.getpostman.com/)).
    2. Importera dessa två filer:
        1. [Postman samling](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman miljö](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Uppdatera de här miljövariablerna:
    
    | Nyckel | Värde   | 
    | -------------------- |-------------|
    | appId   | Infoga dina Facebook App identifieraren här  | 
    | appSecret | Infoga Facebook appens hemlighet här | 
    | short_lived_token | Infoga kort livslängd åtkomsttoken som du genererade i föregående steg |
    4. Kör nu av 3 API: er som anges i samlingen: 
        1. Välj **generera Long-Lived åtkomst-Token** och på **skicka**.
        2. Välj **hämta användar-ID** och på **skicka**.
        3. Välj **hämta Permanent sidan åtkomst-Token** och på **skicka**.
    5. Kopiera den **access_token** värde från svaret och tilldela appinställningen **fb:PageAccessToken**.

Klart!

Lösningen skickar alla bilder och text som anslås på Facebook-sidan innehåll kontrollanten. De arbetsflöden som du tidigare har konfigurerat anropas. Innehåll som inte uppfyller dina kriterier som definierats i resultaten för arbetsflöden i granskningar i verktyget granska. Resten av innehållet publiceras.

## <a name="license"></a>Licens

Alla Microsoft kognitiva Services SDK: er och prover licensieras med MIT-licensen. Mer information finns i [licens](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Utvecklarens regler för uppförande

Utvecklare som använder kognitiva tjänster, inklusive den här klientbiblioteket & exemplet förväntas följa ”Developer koden för genomföra för Microsoft kognitiva Services”, finns på http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Nästa steg

1. [Titta på en demo (video)](https://channel9.msdn.com/Events/Build/2017/T6033) av den här lösningen från Microsoft Build 2017.
1. [Facebook-exempel på Github](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
