---
title: "Använda Visual Studio-koden för att felsöka Azure Functions med Azure IoT kant | Microsoft Docs"
description: "Felsöka C# Azure Functions med Azure IoT-gränsen i VS-kod"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4344a450d218a7424cd055cf086c1e4865c9af10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Använda Visual Studio-koden för att felsöka Azure Functions med Azure IoT kant

Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio Code](https://code.visualstudio.com/) som den huvudsakliga utvecklingsverktyg för att felsöka dina Azure-funktioner på IoT kant.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller kan du simulera enheten IoT kanten på utvecklingsdatorn.

Kontrollera att du har slutfört följande kurser innan du börjar den här vägledningen.
- [Använda Visual Studio-koden för att utveckla och distribuera Azure Functions Azure IoT kant](how-to-vscode-develop-azure-function.md)

När du slutför föregående kursen, bör du ha följande till hands,
- En lokal Docker-registret som körs på utvecklingsdatorn. Du rekommenderas för att använda en lokal Docker-registret för prototyp och testning.
- Den `run.csx` fil med senaste filter funktionskoden.
- En uppdaterad `deployment.json` filen för modulen sensor och filter funktionsmodul.
- En kant körning som körs på utvecklingsdatorn.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Skapa din IoT-Edge-modul för felsökning syfte
1. Om du vill starta felsökning, måste du använda den **dockerfile.debug** att återskapa docker-avbildning och distribuera lösningar Edge igen. Klicka på mappen Docker för att öppna den i VS kod explorer. Klicka på den `linux-x64` mapp, högerklickar du på den **Dockerfile.debug**, och klicka på **skapa IoT kant modulen Docker bild**.
3. I den **Välj mappen** och navigera till den **FilterFunction** projektet och klicka på **Välj mapp som EXE_DIR**.
4. Ange avbildningens namn i popup-textrutan längst upp i fönstret VS-kod. Till exempel: `<your container registry address>/filterfunction:latest`. Om du distribuerar till lokala registret ska `localhost:5000/filterfunction:latest`.
5. Skicka bilden till Docker-databasen. Använd den **kant: Push-gräns för IoT-modulen Docker bild** kommando och ange bildens URL i popup-textrutan längst upp i fönstret VS-kod. Använd samma bild-URL som du använde i senare steg.
6. Du kan återanvända den `deployment.json` att distribuera om. Skriv i kommandot paletten, och välj **kant: starta om Edge** att hämta dina filterfunktionen körs med felsökningsversionen.

## <a name="start-debugging-in-vs-code"></a>Starta felsökning i VS-kod
1. Gå till VS kod felsökningsfönstret. Tryck på **F5** och välj **IoT Edge(.Net Core)**
2. I `launch.json`, gå till **felsöka IoT kant-funktionen (.NET Core)** avsnittet och Fyll i den `<container_name>`under `pipeArgs`. Det bör vara `filterfunction` i den här självstudiekursen.
3. Gå till run.csx. Lägga till en brytpunkt i funktionen.
4. Tryck på **F5** igen. Markera processen för att ansluta till.
5. Du kan se variabler i vänsterpanelen i VS kod Debug-fönstret. 

> [!NOTE]
> Ovanstående exempel visar hur du felsökning .net huvudfunktion IoT kanten på behållaren. Den är baserad på felsökningsversionen av den `Dockerfile.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. Vi rekommenderar att du direkt använda eller anpassa den `Dockerfile` utan VSDBG för produktionsklara IoT kant funktionen när du har slutfört felsökning C#-funktionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen, skapas en Azure-funktion och distribueras till IoT kant för felsökning ändamål och starta felsökning i VS-kod. Du kan fortsätta in på något av följande kurser vill veta mer om andra scenarier när du utvecklar Azure IoT gränsen i VS-kod. 

> [!div class="nextstepaction"]
> [Utveckla och distribuera C#-modulen i VS-kod](how-to-vscode-develop-csharp-module.md)

