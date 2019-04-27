---
title: Utveckla och felsöka moduler i Visual Studio – Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio 2017 för att utveckla och felsöka moduler för Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/03/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f2228726d4edc25efe46a660d25d398959c3ea59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595873"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>Använd Visual Studio 2017 för att utveckla och felsöka moduler för Azure IoT Edge (förhandsversion)

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio 2017 som huvudsakliga verktyget för att utveckla och felsöka moduler.

Azure IoT Edge-verktyg för Visual Studio ger följande fördelar:

- Skapa, redigera, skapa, köra och felsöka Azure IoT Edge-lösningar och moduler på din lokala utvecklingsdator.
- Distribuera din Azure IoT Edge-lösning till Azure IoT Edge-enhet via Azure IoT Hub.
- Koda din Azure IoT-moduler i C eller C# samtidigt som du har alla fördelarna med Visual Studio-utveckling.
- Hantera Azure IoT Edge-enheter och moduler med Användargränssnittet.

Den här artikeln visar hur du använder Azure IoT Edge-verktyg för Visual Studio 2017 för att utveckla IoT Edge-moduler. Du också lära dig hur du distribuerar ditt projekt till din Azure IoT Edge-enhet.

> [!TIP]
> IoT Edge-projektstruktur som skapas av Visual Studio är inte desamma som för Visual Studio Code.
  
## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows som en utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet.

Eftersom den här artikeln används Visual Studio 2017 som det huvudsakliga utvecklingsverktyg, installera Visual Studio. Kontrollera att du inkluderar den **Azure development** och **skrivbordsutveckling med C++** arbetsbelastningar i Visual Studio 2017-installationen. Du kan [ändra Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) att lägga till nödvändiga arbetsbelastningar.

När din Visual Studio 2017 är klar, behöver du följande verktyg och komponenter:

- Ladda ned och installera [Azure IoT Edge-tillägget (förhandsversion)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) från Visual Studio marketplace för att skapa en IoT Edge-projekt i Visual Studio 2017.

- Ladda ned och installera [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn för att skapa och köra dina avbildningar för modulen. Du måste du ange Docker CE ska köras i läget för Linux-behållare eller läge för Windows-behållare.

- Konfigurera din lokala utvecklingsmiljö felsöka, köra och testa din IoT Edge-lösning genom att installera den [utvecklingsverktyg för Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installera [Python (2.7/3.6) och Pip](https://www.python.org/) och installera sedan den **iotedgehubdev** paketet genom att köra följande kommando i terminalen. Kontrollera att din Azure IoT EdgeHub utvecklingsverktyg version är större än 0.3.0-betaversionen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Klona lagringsplatsen och installera Vcpkg biblioteket manager och sedan installera den **azure-iot-sdk-c-paketet** för Windows.

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
  > Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet.

- Om du vill testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet följer du stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge-daemon på utvecklingsdatorn, kan du behöva stoppa EdgeHub och EdgeAgent innan du börjar utveckling i Visual Studio.

### <a name="check-your-tools-version"></a>Kontrollera vilken version av verktyg

1. Från den **verktyg** menyn och välj **tillägg och uppdateringar**. Expandera **installerad > Verktyg** och du hittar **Azure IoT Edge Tools** och **Cloud Explorer för Visual Studio**.

1. Observera den installerade versionen. Du kan jämföra den här versionen med den senaste versionen på Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools))

1. Om din version är äldre än vad som är tillgängligt på Visual Studio Marketplace kan du uppdatera dina verktyg i Visual Studio som du ser i följande avsnitt.

### <a name="update-your-tools"></a>Uppdatera dina verktyg

1. I den **tillägg och uppdateringar** dialogrutan Expandera **uppdateringar > Visual Studio Marketplace**väljer **Azure IoT Edge Tools** eller **Cloud Explorer för visuella objekt Studio** och välj **uppdatering**.

1. När verktyg uppdateringen laddas ned, stänger du Visual Studio till utlösaren verktygen uppdatera med VSIX installationsprogrammet.

1. I installationsprogrammet, Välj **OK** att starta och sedan **ändra** uppdatera verktygen.

1. När uppdateringen har slutförts, markerar **Stäng** och starta om Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Skapa ett Azure IoT Edge-projekt

Azure IoT Edge-projektmallen i Visual Studio skapar ett projekt som kan distribueras till Azure IoT Edge-enheter i Azure IoT Hub. Först skapar du en Azure IoT Edge-lösning och sedan skapa den första modulen i lösningen. Varje IoT Edge-lösning kan innehålla mer än en modul.

1. Välj **Nytt** > **Projekt** från **Arkiv**-menyn i Visual Studio.

1. I den **nytt projekt** dialogrutan **installerad**väljer **Azure IoT**väljer **Azure IoT Edge**, ange ett namn för ditt projekt och Ange platsen och välj sedan **OK**. Standardnamnet för projektet är **AzureIoTEdgeApp1**.

   ![Nytt projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. I den **Lägg till IoT Edge-program och -modulen** väljer **Linux Amd64**, **Windows Amd64**, eller båda som plattformen. Om du väljer både skapar du en lösning med två projekt att var och en hänvisar till kodmodulen standard.

   > [!TIP]
   > Azure IoT Edge-tillägget för Visual Studio stöder inte för närvarande skapa projekt för ARM-plattformen. Se den här [IoT Developer blogginlägg](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) ett exempel på hur du använder Visual Studio Code för att utveckla en lösning för ARM32v7/armhf.

1. Välj antingen  **C# modulen** eller **C modulen** och sedan ange din Modulnamn och modulen avbildningslagringsplatsen. Visual Studio autopopulates modulen namnet med **localhost:5000 / < din Modulnamn\>**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, sedan **localhost** är bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver ***\<registernamn\>*. azurecr.io**. Endast ersätta den **localhost:5000** en del av strängen, så att slutresultatet ser ut som **\<* registernamn*\>.azurecr.io/* \<din Modulnamn\>***. Standardnamnet för modulen är **IoTEdgeModule1**

1. Välj **OK** att skapa Azure IoT Edge-lösning med en modul som använder antingen C# eller C.

Nu har en **AzureIoTEdgeApp1.Linux.Amd64** projekt eller en **AzureIoTEdgeApp1.Windows.Amd64** projekt eller båda, och även ett **IoTEdgeModule1** projekt i din lösning. Varje **AzureIoTEdgeApp1** projektet har en `deployment.template.json` -fil som definierar de moduler som du vill skapa och distribuera för din IoT Edge-lösning, och definierar också rutter mellan moduler. Standardlösningen har en **tempSensor** modulen och en **IoTEdgeModule1** modulen. Den **tempSensor** modulen genererar simulerade data till den **IoTEdgeModule1** modulen, samtidigt som standardkoden i den **IoTEdgeModule1** modulen direkt pipes togs emot meddelanden till Azure IoT Hub.

Den **IoTEdgeModule1** project är ett konsolprogram med .NET Core 2.1. Den innehåller filer som krävs Docker du behöver för din IoT Edge-enhet med en behållare för Windows eller Linux-behållare. Den `module.json` filen beskriver metadata för en modul. Den faktiska modul kod som tar Azure IoT Device SDK som ett beroende finns i den `Program.cs` eller `main.c` fil.

## <a name="develop-your-module"></a>Utveckla din modell

Modulen som medföljer lösningen standardkoden finns på **IoTEdgeModule1** > **Program.cs** (för C#) eller **main.c** (C). Modulen och `deployment.template.json` fil har ställts in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen har utformats för att hämta indata från en källa (i det här fallet den **tempSensor** modul som simulerar data) och skicka det till Azure IoT Hub.

När du är redo att anpassa mallen modulen med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Initiera iotedgehubdev med anslutningssträngen för IoT Edge-enhet

1. Kopiera anslutningssträngen för alla IoT Edge-enhet från **primär anslutningssträng** i Visual Studio Cloud Explorer. Ska du inte kopiera anslutningssträngen för en icke-Edge-enhet som ikon för en IoT Edge-enhet skiljer sig från ikonen för en icke-Edge-enhet.

   ![Kopiera anslutningssträngen för Edge-enhet](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Högerklicka på den **AzureIoTEdgeApp1** projektet och klicka sedan på **ange Edge Enhetsanslutningssträngen** att öppna fönstret för Azure IoT Edge.

   ![Öppna Set Edge-Anslutningsfönster sträng](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Ange anslutningssträngen från det första steget och välj sedan **OK**.

> [!NOTE]
> Du måste följa de här stegen en gång på utvecklingsdatorn som resultatet ska vidtas automatiskt alla efterföljande Azure IoT Edge-lösningar. Den här proceduren kan tillämpas igen om du vill ändra till en annan anslutningssträng.

## <a name="build-and-debug-single-module"></a>Skapa och Felsök enda modul

Normalt ska du testa och felsöka varje modul innan du kör i en hel lösning med flera moduler.

1. Högerklicka på **IoTEdgeModule1** och välj **Set as StartUp Project** på snabbmenyn.

   ![Ange start-projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Tryck på **F5** eller klicka på knappen nedan för att köra modulen; det kan ta 10&ndash;20 sekunder första gången du gör.

   ![Kör modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Du bör se en .NET Core-konsolapp som startar om modulen har initierats.

   ![Modulen körs](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Om du utvecklar i C#, konfigurera en brytpunkt i den `PipeMessage()` fungera i **Program.cs**; om med hjälp av C, konfigurera en brytpunkt i den `InputQueue1Callback()` fungera i **main.c**. Du kan sedan testa den genom att skicka ett meddelande genom att köra följande kommando i en **Git Bash** eller **WSL Bash** shell. (Du kan inte köra den `curl` från en PowerShell eller Kommandotolken.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Felsöka enda modul](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Brytpunkten ska aktiveras. Du kan titta på variabler i Visual Studio **lokala variabler** fönster.

   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden i stället för `curl`.

1. Tryck på **Ctrl + F5** eller klicka på stoppknappen att avsluta felsökningen.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Skapa och Felsök IoT Edge-lösning med flera moduler

När du är klar utvecklar en enda modul måste du kanske vill köra och felsöka en hel lösning med flera moduler.

1. Lägga till en andra modul i lösningen genom att högerklicka på **AzureIoTEdgeApp1** och välja **Lägg till** > **nya IoT Edge-modul**. Standardnamnet för den andra modulen är **IoTEdgeModule2** och fungerar som en annan pipe-modul.

1. Öppna filen `deployment.template.json` så ser du **IoTEdgeModule2** har lagts till i den **moduler** avsnittet. Ersätt den **vägar** avsnittet med följande. Om du har anpassat din Modulnamnen, kontrollera att du uppdaterar dessa namn så att de matchar.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Högerklicka på **AzureIoTEdgeApp1** och välj **Set as StartUp Project** på snabbmenyn.

1. Skapa din brytpunkter och tryck sedan på **F5** att köra och felsöka flera moduler samtidigt. Du bör se flera .NET Core-app konsolfönster, vilket varje fönster som representerar en annan modul.

   ![Felsöka flera moduler](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Tryck på **Ctrl + F5** eller välj stoppknappen för att stoppa felsökningen.

## <a name="build-and-push-images"></a>Skapa och skicka avbildningar

1. Se till att **AzureIoTEdgeApp1** är Start-projekt. Välj antingen **felsöka** eller **versionen** som konfiguration att bygga för bilderna modulen.

    > [!NOTE]
    > När du väljer **felsöka**, Visual Studio använder `Dockerfile.(amd64|windows-amd64).debug` att skapa Docker-avbildningar. Detta inkluderar .NET Core kommandoradsverktyget felsökningsprogrammet VSDBG i en behållaravbildning när du skapar den. För produktionsklara IoT Edge-moduler, rekommenderar vi att du använder den **versionen** konfiguration, som använder `Dockerfile.(amd64|windows-amd64)` utan VSDBG.

1. Om du använder ett privat register som Azure Container Registry kan du använda kommandot Docker för att logga in på den. Om du använder lokala registret, kan du [kör lokala registret](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Om du använder ett privat register som Azure Container Registry kan du behöva lägga till din inloggningsinformation för registret körningsinställningar i filen `deployment.template.json`. Ersätt platshållarna med din faktiska ACR admin användarnamn, lösenord och registret namn.

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

1. Högerklicka på **AzureIoTEdgeApp1** och välj **Build and Push lösning** att bygga och överföra Docker-avbildning för varje modul.

   ![Skapa och skicka avbildningar](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Distribuera lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan också distribuera moduler med Cloud Explorer för Visual Studio. Du redan har ett manifest för distribution som förberedd för ditt scenario, den `deployment.json` fil och allt du behöver göra är väljer du en enhet som tar emot distributionen.

1. Öppna **Cloud Explorer** genom att klicka på **visa** > **Cloud Explorer**. Kontrollera att du har loggat in till Visual Studio 2017.

1. I **Cloud Explorer**, expandera din prenumeration, hitta Azure IoT Hub och Azure IoT Edge-enheten som du vill distribuera.

1. Högerklicka på IoT Edge-enhet för att skapa en distribution för den, du måste välja distribution manifestfilen under den `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Du måste inte välja `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Klicka på Uppdatera om du vill se de nya modulerna som körs tillsammans med den **TempSensor** modulen och **$edgeAgent** och **$edgeHub**.

## <a name="view-generated-data"></a>Visa genererade data

1. För att övervaka D2C-meddelande för en specifik enhet, väljer enheten i listan och klicka sedan på **börja övervaka D2C meddelanden** i den **åtgärd** fönster.

1. Om du vill stoppa dataövervakningen, väljer enheten i listan och välj sedan **stoppa övervakning av D2C meddelanden** i den **åtgärd** fönster.

## <a name="next-steps"></a>Nästa steg

Att utveckla anpassade moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
