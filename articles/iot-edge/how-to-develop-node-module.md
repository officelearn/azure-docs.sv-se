---
title: Felsöka Node.js-moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla och Felsök Node.js-moduler för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 78e952b5b1eedc1757cfe636eb13e411044dce54
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054970"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Utveckla och Felsök Node.js-moduler med Azure IoT Edge för Visual Studio Code

Du kan skicka din affärslogik som ska användas vid gränsen genom att aktivera det i moduler för Azure IoT Edge. Den här artikeln innehåller detaljerade anvisningar för att använda Visual Studio Code (VS Code) som det huvudsakliga utvecklingsverktyg för att utveckla Node.js-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som din utvecklingsdator. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller så kan du simulera en IoT Edge-enhet på utvecklingsdato.

> [!NOTE]
> Den här felsökning självstudien beskrivs hur du ansluter en process i en modul-behållare och felsöka med VS Code. Du kan felsöka Node.js-moduler i linux-amd64, windows och arm32 behållare. Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Eftersom den här artikeln används Visual Studio Code som det huvudsakliga utvecklingsverktyg, installera VS Code och Lägg sedan till tillägg som behövs:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Om du vill skapa en modul, behöver du Node.js som innehåller npm för att skapa projektmappen, Docker för att skapa modulen avbildningen och ett behållarregister ska lagra avbildningen modulen:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

Testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet kan du göra det genom att följa stegen i självstudier för [Windows](quickstart.md) eller [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT Edge-modul baserat på Node.js med hjälp av Visual Studio Code och Azure IoT Edge-tillägget. Du startar genom att skapa en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla flera moduler. 

1. I Visual Studio Code, Välj **visa** > **integrerade terminalen**.
2. I den integrerade terminalen anger du följande kommando för att installera (eller uppdatera) den senaste versionen av mallen för Azure IoT Edge-modul för Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. Gå till Visual Studio Code och välj **Visa** > **Kommandopalett**. 
4. Skriv i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen och klicka på **Välj mapp**. 
6. Ange ett namn för din lösning. 
7. Välj **Node.js-modulen** som mall för den första modulen i lösningen.
8. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister. 
9. Ange avbildningslagringsplatsen för modulen. VS Code autopopulates modulen namn, så du behöver bara ersätta **localhost:5000** med din egen information i registret. Om du använder en lokal Docker-register för testning, är det bra med localhost. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **\<registernamn\>. azurecr.io**.

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser in den i ett nytt fönster.

I lösningen har du tre objekt: 
* En **.vscode** mappen innehåller konfigurationer för felsökning.
* En **moduler** mappen innehåller undermappar för varje modul. Just nu du bara har en, men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. 
* En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den.

   >[!NOTE]
   >Miljö-filen skapas endast om du anger en avbildningslagringsplatsen för modulen. Om du har godkänt localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler. 

* En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

## <a name="develop-your-module"></a>Utveckla din modell

Standard Node.js-kod som medföljer lösningen finns i **moduler** > **\<din Modulnamn\>** > **app.js** . Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub. 

När du är redo att anpassa mallen Node.js med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Skapa och distribuera din modul för felsökning

Det finns flera Docker-filer för olika behållartyper i varje modul-mapp. Du kan använda någon av dessa filer som slutar med tillägget **.debug** att skapa din modul för testning. Node.js-moduler stöder för närvarande endast felsökning i linux-amd64-, windows amd64- och linux-arm32v7 behållare.

1. I VS Code, navigerar du till den `deployment.template.json` filen. Uppdatera din modulen bild-URL genom att lägga till **.debug** i slutet.
2. Ersätt createOptions för Node.js-modulen i **deployment.template.json** med nedan innehåll och spara den här filen: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. Skriv i kommandopaletten VS Code och kör kommandot **Azure IoT Edge: skapa IoT Edge-lösningen**.
3. Välj den `deployment.template.json` -filen för din lösning från kommandopaletten. 
4. Azure IoT Hub-enheter explorer, högerklicka på en IoT Edge-enhets-ID och välj sedan **skapa distribution för IoT Edge-enhet**. 
5. Öppna den **config** mappen för din lösning, välj sedan den `deployment.json` filen. Klicka på **Välj distributionsmanifest för Edge**. 

Du kan sedan se distributionen har skapats med en distribution som ID i VS Code-integrerade terminalen.

Du kan kontrollera din status för container i VS Code Docker-Utforskaren eller genom att köra den `docker ps` i terminalen.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Starta felsökning Node.js-modulen i VS Code

VS Code håller felsökning konfigurationsinformationen i en `launch.json` finns i en `.vscode` mapp i din arbetsyta. Detta `launch.json` filen genererades när du skapade en ny IoT Edge-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. 

1. Gå till felsökningsvyn VS Code och välj debug-konfigurationsfil för.

2. Navigera till `app.js`. Lägg till en brytpunkt i den här filen.

3. Klicka på den **Starta felsökning** knapp eller tryck på **F5**, och välj processen för att ansluta till.

4. Du kan se variabler i vänsterpanelen i VS Code felsöka visas. 

I föregående exempel visar hur du felsöker Node.js IoT Edge-moduler i behållare. Det har lagts till portar i din modul behållare createOptions. När du är klar med att felsöka dina Node.js-moduler rekommenderar vi att du tar bort dessa portar för produktionsklara IoT Edge-moduler.

## <a name="next-steps"></a>Nästa steg

När du har din modul bygger lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md)

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
