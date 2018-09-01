---
title: Felsöka Azure-funktion-moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att felsöka C# Azure functions med Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b4f9bd1c7390d64a0db08b55bfb777498a10cb0
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382713"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka Azure functions för Azure IoT Edge

Den här artikeln visar hur du använder [Visual Studio Code (VS Code)](https://code.visualstudio.com/) att felsöka dina Azure functions på Azure IoT Edge.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som din utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet. Eller du kan simulera IoT Edge-enhet på utvecklingsdatorn.

> [!NOTE]
> Den här felsökning artikeln visar hur du ansluter en process i en modul-behållare och felsöka med VS Code. Du kan bara felsöka C#-funktioner i Linux amd64-behållare. Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Den här artikeln används Visual Studio Code som det huvudsakliga utvecklingsverktyg. Installera VS Code. Lägg sedan till tillägg som behövs: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Om du vill skapa en modul måste .NET för att skapa projektmappen, Docker för att skapa modulen avbildningen och ett behållarregister ska lagra avbildningen modulen:

* [.NET core SDK 2.1](https://www.microsoft.com/net/download)
* [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

Testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet följer du stegen i snabbstarten för [Windows](quickstart.md) eller [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Gör följande för att skapa en IoT Edge-lösning som har en C#-funktionsmodul. Varje lösning kan ha flera moduler.

1. I Visual Studio Code, Välj **visa** > **integrerade terminalen**.
3. Välj **visa** > **kommandot paletten**.
4. Ange i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**. 

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen. Välj **Välj mapp**. 
6. Ange ett namn för din lösning. 
7. Välj **Azure Functions – C#** som mall för den första modulen i lösningen.
8. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister. 
9. Ange avbildningslagringsplatsen för modulen. VS Code autopopulates modulen namnet med **localhost:5000**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, sedan **localhost** är bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **\<registernamn\>. azurecr.io**. Ersätt endast localhost-delen av strängen; ta inte bort modulens namn.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-csharp-function/repository.png)

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning med Azure Functions-projekt och läser sedan in den i ett nytt fönster.

Det finns fyra objekt inne i lösningen: 

* En **.vscode** mappen innehåller konfigurationer för felsökning.
* En **moduler** mappen innehåller undermappar för varje modul. Nu kan har du bara en. Men du kan lägga till fler via kommandopaletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**.
* En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den. 

   >[!NOTE]
   >Miljö-filen skapas endast om du anger en avbildningslagringsplatsen för modulen. Om du har godkänt localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler. 

* En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [Lär dig hur du använder distribution manifest för att distribuera moduler och upprätta vägar](module-composition.md).

## <a name="develop-your-module"></a>Utveckla din modell

Standard Azure Function-koden som medföljer lösningen finns i **moduler** > [Modulnamn] > **EdgeHubTrigger-Csharp** > **run.csx** . Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub. 

När du är redo att anpassa Azure Function-mallen med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet. 

## <a name="build-your-module-for-debugging"></a>Skapa din modul för felsökning
1. Starta felsökning med **Dockerfile.amd64.debug** att återskapa din docker-avbildning och distribuera din lösning igen. I VS Code-Utforskaren navigerar du till den `deployment.template.json` filen. Uppdatera din funktion-bild-URL genom att lägga till `.debug` i slutet.

    ![Skapa debug-avbildning](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Återskapa din lösning. Ange i kommandopaletten VS Code och kör kommandot **Azure IoT Edge: skapa IoT Edge-lösningen**.
3. Högerklicka på en IoT Edge-enhets-ID i Azure IoT Hub-enheter explorer och välj sedan **skapa distribution för Edge-enhet**. Välj den `deployment.json` fil i den `config` mapp. Distributionen har skapats med en distributions-ID i en terminal för VS Code-integrerade visas.

Kontrollera behållarstatus för i VS Code Docker explorer eller genom att köra den `docker ps` i terminalen.

## <a name="start-debugging-c-functions-in-vs-code"></a>Starta felsökning C#-funktioner i VS Code
1. VS Code håller felsökning konfigurationsinformationen i en `launch.json` finns i en `.vscode` mapp i din arbetsyta. Detta `launch.json` filen genererades när du skapade en ny IoT Edge-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. Gå till felsökningsvyn. Välj den motsvarande debug-konfigurationsfilen. Alternativnamn debug bör likna **ModuleName fjärrfelsökning (.NET Core)**.

   ![Välj debug-konfiguration](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navigera till `run.csx`. Lägg till en brytpunkt i funktionen.
3. Välj **Starta felsökning** eller välj **F5**. Välj processen för att ansluta till.
4. I VS Code felsökningsvyn visas variabler i den vänstra panelen. 


> [!NOTE]
> Det här exemplet visar hur du felsöker .net Core IoT Edge-funktioner i behållare. Den är baserad på felsökningsversionen av den `Dockerfile.amd64.debug`, som innehåller .NET Core kommandoradsverktyget felsökningsprogrammet VSDBG i en behållaravbildning när du skapar den. Vi rekommenderar att du direkt använda eller anpassa den `Dockerfile` utan VSDBG för produktionsklara IoT Edge-funktioner när du felsöker dina C#-funktioner.

## <a name="next-steps"></a>Nästa steg

När du har skapat din modul, lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
