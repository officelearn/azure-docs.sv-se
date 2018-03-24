---
title: Installera tillägget beständiga funktioner och exempel - Azure
description: Lär dig hur du installerar tillägget varaktiga funktioner för Azure Functions för portalen utveckling eller Visual Studio-utveckling.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 3451f268e78df6efa5e3fc489b88415349efe169
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installera tillägget beständiga funktioner och exempel (Azure-funktioner)

Den [varaktiga funktioner](durable-functions-overview.md) tillägget för Azure Functions finns i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Den här artikeln visar hur du installerar paketet och en uppsättning exempel för följande utvecklingsmiljöer:

* Visual Studio 2017 (rekommenderas) 

* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio har för närvarande den bästa upplevelsen för att utveckla appar som använder beständiga funktioner.  Dina funktioner kan köras lokalt och kan även publiceras till Azure. Du kan starta med ett tomt projekt eller en uppsättning exempel funktioner.

### <a name="prerequisites"></a>Förutsättningar

* Installera den [senaste versionen av Visual Studio](https://www.visualstudio.com/downloads/) (version 15.3 eller högre). Inkludera den **Azure-utveckling** arbetsbelastningen i din installationsalternativ.

### <a name="start-with-sample-functions"></a>Börja med exempel funktioner

1. Hämta den [Exempelapp ZIP-filen för Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Du behöver inte lägga till NuGet-referens eftersom exempelprojektet har redan.
2. Installera och köra [Azure Storage-emulatorn](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 eller senare. Alternativt kan du uppdatera den *local.appsettings.json* fil med verkliga Azure Storage-anslutningssträngar.
3. Öppna projektet i Visual Studio-2017. 
4. Börja med instruktioner om hur du kör exemplet [fungerar länkning – Hello sekvens exempel](durable-functions-sequence.md). Exemplet kan köra lokalt eller publiceras på Azure.

### <a name="start-with-an-empty-project"></a>Börja med ett tomt projekt
 
Följ samma anvisningar som börjar med exemplet, men gör du följande i stället för att hämta den *.zip* fil:

1. Skapa en Funktionsapp-projekt.
2. Lägg till följande NuGet-paketet referens till din *.csproj* fil:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```
   
## <a name="visual-studio-code"></a>Visual Studio-koden

Visual Studio Code tillhandahåller en lokal utveckling som omfattar alla större plattformar - Windows-, macOS- och Linux.  Dina funktioner kan köras lokalt och kan även publiceras till Azure. Du kan starta med ett tomt projekt eller en uppsättning exempel funktioner.

### <a name="prerequisites"></a>Förutsättningar

* Installera den [senaste versionen av Visual Studio Code](https://code.visualstudio.com/Download) 

* Följ anvisningarna i avsnittet ”Installera Azure Functions Core verktyg” på [koden och testa Azure Functions lokalt](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Om du redan har Azure funktioner Cross Platform verktyg kan du uppdatera dem till den senaste tillgängliga versionen.

*  Installera och köra [Azure Storage-emulatorn](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 eller senare. Alternativt kan du uppdatera den *local.appsettings.json* filen med verkliga Azure Storage-anslutning. 


### <a name="start-with-sample-functions"></a>Börja med exempel funktioner

1. Klona den [varaktiga funktioner databasen](https://github.com/Azure/azure-functions-durable-extension.git).
2. Navigera på din dator till den [exempelmapp för C#-skriptet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Installera Azure Functions varaktiga tillägg genom att köra följande i ett kommando fråga / terminal fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.2.0-beta3
    ```
4. Installera Azure Functions Twilio tillägg genom att köra följande i ett kommando fråga / terminal fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta4
    ```
5. Kör Azure Storage-emulatorn eller uppdatera den *local.appsettings.json* filen verkliga Azure Storage-anslutningssträng.
6. Öppna projektet i Visual Studio-koden. 
7. Börja med instruktioner om hur du kör exemplet [fungerar länkning – Hello sekvens exempel](durable-functions-sequence.md). Exemplet kan köra lokalt eller publiceras på Azure.
8. Starta projektet genom att köra kommandot fråga / terminal följande kommando:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Börja med ett tomt projekt
 
1. Navigera till mappen som ska vara värd för din funktionsapp i kommandot fråga / terminal.
2. Installera tillägget Azure Functions varaktigt genom att köra följande i ett kommando fråga / terminal fönstret:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
3. Skapa en Funktionsapp projekt genom att köra följande kommando:

    ```bash
    func init
    ``` 
4. Kör Azure Storage-emulatorn eller uppdatera den *local.appsettings.json* filen verkliga Azure Storage-anslutningssträng.
5. Sedan skapar en ny funktion genom att köra följande kommando och följer stegen i guiden:

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > För närvarande varaktiga funktionen mallen är inte tillgänglig, men du kan börja med en av alternativ som stöds och sedan ändra koden. Använd för referensen prover för [Orchestration klienten](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Orchestration utlösaren](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), och [aktivitet utlösaren](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Öppna projektmappen i Visual Studio-koden och Fortsätt genom att ändra mallkoden. 
7. Starta projektet genom att köra kommandot fråga / terminal följande kommando:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure Portal

Om du vill kan använda du Azure portal för utveckling av beständiga funktioner.

### <a name="create-an-orchestrator-function"></a>Skapa en orchestrator-funktion

1. Skapa en ny funktionsapp på [functions.azure.com](https://functions.azure.com/signin).

2. Konfigurera funktionsapp till [använder version 2.0 runtime](set-runtime-version.md).

3. Skapa en ny funktion genom att välja **”skapa egna anpassade funktionen”.** .

4. Ändra den **språk** till **C#**, **scenariot** till **varaktiga funktioner** och välj den **varaktiga funktioner Http Starter -C#** mall.

5. Under **inte installerade tilläggen**, klickar du på **installera** att ladda ned tillägget från NuGet.org. 

6. När installationen är klar fortsätter du med skapandet av en funktion för orchestration-klienten – **”HttpStart”** som skapas genom att välja **varaktiga funktioner Http Starter - C#** mall.

7. Nu ska du skapa en orchestration-funktion **”HelloSequence”** från **varaktiga funktioner Orchestrator - C#** mall.

8. Och den senast funktionen anropas **”Hello”** från **varaktiga funktioner aktivitet – C#** mall.

9. Gå till **”HttpStart”** fungerar och kopiera Webbadressen.

10. Använd Postman eller cURL för att anropa funktionen beständig. Innan du testar kan ersätta i URL: en **{functionName}** med funktionsnamnet orchestrator - **HelloSequence**.  Inga data är obligatorisk använder bara POST verb. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Anropa sedan den **”statusQueryGetUri”** slutpunkt och du ser den aktuella statusen för funktionen varaktiga

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Fortsätta anropar den **”statusQueryGetUri”** endpoint tills status ändras till **”slutfört”** 

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

Grattis! Din första varaktiga funktion är aktiv och körs i Azure Portal!

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kör funktionen länkning exempel](durable-functions-sequence.md)
