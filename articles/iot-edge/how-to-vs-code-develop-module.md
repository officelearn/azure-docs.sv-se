---
title: Utveckla och felsöka moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla, skapa och Felsök en modul för Azure IoT Edge med C#, Python, Node.js, Java eller C
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb3d19d35a15d5476594948b035a39ae703f1c3a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551587"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka moduler för Azure IoT Edge

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code som huvudsakliga verktyget för att utveckla och felsöka moduler.

## <a name="prerequisites"></a>Förutsättningar

Du kan använda en dator eller en virtuell dator som kör Windows, macOS eller Linux som din utvecklingsdator. En IoT Edge-enhet kan vara en annan fysisk enhet.

För moduler som skrivits i C#, Node.js eller Java, det finns två sätt att felsöka din modul i Visual Studio Code: Du kan koppla en process i en modul-behållare, eller så kan du starta modulen koden i felsökningsläge. För moduler som skrivits i Python och C, kan de endast att felsöka genom att koppla till en process i Linux amd64-behållare.

> [!TIP]
> Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Installera [Visual Studio Code](https://code.visualstudio.com/) första och Lägg sedan till följande tillägg:

- [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio paket som är specifika för språket som du utvecklar i:
  - C#, inklusive Azure Functions: [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java-tilläggspaketet för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Du måste också installera några ytterligare, språkspecifika verktyg för att utveckla din modell:

- C#, inklusive Azure Functions: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) och [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (som vanligtvis ingår i Python-installationen). När Pip har installerats kan du installera den **Cookiecutteru** paket med följande kommando:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

- Node.js: [Node.js](https://nodejs.org). Du även vill installera [Yeoman](https://www.npmjs.com/package/yo) och [Azure IoT Edge Node.js-modulen Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) och [Maven](https://maven.apache.org/). Du måste [ange den `JAVA_HOME` miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) så att den pekar till den JDK-installationen.

För att kunna skapa och distribuera din modul-avbildning, behöver du Docker för att skapa modulen-avbildning och ett behållarregister ska lagra avbildningen modulen:

- [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet.

Om du utvecklar din modul i C kan du ytterligare Python-baserade [utvecklingsverktyg för Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) för att ställa in din lokala utvecklingsmiljö för att felsöka, köra och testa din IoT Edge-lösning. Om du inte redan har gjort det installerar [Python (2.7/3.6) och Pip](https://www.python.org/) och sedan installera **iotedgehubdev** genom att köra följande kommando i terminalen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Om du vill testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet följer du stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge-daemon på utvecklingsdatorn, kan du behöva stoppa EdgeHub och EdgeAgent innan du går vidare till nästa steg.

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT Edge-modul i det önskade programmeringsspråk (inklusive Azure Functions, skriven i C#) med hjälp av Visual Studio Code och Azure IoT Edge-tillägget. Du startar genom att skapa en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla flera moduler.

1. Välj **visa** > **kommandot paletten**.

1. I kommandopaletten anger och kör du kommandot **Azure IoT Edge: Ny IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

1. Bläddra till mappen där du vill skapa den nya lösningen och välj sedan **Välj mapp**.

1. Ange ett namn för din lösning.

1. Välj en mall för modulen för det programmeringsspråk du önskade ska vara den första modulen i lösningen.

1. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister.

1. Ange namnet på modulens avbildningslagringsplatsen. Visual Studio Code autopopulates modulen namnet med **localhost:5000 / < din Modulnamn\>**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, sedan **localhost** är bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver ***\<registernamn\>*. azurecr.io**. Endast ersätta den **localhost:5000** en del av strängen, så att slutresultatet ser ut som **\<* registernamn*\>.azurecr.io/* \<din Modulnamn\>***.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster.

Det finns fyra objekt inne i lösningen:

- En **.vscode** mappen innehåller konfigurationer för felsökning.

- En **moduler** mappen innehåller undermappar för varje modul. Nu kan har du bara en. Men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Add IoT Edge Module** (Lägg till IoT Edge-modul).

- En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den.

  > [!NOTE]
  > Miljö-filen skapas endast om du anger en avbildningslagringsplatsen för modulen. Om du har godkänt localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler.

- En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [Lär dig hur du använder distribution manifest för att distribuera moduler och upprätta vägar](module-composition.md).

## <a name="add-additional-modules"></a>Lägg till ytterligare moduler

Om du vill lägga till ytterligare moduler i din lösning, kör du kommandot **Azure IoT Edge: Lägg till IoT Edge-modul** från kommandopaletten. Du kan också högerklicka på den **moduler** mapp eller `deployment.template.json` filen i Visual Studio Code-Utforskaren vyn och välj sedan **Lägg till IoT Edge-modul**.

## <a name="develop-your-module"></a>Utveckla din modell

Modulen standardkoden som medföljer lösningen finns på följande plats:

- Azure-funktion (C#): **moduler >  *&lt;din Modulnamn&gt;* > *&lt;din Modulnamn&gt;*.cs**
- C#: **moduler > *&lt;din Modulnamn&gt;* > Program.cs**
- Python: **moduler > *&lt;din Modulnamn&gt;* > main.py**
- Node.js: **moduler > *&lt;din Modulnamn&gt;* > app.js**
- Java: **moduler > *&lt;din Modulnamn&gt;* > src > huvudsakliga > java > com > edgemodulemodules > App.java**
- C: **moduler > *&lt;din Modulnamn&gt;* > main.c**

Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub.

När du är redo att anpassa mallen med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Felsöka en modul utan en behållare (C#, Node.js, Java)

Om du utvecklar i C#, Node.js eller Java, din modulen kräver användning av en **ModuleClient** objektet i koden för standard-modulen så att den kan starta, köra och dirigera meddelanden. Du använder också indatakanal för standard **indata1** att agera när modulen tar emot meddelanden.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge-simulator för IoT Edge-lösning

I en utvecklingsdator måste starta du en IoT Edge-simulator istället för att installera IoT Edge security daemon så att du kan köra din IoT Edge-lösning.

1. Högerklicka på din IoT Edge-enhets-ID i enhetsutforskare till vänster och välj sedan **installationsprogrammet IoT Edge-simulatorn** att starta simulatorn med enhetens anslutningssträng.
1. Du kan se IoT Edge simulatorn har konfigurerats genom att läsa förloppet ingående i den integrerade terminalen.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurera IoT Edge-simulator för enkel modulen app

Om du vill konfigurera och starta simulatorn, kör du kommandot **Azure IoT Edge: Starta IoT Edge Hub Simulator för enkel modulen** från kommandopaletten Visual Studio Code. När du uppmanas använder du värdet **indata1** från modulen standardkoden (eller motsvarande värde från din kod) som indata namn för ditt program. Kommandot-utlösare i **iotedgehubdev** CLI och startar sedan IoT Edge-simulatorn och en testning verktyget modulen behållare. Du kan se utdata nedan i den integrerade terminalen om simulatorn startades i läget för enskild modulen. Du kan också se en `curl` kommando för att skicka meddelande via. Du ska använda det senare.

   ![Konfigurera IoT Edge-simulator för enkel modulen app](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Du kan använda Docker Utforskarvyn i Visual Studio Code för att se status för den modul som körs.

   ![Simulatorn modulens status](media/how-to-develop-csharp-module/simulator-status.png)

   Den **edgeHubDev** behållare utgör kärnan i lokal IoT Edge-simulatorn. Den kan köras på utvecklingsdatorn utan daemonen IoT Edge-säkerhet och ger miljöinställningar för din app för ursprunglig modul eller modulen behållare. Den **inkommande** behållaren exponerar REST-API: er för att överbrygga meddelanden till indatakanal för mål på din modul.

### <a name="debug-module-in-launch-mode"></a>Felsöka modulen i Start-läge

1. Förbereda miljön för felsökning enligt kraven för programmeringsspråk, konfigurera en brytpunkt i din modul och välj debug-konfiguration som ska användas:
   - **C#**
     - I den integrerade Visual Studio Code-terminalen ändra katalogen till den ***&lt;din Modulnamn&gt;*** mappen och kör sedan följande kommando för att skapa .net Core program.

       ```cmd
       dotnet build
       ```

     - Öppna filen `program.cs` och lägga till en brytpunkt.

     - Gå till vyn Visual Studio-koden och felsöka genom att välja **Visa > Felsöka**. Välj debug konfigurationen  ***&lt;din Modulnamn&gt;* lokala felsöka (.NET Core)** i listrutan.

        > [!NOTE]
        > Om din .net Core `TargetFramework` stämmer inte överens med din sökväg till programmet i `launch.json`, måste du manuellt uppdatera sökvägen till programmet i `launch.json` så att den matchar den `TargetFramework` i filen .csproj så att Visual Studio Code kan starta detta program.

   - **Node.js**
     - I den integrerade Visual Studio Code-terminalen ändra katalogen till den ***&lt;din Modulnamn&gt;*** mappen och kör sedan följande kommando för att installera paket i noden

       ```cmd
       npm install
       ```

     - Öppna filen `app.js` och lägga till en brytpunkt.

     - Gå till vyn Visual Studio-koden och felsöka genom att välja **Visa > Felsöka**. Välj debug konfigurationen  ***&lt;din Modulnamn&gt;* lokala felsöka (Node.js)** i listrutan.
   - **Java**
     - Öppna filen `App.java` och lägga till en brytpunkt.

     - Gå till vyn Visual Studio-koden och felsöka genom att välja **Visa > Felsöka**. Välj debug konfigurationen  ***&lt;din Modulnamn&gt;* lokala felsöka (Java)** i listrutan.

1. Klicka på **Starta felsökning** eller tryck på **F5** att starta felsökningssessionen.

1. I den integrerade Visual Studio Code-terminalen kör du följande kommando för att skicka en **Hello World** meddelande till din modul. Det här är kommandot visas i föregående steg när du ställer in IoT Edge-simulatorn.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Om du använder Windows kontrollerat att shell integrerade terminalen Visual Studio Code är **Git Bash** eller **WSL Bash**. Du kan inte köra den `curl` från en PowerShell eller Kommandotolken.
   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden via i stället för `curl`.

1. I Visual Studio Code Debug-vyn visas variabler i den vänstra panelen.

1. Om du vill stoppa din felsökningssession, väljer du den stopp-knappen eller tryck på **SKIFT + F5**, och kör sedan **Azure IoT Edge: Stoppa IoT Edge-simulatorn** i kommandopaletten stoppa simulatorn och rensa.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Felsökning i bifoga läge med IoT Edge-Simulator (C#, Node.js, Java, Azure Functions)

Standardlösningen innehåller två moduler, en är en simulerad temperatur sensor modul och den andra är pipe-modulen. Simulerade temperatursensorn skickar meddelanden till pipe-modulen och sedan meddelandena som skickas till IoT Hub. Det finns flera Docker-filer för olika behållartyper i modulmappen som du skapade. Använda någon av de filer som slutar med tillägget **.debug** att skapa din modul för testning.

För närvarande felsökning i bifoga läge stöds endast på följande sätt:

- C#moduler, inklusive de för Azure Functions, stöd för felsökning i Linux amd64-behållare
- Node.js-moduler som stöd för felsökning i Linux amd64 och arm32v7 behållare och Windows amd64 behållare
- Java-moduler som stöd för felsökning i Linux-behållare för amd64 och arm32v7

> [!TIP]
> Du kan växla bland alternativen för standard-plattformen för din IoT Edge-lösning genom att klicka på objektet i statusfältet för Visual Studio Code.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge-simulator för IoT Edge-lösning

I en utvecklingsdator måste starta du en IoT Edge-simulator istället för att installera IoT Edge security daemon så att du kan köra din IoT Edge-lösning.

1. Högerklicka på din IoT Edge-enhets-ID i enhetsutforskare till vänster och välj sedan **installationsprogrammet IoT Edge-simulatorn** att starta simulatorn med enhetens anslutningssträng.

1. Du kan se IoT Edge simulatorn har konfigurerats genom att läsa förloppet ingående i den integrerade terminalen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Skapa och köra behållare för felsökning och felsökning i Koppla läge

1. Öppna filen för modulen (`program.cs`, `app.js`, `App.java`, eller `<your module name>.cs`) och Lägg till en brytpunkt.

1. I vyn Visual Studio Code-Utforskaren högerklickar du på den `deployment.debug.template.json` för din lösning och välj sedan **bygga och köra IoT Edge-lösning i simulatorn**. Du kan titta på behållaren för modulen loggar i samma fönster. Du kan också navigera till vyn Docker för att se status för container.

   ![Titta på variabler](media/how-to-develop-csharp-module/view-log.png)

1. Gå till vyn Visual Studio-koden och felsöka och välj debug-konfigurationsfil för. Alternativnamn debug bör likna  ***&lt;din Modulnamn&gt;* fjärrfelsökning**

1. Välj **Starta felsökning** eller tryck på **F5**. Välj processen för att ansluta till.

1. I Visual Studio Code Debug vyn visas variabler i den vänstra panelen.

1. Stoppa felsökningssessionen genom först välja stopp-knappen eller tryck på **SKIFT + F5**, och välj sedan **Azure IoT Edge: Stoppa IoT Edge-simulatorn** från kommandopaletten.

> [!NOTE]
> I föregående exempel visar hur du felsöker IoT Edge-moduler i behållare. Portar läggs den till din modul behållare `createOptions` inställningar. När du är klar med att felsöka dina moduler rekommenderar vi att du tar bort dessa portar för produktionsklara IoT Edge-moduler.
>
> För moduler som skrivits i C#, inklusive Azure Functions, det här exemplet är baserad på felsökningsversionen av `Dockerfile.amd64.debug`, som innehåller den kommandorad felsökaren för .NET Core (VSDBG) i en behållaravbildning när du skapar den. När du felsöker din C# moduler, rekommenderar vi att du använder Dockerfile utan VSDBG direkt för produktionsklara IoT Edge-moduler.

## <a name="debug-a-module-with-iot-edge-runtime-python-c"></a>Felsöka en modul med IoT Edge-körning (Python, C)

Det finns flera Docker-filer för olika behållartyper i varje modul-mapp. Använda någon av de filer som slutar med tillägget **.debug** att skapa din modul för testning.

Felsökning för Python- och C-moduler är för närvarande endast tillgängliga i Linux amd64-behållare.

### <a name="build-and-deploy-your-module"></a>Skapa och distribuera din modul

1. I Visual Studio Code, öppna den `deployment.debug.template.json` filen som innehåller felsökningsversionen modulen behållaravbildningar med rätt `createOptions` set-värden.

1. Om du utvecklar din modul i Python, Följ dessa steg innan du fortsätter:
   - Öppna filen `main.py` och lägger till den här koden efter importavsnittet:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```
   - Lägg till följande rad med kod i det återanrop som du vill felsöka:

      ```python
      ptvsd.break_into_debugger()
      ```

     Exempel: Om du vill felsöka det `receive_message_callback` metod, infoga den rad med kod som visas nedan:

      ```python
      def receive_message_callback(message, hubManager):
          ptvsd.break_into_debugger()
          global RECEIVE_CALLBACKS
          message_buffer = message.get_bytearray()
          size = len(message_buffer)
          print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode ('utf-8'), size) )
          map_properties = message.properties()
          key_value_pair = map_properties.get_internals()
          print ( "    Properties: %s" % key_value_pair )
          RECEIVE_CALLBACKS += 1
          print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
          hubManager.forward_event_to_output("output1", message, 0)
          return IoTHubMessageDispositionResult.ACCEPTED
      ```

1. I Visual Studio Code kommandopaletten:
   1. Kör kommandot **Azure IoT Edge: Bygg- och Pusha IoT Edge-lösningen**.

   1. Välj den `deployment.debug.template.json` filen för din lösning.

1. I den **Azure IoT Hub-enheter** avsnittet av vyn Visual Studio Code-Utforskaren:
   1. Högerklicka på en IoT Edge-enhets-ID och välj sedan **skapa distribution för enskild enhet**.

   1. Gå till din lösning **config** mapp, Välj den `deployment.debug.amd64.json` och väljer sedan **Välj Edge-distribution Manifest**.

Du ser den distribution som har skapats med en distributions-ID i den integrerade terminalen.

Du kan kontrollera din status för container i Visual Studio Code Docker-vy eller genom att köra den `docker ps` i terminalen.

### <a name="debug-your-module"></a>Felsöka din modul

Visual Studio Code håller felsökning konfigurationsinformationen i en `launch.json` finns i en `.vscode` mapp i din arbetsyta. Detta `launch.json` filen genererades när du skapade en ny IoT Edge-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning.

1. Välj debug-konfigurationsfilen för din modul i Visual Studio Code Debug-vy. Alternativnamn debug bör likna  ***&lt;din Modulnamn&gt;* fjärrfelsökning**

1. Öppna filen för modulen för programmeringsspråk och lägga till en brytpunkt:
   - **Python**: Öppna `main.py` och Lägg till en brytpunkt i motringningsmetoden där du lade till den `ptvsd.break_into_debugger()` rad.
   - **C**: Öppna filen `main.c` och lägga till en brytpunkt.

1. Välj **Starta felsökning** eller välj **F5**. Välj processen för att ansluta till.

1. I Visual Studio Code Debug-vyn visas variabler i den vänstra panelen.

> [!NOTE]
> I föregående exempel visar hur du felsöker IoT Edge-moduler i behållare. Portar läggs den till din modul behållare `createOptions` inställningar. När du är klar med att felsöka dina moduler rekommenderar vi att du tar bort dessa portar för produktionsklara IoT Edge-moduler.

## <a name="next-steps"></a>Nästa steg

När du har skapat din modul, lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
