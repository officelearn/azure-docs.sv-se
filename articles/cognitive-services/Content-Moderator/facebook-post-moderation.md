---
title: Facebook innehållsmoderering med Azure Content Moderator | Microsoft Docs
description: Måttlig Facebook-sidor med maskininlärning baserad Content Moderator
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 4570b514bef33c8dfa6d220ee4cd88ad068ba4d0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "41987982"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Facebook innehållsmoderering med Content Moderator

Vi Lär dig hur du använder machine-learning-baserade Content Moderator för att måttlig Facebook-inlägg och kommentarer i de här självstudierna.

Kursen vägleder dig genom de här stegen:

1. Skapa ett Content Moderator-team.
2. Skapa Azure Functions som lyssnar efter HTTP-händelser från Content Moderator och Facebook.
3. Skapa en Facebook Page och appen och ansluter den till Content Moderator.

När vi är klar skickar Facebook innehåll som publicerats av besökarna Content Moderator. Baserat på tröskelvärdena som matchar dina arbetsflöden för Content Moderator publicera innehållet eller skapa granskningar inom granskningsverktyget. 

Följande bild visar byggstenarna i lösningen.

![Moderering av Facebook-inlägg](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Skapa ett team av Content Moderator

Referera till den [snabbstarten](quick-start.md) sidan för att registrera dig för Content Moderator och skapa ett team.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurera bildarbetsflöde moderering (threshold)

Referera till den [arbetsflöden](review-tool-user-guide/workflows.md) att konfigurera en anpassad avbildning arbetsflöde (tröskelvärde). Observera arbetsflödet **namn**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurera arbetsflöde för moderering av text (threshold)

Med åtgärder som den [arbetsflöden](review-tool-user-guide/workflows.md) att konfigurera ett arbetsflöde och anpassad text tröskelvärdet. Observera arbetsflödet **namn**.

![Konfigurera arbetsflöde för Text](images/text-workflow-configure.PNG)

Testa ditt arbetsflöde med hjälp av knappen ”Kör arbetsflöde”.

![Testa arbetsflödet för Text](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Skapa Azure Functions

Logga in på den [Azure-hanteringsportalen](https://portal.azure.com/) att skapa dina Azure-funktioner. Följ de här stegen:

1. Skapa en Azure Function-App som du ser på den [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) sidan.
2. Öppna den nya Funktionsappen.
3. Navigera till i appen, **funktioner -> programinställningar**
4. Definiera följande [programinställningar](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> Den **cm: Region** bör vara namnet på regionen (utan blanksteg).
> Till exempel **westeurope**, inte Västeuropa **westcentralus**, inte västra centrala USA och så vidare.
>

| App-inställning | Beskrivning   | 
| -------------------- |-------------|
| cm:TeamId   | Content Moderator-TeamId  | 
| cm:SubscriptionKey | Prenumerationsnyckeln Content Moderator - Se [autentiseringsuppgifter](/review-tool-user-guide/credentials.md) | 
| cm:region | Content Moderator region namnet på din utan blankstegen. Se föregående kommentar. |
| cm:ImageWorkflow | Namnet på arbetsflödet ska köras på avbildningar |
| cm:TextWorkflow | Namnet på arbetsflödet ska köras på Text |
| cm:CallbackEndpoint | URL: en för CMListener Funktionsappen som du skapar senare i den här guiden |
| FB:VerificationToken | Hemlig token, används också för att prenumerera på Facebook feed-händelser |
| FB:PageAccessToken | Facebook graph api-åtkomsttoken upphör att gälla inte och tillåter funktionen Dölj/ta bort inlägg på din räkning. |

5. Skapa en ny **HttpTrigger-CSharp** funktion med namnet **FBListener**. Den här funktionen tar emot händelser från Facebook. Skapa den här funktionen genom att följa dessa steg:

    1. Behåll den [Azure Functions skapa](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) öppen referens.
    2. Klicka på den **+** Lägg till för att skapa ny funktion.
    3. I stället för de inbyggda mallarna, Välj den **komma igång med din egen/anpassad funktion** alternativet.
    4. Klicka på panelen som säger **HttpTrigger-CSharp**.
    5. Ange namnet **FBListener**. Den **Auktorisationsnivå** fältet ska vara inställd på **funktionen**.
    6. Klicka på **Skapa**.
    7. Ersätt innehållet i den **run.csx** med innehållet från [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Skapa en ny **HttpTrigger-CSharp** funktion med namnet **CMListener**. Den här funktionen tar emot händelser från Content Moderator. Följ stegen nedan för att skapa den här funktionen.

    1. Behåll den [Azure Functions skapa](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) öppen referens.
    2. Klicka på den **+** Lägg till för att skapa ny funktion.
    3. I stället för de inbyggda mallarna, Välj den **komma igång med din egen/anpassad funktion** alternativet.
    4. Klicka på panelen som säger **HttpTrigger-c#**
    5. Ange namnet **CMListener**. Den **Auktorisationsnivå** fältet ska vara inställd på **funktionen**.
    6. Klicka på **Skapa**.
    7. Ersätt innehållet i den **run.csx** med innehållet från [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sida och App
1. Skapa en Facebook-App.

    1. Navigera till den [Facebook-utvecklarwebbplatsen](https://developers.facebook.com/)
    2. Klicka på **Mina appar**.
    3. Lägg till en ny App.
    4. Välj **Webhooks -> Get igång**
    5. Välj **sidan -> prenumerera på det här ämnet**
    6. Ange den **FBListener Url** som Motringnings-URL och **verifiera Token** du konfigurerade den **Funktionsappinställningar**
    7. När du prenumererar på, rulla flöde och välj **prenumerera**.

2. Skapa en Facebook-sida.

    1. Gå till [Facebook](https://www.facebook.com/bookmarks/pages) och skapa en **nya Facebook Page**.
    2. Tillåt Facebook appen åtkomst till den här sidan genom att följa dessa steg:
        1. Navigera till den [Graph API-Utforskaren](https://developers.facebook.com/tools/explorer/).
        2. Välj **program**.
        3. Välj **sidan åtkomsttoken**, skicka en **hämta** begäran.
        4. Klicka på den **sid-ID** i svaret.
        5. Lägg nu till den **/subscribed_apps** till URL och skicka en **hämta** (tomt svar) begäran.
        6. Skicka en **Post** begäran. Du får svar som **lyckades: SANT**.

3. Skapa en åtkomsttoken för obegränsade Graph API.

    1. Navigera till den [Graph API-Utforskaren](https://developers.facebook.com/tools/explorer/).
    2. Välj den **program** alternativet.
    3. Välj den **får användaren åtkomsttoken** alternativet.
    4. Under den **Select-behörigheter**väljer **manage_pages** och **publish_pages** alternativ.
    5. Vi använder den **åtkomsttoken** (kort bott Token) i nästa steg.

4. Vi använder Postman för de efterföljande stegen.

    1. Öppna **Postman** (eller hämta den [här](https://www.getpostman.com/)).
    2. Importera dessa två filer:
        1. [Postman-samling](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman-miljö](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Uppdatera dessa miljövariabler:
    
    | Nyckel | Värde   | 
    | -------------------- |-------------|
    | appId   | Infoga dina Facebook App identifieraren här  | 
    | appSecret | Infoga här appens Facebook-hemlighet | 
    | short_lived_token | Infoga kort livslängd åtkomsttoken som du genererade i föregående steg |
    4. Kör nu 3 API: er som visas i samlingen: 
        1. Välj **generera Long-Lived åtkomsttoken** och klicka på **skicka**.
        2. Välj **hämta användar-ID** och klicka på **skicka**.
        3. Välj **hämta åtkomsttoken för Permanent sidan** och klicka på **skicka**.
    5. Kopiera den **access_token** från svaret och tilldela den till App-inställning **fb:PageAccessToken**.

Klart!

Lösningen skickar alla bilder och text som publiceras på din Facebook-sida på Content Moderator. De arbetsflöden som du konfigurerade tidigare anropas. Det innehåll som inte uppfyller dina kriterier som definierats i arbetsflöden leder till recensioner i granskningsverktyget. Resten av innehållet publiceras.

## <a name="license"></a>Licens

Alla Microsoft Cognitive Services SDK: er och exempel har en licens för MIT-licensen. Mer information finns i [licens](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Utvecklarens regler för uppförande

Utvecklare som använder kognitiva tjänster, inklusive det här klientbiblioteket & exemplet förväntas följa den ”Developer kod av genomför för Microsoft Cognitive Services”, finns på http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Nästa steg

1. [Titta på en demo (video)](https://channel9.msdn.com/Events/Build/2017/T6033) av den här lösningen från Microsoft Build 2017.
1. [Facebook-exemplet på Github](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
