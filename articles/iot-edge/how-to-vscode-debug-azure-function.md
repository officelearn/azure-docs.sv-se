---
title: Felsöka funktioner moduler för Azure IoT kant | Microsoft Docs
description: Använda Visual Studio-koden för att felsöka C# Azure Functions med Azure IoT kant
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52c5afbd37459267181cfc1d8d8d2620aff37913
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630790"
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Använda Visual Studio-koden för att felsöka Azure Functions med Azure IoT kant

Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio (VS) kod](https://code.visualstudio.com/) att felsöka dina Azure-funktioner på IoT kant.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller kan du simulera enheten IoT kanten på utvecklingsdatorn.

> [!NOTE]
> Du kan bara felsöka C#-funktioner i linux-amd64-behållare.

Innan du följer riktlinjerna i den här artikeln bör du slutföra stegen i [utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Sedan kan ha du följande till hands:
- En lokal Docker-registret som körs på utvecklingsdatorn. Du rekommenderas för att använda en lokal Docker-registret för prototyp och testning. Du kan uppdatera registret behållare i den `module.json` filen i varje modul.
- En IoT-Edge lösning Projektarbetsyta med en Azure-funktion modulen undermapp i den.
- Den `run.csx` med funktionskod.
- En kant körning som körs på utvecklingsdatorn.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Skapa din IoT kant funktionen-modul för felsökning syfte
1. Om du vill starta felsökning, måste du använda **Dockerfile.amd64.debug** att återskapa docker-avbildning och distribuera lösningar Edge igen. VS kod explorer, navigera till `deployment.template.json` filen. Uppdatera din funktion bild-URL genom att lägga till en `.debug` i slutet.

    ![Skapa Debug-bild](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Återskapa din lösning. Skriv i VS kod kommandot paletten och kör kommandot **kant: skapa gräns för IoT-lösningen**.
3. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för gränsenheten**. Välj den `deployment.json` filen den `config` mapp. Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

Du kan kontrollera din behållaren status i VS kod Docker-Utforskaren eller genom att köra den `docker images` i terminalen.

## <a name="start-debugging-c-function-in-vs-code"></a>Starta felsökning C#-funktionen i VS-kod
1. VS-kod för att hålla felsökning konfigurationsinformationen i en `launch.json` fil i en `.vscode` mapp i arbetsytan. Detta `launch.json` filen genererades när du skapade en ny gräns för IoT-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. Navigera till felsökningsvyn och välj den motsvarande debug-konfigurationsfilen.
    ![Välj debug-konfiguration](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navigera till `run.csx`. Lägga till en brytpunkt i funktionen.
3. Klicka på den **Start Debugging** eller tryck på knappen **F5**, och välj den process för att ansluta till.
4. I VS kod Felsöka vyn ser variabler i den vänstra panelen. 


> [!NOTE]
> Ovanstående exempel visar hur du felsökning .net huvudfunktion IoT kanten på behållaren. Den är baserad på felsökningsversionen av den `Dockerfile.amd64.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. Vi rekommenderar att du direkt använda eller anpassa den `Dockerfile` utan VSDBG för produktionsklara IoT kant funktionen när du har slutfört felsökning C#-funktionen.

## <a name="next-steps"></a>Nästa steg


[Använda Visual Studio-koden för att felsöka C#-modulen med Azure IoT kant](how-to-vscode-debug-csharp-module.md)

