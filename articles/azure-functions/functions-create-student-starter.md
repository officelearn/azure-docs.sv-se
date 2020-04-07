---
title: Skapa en funktion med Startmotorn Azure för studenter
description: Lär dig hur du skapar en Azure-funktion från en Azure for Student Starter-prenumeration
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756470"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Skapa en funktion med Startmotorn Azure för studenter

I den här självstudien skapar vi en HTTP-funktion för "hello world" i en Azure for Students Starter-prenumeration. Vi går också igenom vad som är tillgängligt i Azure Functions i den här prenumerationstypen.

Microsoft *Azure för studenter Starter* får dig igång med De Azure-produkter som du behöver utveckla i molnet utan kostnad för dig. [Läs mer om erbjudandet här.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. [Läs mer om Funktioner här.](./functions-overview.md)

## <a name="create-a-function"></a>Skapa en funktion

 I det här avsnittet lär du dig hur du använder Funktioner för att skapa en HTTP-utlöst "hello world"-funktion i Azure-portalen.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en funktionsapp kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Välj knappen **Skapa en resurs** som finns i det övre vänstra hörnet av Azure-portalen. Välj sedan > **Beräkningsfunktionsapp**. **Compute**

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
   | **[App Service Plan/Plats](./functions-scale.md)** | Ny | Värdplanen som styr vilken region din funktionsapp distribueras till och tätheten av dina resurser. Flera funktionsappar som distribueras till samma plan delar alla samma enda kostnadsfria instans. Detta är en begränsning av Student Starter planen. Den fullständiga hosting alternativ [förklaras här.](./functions-scale.md)|
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled | Application Insights används för att lagra och analysera funktionsappens loggar. Den är aktiverad som standard om du väljer en plats som stöder Application Insights. Application Insights kan aktiveras för alla funktioner genom att manuellt välja en närliggande region för att distribuera Application Insights. Utan Application Insights kan du bara visa livestreamingloggar.

3. Välj **App Service-plan/plats** ovan om du vill välja en annan plats

4. Välj **Skapa nytt** och ge sedan din plan ett unikt namn.

5. Välj den plats som ligger närmast dig. [Se en fullständig karta över Azure-regioner här.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Välj **Skapa** för att etablera och distribuera funktionsappen.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/functions-create-student-starter/function-app-create-notification.png)

8. Välj **Gå till resurs** att visa den nya funktionsappen.

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Skapa en HTTP-utlöst funktion

1. Expandera **+** den nya funktionsappen och välj sedan knappen bredvid **Funktioner,** välj **I-portalen**och välj **Fortsätt**.

    ![Functions-snabbstart, välj plattform.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Välj **WebHook + API** och välj sedan **Skapa**.

    ![Snabbstart för funktioner i Azure Portal.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

En funktion skapas med hjälp av en språkspecifik mall för en HTTP-utlöst funktion.

Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen klickar du på **</> Hämta funktionswebbadress** längst upp till höger och väljer **Standard (funktionsnyckel)**. Sedan klickar du på **Kopiera**. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<yourname>` i slutet av den här webbadressen och tryck på knappen `Enter` på tangentbordet för att utföra begäran. Du bör se svaret som returnerades av funktionen som visas i webbläsaren.  

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

3. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill visa spårningsinformationen från föregående körning återgår du till funktionen i portalen och klickar på pilen längst ned på skärmen så att **Loggar** expanderas.

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Funktioner som stöds i Azure for Students Starter

I Azure for Students Starter har du tillgång till de flesta av funktionerna i Azure Functions-körningen, med flera viktiga begränsningar som anges nedan:

* HTTP-utlösaren är den enda utlösartyp som stöds.
    * Alla ingångar och alla utdatabindningar stöds! [Se hela listan här.](functions-triggers-bindings.md)
* Språk som stöds: 
    * C# (.NET Kärna 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Kärna 2)
    * [Se språk som stöds i högre planer här](supported-languages.md)
* Windows är det enda operativsystem som stöds.
* Skalan är begränsad till [en kostnadsfri nivåinstans](https://azure.microsoft.com/pricing/details/app-service/windows/) som körs i upp till 60 minuter varje dag. Du skalar automatiskt från 0 till 1 instans automatiskt när HTTP-trafik tas emot, men inte längre.
* Endast [version 2.x och senare](functions-versions.md) av funktionskörningen stöds.
* Alla utvecklarverktyg stöds för redigerings- och publiceringsfunktioner. Detta inkluderar VS-kod, Visual Studio, Azure CLI och Azure-portalen. Om du vill använda något annat än portalen måste du först skapa en app i portalen och sedan välja den appen som ett distributionsmål i ditt önskade verktyg.

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktionsapp med en enkel HTTP-utlöst funktion! Nu kan du utforska lokala verktyg, fler språk, övervakning och integrationer.

 * [Skapa din första funktion med Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Skapa din första funktion med Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Utvecklarhandboken för Azure Functions JavaScript](./functions-reference-node.md)
 * [Använda Azure-funktioner för att ansluta till en Azure SQL-databas](./functions-scenario-database-table-cleanup.md)
 * [Läs mer om HTTP-bindningar i Azure Functions](./functions-bindings-http-webhook.md).
 * [Övervaka dina Azure-funktioner](./functions-monitoring.md)
