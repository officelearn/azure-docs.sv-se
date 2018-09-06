---
title: Felsöka C#-moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla, skapa och Felsök en C#-modul för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b1c6209c4d589093d7a29cd8a883d3e5d4ca12f9
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782310"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka C#-moduler för Azure IoT Edge

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code (VS Code) som det huvudsakliga verktyget för att utveckla och felsöka C#-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows, macOS eller Linux som din utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet.

> [!NOTE]
> Den här felsökning artikeln visar två vanliga sätt att felsöka dina C#-modul i VS Code. Ett sätt är att koppla en process i en modul-behållare, medan den andra är att lanuch modulen koden i felsökningsläge. Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Eftersom den här artikeln används Visual Studio Code som det huvudsakliga utvecklingsverktyg, installera VS Code. Lägg sedan till tillägg som behövs:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Om du vill skapa en modul måste .NET för att skapa projektmappen, Docker för att skapa modulen avbildningen och ett behållarregister ska lagra avbildningen modulen:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

Konfigurera lokal utvecklingsmiljö för att felsöka, köra och testa din IoT Edge-lösning måste du [utvecklingsverktyg för Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installera [Python (2.7/3.6) och Pip](https://www.python.org/). Installera sedan **iotedgehubdev** genom att köra nedanstående kommando i terminalen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet följer du stegen i snabbstarten för [Windows](quickstart.md) eller [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-with-c-module"></a>Skapa en ny lösning med C#-modul

Gör följande för att skapa en IoT Edge-modul som baseras på .NET Core 2.0 med hjälp av Visual Studio Code och Azure IoT Edge-tillägget. Först skapar du en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla mer än en modul. 

1. I Visual Studio Code, Välj **visa** > **integrerade terminalen**.
3. Välj **visa** > **kommandot paletten**. 
4. Ange i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen. Välj **Välj mapp**. 
6. Ange ett namn för din lösning. 
7. Välj **C#-modul** som mall för den första modulen i lösningen.
8. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister. 
9. Ange namnet på modulens avbildningslagringsplatsen. VS Code autopopulates modulen namnet med **localhost:5000**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, sedan **localhost** är bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **\<registernamn\>. azurecr.io**. Ersätt endast localhost-delen av strängen; ta inte bort modulens namn.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-csharp-module/repository.png)

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster.

   ![Visa IoT Edge-lösning](./media/how-to-develop-csharp-module/view-solution.png)

Det finns fyra objekt inne i lösningen: 
* En **.vscode** mappen innehåller konfigurationer för felsökning.
* En **moduler** mappen innehåller undermappar för varje modul. Nu kan har du bara en. Men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. 
* En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den. 

   >[!NOTE]
   >Miljö-filen skapas endast om du anger en avbildningslagringsplatsen för modulen. Om du har godkänt localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler. 

* En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [Lär dig hur du använder distribution manifest för att distribuera moduler och upprätta vägar](module-composition.md). 

## <a name="develop-your-module"></a>Utveckla din modell

C#-modul som medföljer lösningen standardkoden finns på **moduler** > ** [Modulnamn] ** > **Program.cs**. Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub. 

När du är redo att anpassa mallen C# med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet. 

C#-stöd i VS Code är optimerat för plattformsoberoende .NET Core-utveckling. Läs mer om [hur du arbetar med C# i VS Code](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Starta och felsöka modulen kod utan behållare
IoT Edge C#-modul är en .net Core program. Och det är beroende av Azure IoT C#-enhets-SDK. I modul standardkoden du initiera en **ModuleClient** miljöinställningar Indatanamnet, vilket innebär att IoT Edge C#-modulen kräver miljöinställningar att starta och köra och du måste också skicka eller dirigera meddelanden till den inkommande kanaler. Din standard C#-modul som endast innehåller en indatakanal och namnet är **indata1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Konfigurera IoT Edge-simulator för enkel modulen app

1. För att konfigurera och starta simulatorn i VS Code kommandopalett skriver och välj **Azure IoT Edge: starta IoT Edge Hub Simulator för enkel modulen**. Du måste också ange Indatanamn för din enda modulen program skriver **indata1** och tryck på RETUR. Kommandot utlöser **iotedgehubdev** CLI och starta IoT Edge-simulatorn och en testning verktyget modulen behållare. Du kan se utdata nedan i den integrerade terminalen om simulatorn startades i läget för enskild modulen. Du kan också se en `curl` kommando för att skicka meddelande via. Du ska använda det senare.

   ![Konfigurera IoT Edge-simulator för enkel modulen app](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Du kan flytta till Docker-Utforskaren och finns i modulen Körningsstatus.

   ![Simulatorn modulens status](media/how-to-develop-csharp-module/simulator-status.png)

   Den **edgeHubDev** behållare utgör kärnan i lokal IoT Edge-simulatorn. Det kan köras på utvecklingsdatorn utan IoT Edge security daemon och ange miljöinställningar för ursprunglig modul appen eller modulen behållare. Den **inkommande** behållare exponeras restAPIs för att överbrygga meddelanden att rikta indatakanal på din modul.

2. I VS Code kommandopaletten, skriver du och väljer **Azure IoT Edge: ange autentiseringsuppgifter för modulen användarinställningar** att ställa in modulen miljöinställningar i `azure-iot-edge.EdgeHubConnectionString` och `azure-iot-edge.EdgeModuleCACertificateFile` i användarinställningarna. Du hittar dessa miljöinställningar refereras till i **.vscode** > **launch.json** och [användarinställningar för VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>Skapa modulen app och Felsök i Start-läge

1. Ändra katalogen till i integrerade terminalen **CSharpModule** mapp, kör följande kommando för att skapa .net Core program.

    ```cmd
    dotnet build
    ```

   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden via i stället för `curl`.

2. Navigera till `program.cs`. Lägg till en brytpunkt i den här filen.

3. Gå till felsökningsvyn för VS Code. Välj debug konfigurationen **ModuleName lokala felsöka (.NET Core)**. 

4. Klicka på **Starta felsökning** eller tryck på **F5**. Felsökningssessionen startas.

5. I VS Code-integrerade terminalen, kör du följande kommando för att skicka en **Hello World** meddelande till din modul. Det här är kommandot visade i föregående steg när har installationen IoT Edge-simulatorn.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Om du använder Windows kontrollerat att gränssnittet för den integrerade VS Code-terminalen är **Git Bash** eller **WSL Bash**. Du kan inte köra `curl` i PowerShell eller Kommandotolken. 
   
   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden via i stället för `curl`.

6. I VS Code Felsöka vyn visas variabler i den vänstra panelen. 

    ![Titta på variabler](media/how-to-develop-csharp-module/single-module-variables.png)

7. Stoppa felsökningssessionen genom klicka på stoppknappen eller tryck på **SKIFT + F5**. I VS Code kommandopaletten, och Skriv Välj **Azure IoT Edge: stoppa IoT Edge-simulatorn**.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Skapa modulen behållare för felsökning och felsökning i Koppla läge

Standardlösningen innehåller två moduler, en är en modul för simulerad temperatur sensorn och den andra är pipe-modul C#. Simulerade temperatursensorn ser till att skicka meddelanden till C# pipe-modell och sedan meddelandena som skickas till IoT Hub. Det finns flera Docker-filer för olika behållartyper i modulmappen som du skapade. Använd någon av dessa filer som slutar med tillägget **.debug** att skapa din modul för testning. För närvarande C# moduler support felsökning endast i Linux amd64-behållare i Koppla läge. 

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge-simulator för IoT Edge-lösning

I en utvecklingsdator måste starta du IoT Edge-simulator istället för att installera IoT Edge-daemon för säkerhet för att köra din IoT Edge-lösning. 

1. I enhetsutforskare till vänster, högerklickar du på din IoT Edge enhets-ID, Välj **installationsprogrammet IoT Edge-simulatorn** att starta simulatorn med enhetens anslutningssträng.

2. Du kan se IoT Edge simulatorn har har konfigurerats i integrerade terminalen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Skapa och köra behållare för felsökning och felsökning i Koppla läge

1. I VS Code, navigerar du till den `deployment.template.json` filen. Uppdatera dina C#-modulen bild-URL genom att lägga till **.debug** i slutet.

   ![Lägg till *** .debug till din avbildningsnamn](./media/how-to-develop-csharp-module/image-debug.png)

2. Navigera till `program.cs`. Lägg till en brytpunkt i den här filen.
3. I VS Code-Utforskaren, Välj den `deployment.template.json` klickar du på filen för din lösning på snabbmenyn **bygga och köra IoT Edge-lösning i simulatorn**. Du kan titta på behållaren för modulen loggar i samma fönster. Du kan också navigera till Docker-Utforskaren kan du titta på status för container.

   ![Titta på variabler](media/how-to-develop-csharp-module/view-log.png)

5. Gå till felsökningsvyn VS Code. Välj debug-konfigurationsfil för. Alternativnamn debug bör likna **ModuleName fjärrfelsökning (.NET Core)**

   ![Välj konfiguration](media/how-to-develop-csharp-module/debug-config.png)

6. Välj **Starta felsökning** eller välj **F5**. Välj processen för att ansluta till.

7. I VS Code Felsöka vyn visas variabler i den vänstra panelen.

8. Stoppa felsökningssessionen genom klicka på stoppknappen eller tryck på **SKIFT + F5**. I VS Code kommandopaletten, och Skriv Välj **Azure IoT Edge: stoppa IoT Edge-simulatorn**.

> [!NOTE]
> Det här exemplet visar hur du felsöker .NET Core IoT Edge-moduler i behållare. Den är baserad på felsökningsversionen av `Dockerfile.debug`, som innehåller .NET Core kommandoradsverktyget felsökningsprogrammet VSDBG i en behållaravbildning när du skapar den. När du felsöker dina C#-moduler, rekommenderar vi att du direkt använda eller anpassa `Dockerfile` utan VSDBG för produktionsklara IoT Edge-moduler.

## <a name="next-steps"></a>Nästa steg

När du har skapat din modul, lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
