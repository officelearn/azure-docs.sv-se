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
ms.openlocfilehash: 5d31285ca305ba7fefdf31b4a97e3183f58b3e3b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233805"
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

> [!IMPORTANT]
> I 2018 implementerat Facebook en striktare veta Facebook-appar. Du kommer inte att kunna slutföra stegen i den här självstudien om din app inte har granskas och godkänns av granskningsteamet Facebook.

## <a name="prerequisites"></a>Nödvändiga komponenter

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
1. Gå till den nya Funktionsappen.
1. I appen, går du till den **plattformsfunktioner** fliken och markera **Configuration**. I den **programinställningar** avsnittet på nästa sida väljer **nya programinställning** att lägga till följande nyckel/värde-par:
    
    | App inställningsnamn | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | Ditt team-ID för Content Moderator  | 
    | cm:SubscriptionKey | Prenumerationsnyckeln för Content Moderator – Se [Autentiseringsuppgifter](review-tool-user-guide/credentials.md) |
    | cm:Region | Regionnamnet i Content Moderator utan blanksteg. |
    | cm:ImageWorkflow | Namnet på arbetsflödet som ska köras på bilderna |
    | cm:TextWorkflow | Namnet på arbetsflödet som ska köras på texten |
    | cm:CallbackEndpoint | URL: en för CMListener Funktionsappen som skapas senare i den här guiden |
    | fb:VerificationToken | En hemlig token som du skapar, som används för att prenumerera på Facebook feed-händelser |
    | fb:PageAccessToken | Din åtkomsttoken för Facebooks Graph API upphör inte att gälla och kan dölja/ta bort inlägg åt dig. Du får detta i ett senare skede. |

    Klicka på den **spara** längst upp på sidan.

1. Gå tillbaka till den **plattformsfunktioner** fliken. Använd den **+** knappen i det vänstra fönstret för att ta fram den **ny funktion** fönstret. Du håller på att skapa funktionen visas händelser från Facebook.

    ![Azure Functions-rutan med knappen Lägg till funktion är markerad.](images/new-function.png)

    1. Klicka på panelen som säger **Http-utlösare**.
    1. Ange namnet **FBListener**. Fältet **Auktorisationsnivå** ska vara inställt på **Funktion**.
    1. Klicka på **Skapa**.
    1. Ersätt innehållet i den **run.csx** med innehållet från **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Skapa en ny **Http-utlösare** funktion med namnet **CMListener**. Den här funktionen tar emot händelser från Content Moderator. Ersätt innehållet i den **run.csx** med innehållet från **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sidan och appen

1. Skapa en Facebook-app.

    ![Facebook-sida för utvecklare](images/facebook-developer-app.png)

    1. Gå till [Facebooks webbplats för utvecklare](https://developers.facebook.com/)
    1. Klicka på **Mina appar**.
    1. Lägg till en ny app.
    1. ger det något namn
    1. Välj **Webhooks -> Set upp**
    1. Välj **sidan** i den nedrullningsbara menyn och välj **prenumerera på det här objektet**
    1. Ange **FBListener Url** som motringnings-URL och den **verifieringstoken** som du konfigurerade i **Funktionsappinställningar**
    1. När prenumerationen är klar bläddrar du i flödet och väljer **prenumerera**.
    1. Klicka på den **testa** knappen av den **feed** rad för att skicka ett testmeddelande till din FBListener Azure-funktion och välj sedan den **skicka till min Server** knappen. Du bör se begäran tas emot på din FBListener.

1. Skapa en Facebook-sida.

    > [!IMPORTANT]
    > I 2018 implementerat Facebook en striktare veta Facebook-appar. Du kommer inte att kunna köra avsnitt 2, 3 och 4 om din app inte har granskas och godkänns av granskningsteamet Facebook.

    1. Gå till [Facebook](https://www.facebook.com/bookmarks/pages) och skapa en **ny Facebook-sida**.
    1. Ge Facebook-appen åtkomst till sidan genom att följa dessa steg:
        1. Gå till [Graph API Explorer](https://developers.facebook.com/tools/explorer/) (Graph API-utforskaren).
        1. Välj **Program**.
        1. Välj **Page Access Token** (Åtkomsttoken för sida), skicka en **Get**-begäran.
        1. Klicka på **Sid-ID** i svaret.
        1. Lägg nu till **/subscribed_apps** i URL:en och skicka en **Get**-begäran (tomt svar).
        1. Skicka en **Post**-begäran. Du får svaret **success: true**.

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
