---
title: Skapa en funktion med Start programmet för Azure för studenter
description: Lär dig hur du skapar en Azure-funktion från en Azure for student starter-prenumeration
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 189a0bc80ab2894b2888b496b3beb45249d859f6
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921138"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Skapa en funktion med Start programmet för Azure för studenter

I den här självstudien skapar vi en "Hello World"-HTTP-funktion i en start prenumeration för Azure för studenter. Vi går också igenom vad som är tillgängligt i Azure Functions i den här prenumerations typen.

*Start programmet för Microsoft Azure för studenter* hjälper dig att komma igång med de Azure-produkter du behöver för att utveckla i molnet utan kostnad. [Läs mer om det här erbjudandet här.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. [Lär dig mer om funktioner här.](./functions-overview.md)

## <a name="create-a-function"></a>Skapa en funktion

 I det här avsnittet får du lära dig hur du använder Functions för att skapa en HTTP-utlöst "Hello World"-funktion i Azure Portal.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Välj knappen **skapa en resurs** som finns i det övre vänstra hörnet av Azure Portal. Välj sedan **compute** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
   | **[App Service plan/plats](./functions-scale.md)** | Nytt | Värd planen som styr vilken region som din Function-app distribueras till och densiteten för dina resurser. Flera funktions program som distribueras till samma plan delar alla samma kostnads fria instans. Detta är en begränsning av student starts planen. De fullständiga värd alternativen [beskrivs här.](./functions-scale.md)|
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled | Application Insights används för att lagra och analysera funktions programmets loggar. Den är aktive rad som standard om du väljer en plats som har stöd för Application Insights. Application Insights kan aktive ras för en funktion genom att manuellt välja en närliggande region för att distribuera Application Insights. Utan Application Insights kommer du bara att kunna visa direkt uppspelnings loggar.

3. Välj **App Service plan/plats** ovan om du vill välja en annan plats

4. Välj **Skapa ny** och ge sedan planen ett unikt namn.

5. Välj den plats som är närmast dig. [Se en fullständig karta över Azure-regioner här.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Välj **Skapa** för att etablera och distribuera funktionsappen.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/functions-create-student-starter/function-app-create-notification.png)

8. Välj **Gå till resurs** att visa den nya funktionsappen.

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-function"></a>Skapa en HTTP-utlöst funktion

1. Expandera den nya funktionsappen. Välj knappen **+** intill knappen **Functions**, välj **I portalen** och välj **Fortsätt**.

    ![Functions-snabbstart, välj plattform.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Välj **WebHook + API** och välj sedan **Skapa**.

    ![Snabbstart för funktioner i Azure Portal.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

En funktion skapas med hjälp av en språkspecifik mall för en HTTP-utlöst funktion.

Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen klickar du på **</> Hämta funktionswebbadress** längst upp till höger och väljer **Standard (funktionsnyckel)** . Sedan klickar du på **Kopiera**. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<yourname>` i slutet av den här webbadressen och tryck på knappen `Enter` på tangentbordet för att utföra begäran. Du bör se svaret som returnerades av funktionen som visas i webbläsaren.  

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

3. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill visa spårningsinformationen från föregående körning återgår du till funktionen i portalen och klickar på pilen längst ned på skärmen så att **Loggar** expanderas.

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Funktioner som stöds i Start programmet för Azure för studenter

I Azure for Students Starter har du till gång till de flesta av funktionerna i Azure Functions runtime, med flera viktiga begränsningar i listan nedan:

* HTTP-utlösaren är den enda utlösnings typ som stöds.
    * Alla indata och alla utgående bindningar stöds! [Se den fullständiga listan här.](functions-triggers-bindings.md)
* Språk som stöds: 
    * C#(.NET Core 2)
    * Java Script (Node. js 8 & 10)
    * F#(.NET Core 2)
    * [Se språk som stöds i högre planer här](supported-languages.md)
* Windows är det enda operativ system som stöds.
* Skalningen är begränsad till [en instans på en kostnads fri nivå](https://azure.microsoft.com/pricing/details/app-service/windows/) som körs i upp till 60 minuter varje dag. Du kommer serverlessly att skala från 0 till 1 instans automatiskt när HTTP-trafik tas emot, men inte ytterligare.
* Endast [version 2. x och senare](functions-versions.md) av Functions-körningen stöds.
* Alla verktyg för utvecklare stöds för redigerings-och publicerings funktioner. Detta inkluderar VS Code, Visual Studio, Azure CLI och Azure Portal. Om du vill använda något annat än portalen måste du först skapa en app i portalen och sedan välja appen som ett distributions mål i önskat verktyg.

## <a name="next-steps"></a>Nästa steg

Du har skapat en Function-app med en enkel HTTP-utlöst funktion! Nu kan du utforska lokala verktyg, fler språk, övervakning och integreringar.

 * [Skapa din första funktion med Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Skapa din första funktion med Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Azure Functions JavaScript-guide för utvecklare](./functions-reference-node.md)
 * [Använd Azure Functions för att ansluta till en Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Läs mer om hur du Azure Functions HTTP-bindningar](./functions-bindings-http-webhook.md).
 * [Övervaka dina Azure Functions](./functions-monitoring.md)
