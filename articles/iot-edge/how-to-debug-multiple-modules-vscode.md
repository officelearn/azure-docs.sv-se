---
title: Felsöka flera moduler i Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att felsöka flera moduler med Azure IoT Edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0c421eb1532536a3d11013073f0474f5ac37311b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100469"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Använd Visual Studio Code för att felsöka flera moduler med Azure IoT Edge
Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio (VS) kod](https://code.visualstudio.com/) felsöka flera moduler på IoT Edge.

## <a name="prerequisites"></a>Förutsättningar
I självstudiekursen [utveckla en IoT Edge-lösning med flera moduler i Visual Studio Code](tutorial-multiple-modules-in-vscode.md) och kontrollera att du har minst två moduler som körs på din IoT Edge-enhet.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Flera mål och fjärrfelsökning i VS Code
Du kan koppla modulen processen i en behållare, oavsett om behållaren körs på utvecklingsdatorn eller i en fjärransluten fysiska IoT Edge-enhet med VS Code och Azure IoT Edge-tillägget. Felsöka med flera moduler som körs i behållare faktiskt koppla fler än en process i en separat behållare. VS Code [flera mål felsökning](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) kan användas när du felsöker med flera moduler.

   > [!TIP]
   > Om modulen behållaren körs i en fjärransluten fysiska IoT Edge-enhet kan du behöva installationen [Docker Machine](https://docs.docker.com/machine/overview/) så att Docker-motorn på utvecklingsdatorn kan kommunicera med de fjärranslutna Docker-värdarna.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Skapa din IoT-Edge moduler för felsökning
1. Om du vill starta flera modulen felsökning, måste du använda **Dockerfile.amd64.debug** att återskapa din docker-avbildningar och distribuera din lösning igen. I VS Code-Utforskaren navigerar du till `deployment.template.json` fil. Uppdatera din bild-URL: er genom att lägga till en `.debug` i slutet. Du behöver två modulen avbildningar med `.debug` minst. Om du arbetar med lösningen från föregående självstudiekursen, bör du ha en C# functions-modulen och en C# modulen. Uppdatera dessa två bild-URL: er genom att lägga till en `.debug` i slutet och spara den här filen. 
2. Återskapa din lösning. I VS Code kommandopalett skriver och kör kommandot **Azure IoT Edge: skapa IoT Edge-lösningen**.
3. Azure IoT Hub-enheter explorer, högerklicka på en IoT Edge-enhets-ID och välj sedan **skapa distribution för Edge-enhet**. Välj den `deployment.json` filen under den `config` mapp. Du kan sedan se distributionen har skapats med en distribution som ID i VS Code-integrerade terminalen.

Du kan kontrollera din status för container i VS Code Docker-Utforskaren eller genom att köra den `docker ps` i terminalen.

### <a name="start-debugging-c-function-in-vs-code"></a>Starta felsökning C# funktion i VS Code
1. VS Code håller felsökning konfigurationsinformationen i en `launch.json` finns i en `.vscode` mapp i din arbetsyta. Detta `launch.json` filen genererades när du skapade en ny IoT Edge-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. Gå till felsökningsvyn och välj motsvarande debug-konfigurationsfilen för C# functions modulen fjärrfelsökning.
2. Navigera till `run.csx`. Lägg till en brytpunkt i funktionen.
3. Klicka på den **Starta felsökning** knapp eller tryck på **F5**, och välj processen för att ansluta till.
4. Du kan se variabler i vänsterpanelen i VS Code felsöka visas. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Starta felsökning C# modulen samtidigt i VS Code
1. I VS Code kommandopaletten, Skriv och kör kommandot ”arbetsyta: duplicera arbetsyta i nytt fönster”. En ny VS Code-fönstret börjar med samma arbetsyta.
2. Gå till felsökningsvyn och välj motsvarande debug-konfigurationsfilen för C# modulen fjärrfelsökning.
3. Navigera till `program.cs`. Lägg till en brytpunkt i den C# modulen.
4. Klicka på den **Starta felsökning** knapp eller tryck på **F5**, och välj processen för att ansluta till.
5. Du kan se variabler i vänsterpanelen i VS Code felsöka visas. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Se variabler i flera felsökning windows
1. Nu har du minst två felsökningssessionen som körs i två VS Code-fönstret. En av brytpunkten bör vara träffar.
2. Tryck på `F10` eller klicka på Stega över i den **Debug verktygsfältet**.
3. Vara bör kommer till brytpunkten i en annan VS Code-fönstret. 
4. Du kan se variabler från fler moduler i flera VS Code felsökning av windows för att fortsätta två stegen ovan.

> [!NOTE]
> Ovan exempel visar hur felsökning flera moduler med Azure IoT Edge på. Den är baserad på felsökningsversionen av den `Dockerfile.amd64.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i en behållaravbildning när du skapar den. Vi rekommenderar att du direkt använda eller anpassa den `Dockerfile` utan VSDBG för produktionsklara IoT Edge-funktion när du har slutfört felsökning din C# funktion.

## <a name="next-steps"></a>Nästa steg

När du har din modul bygger lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) 0
