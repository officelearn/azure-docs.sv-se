---
title: Utveckla och felsöka moduler i Visual Studio - Azure IoT Edge
description: Använd Visual Studio med Azure IoT Tools för att utveckla en C- eller C# IoT Edge-modul och skicka den från din IoT Hub till en IoT-enhet, som konfigurerats av ett distributionsmanifest.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384865"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Använda Visual Studio 2019 för att utveckla och felsöka moduler för Azure IoT Edge

Du kan förvandla din affärslogik till moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio 2019 som huvudverktyg för att utveckla och felsöka moduler.

Azure IoT Edge Tools för Visual Studio ger följande fördelar:

- Skapa, redigera, skapa, köra och felsöka Azure IoT Edge-lösningar och moduler på din lokala utvecklingsdator.
- Distribuera din Azure IoT Edge-lösning till Azure IoT Edge-enhet via Azure IoT Hub.
- Koda dina Azure IoT-moduler i C eller C# samtidigt som du har alla fördelar med Visual Studio-utveckling.
- Hantera Azure IoT Edge-enheter och -moduler med användargränssnittet.

Den här artikeln visar hur du använder Azure IoT Edge Tools för Visual Studio 2019 för att utveckla dina IoT Edge-moduler. Du lär dig också hur du distribuerar ditt projekt till din Azure IoT Edge-enhet. För närvarande ger Visual Studio 2019 stöd för moduler skrivna i C och C#. Enhetsarkitekturerna som stöds är Windows X64 och Linux X64 eller ARM32. Mer information om operativsystem, språk och arkitekturer som stöds finns i [Stöd för språk och arkitektur](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows som utvecklingsdator. På Windows-datorer kan du utveckla antingen Windows- eller Linux-moduler. Om du vill utveckla Windows-moduler använder du en Windows-dator som kör version 1809/build 17763 eller nyare. Om du vill utveckla Linux-moduler använder du en Windows-dator som uppfyller [kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Eftersom den här artikeln använder Visual Studio 2019 som huvudutvecklingsverktyg installerar du Visual Studio. Se till att du inkluderar **Azure-utveckling** och **skrivbordsutveckling med C++-arbetsbelastningar** i din Visual Studio 2019-installation. Du kan [ändra Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) för att lägga till nödvändiga arbetsbelastningar.

När Visual Studio 2019 är klart behöver du även följande verktyg och komponenter:

- Ladda ned och installera [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) från Visual Studio-marknadsplatsen för att skapa ett IoT Edge-projekt i Visual Studio 2019.

> [!TIP]
> Om du använder Visual Studio 2017 kan du hämta och installera [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) för VS 2017 från Visual Studio-marknadsplatsen

- Hämta och installera [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn för att skapa och köra modulavbildningar. Du måste ställa in Docker CE så att den körs i antingen Linux-behållarläge eller Windows-behållarläge.

- Konfigurera din lokala utvecklingsmiljö för att felsöka, köra och testa din IoT Edge-lösning genom att installera [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/). Installera [Python (2.7/3.6+) och Pip](https://www.python.org/) och installera sedan **iotedgehubdev-paketet** genom att köra följande kommando i din terminal. Kontrollera att din Azure IoT EdgeHub Dev Tool-version är större än 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Klona databasen och installera Vcpkg-bibliotekshanteraren och installera sedan **azure-iot-sdk-c-paketet** för Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Du kan använda ett lokalt Docker-register för prototyp- och testningsändamål i stället för ett molnregister.

- Om du vill testa modulen på en enhet behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda datorn som IoT Edge-enhet följer du stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge-demon på utvecklingsdatorn kan du behöva stoppa EdgeHub och EdgeAgent innan du börjar utveckla i Visual Studio.

### <a name="check-your-tools-version"></a>Kontrollera din verktygsversion

1. Välj **Hantera tillägg**på **menyn Tillägg.** Expandera **installerade > verktyg** och du kan hitta Azure **IoT Edge Tools för Visual Studio** och Cloud Explorer för Visual **Studio**.

1. Observera den installerade versionen. Du kan jämföra den här versionen med den senaste versionen på Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

1. Om din version är äldre än vad som är tillgängligt på Visual Studio Marketplace uppdaterar du dina verktyg i Visual Studio enligt följande avsnitt.

### <a name="update-your-tools"></a>Uppdatera dina verktyg

1. I fönstret **Hantera tillägg** expanderar du Uppdateringar > Visual **Studio Marketplace,** väljer **Azure IoT Edge Tools** eller Cloud Explorer för Visual **Studio** och väljer **Uppdatera**.

1. När verktygsuppdateringen har hämtats stänger du Visual Studio för att utlösa verktygsuppdateringen med VSIX-installationsprogrammet.

1. I installationsprogrammet väljer du **OK** för att starta och **sedan Ändra** för att uppdatera verktygen.

1. När uppdateringen är klar väljer du **Stäng** och starta om Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Skapa ett Azure IoT Edge-projekt

Azure IoT Edge-projektmallen i Visual Studio skapar ett projekt som kan distribueras till Azure IoT Edge-enheter i Azure IoT Hub. Först skapar du en Azure IoT Edge-lösning och sedan genererar du den första modulen i den lösningen. Varje IoT Edge-lösning kan innehålla mer än en modul.

> [!TIP]
> IoT Edge-projektstrukturen som skapats av Visual Studio är inte densamma som i Visual Studio-kod.

1. I dialogrutan För nya projekt i Visual Studio söker du efter och väljer **Azure IoT Edge-projekt** och klickar på **Nästa**. I projektkonfigurationsfönstret anger du ett namn för projektet och anger platsen och väljer sedan **Skapa**. Standardprojektnamnet är **AzureIoTEdgeApp1**.

   ![Skapa nytt projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. I fönstret **Lägg till IoT Edge-program och modul** väljer du antingen **C# Modul** eller **C-modul** och anger sedan modulnamnet och modulavbildningsdatabasen. Visual Studio fyller automatiskt i modulnamnet med **localhost:5000/<ditt modulnamn\>**. Ersätt den med din egen registerinformation. Om du använder ett lokalt Docker-register för testning är **localhost** bra. Om du använder Azure Container Registry använder du inloggningsservern från registrets inställningar. Inloggningsservern ser ut som ** _ \<registernamnet\>_.azurecr.io**. Ersätt endast den **localhost:5000** delen av strängen så att slutresultatet ser ut som ** \< *registernamn*\>.azurecr.io/_\<modulnamnet\>_**. Standardmodulnamnet är **IotEdgeModule1**

   ![Lägg till program och modul](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Välj **OK** för att skapa Azure IoT Edge-lösningen med en modul som använder antingen C# eller C.

Nu har du ett **AzureIoTEdgeApp1.Linux.Amd64-projekt** eller ett **AzureIoTEdgeApp1.Windows.Amd64-projekt** och även ett **IotEdgeModule1-projekt** i din lösning. Varje **AzureIoTEdgeApp1-projekt** har en `deployment.template.json` fil som definierar de moduler som du vill skapa och distribuera för din IoT Edge-lösning och som också definierar flödena mellan modulerna. Standardlösningen har en **SimulatedTemperatureSensor-modul** och en **IotEdgeModule1-modul.** **Modulen SimulatedTemperatureSensor** genererar simulerade data till **modulen IotEdgeModule1,** medan standardkoden i **modulen IotEdgeModule1** direkt tog emot meddelanden till Azure IoT Hub.

Om du vill se hur den simulerade temperatursensorn fungerar visar du [källkoden SimuleradTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

**IotEdgeModule1-projektet** är ett .NET Core 2.1-konsolprogram om det är en C#-modul. Den innehåller nödvändiga Docker-filer som du behöver för din IoT Edge-enhet som körs med antingen en Windows-behållare eller Linux-behållare. Filen `module.json` beskriver metadata för en modul. Den faktiska modulkoden, som tar Azure IoT Device SDK `Program.cs` `main.c` som ett beroende, finns i filen eller.

## <a name="develop-your-module"></a>Utveckla din modul

Standardmodulkoden som medföljer lösningen finns på **IotEdgeModule1** > **Program.cs** (för C#) eller **main.c** (C). Modulen och `deployment.template.json` filen är konfigurerade så att du kan skapa lösningen, skicka den till behållarregistret och distribuera den till en enhet för att starta testningen utan att röra någon kod. Modulen är byggd för att ta indata från en källa (i det här fallet **modulen SimuleradTemperatureSensor** som simulerar data) och leda den till Azure IoT Hub.

När du är redo att anpassa modulmallen med din egen kod använder du [Azure IoT Hub SDK:er](../iot-hub/iot-hub-devguide-sdks.md) för att skapa moduler som tillgodoser de viktigaste behoven för IoT-lösningar som säkerhet, enhetshantering och tillförlitlighet.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Initiera iotedgehubdev med anslutningssträng för IoT Edge-enhet

1. Kopiera anslutningssträngen för en IoT Edge-enhet från **primär anslutningssträng** i Visual Studio Cloud Explorer. Var noga med att inte kopiera anslutningssträngen för en icke-Edge-enhet, eftersom ikonen för en IoT Edge-enhet skiljer sig från ikonen för en icke-Edge-enhet.

   ![Kopiera anslutningssträng för kantenhet](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Välj **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**på **Verktyg-menyn,** klistra in anslutningssträngen och klicka på **OK**.

   ![Strängfönster för öppen kantanslutning](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Ange anslutningssträngen från det första steget och välj sedan **OK**.

> [!NOTE]
> Du behöver bara följa dessa steg en gång på utvecklingsdatorn eftersom resultaten automatiskt tillämpas på alla efterföljande Azure IoT Edge-lösningar. Den här proceduren kan följas igen om du behöver byta till en annan anslutningssträng.

## <a name="build-and-debug-single-module"></a>Skapa och felsöka enmodul

Vanligtvis vill du testa och felsöka varje modul innan du kör den i en hel lösning med flera moduler.

1. Högerklicka på **IotEdgeModule1** i **Solution Explorer**och välj Ange **som StartUp Project** på snabbmenyn.

   ![Konfigurera startprojekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Tryck på **F5** eller klicka på knappen nedan för att köra modulen. det kan ta&ndash;10 20 sekunder första gången du gör det.

   ![Kör modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Du bör se en .NET Core-konsolapp startas om modulen har initierats.

   ![Modul körs](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Om du utvecklar i C#, `PipeMessage()` ange en brytpunkt i funktionen i **Program.cs**; Om du använder C ställer `InputQueue1Callback()` du in en brytpunkt i funktionen i **main.c**. Du kan sedan testa det genom att skicka ett meddelande genom att köra följande kommando i ett **Git Bash-** eller **WSL Bash-skal.** (Du kan `curl` inte köra kommandot från en PowerShell eller kommandotolk.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Felsöka enmodul](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Brytpunkten ska utlösas. Du kan titta på variabler i fönstret **Windows-studio.**

   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden i stället för `curl`.

1. Tryck på **Ctrl + F5** eller klicka på stoppknappen för att sluta felsöka.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Skapa och felsöka IoT Edge-lösning med flera moduler

När du är klar med att utveckla en enda modul kanske du vill köra och felsöka en hel lösning med flera moduler.

1. I **Solution Explorer**lägger du till en andra modul i lösningen genom att högerklicka på **AzureIoTEdgeApp1** och välja Lägg **till** > **ny IoT Edge Module**. Standardnamnet på den andra modulen är **IotEdgeModule2** och fungerar som en annan rörmodul.

1. Öppna filen `deployment.template.json` och du ser **IotEdgeModule2** har lagts till i **modulen** avsnitt. Ersätt **vägavsnittet** med följande. Om du har anpassat modulnamnen måste du uppdatera dessa namn så att de matchar.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Högerklicka på **AzureIoTEdgeApp1** och välj **Ange som StartUp Project** på snabbmenyn.

1. Skapa brytpunkter och tryck sedan på **F5** för att köra och felsöka flera moduler samtidigt. Du bör se flera .NET Core-konsolappfönster, som varje fönster representerar en annan modul.

   ![Felsöka flera moduler](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Tryck på **Ctrl + F5** eller markera stoppknappen för att sluta felsöka.

## <a name="build-and-push-images"></a>Skapa och pusha bilder

1. Kontrollera att **AzureIoTEdgeApp1** är startprojektet. Välj antingen **Felsökning** eller **Släpp** som konfiguration att bygga för dina modulavbildningar.

    > [!NOTE]
    > När du väljer **Felsökning** `Dockerfile.(amd64|windows-amd64).debug` används Visual Studio för att skapa Docker-avbildningar. Detta inkluderar .NET Core kommandoradsfelsökare VSDBG i behållaravbildningen när du skapar den. För produktionsklara IoT Edge-moduler rekommenderar vi att du `Dockerfile.(amd64|windows-amd64)` använder versionskonfigurationen, som används utan VSDBG. **Release**

1. Om du använder ett privat register som Azure Container Registry (ACR) använder du följande Docker-kommando för att logga in på det.  Du kan hämta användarnamnet och lösenordet från sidan **Access-nycklar** i registret i Azure-portalen. Om du använder det lokala registret kan du [köra ett lokalt register](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Om du använder ett privat register som Azure Container Registry måste du lägga till registerinloggningsinformationen i körningsinställningarna som finns i filen `deployment.template.json`. Ersätt platshållarna med ditt faktiska ANVÄNDARNAMN, lösenord och registernamn för ACR-administratören.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Högerklicka på **AzureIoTEdgeApp1** i **Solution Explorer**och välj Bygg **och pushA IoT-edgemoduler** för att skapa och driva Docker-avbildningen för varje modul.

## <a name="deploy-the-solution"></a>Distribuera lösningen

I snabbstartsartikeln som du använde för att ställa in din IoT Edge-enhet distribuerade du en modul med hjälp av Azure Portal. Du kan också distribuera moduler med Cloud Explorer för Visual Studio. Du har redan ett distributionsmanifest `deployment.json` förberett för ditt scenario, filen och allt du behöver göra är att välja en enhet för att ta emot distributionen.

1. Öppna **Cloud Explorer** genom att klicka på **Visa** > Cloud**Explorer**. Kontrollera att du har loggat in i Visual Studio 2019.

1. I **Cloud Explorer**kan du utöka din prenumeration, hitta din Azure IoT-hubb och den Azure IoT Edge-enhet som du vill distribuera.

1. Högerklicka på IoT Edge-enheten för att skapa en distribution för den. Navigera till distributionsmanifestet som konfigurerats för din plattform i `deployment.arm32v7.json` **konfigurationsmappen** i Visual Studio-lösningen, till exempel .

1. Klicka på uppdateringsknappen om du vill se de nya modulerna som körs tillsammans med modulen **SimuleradTemperatureSensor** och **$edgeAgent** och **$edgeHub**.

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka D2C-meddelandet för en viss IoT-Edge-enhet markerar du det i IoT-hubben i **Cloud Explorer** och klickar sedan på **Starta övervakningsenad händelseslutpunkt** i **åtgärdsfönstret.**

1. Om du vill sluta övervaka data väljer du **Sluta övervaka inbyggt händelseslutpunkt** i **åtgärdsfönstret.**

## <a name="next-steps"></a>Nästa steg

Om du vill utveckla anpassade moduler för dina IoT Edge-enheter [förstår och använder du Azure IoT Hub SDK:er](../iot-hub/iot-hub-devguide-sdks.md).
