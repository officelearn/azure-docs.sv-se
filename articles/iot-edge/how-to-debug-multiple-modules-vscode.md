---
title: Felsöka flera moduler för Azure IoT gränsen i VS kod | Microsoft Docs
description: Använda Visual Studio-koden för att felsöka flera moduler med Azure IoT kant
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049599"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Använda Visual Studio-koden för att felsöka flera moduler med Azure IoT kant
Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio (VS) kod](https://code.visualstudio.com/) felsöka flera moduler på IoT kant.

## <a name="prerequisites"></a>Förutsättningar
Slutför guiden [utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code](tutorial-multiple-modules-in-vscode.md) och kontrollera att du har minst två moduler som körs på enheten IoT kant.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Flera mål och fjärrfelsökning i VS-kod
Du kan koppla modulen processen i en behållare om behållaren körs på utvecklingsdatorn eller i en fjärransluten fysiska IoT Edge-enhet med VS-kod och Azure IoT kant-tillägget. Felsök mutiple moduler som körs i behållare faktiskt kopplar mer än en process i separata behållare. VS kod [flera mål felsökning](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) kan användas vid felsökning av mutiple moduler.

   > [!TIP]
   > Om modulen-behållare körs i en fjärransluten fysiska IoT insticksenhet, du kan behöva installationen [Docker datorn](https://docs.docker.com/machine/overview/) så att Docker-motorn på utvecklingsdatorn kan kommunicera med de fjärranslutna Docker-värdarna.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Skapa din IoT-Edge moduler för felsökning syfte
1. Om du vill starta flera moduler felsökning, måste du använda **Dockerfile.amd64.debug** att återskapa docker-bilder och distribuera lösningar Edge igen. VS kod explorer, navigera till `deployment.template.json` filen. Uppdatera din bild-URL: er genom att lägga till en `.debug` i slutet. Du behöver två modulen bilder med `.debug` minst. Om du arbetar med lösningar från tidigare kursen bör du ha en C#-funktioner modul och en C#-modul. Uppdatera dessa två bild-URL: er genom att lägga till en `.debug` i slutet och spara den här filen. 
2. Återskapa din lösning. Skriv i VS kod kommandot paletten och kör kommandot **Azure IoT kant: skapa gräns för IoT-lösningen**.
3. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för gränsenheten**. Välj den `deployment.json` filen den `config` mapp. Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

Du kan kontrollera din behållaren status i VS kod Docker-Utforskaren eller genom att köra den `docker ps` i terminalen.

### <a name="start-debugging-c-function-in-vs-code"></a>Starta felsökning C#-funktionen i VS-kod
1. VS-kod för att hålla felsökning konfigurationsinformationen i en `launch.json` fil i en `.vscode` mapp i arbetsytan. Detta `launch.json` filen genererades när du skapade en ny gräns för IoT-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. Navigera till felsökningsvyn och välj den motsvarande debug-konfigurationsfilen för C# funktioner modulen fjärrfelsökning.
2. Navigera till `run.csx`. Lägga till en brytpunkt i funktionen.
3. Klicka på den **Start Debugging** eller tryck på knappen **F5**, och välj den process för att ansluta till.
4. I VS kod Felsöka vyn ser variabler i den vänstra panelen. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Starta felsökning C#-modulen samtidigt i VS-kod
1. Skriv i VS kod kommandot paletten och kör kommandot ”arbetsytan: duplicera arbetsytan i nytt fönster”. Ett nytt fönster för VS kod börjar med samma arbetsyta.
2. Navigera till felsökningsvyn och välj den motsvarande debug-konfigurationsfilen för C# modulen fjärrfelsökning.
3. Navigera till `program.cs`. Lägga till en brytpunkt i C#-modulen.
4. Klicka på den **Start Debugging** eller tryck på knappen **F5**, och välj den process för att ansluta till.
5. I VS kod Felsöka vyn ser variabler i den vänstra panelen. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Se variabler i flera fönster för felsökning
1. Nu har du minst två felsökningssessionen körs i två VS-kod. Vara bör nådde en brytpunkten.
2. Tryck på `F10` eller klicka på knappen Stega i den **Debug verktygsfältet**.
3. Vara bör nådde brytpunkt i en annan VS-kod. 
4. Du kan se variabler från fler moduler i flera VS kod felsökning av windows för att fortsätta ovan två steg.

> [!NOTE]
> Ovanstående exempel visar hur felsökning flera moduler med Azure IoT kanten på. Den är baserad på felsökningsversionen av den `Dockerfile.amd64.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. Vi rekommenderar att du direkt använda eller anpassa den `Dockerfile` utan VSDBG för produktionsklara IoT kant funktionen när du har slutfört felsökning C#-funktionen.

## <a name="next-steps"></a>Nästa steg

När du har den inbyggda modulen lär du dig hur du [distribuera Azure IoT kant moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) 0
