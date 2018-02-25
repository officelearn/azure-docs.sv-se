---
title: "Använda Visual Studio-koden för att felsöka C#-modulen med Azure IoT kant | Microsoft Docs"
description: "Felsöka C#-modulen med Azure IoT gränsen i Visual Studio-koden."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5ed517cf8d70cd279a55b79ad448709116cf511b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Använda Visual Studio-koden för att felsöka C#-modulen med Azure IoT kant
Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio Code](https://code.visualstudio.com/) som den huvudsakliga utvecklingsverktyg för att felsöka dina Azure IoT kant-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller du kan simulera enheten IoT kanten på utvecklingsdatorn.

Innan du börjar den här vägledningen måste du slutföra följande kursen:
- [Använd Visual Studio-koden för att utveckla C#-modulen med Azure IoT kant](how-to-vscode-develop-csharp-module.md)

Du bör ha följande till hands när du har slutfört föregående kursen:
- En lokal Docker-registret som körs på utvecklingsdatorn. Det här är för prototyper och testning.
- Den `Program.cs` filen med den senaste filter modulen kod.
- En uppdaterad `deployment.json` -filen för modulerna sensor och filter.
- En IoT-Edge-körning som körs på utvecklingsdatorn.

## <a name="build-your-iot-edge-module-for-debugging"></a>Skapa din IoT-Edge-modul för felsökning
1. Starta felsökning med **dockerfile.debug** att återskapa Docker-avbildning och distribuera kant för IoT-lösningen igen. Välj mappen Docker att öppna den i Visual Studio Code Explorer. Välj sedan den **linux x64** mapp, högerklickar du på **Dockerfile.debug**, och välj **skapa IoT kant modulen Docker bild**.

    ![Skärmbild av VS kod Explorer](./media/how-to-debug-csharp-module/build-debug-image.png)

3. I den **Välj mappen** och bläddra till eller ange **./bin/Debug/netcoreapp2.0/publish**. Välj sedan **Välj mapp som EXE_DIR**.
4. Ange avbildningens namn i popup-textrutan längst upp i fönstret VS-kod. Till exempel: `<your container registry address>/filtermodule:latest`. Om du distribuerar till lokala registret ska: `localhost:5000/filtermodule:latest`.
5. Skicka bilden till Docker-databasen. Använd den **kant: Push-gräns för IoT-modulen Docker bild** kommando och ange bildens URL i popup-textrutan längst upp i fönstret VS-kod. Använd samma bild-URL som du använde i föregående steg.
6. Du kan återanvända den `deployment.json` att distribuera om. Skriv i paletten kommando, och välj **kant: starta om Edge** att hämta dina filtermodul som körs med felsökningsversionen.

## <a name="start-debugging-in-vs-code"></a>Starta felsökning i VS-kod
1. Gå till felsökningsfönstret VS-kod. Tryck på **F5**, och välj **IoT Edge(.NET Core)**.

    ![Skärmbild av VS kod felsökningsfönstret](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. I `launch.json`, bläddra till den **felsöka IoT kant anpassad modul (.NET Core)** avsnitt. Under **pipeArgs**, Fyll i den `<container_name>`. Det bör vara `filtermodule` i den här självstudiekursen.

    ![Skärmbild av VS Code launch.json](./media/how-to-debug-csharp-module/add-container-name.png)

3. Bläddra till **Program.cs**. Lägga till en brytpunkt i den `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Tryck på **F5** igen, och välj den process för att ansluta till. I den här självstudiekursen processens namn bör vara `FilterModule.dll`.

    ![Skärmbild av VS kod felsökningsfönstret](./media/how-to-debug-csharp-module/attach-process.png)

5. Du kan se variabler i vänsterpanelen i felsökningsfönstret VS-kod. 

> [!NOTE]
> Föregående exempel visar hur du felsöka .NET Core IoT kant moduler i behållare. Den är baserad på felsökningsversionen av den `Dockerfile.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. När du är klar med att felsöka C#-moduler rekommenderar vi du direkt använda eller anpassa `Dockerfile` utan VSDBG för produktionsklara IoT kant moduler.

## <a name="next-steps"></a>Nästa steg

I kursen får du skapat en gräns för IoT-modul och distribuerat för felsökning. Du startade felsökning i VS-kod. Mer information om andra scenarier när du utvecklar Azure IoT gränsen i VS koden finns: 

> [!div class="nextstepaction"]
> [Utveckla och distribuera C#-modulen i VS-kod](how-to-vscode-develop-csharp-module.md)
