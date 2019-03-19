---
title: 'Självstudier: Moderate Facebook content - Content Moderator'
titlesuffix: Azure Cognitive Services
description: I självstudien lär du dig att använda maskininlärningsbaserade Content Moderator till att moderera Facebook-inlägg och kommentarer.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: pafarley
ms.openlocfilehash: 662eca2a727f3112f169ab8d669bf18c81700275
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57871036"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Självstudier: Måttlig Facebook-inlägg och kommandon med Azure Content Moderator

I den här självstudien får du lära dig hur du använder Azure Content Moderator för att ändra inlägg och kommentarer om en Facebook-sida. Facebook skickar det innehåll som publicerats av besökare Content Moderator-tjänsten. Sedan ska Content Moderator-arbetsflöden publicera innehållet eller skapa granskningar inom granskningsverktyget, beroende på innehållet poäng och tröskelvärden. Se den [Build 2017 demonstrationsvideon](https://channel9.msdn.com/Events/Build/2017/T6033) för ett exempel på det här scenariot.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Skapa ett Content Moderator-team.
> * Skapa Azure Functions som lyssnar efter HTTP-händelser från Content Moderator och Facebook.
> * Länka en Facebook-sida till Content Moderator med hjälp av en Facebook-programmet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Det här diagrammet visar varje komponent i det här scenariot:

![Diagram över Content Moderator skicka information via ”CMListener” och ta emot information från Facebook via ”FBListener”](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Förutsättningar

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- En [Facebook-konto](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Referera till den [försök Content Moderator på webben](quick-start.md) Snabbstart för instruktioner om hur du registrerar dig för den [Content Moderator granskar verktyget](https://contentmoderator.cognitive.microsoft.com/) och skapa en granskningsteam. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="configure-image-moderation-workflow"></a>Konfigurera bildarbetsflöde för moderering

Referera till den [definiera, testa och använda arbetsflöden](review-tool-user-guide/workflows.md) guide om hur du skapar en anpassad avbildning-arbetsflöde. Detta gör att Content Moderator automatiskt kontrollera bilder på Facebook och skicka vissa till granskningsverktyget. Ta del av arbetsflödet **namn**.

## <a name="configure-text-moderation-workflow"></a>Konfigurera arbetsflöde för moderering av text

Igen, referera till den [definiera, testa och använda arbetsflöden](review-tool-user-guide/workflows.md) guide; nu skapa en anpassad text-arbetsflöde. Detta gör att Content Moderator att automatiskt kontrollera textinnehåll. Ta del av arbetsflödet **namn**.

![Konfigurera textarbetsflöden](images/text-workflow-configure.PNG)

Testa ditt arbetsflöde med hjälp av den **köra arbetsflödet** knappen.

![Testa textarbetsflödet](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Skapa Azure Functions

Logga in på den [Azure-portalen](https://portal.azure.com/) och gör följande:

1. Skapa en Azure-funktionsapp enligt beskrivningen på sidan [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Gå till den nya Funktionsappen.
3. I appen, går du till den **plattformsfunktioner** fliken och markera **programinställningar**. I den **programinställningar** avsnittet på nästa sida, bläddra längst ned i listan och klicka på **Lägg till ny inställning**. Lägg till följande nyckel/värde-par
    
    | App inställningsnamn | värde   | 
    | -------------------- |-------------|
    | cm:TeamId   | Ditt team-ID för Content Moderator  | 
    | cm:SubscriptionKey | Prenumerationsnyckeln för Content Moderator – Se [Autentiseringsuppgifter](review-tool-user-guide/credentials.md) | 
    | cm:Region | Regionnamnet i Content Moderator utan blanksteg. Se föregående kommentar. |
    | cm:ImageWorkflow | Namnet på arbetsflödet som ska köras på bilderna |
    | cm:TextWorkflow | Namnet på arbetsflödet som ska köras på texten |
    | cm:CallbackEndpoint | URL:en för den CMListener-funktionsapp som du skapar senare i den här guiden |
    | fb:VerificationToken | Hemlig token, som även används till att prenumerera på händelser i Facebook-feeden |
    | fb:PageAccessToken | Din åtkomsttoken för Facebooks Graph API upphör inte att gälla och kan dölja/ta bort inlägg åt dig. |

    Klicka på den **spara** längst upp på sidan.

1. Med hjälp av den **+** knappen i det vänstra fönstret, visas den nya funktion-rutan.

    ![Azure Functions-rutan med knappen Lägg till funktion är markerad.](images/new-function.png)

    Klicka sedan på **+ ny funktion** överst på sidan. Den här funktionen tar emot händelser från Facebook. Skapa funktionen genom att följa dessa steg:

    1. Klicka på panelen som säger **Http-utlösare**.
    1. Ange namnet **FBListener**. Fältet **Auktorisationsnivå** ska vara inställt på **Funktion**.
    1. Klicka på **Skapa**.
    1. Ersätt innehållet i den **run.csx** med innehållet från **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-160)]

1. Skapa en ny **Http-utlösare** funktion med namnet **CMListener**. Den här funktionen tar emot händelser från Content Moderator. Ersätt innehållet i den **run.csx** med innehållet från **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-106)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sidan och appen
1. Skapa en Facebook-app.

    ![Facebook-sida för utvecklare](images/facebook-developer-app.png)

    1. Gå till [Facebooks webbplats för utvecklare](https://developers.facebook.com/)
    2. Klicka på **Mina appar**.
    3. Lägg till en ny app.
    1. ger det något namn
    1. Välj **Webhooks -> Set upp**
    1. Välj **sidan** i den nedrullningsbara menyn och välj **prenumerera på det här objektet**
    1. Ange **FBListener Url** som motringnings-URL och den **verifieringstoken** som du konfigurerade i **Funktionsappinställningar**
    1. När prenumerationen är klar bläddrar du i flödet och väljer **prenumerera**.

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

Lösningen skickar alla bilder och texter som publiceras på din Facebook-sida till Content Moderator. Sedan anropas de arbetsflöden som du konfigurerade tidigare. Hämtar innehållet som inte uppfyller dina kriterier som definierats i arbetsflöden som skickas till recensioner i granskningsverktyget. Resten av innehållet publiceras automatiskt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du ett program för att analysera produktbilder i syfte att tagga dem via produkttyp och låta ett granskningsteam fatta välgrundade beslut om innehållsmoderering. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Bildmoderering](./image-moderation-api.md)
