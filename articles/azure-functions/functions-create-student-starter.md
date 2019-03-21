---
title: Skapa en funktion som använder Azure för studenter Starter | Microsoft Docs
description: Lär dig hur du skapar en Azure-funktion från en prenumeration för Azure för studenter Starter
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 5fd9f51185b8461269d498b254d9e9d9f4118270
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344103"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Skapa en funktion som använder Azure för studenter Starter

I den här självstudiekursen skapar vi en hello world HTTP-funktion i en prenumeration för Azure för studenter Starter. Vi också igenom vad som finns i Azure Functions i den här prenumerationstypen.

Microsoft *Azure för studenter Starter* kommer du igång med Azure-produkter du behöver utveckla i molnet utan kostnad för dig. [Läs mer om det här erbjudandet här.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. [Läs mer om funktioner här.](./functions-overview.md)

## <a name="create-a-function"></a>Skapa en funktion

 I det här avsnittet lär du dig hur du använder Functions för att skapa en HTTP-utlöst ”hello world”-funktion i Azure-portalen.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. I en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. 

1. Välj knappen **Nytt** längst upp till vänster i Azure Portal, välj sedan **Compute** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
   | **[App Service-Plan/plats](./functions-scale.md)** | Ny | Den som är värd planera som kontrollerar vilken region som funktionsappen har distribuerats till och densitet för dina resurser. Flera Funktionsappar som har distribuerats till samma plan ska dela samma enda kostnadsfri instans. Det här är en begränsning av Student Starter-planen. Fullständig värdalternativ är [beskrivs här.](./functions-scale.md)|
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled | Application Insights används för att lagra och analysera loggar i din funktionsapp. Det är aktiverat som standard om du väljer en plats som har stöd för Application Insights. Application Insights kan aktiveras för en funktion genom att manuellt välja en närliggande region för att distribuera Application Insights. Utan Application Insights, kommer du bara att kunna visa live direktuppspelning loggar.

3. Välj **App Service-plan/plats** ovan för att välja en annan plats

4. Välj **Skapa nytt** och ge din plan ett unikt namn.

5. Välj platsen som är närmast dig. [Se en fullständig karta över Azure-regioner här.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Välj **Skapa** för att etablera och distribuera funktionsappen.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/functions-create-student-starter/function-app-create-notification.png)

8. Välj **Gå till resurs** att visa den nya funktionsappen.

> [!TIP]
> Är det svårt att hitta dina funktionsappar i portalen kan du prova att [lägga till funktionsappar till dina favoriter i Azure-portalen](./functions-how-to-use-azure-function-app-settings.md#favorite).
Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-function"></a>Skapa en HTTP-utlöst funktion

1. Expandera den nya funktionsappen. Välj knappen **+** intill knappen **Functions**, välj **I portalen** och välj **Fortsätt**.

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

## <a name="supported-features-in-azure-for-students-starter"></a>Funktioner som stöds i Azure för studenter Starter

I Azure for Student Starter har du åtkomst till de flesta av funktionerna i Azure Functions-körningen med flera viktiga begränsningar som anges nedan:

* HTTP-utlösaren är den enda Utlösartyp som stöds.
    * Alla indata och utdata stöds! [Se hela listan här.](functions-triggers-bindings.md)
* Språk som stöds: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Se språk som stöds i den här högre planer](supported-languages.md)
* Windows är det enda operativsystemet som stöds.
* Skalning är begränsad till [en kostnadsfri nivå instans](https://azure.microsoft.com/pricing/details/app-service/windows/) som körs i upp till 60 minuter under en dag. Du kommer serverlessly skala från 0 till 1 instans automatiskt vid HTTP-trafik som tas emot, men inga fler.
* Endast [2.x-körningen](functions-versions.md) stöds.
* Alla utvecklarverktyg har stöd för att redigera och publicera funktioner. Detta inkluderar VS Code, Visual Studio, Azure CLI och Azure-portalen. Om du vill använda något annat än portalen behöver du först skapa en app i portalen och välj sedan appen som ett mål för distribution i din önskade verktyg.

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktionsapp med en enkel HTTP-utlöst funktion! Nu kan du utforska lokala verktyg, flera språk, övervakning och integreringar.

 * [Skapa din första funktion med Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Skapa din första funktion med Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Utvecklarguide för Azure Functions JavaScript](./functions-reference-node.md)
 * [Använd Azure Functions för att ansluta till en Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Läs mer om Azure Functions HTTP-bindningar](./functions-bindings-http-webhook.md).
 * [Övervaka dina Azure Functions](./functions-monitoring.md)
