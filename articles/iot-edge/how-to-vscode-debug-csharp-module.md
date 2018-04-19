---
title: Använda Visual Studio-koden för att felsöka C#-modulen med Azure IoT kant | Microsoft Docs
description: Felsöka C#-modulen med Azure IoT gränsen i Visual Studio-koden.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 65f2fb4526f1048ae88193f85a552a2202afa1d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Använda Visual Studio-koden för att felsöka C#-modulen med Azure IoT kant
Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio Code](https://code.visualstudio.com/) som den huvudsakliga utvecklingsverktyg för att felsöka dina Azure IoT kant-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller så kan du simulera en IoT Edge-enhet på utvecklingsdato.

Innan du följer riktlinjerna i den här artikeln bör du slutföra stegen i [utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Sedan kan ha du följande till hands:
- En lokal Docker-registret som körs på utvecklingsdatorn. Du rekommenderas för att använda en lokal Docker-registret för prototyp och testning. Du kan uppdatera registret behållare i den `module.json` filen i varje modul.
- En IoT-Edge lösning Projektarbetsyta med en C#-modulen undermapp i den.
- Den `Program.cs` filen med den senaste modul kod.
- En kant körning som körs på utvecklingsdatorn.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Skapa din IoT kant C#-modul för felsökning
1. Om du vill starta felsökning, måste du använda den **Dockerfile.amd64.debug** att återskapa docker-avbildning och distribuera lösningar Edge igen. VS kod explorer, navigera till `deployment.template.json` filen. Uppdatera din funktion bild-URL genom att lägga till en `.debug` i slutet.

2. Återskapa din lösning. Skriv i VS kod kommandot paletten och kör kommandot **kant: skapa gräns för IoT-lösningen**.

3. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för gränsenheten**. Välj den `deployment.json` under `config` mapp. Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

> [!NOTE]
> Du kan kontrollera din behållaren status i VS kod Docker-Utforskaren eller genom att köra den `docker images` i terminalen.

## <a name="start-debugging-c-module-in-vs-code"></a>Starta felsökning C#-modulen i VS-kod
1. VS-kod för att hålla felsökning konfigurationsinformationen i en `launch.json` fil i en `.vscode` mapp i arbetsytan. Detta `launch.json` filen har genererats när du skapar en ny gräns för IoT-lösning. Och uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. Navigera till felsökningsvyn och välj den motsvarande debug-konfigurationsfilen.
    ![Välj debug-konfiguration](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navigera till `program.cs`. Lägga till en brytpunkt i den här filen.

3. Klicka på knappen Start Debugging eller tryck på **F5**, och välj den process för att ansluta till.

4. I VS kod Felsöka vyn ser variabler i den vänstra panelen. 

> [!NOTE]
> Föregående exempel visar hur du felsöka .NET Core IoT kant moduler i behållare. Den är baserad på felsökningsversionen av den `Dockerfile.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. När du är klar med att felsöka C#-moduler rekommenderar vi du direkt använda eller anpassa `Dockerfile` utan VSDBG för produktionsklara IoT kant moduler.

## <a name="next-steps"></a>Nästa steg

[Använda Visual Studio-koden för att felsöka Azure Functions med Azure IoT kant](how-to-vscode-debug-azure-function.md)

