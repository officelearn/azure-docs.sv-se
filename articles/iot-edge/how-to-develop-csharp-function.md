---
title: Felsöka funktioner moduler för Azure IoT kant | Microsoft Docs
description: Använda Visual Studio-koden för att felsöka C# Azure Functions med Azure IoT kant
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052835"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Använda Visual Studio-koden för att utveckla och felsöka Azure Functions för Azure IoT kant

Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio (VS) kod](https://code.visualstudio.com/) att felsöka dina Azure-funktioner på IoT kant.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller kan du simulera enheten IoT kanten på utvecklingsdatorn.

> [!NOTE]
> Självstudierna felsökning beskriver hur du ansluter en process i en modul-behållare och felsöka med VS-kod. Du kan bara felsöka C#-moduler i linux-amd64-behållare. Om du inte är bekant med funktionerna felsökning i Visual Studio Code kan läsa om [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Den här artikeln används Visual Studio Code som den huvudsakliga utvecklingsverktyg. Installera VS-kod och lägger till tillägg som behövs: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT kant-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Om du vill skapa en modul måste .NET för att skapa projektmappen Docker att skapa modulen avbildningen och en behållare registret för att lagra avbildningen modulen:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) på utvecklingsdatorn. 
* [Azure-behållaren registret](https://docs.microsoft.com/azure/container-registry/) eller [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Du kan använda en lokal Docker-registret för prototyp och testning, i stället för ett moln-register. 

Om du vill testa din modulen på en enhet måste en aktiv IoT-hubb med minst en IoT-enhet. Om du vill använda din dator som en IoT-enhet kan du göra det genom att följa stegen i självstudier för [Windows](quickstart.md) eller [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT-Edge-lösning som innehåller en C#-funktionsmodul. Varje lösning kan innehålla flera moduler.

1. Välj i Visual Studio Code **visa** > **integrerad Terminal**.
3. Välj **visa** > **kommandot paletten**.
4. Skriv i paletten kommandot och kör kommandot **Azure IoT kant: ny IoT kant lösning**. 

   ![Kör ny gräns för IoT-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen och klicka på **väljer mappen**. 
6. Ange ett namn för din lösning. 
7. Välj **Azure Functions - C#** som mall för den första modulen i lösningen.
8. Ange ett namn för din modulen. Välj ett namn som är unikt i behållaren registret. 
9. Ange avbildningslagringsplatsen för modulen. VS kod autopopulates modulen namn, så du behöver bara ersätta **localhost:5000** med din egen information i registret. Om du använder en lokal Docker-registret för att testa räcker det bra med localhost. Om du använder Azure-behållare registret kan sedan använda inloggnings-servern från din registerinställningar. Inloggningsserver ser ut som  **\<registrets\>. azurecr.io**.

VS-koden tar den information du tillhandahålls, skapar en gräns för IoT-lösning med ett projekt för funktionen och läser in den i ett nytt fönster.

Du har tre objekt i lösningen: 

* En **.vscode** mapp som innehåller konfigurationer för felsökning.
* En **moduler** mapp som innehåller undermappar för varje modul. Just nu bara ha en, men du kan lägga till fler via paletten kommando med kommandot **Azure IoT kant: Lägg till IoT kant modul**.
* En **.env** filen visar aktuella miljövariabler. Om du ACR som registret, just nu har ACR användarnamn och lösenord i den. 
* En **deployment.template.json** filen visar dina nya modulen tillsammans med ett exempel på en **tempSensor** module, som simulerar data som du kan använda för att testa. Mer information om hur distributionen visar arbete finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Skapa din IoT kant funktionen-modul för felsökning syfte
1. Om du vill starta felsökning, måste du använda **Dockerfile.amd64.debug** att återskapa docker-avbildning och distribuera lösningar Edge igen. VS kod explorer, navigera till `deployment.template.json` filen. Uppdatera din funktion bild-URL genom att lägga till en `.debug` i slutet.

    ![Skapa Debug-bild](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Återskapa din lösning. Skriv i VS kod kommandot paletten och kör kommandot **Azure IoT kant: skapa gräns för IoT-lösningen**.
3. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för gränsenheten**. Välj den `deployment.json` filen den `config` mapp. Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

Du kan kontrollera din behållaren status i VS kod Docker-Utforskaren eller genom att köra den `docker images` i terminalen.

## <a name="start-debugging-c-function-in-vs-code"></a>Starta felsökning C#-funktionen i VS-kod
1. VS-kod för att hålla felsökning konfigurationsinformationen i en `launch.json` fil i en `.vscode` mapp i arbetsytan. Detta `launch.json` filen genererades när du skapade en ny gräns för IoT-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. Navigera till felsökningsvyn och välj den motsvarande debug-konfigurationsfilen. Alternativnamn debug ska vara som **Modulnamn fjärrfelsökning (.NET Core)** ![väljer debug-konfiguration](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navigera till `run.csx`. Lägga till en brytpunkt i funktionen.
3. Klicka på den **Start Debugging** eller tryck på knappen **F5**, och välj den process för att ansluta till.
4. I VS kod Felsöka vyn ser variabler i den vänstra panelen. 


> [!NOTE]
> I ovanstående exempel visas hur du felsökning .net huvudfunktion IoT kanten på behållaren. Den är baserad på felsökningsversionen av den `Dockerfile.amd64.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. Vi rekommenderar att du direkt använda eller anpassa den `Dockerfile` utan VSDBG för produktionsklara IoT kant funktionen när du har slutfört felsökning C#-funktionen.

## <a name="next-steps"></a>Nästa steg

När du har den inbyggda modulen lär du dig hur du [distribuera Azure IoT kant moduler från Visual Studio Code](how-to-deploy-modules-vscode.md)
