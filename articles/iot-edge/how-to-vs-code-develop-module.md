---
title: Utveckla och felsöka moduler för Azure IoT Edge | Microsoft-dokument
description: Använda Visual Studio-kod för att utveckla, skapa och felsöka en modul för Azure IoT Edge med C#, Python, Node.js, Java eller C
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944299"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Använda Visual Studio-kod för att utveckla och felsöka moduler för Azure IoT Edge

Du kan förvandla din affärslogik till moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code som huvudverktyg för att utveckla och felsöka moduler.

Det finns två sätt att felsöka moduler skrivna i C#, Node.js eller Java i Visual Studio-kod: Du kan antingen bifoga en process i en modulbehållare eller starta modulkoden i felsökningsläge. För att felsöka moduler skrivna i Python eller C kan du bara koppla till en process i Linux amd64-behållare.

Om du inte är bekant med felsökningsfunktionerna i Visual Studio-kod läser du om [Felsökning](https://code.visualstudio.com/Docs/editor/debugging).

Den här artikeln innehåller instruktioner för att utveckla och felsöka moduler på flera språk för flera arkitekturer. För närvarande ger Visual Studio Code stöd för moduler skrivna i C#, C, Python, Node.js och Java. Enhetsarkitekturerna som stöds är X64 och ARM32. Mer information om operativsystem, språk och arkitekturer som stöds finns i [Stöd för språk och arkitektur](module-development.md#language-and-architecture-support).

>[!NOTE]
>Utveckla och felsöka stöd för Linux ARM64-enheter är i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [Utveckla och felsöka ARM64 IoT Edge-moduler i Visual Studio Code (förhandsversion).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

## <a name="prerequisites"></a>Krav

Du kan använda en dator eller en virtuell dator som kör Windows, macOS eller Linux som utvecklingsdator. På Windows-datorer kan du utveckla antingen Windows- eller Linux-moduler. Om du vill utveckla Windows-moduler använder du en Windows-dator som kör version 1809/build 17763 eller nyare. Om du vill utveckla Linux-moduler använder du en Windows-dator som uppfyller [kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Installera [Visual Studio-kod](https://code.visualstudio.com/) först och lägg sedan till följande tillägg:

- [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio-tillägg som är specifika för det språk du utvecklar på:
  - C#, inklusive Azure-funktioner: [C#-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++-förlängning](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Du måste också installera några ytterligare språkspecifika verktyg för att utveckla modulen:

- C#, inklusive Azure-funktioner: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) och [Pip](https://pip.pypa.io/en/stable/installing/#installation) för installation av Python-paket (ingår vanligtvis i din Python-installation).

- Node.js: [nod.js.](https://nodejs.org) Du vill också installera [Yeoman](https://www.npmjs.com/package/yo) och [Azure IoT Edge Node.js Module Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) och [Maven](https://maven.apache.org/). Du måste [ställa in `JAVA_HOME` miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) så att den pekar på JDK-installationen.

Om du vill skapa och distribuera modulavbildningen måste Du docker skapa modulavbildningen och ett behållarregister för att hålla modulavbildningen:

- [Docker Community Edition](https://docs.docker.com/install/) på din utvecklingsmaskin.

- [Azure-behållarregister-](https://docs.microsoft.com/azure/container-registry/) eller [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Du kan använda ett lokalt Docker-register för prototyp- och testningsändamål i stället för ett molnregister.

Om du inte utvecklar din modul i C behöver du också python-baserade [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) för att konfigurera din lokala utvecklingsmiljö för att felsöka, köra och testa din IoT Edge-lösning. Om du inte redan har gjort det installerar du [Python (2.7/3.6/3.7) och Pip](https://www.python.org/) och installerar sedan **iotedgehubdev** genom att köra det här kommandot i din terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> För närvarande använder iotedgehubdev ett docker-py-bibliotek som inte är kompatibelt med Python 3.8.
>
> Om du har flera Python inklusive förinstallerad python 2.7 (till exempel på Ubuntu eller macOS), se till att du använder rätt `pip` eller `pip3` installera **iotedgehubdev**

Om du vill testa modulen på en enhet behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda datorn som IoT Edge-enhet följer du stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge-demon på utvecklingsdatorn kan du behöva stoppa EdgeHub och EdgeAgent innan du går vidare till nästa steg.

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT Edge-modul på önskat utvecklingsspråk (inklusive Azure-funktioner, skrivna i C#) med Visual Studio-kod och Azure IoT Tools. Du börjar med att skapa en lösning och sedan generera den första modulen i den lösningen. Varje lösning kan innehålla flera moduler.

1. Välj **Visa** > **kommandopalett**.

1. Ange och kör kommandot **Azure IoT Edge: Ny IoT Edge-lösning**i kommandopaletten .

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

1. Bläddra till mappen där du vill skapa den nya lösningen och välj sedan **Välj mapp**.

1. Ange ett namn på din lösning.

1. Välj en modulmall för önskat utvecklingsspråk som den första modulen i lösningen.

1. Ange ett namn på modulen. Välj ett namn som är unikt i behållarregistret.

1. Ange namnet på modulens bilddatabas. Visual Studio Code fyller automatiskt i modulnamnet med **localhost:5000/<ditt modulnamn\>**. Ersätt den med din egen registerinformation. Om du använder ett lokalt Docker-register för testning är **localhost** bra. Om du använder Azure Container Registry använder du inloggningsservern från registrets inställningar. Inloggningsservern ser ut som ** _ \<registernamnet\>_.azurecr.io**. Ersätt endast den **localhost:5000** delen av strängen så att slutresultatet ser ut som ** \< *registernamn*\>.azurecr.io/_\<modulnamnet\>_**.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code tar den information du angav, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster.

Det finns fyra objekt i lösningen:

- En **VSCODE-mapp** innehåller felsökningskonfigurationer.

- En **moduler** mapp har undermappar för varje modul.  I mappen för varje modul finns en fil, **module.json**, som styr hur moduler byggs och distribueras.  Den här filen måste ändras för att ändra modulen distributionsbehållare register från localhost till ett fjärrregister. Nu har du bara en modul.  Men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Add IoT Edge Module**.

- En **.env-fil** listar dina miljövariabler. Om Azure Container Registry är ditt register har du ett användarnamn och lösenord för Azure Container Registry i det.

  > [!NOTE]
  > Miljöfilen skapas bara om du tillhandahåller en avbildningsdatabas för modulen. Om du accepterade localhost standardvärden för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler.

- I en **deployment.template.json-fil** visas den nya modulen tillsammans med en exempelmodul **SimulatedTemperatureSensor** som simulerar data som du kan använda för testning. Mer information om hur distributionsmanifest fungerar finns i [Lär dig hur du använder distributionsmanifest för att distribuera moduler och upprätta vägar](module-composition.md).

Om du vill se hur den simulerade temperaturmodulen fungerar visar du [källkoden SimuleradTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Lägg till ytterligare moduler

Om du vill lägga till ytterligare moduler i din lösning kör du kommandot **Azure IoT Edge: Add IoT Edge Module** från kommandopaletten. Du kan också högerklicka på **modulens** mapp eller `deployment.template.json` filen i vyn Visual Studio-kodutforskaren och sedan välja Lägg till **IoT Edge Module**.

## <a name="develop-your-module"></a>Utveckla din modul

Standardmodulkoden som medföljer lösningen finns på följande plats:

- Azure Function (C#): **moduler > * &lt;din&gt;modul namn* > *&lt;din modul&gt;namn*.cs**
- C#: **moduler > * &lt;modulnamnet&gt; * > Program.cs**
- Python: **moduler > * &lt;ditt&gt; modulnamn* > main.py**
- Node.js: **moduler > * &lt;modulnamnet&gt; * > app.js**
- Java: **moduler > * &lt;din modul&gt; namn* > src > viktigaste > java > com > edgemodulemodules > App.java**
- C: **moduler > * &lt;modulnamnet&gt; * > main.c**

Modulen och filen deployment.template.json är konfigurerade så att du kan skapa lösningen, skicka den till behållarregistret och distribuera den till en enhet för att starta testningen utan att röra någon kod. Modulen är byggd för att helt enkelt ta indata från en källa (i det här fallet modulen SimuleradTemperatureSensor som simulerar data) och leda den till IoT Hub.

När du är redo att anpassa mallen med din egen kod använder du [Azure IoT Hub SDK:er](../iot-hub/iot-hub-devguide-sdks.md) för att skapa moduler som tillgodoser de viktigaste behoven för IoT-lösningar som säkerhet, enhetshantering och tillförlitlighet.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Felsöka en modul utan behållare (C#, Node.js, Java)

Om du utvecklar i C#, Node.js eller Java kräver modulen användning av ett **ModuleClient-objekt** i standardmodulkoden så att den kan starta, köra och dirigera meddelanden. Du använder också **standardindatakanalen indata1** för att vidta åtgärder när modulen tar emot meddelanden.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge-simulator för IoT Edge-lösning

På din utvecklingsmaskin kan du starta en IoT Edge-simulator i stället för att installera säkerhetsdemonen IoT Edge så att du kan köra din IoT Edge-lösning.

1. Högerklicka på IoT Edge-enhets-ID:n i enhetsutforskaren på vänster sida och välj sedan **Setup IoT Edge Simulator** för att starta simulatorn med enhetsanslutningssträngen.
1. Du kan se att IoT Edge Simulator har konfigurerats genom att läsa förloppsdetaljerna i den integrerade terminalen.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurera IoT Edge-simulator för enmodulsapp

Om du vill konfigurera och starta simulatorn kör du kommandot **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** från kommandopaletten Visual Studio Code. När du uppmanas till det använder du värdet **input1** från standardmodulkoden (eller motsvarande värde från koden) som indatanamn för ditt program. Kommandot utlöser **iotedgehubdev** CLI och startar sedan IoT Edge-simulatorn och en testverktygsmodulbehållare. Du kan se utgångarna nedan i den integrerade terminalen om simulatorn har startats i enmodulläge. Du kan också `curl` se ett kommando som hjälper till att skicka meddelande via. Du ska använda det senare.

   ![Konfigurera IoT Edge-simulator för enmodulsapp](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Du kan använda Docker Explorer-vyn i Visual Studio-kod för att se modulens status som körs.

   ![Status för simulatormodul](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev-behållaren** är kärnan i den lokala IoT Edge-simulatorn. Den kan köras på din utvecklingsmaskin utan IoT Edge-säkerhetsdemonen och tillhandahåller miljöinställningar för din inbyggda modulapp eller modulbehållare. **Indatabehållaren** exponerar REST-API:er för att skicka meddelanden till målinmatningskanalen på modulen.

### <a name="debug-module-in-launch-mode"></a>Felsökningsmodul i startläge

1. Förbered din miljö för felsökning enligt kraven i ditt utvecklingsspråk, ange en brytpunkt i modulen och välj den felsökningskonfiguration som ska användas:
   - **C#**
     - I den integrerade terminalen för Visual *** &lt;&gt; *** Studio-kod ändrar du katalogen till mappen för modulnamn och kör sedan följande kommando för att skapa .NET Core-programmet.

       ```cmd
       dotnet build
       ```

     - Öppna filen `Program.cs` och lägg till en brytpunkt.

     - Navigera till vyn Debug för visuell studiokod genom att välja **Visa > Debug**. Välj felsökningskonfigurationen för ** * &lt;modulnamnet&gt; * Local Debug (.NET Core)** i listrutan.

        > [!NOTE]
        > Om .NET `TargetFramework` Core inte överensstämmer med `launch.json`programsökvägen i måste du uppdatera `launch.json` programsökvägen `TargetFramework` manuellt för att matcha filen i CSproj så att Visual Studio Code kan starta programmet manuellt.

   - **Node.js**
     - I den integrerade terminalen för Visual *** &lt;&gt; *** Studio-kod ändrar du katalogen till mappen för modulnamn och kör sedan följande kommando för att installera nodpaket

       ```cmd
       npm install
       ```

     - Öppna filen `app.js` och lägg till en brytpunkt.

     - Navigera till vyn Debug för visuell studiokod genom att välja **Visa > Debug**. Välj felsökningskonfigurationen för ** * &lt;modulnamnet&gt; * Local Debug (Node.js)** i listrutan.
   - **Java**
     - Öppna filen `App.java` och lägg till en brytpunkt.

     - Navigera till vyn Debug för visuell studiokod genom att välja **Visa > Debug**. Välj felsökningskonfigurationen för ** * &lt;modulnamnet&gt; * Local Debug (Java)** i listrutan.

1. Klicka på **Starta felsökning** eller tryck på **F5** för att starta felsökningssessionen.

1. I den integrerade visual studiokoden kör du följande kommando för att skicka ett **Hello World-meddelande** till din modul. Det här är kommandot som visas i föregående steg när du ställer in IoT Edge-simulatorn.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Om du använder Windows, se till att skalet i din Visual Studio Code integrerad terminal är **Git Bash** eller **WSL Bash**. Du kan `curl` inte köra kommandot från en PowerShell eller kommandotolk.
   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden i stället för `curl`.

1. I vyn Felsökning av visuell studio-kod visas variablerna på den vänstra panelen.

1. Om du vill stoppa felsökningssessionen markerar du stoppknappen eller trycker på **Skift + F5**och kör sedan **Azure IoT Edge: Stop IoT Edge Simulator** i kommandopaletten för att stoppa simulatorn och rensa.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Felsöka i bifoga-läge med IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Din standardlösning innehåller två moduler, en är en simulerad temperatursensormodul och den andra är rörmodulen. Den simulerade temperatursensorn skickar meddelanden till rörmodulen och sedan leds meddelandena till IoT Hub. I modulmappen som du skapade finns flera Docker-filer för olika behållartyper. Använd någon av de filer som slutar med tillägget **.debug** för att skapa din modul för testning.

Felsökning i anslutningsläge stöds för närvarande endast på följande sätt:

- C#-moduler, inklusive de för Azure-funktioner, stöder felsökning i Linux amd64-behållare
- Node.js-moduler stöder felsökning i Linux amd64- och arm32v7-behållare och Windows amd64-behållare
- Java-moduler stöder felsökning i Linux amd64 och arm32v7 behållare

> [!TIP]
> Du kan växla mellan alternativ för standardplattformen för IoT Edge-lösningen genom att klicka på objektet i statusfältet Visual Studio-kod.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge-simulator för IoT Edge-lösning

I din utvecklingsmaskin kan du starta en IoT Edge-simulator i stället för att installera säkerhetsdemonen IoT Edge så att du kan köra din IoT Edge-lösning.

1. Högerklicka på IoT Edge-enhets-ID:n i enhetsutforskaren på vänster sida och välj sedan **Setup IoT Edge Simulator** för att starta simulatorn med enhetsanslutningssträngen.

1. Du kan se att IoT Edge Simulator har konfigurerats genom att läsa förloppsdetaljerna i den integrerade terminalen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Skapa och kör behållare för felsökning och felsökning i bifoga-läge

1. Öppna modulfilen`Program.cs` `app.js`( `App.java`, `<your module name>.cs`, eller ) och lägg till en brytpunkt.

1. Högerklicka på filen för din lösning `deployment.debug.template.json` i vyn Visual Studio-kodutforskaren och välj sedan **Bygg och kör IoT Edge-lösning i Simulator**. Du kan titta på alla modulbehållareloggar i samma fönster. Du kan också navigera till Docker-vyn för att titta på behållarstatus.

   ![Titta på variabler](media/how-to-vs-code-develop-module/view-log.png)

1. Navigera till visual studio-kodfelsökningsvyn och välj felsökningskonfigurationsfilen för din modul. Felsökningsalternativnamnet bör likna ** * &lt;modulnamnet&gt; * Remote Debug**

1. Välj **Starta felsökning** eller tryck på **F5**. Välj den process som ska kopplas till.

1. I visual studio-kodfelsökningsvyn visas variablerna på den vänstra panelen.

1. Om du vill stoppa felsökningssessionen väljer du först stoppknappen eller trycker på **Skift + F5**och väljer sedan **Azure IoT Edge: Stop IoT Edge Simulator** från kommandopaletten.

> [!NOTE]
> I föregående exempel visas hur du felsöker IoT Edge-moduler på behållare. Det lade till exponerade portar `createOptions` till modulens behållarinställningar. När du har felsökt dina moduler rekommenderar vi att du tar bort dessa exponerade portar för produktionsklara IoT Edge-moduler.
>
> För moduler som är skrivna i C#, inklusive Azure-funktioner, baseras det här exemplet på felsökningsversionen av `Dockerfile.amd64.debug`, som innehåller .NET Core-kommandoradsfelsökaren (VSDBG) i behållaravbildningen när du skapar den. När du har felsökt dina C#-moduler rekommenderar vi att du använder Dockerfile direkt utan VSDBG för produktionsklara IoT Edge-moduler.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Felsöka en modul med IoT Edge-körningen

I varje modulmapp finns det flera Docker-filer för olika behållartyper. Använd någon av de filer som slutar med tillägget **.debug** för att skapa din modul för testning.

När du felsöker moduler med den här metoden körs modulerna ovanpå IoT Edge-körningen. IoT Edge-enheten och din Visual Studio-kod kan finnas på samma dator, eller mer typiskt, Visual Studio Code finns på utvecklingsdatorn och IoT Edge-körningen och modulerna körs på en annan fysisk dator. För att kunna felsöka från Visual Studio Code måste du:

- Konfigurera din IoT Edge-enhet, bygg din IoT Edge-moduler med **.debug** Dockerfile och distribuera sedan till IoT Edge-enheten.
- Exponera IP och porten för modulen så att felsökaren kan anslutas.
- Uppdatera `launch.json` så att Visual Studio-kod kan kopplas till processen i behållaren på fjärrdatorn. Den här filen `.vscode` finns i mappen på arbetsytan och uppdateras varje gång du lägger till en ny modul som stöder felsökning.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Skapa och distribuera din modul till IoT Edge-enheten

1. Öppna `deployment.debug.template.json` filen i Visual Studio-kod, som innehåller felsökningsversionen av `createOptions` modulavbildningarna med rätt värden inställda.

1. Om du utvecklar modulen i Python följer du dessa steg innan du fortsätter:
   - Öppna filen `main.py` och lägg till den här koden efter importavsnittet:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Lägg till följande enda kodrad i den motringning som du vill felsöka:

      ```python
      ptvsd.break_into_debugger()
      ```

     Om du till exempel vill `receive_message_listener` felsöka funktionen infogar du den kodraden enligt nedan:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. I kommandopaletten För Visual Studio-kod:
   1. Kör kommandot **Azure IoT Edge: Build and Push IoT Edge-lösning**.

   1. Välj `deployment.debug.template.json` filen för din lösning.

1. I avsnittet **Azure IoT Hub Devices** i vyn Visual Studio-kodutforskaren:
   1. Högerklicka på ett IoT Edge-enhets-ID och välj sedan **Skapa distribution för en enhet**.

      > [!TIP]
      > Om du vill bekräfta att enheten du har valt är en IoT Edge-enhet markerar du den för att expandera listan över moduler och verifiera förekomsten av **$edgeHub** och **$edgeAgent**. Varje IoT Edge-enhet innehåller dessa två moduler.

   1. Navigera till lösningens **konfigurationsmapp,** `deployment.debug.amd64.json` markera filen och välj sedan Välj Edge Deployment **Manifest**.

Distributionen har skapats med ett distributions-ID i den integrerade terminalen.

Du kan kontrollera behållarstatusen `docker ps` genom att köra kommandot i terminalen. Om din Visual Studio-kod och IoT Edge-körning körs på samma dator kan du också kontrollera statusen i vyn Visual Studio-koddockare.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Exponera IP-adressen och porten för modulen för felsökaren

Du kan hoppa över det här avsnittet om modulerna körs på samma dator som Visual Studio Code, eftersom du använder localhost för att ansluta till `createOptions` behållaren `launch.json` och redan har rätt portinställningar i **.debug** Dockerfile, modulens behållarinställningar och fil. Om dina moduler och Visual Studio-kod körs på separata datorer följer du stegen för utvecklingsspråket.

- **C#, inklusive Azure-funktioner**

  [Konfigurera SSH-kanalen på utvecklingsmaskinen och IoT Edge-enheten](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) och redigera `launch.json` sedan filen för att bifoga.

- **Node.js**

  - Kontrollera att modulen på maskinen som ska felsökas körs och är redo för felsökare att ansluta och att port 9229 är tillgänglig externt. Du kan verifiera `http://<target-machine-IP>:9229/json` detta genom att öppna på felsökningsmaskinen. Den här URL:en ska visa information om modulen Node.js som ska avsökas.
  
  - På utvecklingsdatorn öppnar du Visual `launch.json` Studio-kod och redigerar sedan så att adressvärdet ** * &lt;för modulnamnet&gt; * Remote Debug (Node.js)** profil (eller ** * &lt;modulnamnet&gt; * Remote Debug (Node.js in Windows Container)** om modulen körs som en Windows-behållare) är IP-adressen för den maskin som avskuggas.

- **Java**

  - Bygg en SSH-tunnel till maskinen som ska `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`avskuggas genom att köra .
  
  - Öppna Visual Studio-koden på utvecklingsdatorn och redigera `launch.json` ** * &lt;modulnamnet&gt; * Remote Debug (Java)** så att du kan koppla till måldatorn. Mer information om `launch.json` hur du redigerar och felsöker Java med Visual Studio-kod finns i avsnittet om hur du [konfigurerar felsökningen](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Se till att port 5678 på maskinen som ska avsökas är öppen och åtkomlig.

  - Ändra `ptvsd.enable_attach(('0.0.0.0', 5678))` **0.0.0.0** till IP-adressen för den dator som ska avsökas i koden som du tidigare infogade i. `main.py` Skapa, tryck och distribuera din IoT Edge-modul igen.

  - Öppna Visual Studio-kod på utvecklingsdatorn `launch.json` och `host` redigera sedan så att värdet för ** * &lt;modulens namn&gt; * Remote Debug (Python)** använder måldatorns IP-adress i stället för . `localhost`

### <a name="debug-your-module"></a>Felsöka din modul

1. Välj felsökningskonfigurationsfilen för modulen i vyn Debug i Visual Studio-kod. Felsökningsalternativnamnet bör likna ** * &lt;modulnamnet&gt; * Remote Debug**

1. Öppna modulfilen för utvecklingsspråket och lägg till en brytpunkt:

   - **Azure-funktion (C#):** Lägg till brytpunkten i filen `<your module name>.cs`.
   - **C#**: Lägg till brytpunkten i filen `Program.cs`.
   - **Node.js**: Lägg till brytpunkten i filen `app.js`.
   - **Java**: Lägg till brytpunkten i filen `App.java`.
   - **Python**: Lägg till brytpunkten i filen `main.py`i motringningsmetoden `ptvsd.break_into_debugger()` där du lade till raden.
   - **C**: Lägg till brytpunkten i filen `main.c`.

1. Välj **Starta felsökning** eller välj **F5**. Välj den process som ska kopplas till.

1. I vyn Felsökning av visuell studio-kod visas variablerna på den vänstra panelen.

> [!NOTE]
> I föregående exempel visas hur du felsöker IoT Edge-moduler på behållare. Det lade till exponerade portar `createOptions` till modulens behållarinställningar. När du har felsökt dina moduler rekommenderar vi att du tar bort dessa exponerade portar för produktionsklara IoT Edge-moduler.

## <a name="build-and-debug-a-module-remotely"></a>Fjärrbygga och felsöka en modul

Med de senaste ändringarna i både Docker- och Moby-motorerna för att stödja SSH-anslutningar och en ny inställning i Azure IoT Tools som möjliggör injektion av miljöinställningar i kommandopaletten för Visual Studio-kod och Azure IoT Edge-terminaler kan du nu skapa och felsöka moduler på fjärrenheter.

Mer information och steg-för-steg-instruktioner finns i det här blogginlägget för [IoT-utvecklare.](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)

## <a name="next-steps"></a>Nästa steg

När du har skapat modulen kan du läsa om hur du [distribuerar Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

Om du vill utveckla moduler för dina IoT Edge-enheter [förstår och använder du Azure IoT Hub SDK:er](../iot-hub/iot-hub-devguide-sdks.md).
