---
title: 'Handledning: Måttligt Facebook-innehåll - Innehållsmoderator'
titleSuffix: Azure Cognitive Services
description: I självstudien lär du dig att använda maskininlärningsbaserade Content Moderator till att moderera Facebook-inlägg och kommentarer.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 9f25c845302d62e3bc9e230b4a6f8f2669f4ac35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774273"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Självstudiekurs: Moderera Facebook-inlägg och kommandon med Azure Content Moderator

I den här självstudien får du lära dig hur du använder Azure Content Moderator för att moderera inlägg och kommentarer på en Facebook-sida. Facebook skickar innehållet som publiceras av besökare till contentmoderatortjänsten. Då publicerar dina content moderator-arbetsflöden antingen innehållet eller skapar recensioner i granskningsverktyget, beroende på innehållspoäng och tröskelvärden. Se [demovideon Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) för ett fungerande exempel på det här scenariot.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Skapa ett Content Moderator-team.
> * Skapa Azure Functions som lyssnar efter HTTP-händelser från Content Moderator och Facebook.
> * Länka en Facebook-sida till Innehållsmoderator med hjälp av ett Facebook-program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Det här diagrammet illustrerar varje komponent i det här scenariot:

![Diagram över Content Moderator ta emot information från Facebook via "FBListener" och skicka information via "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> Under 2018 implementerade Facebook en striktare granskningspolicy för Facebook Apps. Du kommer inte att kunna slutföra stegen i den här självstudien om din app inte har granskats och godkänts av Facebooks granskningsteam.

## <a name="prerequisites"></a>Krav

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- Ett [Facebook-konto](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Se [snabbstarten prova innehållsrekomtorn på webben](quick-start.md) för instruktioner om hur du registrerar dig för granskningsverktyget för [innehållsmoderatorer](https://contentmoderator.cognitive.microsoft.com/) och skapar ett granskningsteam. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="configure-image-moderation-workflow"></a>Konfigurera arbetsflöde för bildmoderering

Se guiden [Definiera, testa och använda arbetsflöden](review-tool-user-guide/workflows.md) för att skapa ett anpassat avbildningsarbetsflöde. Innehållsmoderator använder det här arbetsflödet för att automatiskt kontrollera bilder på Facebook och skicka några till granskningsverktyget. Ta del av **arbetsflödesnamnet**.

## <a name="configure-text-moderation-workflow"></a>Konfigurera arbetsflöde för textmoderering

Se återigen [guiden Definiera, testa och använda arbetsflöden.](review-tool-user-guide/workflows.md) den här gången skapar du ett anpassat textarbetsflöde. Content Moderator kommer att använda detta arbetsflöde för att automatiskt kontrollera textinnehåll. Ta del av **arbetsflödesnamnet**.

![Konfigurera textarbetsflöden](images/text-workflow-configure.PNG)

Testa arbetsflödet med knappen **Kör arbetsflöde.**

![Testa textarbetsflödet](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Skapa Azure Functions

Logga in på [Azure-portalen](https://portal.azure.com/) och gör så här:

1. Skapa en Azure-funktionsapp enligt beskrivningen på sidan [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Gå till den nyskapade funktionsappen.
1. Gå till fliken **Plattformsfunktioner** i appen och välj **Konfiguration**. I avsnittet **Programinställningar** på nästa sida väljer du **Ny programinställning** för att lägga till följande nyckel-/värdepar:
    
    | Namn på appinställning | värde   | 
    | -------------------- |-------------|
    | cm:TeamId   | Ditt team-ID för Content Moderator  | 
    | cm:SubscriptionKey | Prenumerationsnyckeln för Content Moderator – Se [Autentiseringsuppgifter](review-tool-user-guide/credentials.md) |
    | cm:Region | Regionnamnet i Content Moderator utan blanksteg. Du hittar detta i fältet **Plats** på fliken **Översikt för** din Azure-resurs.|
    | cm:ImageWorkflow | Namnet på arbetsflödet som ska köras på bilderna |
    | cm:TextWorkflow | Namnet på arbetsflödet som ska köras på texten |
    | cm:CallbackEndpoint | Url för CMListener Funktion App som du kommer att skapa senare i den här guiden |
    | fb:VerificationToken | En hemlig token som du skapar, som används för att prenumerera på Facebook-flödeshändelserna |
    | fb:PageAccessToken | Din åtkomsttoken för Facebooks Graph API upphör inte att gälla och kan dölja/ta bort inlägg åt dig. Du kommer att få denna token vid ett senare steg. |

    Klicka på knappen **Spara** högst upp på sidan.

1. Gå tillbaka till fliken **Plattformsfunktioner.** Använd **+** knappen i den vänstra rutan för att visa fönstret **Nytt funktion.** Den funktion du ska skapa får händelser från Facebook.

    ![Azure Functions-fönstret med knappen Lägg till funktion markerad.](images/new-function.png)

    1. Klicka på panelen som säger **Http-utlösare**.
    1. Ange namnet **FBListener**. Fältet **Auktorisationsnivå** ska vara inställt på **Funktion**.
    1. Klicka på **Skapa**.
    1. Ersätt innehållet i **run.csx** med innehållet från **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Skapa en ny **Http-utlösarfunktion** med namnet **CMListener**. Den här funktionen tar emot händelser från Content Moderator. Ersätt innehållet i **run.csx** med innehållet från **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sidan och appen

1. Skapa en Facebook-app.

    ![facebook-utvecklarsida](images/facebook-developer-app.png)

    1. Gå till [Facebooks webbplats för utvecklare](https://developers.facebook.com/)
    1. Klicka på **Mina appar**.
    1. Lägg till en ny app.
    1. namnge det något
    1. Välj **Webhooks -> Konfigurera**
    1. Välj **Sida** på rullgardinsmenyn och välj **Prenumerera på det här objektet**
    1. Ange **FBListener Url** som motringnings-URL och den **verifieringstoken** som du konfigurerade i **Funktionsappinställningar**
    1. När prenumerationen är klar bläddrar du i flödet och väljer **prenumerera**.
    1. Klicka på **knappen Testa** i **flödesraden** för att skicka ett testmeddelande till din FBListener Azure-funktion och tryck sedan på knappen Skicka till min **server.** Du bör se begäran som tas emot på din FBListener.

1. Skapa en Facebook-sida.

    > [!IMPORTANT]
    > Under 2018 implementerade Facebook en striktare granskning av Facebook-appar. Du kommer inte att kunna köra avsnitten 2, 3 och 4 om din app inte har granskats och godkänts av Facebooks granskningsteam.

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

Lösningen skickar alla bilder och texter som publiceras på din Facebook-sida till Content Moderator. Sedan anropas de arbetsflöden som du har konfigurerat tidigare. Innehållet som inte klarar dina kriterier som definierats i arbetsflödena skickas till granskningar inom granskningsverktyget. Resten av innehållet publiceras automatiskt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du ett program för att analysera produktbilder i syfte att tagga dem via produkttyp och låta ett granskningsteam fatta välgrundade beslut om innehållsmoderering. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Bildmoderering](./image-moderation-api.md)
