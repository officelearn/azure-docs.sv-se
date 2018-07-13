---
title: Felsöka C#-moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla, skapa och Felsök en C#-modul för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 93f5e4447f43cd8cda346743d813236bcc4ac947
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006336"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka C#-moduler för Azure IoT Edge

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code (VS Code) som det huvudsakliga verktyget för att utveckla och felsöka C#-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som din utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet. Eller du kan simulera IoT Edge-enhet på utvecklingsdatorn.

> [!NOTE]
> Den här felsökning artikeln visar hur du ansluter en process i en modul-behållare och felsöka med VS Code. Du kan bara felsöka C#-funktioner i Linux amd64-behållare. Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Eftersom den här artikeln används Visual Studio Code som det huvudsakliga utvecklingsverktyg, installera VS Code. Lägg sedan till tillägg som behövs:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Om du vill skapa en modul måste .NET för att skapa projektmappen, Docker för att skapa modulen avbildningen och ett behållarregister ska lagra avbildningen modulen:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

Testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet följer du stegen i snabbstarten för [Windows](quickstart.md) eller [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Gör följande för att skapa en IoT Edge-modul som baseras på .NET Core 2.0 med hjälp av Visual Studio Code och Azure IoT Edge-tillägget. Först skapar du en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla mer än en modul. 

1. I Visual Studio Code, Välj **visa** > **integrerade terminalen**.
3. Välj **visa** > **kommandot paletten**. 
4. Ange i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen. Välj **Välj mapp**. 
6. Ange ett namn för din lösning. 
7. Välj **C#-modul** som mall för den första modulen i lösningen.
8. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister. 
9. Ange namnet på modulens avbildningslagringsplatsen. VS Code autopopulates modulen namnet med **localhost:5000**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, sedan **localhost** är bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **\<registernamn\>. azurecr.io**.

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster.

   ![Visa IoT Edge-lösning](./media/how-to-develop-csharp-module/view-solution.png)

Det finns fyra objekt inne i lösningen: 
* En **.vscode** mappen innehåller konfigurationer för felsökning.
* En **moduler** mappen innehåller undermappar för varje modul. Nu kan har du bara en. Men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. 
* En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den. 
* En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [Lär dig hur du använder distribution manifest för att distribuera moduler och upprätta vägar](module-composition.md). 

## <a name="build-and-deploy-your-module-for-debugging"></a>Skapa och distribuera din modul för felsökning

Det finns flera Docker-filer för olika behållartyper i varje modul-mapp. Använd någon av dessa filer som slutar med tillägget **.debug** att skapa din modul för testning. För närvarande stöder C#-moduler felsökning endast i Linux amd64-behållare.

1. I VS Code, navigerar du till den `deployment.template.json` filen. Uppdatera din funktion-bild-URL genom att lägga till **.debug** i slutet.

   ![Lägg till *** .debug till din avbildningsnamn](./media/how-to-develop-csharp-module/image-debug.png)

2. Ange i kommandopaletten VS Code och kör kommandot **Edge: skapa IoT Edge-lösningen**.
3. Välj den `deployment.template.json` -filen för din lösning från kommandopaletten. 
4. I Azure IoT Hub Device Explorer högerklickar du på en IoT Edge-enhets-ID. Välj sedan **skapa distribution för IoT Edge-enhet**. 
5. Öppna din lösning **config** mapp. Välj sedan den `deployment.json` filen. Välj **Välj Edge-distribution Manifest**. 

Distributionen har skapats med en distributions-ID i en terminal för VS Code-integrerade visas.

Kontrollera behållarstatus för i VS Code Docker-Utforskaren eller genom att köra den `docker ps` i terminalen.

## <a name="start-debugging-c-module-in-vs-code"></a>Starta felsökning C#-modul i VS Code
VS Code håller felsökning konfigurationsinformationen i en `launch.json` finns i en `.vscode` mapp i din arbetsyta. Detta `launch.json` filen genererades när du skapade en ny IoT Edge-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. 

1. Gå till felsökningsvyn VS Code. Välj debug-konfigurationsfil för. Alternativnamn debug bör likna **ModuleName fjärrfelsökning (.NET Core)** ![väljer debug configuration](./media/how-to-develop-csharp-module/debug-config.png).

2. Navigera till `program.cs`. Lägg till en brytpunkt i den här filen.

3. Välj **Starta felsökning** eller välj **F5**. Välj processen för att ansluta till.

4. I VS Code Felsöka vyn visas variabler i den vänstra panelen. 

> [!NOTE]
> Det här exemplet visar hur du felsöker .NET Core IoT Edge-moduler i behållare. Den är baserad på felsökningsversionen av `Dockerfile.debug`, som innehåller .NET Core kommandoradsverktyget felsökningsprogrammet VSDBG i en behållaravbildning när du skapar den. När du felsöker dina C#-moduler, rekommenderar vi att du direkt använda eller anpassa `Dockerfile` utan VSDBG för produktionsklara IoT Edge-moduler.

## <a name="next-steps"></a>Nästa steg

När du har skapat din modul, lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

