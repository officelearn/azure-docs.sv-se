---
title: Felsöka C#-moduler för Azure IoT kant | Microsoft Docs
description: Använda Visual Studio-koden för att utveckla, skapa och felsöka C#-modulen för Azure IoT kant
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c237b1eb9874357afa922f809109cf8f2181561e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048194"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Använda Visual Studio-koden för att utveckla och felsöka C#-moduler för Azure IoT kant

Du kan skicka affärslogik ska fungera i utkanten av förvandlar den till moduler för Azure IoT kant. Den här artikeln innehåller detaljerade anvisningar för att använda Visual Studio-koden (VS) som den huvudsakliga utvecklingsverktyg för att utveckla C#-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller så kan du simulera en IoT Edge-enhet på utvecklingsdato.

> [!NOTE]
> Självstudierna felsökning beskriver hur du ansluter en process i en modul-behållare och felsöka med VS-kod. Du kan bara felsöka C#-funktioner i linux-amd64-behållare. Om du inte är bekant med funktionerna felsökning i Visual Studio Code kan läsa om [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Eftersom den här artikeln används Visual Studio Code som den huvudsakliga utvecklingsverktyg, installera VS koden och Lägg sedan till tillägg som behövs:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT kant-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Om du vill skapa en modul måste .NET som bygger projektmappen Docker att skapa modulen avbildningen och en behållare registret för att lagra avbildningen modulen:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) på utvecklingsdatorn. 
* [Azure-behållaren registret](https://docs.microsoft.com/azure/container-registry/) eller [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Du kan använda en lokal Docker-registret för prototyp och testning, i stället för ett moln-register. 

Om du vill testa din modulen på en enhet måste en aktiv IoT-hubb med minst en IoT-enhet. Om du vill använda din dator som en IoT-enhet kan du göra det genom att följa stegen i självstudier för [Windows](quickstart.md) eller [Linux](quickstart-linux.md) 

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT kant-modul som baseras på .NET Core 2.0 med hjälp av Visual Studio Code och Azure IoT kant-tillägget. Börja med att skapa en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla flera moduler. 

1. Välj i Visual Studio Code **visa** > **integrerad Terminal**.
3. Välj **visa** > **kommandot paletten**. 
4. Skriv i paletten kommandot och kör kommandot **Azure IoT kant: ny IoT kant lösning**.

   ![Kör ny gräns för IoT-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen och klicka på **väljer mappen**. 
6. Ange ett namn för din lösning. 
7. Välj **C#-modulen** som mall för den första modulen i lösningen.
8. Ange ett namn för din modulen. Välj ett namn som är unikt i behållaren registret. 
9. Ange avbildningslagringsplatsen för modulen. VS kod autopopulates modulen namn, så du behöver bara ersätta **localhost:5000** med din egen information i registret. Om du använder en lokal Docker-registret för att testa räcker det bra med localhost. Om du använder Azure-behållare registret kan sedan använda inloggnings-servern från din registerinställningar. Inloggningsserver ser ut som  **\<registrets\>. azurecr.io**.

VS-koden tar den information du tillhandahålls, skapar en gräns för IoT-lösning och läser in den i ett nytt fönster.

   ![Visa kant för IoT-lösning](./media/how-to-develop-csharp-module/view-solution.png)

Du har tre objekt i lösningen: 
* En **.vscode** mappen innehåller debug-konfigurationer.
* En **moduler** mappen innehåller undermappar för varje modul. Just nu bara ha en, men du kan lägga till fler i paletten kommando med kommandot **Azure IoT kant: Lägg till IoT kant modul**. 
* En **.env** filen visar aktuella miljövariabler. Om du ACR som registret, just nu har ACR användarnamn och lösenord i den. 
* En **deployment.template.json** filen visar dina nya modulen tillsammans med ett exempel på en **tempSensor** modul som simulerar data som du kan använda för att testa. Mer information om hur distributionen visar arbete finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Skapa och distribuera din modul för felsökning

Det finns flera Docker-filer för olika behållartyper i varje modul-mapp. Du kan använda någon av dessa filer som slutar med filnamnstillägget **.debug** att skapa en modul för testning. C#-moduler stöder för närvarande endast felsökning i linux-amd64-behållare.

1. VS-kod, navigera till den `deployment.template.json` filen. Uppdatera din funktion bild-URL genom att lägga till **.debug** till slutet.

   ![Lägg till .debug avbildningens namn](./media/how-to-develop-csharp-module/image-debug.png)

2. Skriv i paletten VS kod kommandot och kör kommandot **kant: skapa gräns för IoT-lösningen**.
3. Välj den `deployment.template.json` filen för din lösning från paletten kommando. 
4. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för IoT-enhet**. 
5. Öppna den **config** mapp i lösningen, välj sedan den `deployment.json` filen. Klicka på **Välj kant Distributionsmanifestet**. 

Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

Du kan kontrollera din behållaren status i VS kod Docker-Utforskaren eller genom att köra den `docker ps` i terminalen.

## <a name="start-debugging-c-module-in-vs-code"></a>Starta felsökning C#-modulen i VS-kod
VS-kod för att hålla felsökning konfigurationsinformationen i en `launch.json` fil i en `.vscode` mapp i arbetsytan. Detta `launch.json` filen genererades när du skapade en ny gräns för IoT-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. 

1. Navigera till felsökningsvyn VS-kod och välj debug-konfigurationsfil för ditt modulen. Alternativnamn debug ska vara som **Modulnamn fjärrfelsökning (.NET Core)** ![väljer debug-konfiguration](./media/how-to-develop-csharp-module/debug-config.png)

2. Navigera till `program.cs`. Lägga till en brytpunkt i den här filen.

3. Klicka på den **Start Debugging** eller tryck på knappen **F5**, och välj den process för att ansluta till.

4. I VS kod Felsöka vyn ser variabler i den vänstra panelen. 

> [!NOTE]
> Föregående exempel visar hur du felsöka .NET Core IoT kant moduler i behållare. Den är baserad på felsökningsversionen av den `Dockerfile.debug`, som innehåller VSDBG (.NET Core kommandoradsverktyget felsökare) i behållaren avbildningen när du skapar den. När du är klar med att felsöka C#-moduler rekommenderar vi du direkt använda eller anpassa `Dockerfile` utan VSDBG för produktionsklara IoT kant moduler.

## <a name="next-steps"></a>Nästa steg

När du har den inbyggda modulen lär du dig hur du [distribuera Azure IoT kant moduler från Visual Studio Code](how-to-deploy-modules-vscode.md)

