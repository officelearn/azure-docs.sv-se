---
title: Installera tillägget varaktiga funktioner och -exempel – Azure
description: Lär dig hur du installerar tillägget varaktiga funktioner för Azure Functions för portalen utvecklings- eller Visual Studio-utveckling.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 8c5f3114172a7d27685e7aee2972b43b9ebef4e9
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093014"
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installera tillägget varaktiga funktioner och exempel (Azure Functions)

Den [varaktiga funktioner](durable-functions-overview.md) tillägget för Azure Functions finns i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Den här artikeln visar hur du installerar paketet och exempel för följande utvecklingsmiljöer:

* Visual Studio 2017 (rekommenderas för C#) 
* Visual Studio Code (rekommenderas för JavaScript)
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio har för närvarande den bästa upplevelsen för att utveckla appar som använder varaktiga funktioner.  Dina funktioner kan köras lokalt och kan även publiceras till Azure. Du kan börja med ett tomt projekt eller med en uppsättning exempel funktioner.

### <a name="prerequisites"></a>Förutsättningar

* Installera den [senaste versionen av Visual Studio](https://www.visualstudio.com/downloads/) (version 15.3 eller senare). Inkludera den **Azure development** arbetsbelastningen i din installationsalternativ.

### <a name="start-with-sample-functions"></a>Börja med exempel 

1. Ladda ned den [Exempelapp .zip-filen för Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Du behöver inte lägga till NuGet-referens, eftersom exempelprojektet redan har den.
2. Installera och köra [Azure Storage-emulatorn](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 eller senare. Alternativt kan du uppdatera den *local.appsettings.json* fil med verkliga Azure Storage-anslutningssträngar.
3. Öppna projektet i Visual Studio 2017. 
4. Börja med anvisningar om hur du kör exemplet [fungera länkning – Hello sekvens exempel](durable-functions-sequence.md). Exemplet kan köras lokalt eller publicerat till Azure.

### <a name="start-with-an-empty-project"></a>Börja med ett tomt projekt
 
Följ samma anvisningar som börjar med exemplet, men utför följande steg istället för att hämta den *.zip* fil:

1. Skapa ett projekt för Funktionsappen.
2. Sök efter den följande NuGet referens med *hantera NuGet-paket* och lägga till den i projektet: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.5.0
   
## <a name="visual-studio-code"></a>Visual Studio-koden

Visual Studio Code är en lokal utveckling som täcker alla större plattformar – Windows, macOS och Linux.  Dina funktioner kan köras lokalt och även publiceras till Azure. Du kan börja med ett tomt projekt eller med en uppsättning exempel funktioner.

### <a name="prerequisites"></a>Förutsättningar

* Installera den [senaste versionen av Visual Studio Code](https://code.visualstudio.com/Download) 

* Följ anvisningarna i avsnittet ”Installera the Azure Functions Core Tools” på [kod och testa Azure Functions lokalt](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Om du redan har Azure Functions Cross Platform Tools kan du uppdatera dem till den senaste tillgängliga versionen.

    >[!IMPORTANT]
    >Varaktiga funktioner i JavaScript krävs version 2.x av Azure Functions Core Tools.

*  Om du är på en Windows-dator, installera och köra [Azure Storage-emulatorn](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 eller senare. Alternativt kan du uppdatera den *local.appsettings.json* -fil med verkliga Azure Storage-anslutning. 


### <a name="start-with-sample-functions"></a>Börja med exempel

#### <a name="c"></a>C#

1. Klona den [varaktiga funktioner databasen](https://github.com/Azure/azure-functions-durable-extension.git).
2. Navigera på din dator till den [exempelmapp för C#-skript](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Installera Azure Functions varaktiga Extension genom att köra följande i ett kommando fråga / terminal-fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.5.0
    ```
4. Installera Azure Functions Twilio-tillägget genom att köra följande i ett kommando fråga / terminal-fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta5
    ```
5. Kör Azure Storage-emulatorn eller uppdatera den *local.appsettings.json* fil med verkliga Azure Storage-anslutningssträng.
6. Öppna projektet i Visual Studio Code. 
7. Börja med anvisningar om hur du kör exemplet [fungera länkning – Hello sekvens exempel](durable-functions-sequence.md). Exemplet kan köras lokalt eller publicerat till Azure.
8. Starta projektet genom att köra i kommandot fråga / terminal följande kommando:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

1. Klona den [varaktiga funktioner databasen](https://github.com/Azure/azure-functions-durable-extension.git).
2. Navigera på din dator till den [JavaScript exempelmapp](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Installera Azure Functions varaktiga Extension genom att köra följande i ett kommando fråga / terminal-fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.5.0
    ```
4. Återställ npm-paket genom att köra följande i ett kommando fråga / terminal-fönstret:
    
    ```bash
    npm install
    ``` 
5. Uppdatera den *local.appsettings.json* filen med den verkliga Azure Storage-anslutningssträngen.
6. Öppna projektet i Visual Studio Code. 
7. Börja med anvisningar om hur du kör exemplet [fungera länkning – Hello sekvens exempel](durable-functions-sequence.md). Exemplet kan köras lokalt eller publicerat till Azure.
8. Starta projektet genom att köra i kommandot fråga / terminal följande kommando:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Börja med ett tomt projekt
 
1. Navigera till den mapp som ska vara värd för din funktionsapp i kommandot fråga / terminal.
2. Installera Azure Functions varaktiga Extension genom att köra följande i ett kommando fråga / terminal-fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.5.0
    ```
3. Skapa en Funktionsapp-projekt genom att köra följande kommando:

    ```bash
    func init
    ``` 
4. Kör Azure Storage-emulatorn eller uppdatera den *local.appsettings.json* fil med verkliga Azure Storage-anslutningssträng.
5. Sedan skapar en ny funktion genom att köra följande kommando och följ stegen i guiden:

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > För närvarande varaktiga funktionsmallen är inte tillgänglig men du kan börja med någon av alternativ som stöds och sedan ändra koden. Använd exempel för referens [Orkestreringsklient](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Orchestration utlösaren](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), och [aktivitet utlösaren](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Öppna projektmappen i Visual Studio Code och fortsätta genom att ändra mallkoden. 
7. Starta projektet genom att köra i kommandot fråga / terminal följande kommando:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure Portal

Om du vill kan du använda den [Azure-portalen](https://portal.azure.com) för utveckling av varaktiga funktioner.

   > [!NOTE]
   > Varaktiga funktioner i JavaScript är ännu inte tillgängliga i portalen.

### <a name="create-an-orchestrator-function"></a>Skapa en orchestrator-funktion

1. Skapa en ny funktionsapp i portalen, enligt den [Functions snabbstartsartikel](functions-create-first-azure-function.md#create-a-function-app).

2. Konfigurera appen att [använda version 2.0 runtime](set-runtime-version.md).

   Tillägget varaktiga funktioner fungerar på både 1.X-körningen och 2.0 runtime, men Azure Portal-mallarna är bara tillgängliga när måldatorn 2.0 runtime.

3. Skapa en ny funktion genom att välja **”skapa din egen anpassade funktion”.** .

4. Ändra den **språk** till **C#**, **scenariot** till **varaktiga funktioner** och välj den **Http-Starter för beständiga funktioner -C#** mall.

5. Under **tillägg som inte har installerats**, klickar du på **installera** att ladda ned tillägget från NuGet.org. 

6. När installationen är klar fortsätter du med att skapa en funktion för orchestration-klienten – **”HttpStart”** som skapas genom att välja **varaktiga funktioner Http-Starter - C#** mall.

7. Nu skapar du en orchestration-funktion **”HelloSequence”** från **Orchestrator för beständiga funktioner – C#** mall.

8. Och den senaste funktionen anropas **”Hello”** från **aktivitet för beständiga funktioner – C#** mall.

9. Gå till **”HttpStart”** funktionen och kopiera URL: en.

10. Använda Postman eller cURL för att anropa funktionen varaktiga. Innan du testar kan ersättas i URL: en **{functionName}** med funktionsnamnet orchestrator - **HelloSequence**.  Inga data krävs, Använd bara INLÄGG verb. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Anropa sedan den **”statusQueryGetUri”** slutpunkt och du se aktuell status för funktionen varaktiga

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Fortsätta anropar den **”statusQueryGetUri”** slutpunkten tills status ändras till **”slutfört”** 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Grattis! Din första varaktiga funktion är igång och körs i Azure Portal!

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kör funktionen länkning exemplet](durable-functions-sequence.md)
