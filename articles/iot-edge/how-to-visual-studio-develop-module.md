---
title: Utveckla och felsöka moduler i Visual Studio – Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio 2019 för att utveckla och felsöka moduler för Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0ed7d65601465a197cb4d7f92f500e1bf29ad8c2
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839674"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Använd Visual Studio 2019 för att utveckla och felsöka moduler för Azure IoT Edge

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio 2019 som huvud verktyg för att utveckla och felsöka moduler.

Azure IoT Edge-verktyg för Visual Studio ger följande fördelar:

- Skapa, redigera, skapa, köra och Felsök Azure IoT Edge lösningar och moduler på din lokala utvecklings dator.
- Distribuera din Azure IoT Edge-lösning till Azure IoT Edge-enhet via Azure IoT Hub.
- Koda dina Azure IoT-moduler i C C# eller med alla fördelar med Visual Studio-utveckling.
- Hantera Azure IoT Edge-enheter och moduler med Användargränssnittet.

Den här artikeln visar hur du använder Azure IoT Edge verktyg för Visual Studio 2019 för att utveckla dina IoT Edge-moduler. Du också lära dig hur du distribuerar ditt projekt till din Azure IoT Edge-enhet. För närvarande tillhandahåller Visual Studio 2019 stöd för moduler som skrivits i C C#och. De enhets arkitekturer som stöds är Windows x64 och Linux x64 eller ARM32. Mer information om operativ system, språk och arkitekturer som stöds finns i [stöd för språk och arkitektur](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows som en utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet.

Eftersom den här artikeln använder Visual Studio 2019 som huvud utvecklings verktyg, installerar du Visual Studio. Se till att du inkluderar **Azure-utveckling** och **Skriv bords C++ utveckling med** arbets belastningar i Visual Studio 2019-installationen. Du kan [ändra Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) för att lägga till de nödvändiga arbets belastningarna.

När Visual Studio 2019 är klar behöver du även följande verktyg och komponenter:

- Hämta och installera [Azure IoT Edge verktyg](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) från Visual Studio Marketplace och skapa ett IoT Edge-projekt i visual Studio 2019.

> [!TIP]
> Om du använder Visual Studio 2017 laddar plrease ned och installerar [Azure IoT Edge verktyg](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) för vs 2017 från Visual Studio Marketplace

- Hämta och installera [Docker Community Edition](https://docs.docker.com/install/) på utvecklings datorn för att skapa och köra dina modulblad. Du måste ange Docker CE för att köras i Linux container mode eller Windows container mode.

- Konfigurera din lokala utvecklings miljö för att felsöka, köra och testa din IoT Edge-lösning genom att installera [verktyget Azure IoT EdgeHub dev](https://pypi.org/project/iotedgehubdev/). Installera [python (2.7/3.6) och pip](https://www.python.org/) och installera sedan **iotedgehubdev** -paketet genom att köra följande kommando i terminalen. Kontrollera att din Azure IoT EdgeHub utvecklingsverktyg version är större än 0.3.0-betaversionen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Klona lagrings platsen och installera Vcpkg library Manager och installera sedan **Azure-IoT-SDK-c-paketet** för Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) -eller Docker- [hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet.

- Om du vill testa modulen på en enhet behöver du en aktiv IoT-hubb med minst en IoT Edge enhet. Om du vill använda datorn som en IoT Edge enhet följer du stegen i snabb starten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge daemon på din utvecklings dator kan du behöva stoppa EdgeHub och EdgeAgent innan du börjar utveckla i Visual Studio.

### <a name="check-your-tools-version"></a>Kontrollera vilken version av verktyg

1. Från **verktyg** -menyn väljer du **tillägg och uppdateringar**. Expandera **installerade >-verktyg** och du hittar **Azure IoT Edge-verktyg** och **Cloud Explorer för Visual Studio**.

1. Observera den installerade versionen. Du kan jämföra den här versionen med den senaste versionen på Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Om din version är äldre än vad som är tillgängligt på Visual Studio Marketplace uppdaterar du verktygen i Visual Studio enligt följande avsnitt.

### <a name="update-your-tools"></a>Uppdatera dina verktyg

1. I dialog **rutan tillägg och uppdateringar** expanderar du **uppdateringar > Visual Studio Marketplace**, väljer **Azure IoT Edge verktyg** eller **Cloud Explorer för Visual Studio** och väljer **Uppdatera**.

1. När verktyg uppdateringen laddas ned, stänger du Visual Studio till utlösaren verktygen uppdatera med VSIX installationsprogrammet.

1. I installations programmet väljer du **OK** för att starta och sedan **ändra** för att uppdatera verktygen.

1. När uppdateringen är klar väljer du **Stäng** och starta om Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Skapa ett Azure IoT Edge-projekt

Azure IoT Edge-projektmallen i Visual Studio skapar ett projekt som kan distribueras till Azure IoT Edge-enheter i Azure IoT Hub. Först skapar du en Azure IoT Edge-lösning och genererar sedan den första modulen i lösningen. Varje IoT Edge-lösning kan innehålla mer än en modul.

> [!TIP]
> Den IoT Edge projekt strukturen som skapats av Visual Studio är inte samma som i Visual Studio Code.

1. I dialog rutan nytt projekt i Visual Studio, Sök och välj **Azure IoT Edge** projekt och klicka på **Nästa**. I fönstret projekt konfiguration anger du ett namn för projektet och anger platsen och väljer sedan **skapa**. Standardnamnet för projektet är **AzureIoTEdgeApp1**.

   ![Skapa nytt projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. I fönstret **Lägg till IoT Edge program och modul** väljer du antingen  **C# modul** eller **C-modul** och anger sedan namnet på modulen och avbildnings lagrings platsen. Visual Studio fyller automatiskt i modulnamnet med **localhost: 5000/\>< ditt Modulnamn**. Ersätt den med din egen information i registret. Om du använder ett lokalt Docker-register för testning är **localhost** bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Inloggnings servern ser ut som   **_\<register\>namn_. azurecr.io**. Ersätt endast **localhost: 5000** del av strängen så att det slutliga resultatet ser ut som  **\< *register*\> _\<\>_ namn. azurecr.io/ditt Modulnamn**. Standardnamnet för modulen är **IoTEdgeModule1**

   ![Lägg till program och modul](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Välj **OK** för att skapa Azure IoT Edge-lösningen med en modul som använder C# antingen eller C.

Nu har du ett **AzureIoTEdgeApp1. Linux. amd64** -projekt eller ett **AzureIoTEdgeApp1. Windows. amd64** -projekt och även ett **IoTEdgeModule1** -projekt i din lösning. Varje **AzureIoTEdgeApp1** -projekt har `deployment.template.json` en fil som definierar de moduler som du vill bygga och distribuera för din IoT Edge-lösning, och som även definierar vägar mellan moduler. Standardlösningen har en **SimulatedTemperatureSensor** -modul och en **IoTEdgeModule1** -modul. **SimulatedTemperatureSensor** -modulen genererar simulerade data till **IoTEdgeModule1** -modulen, medan standard koden i modulen **IoTEdgeModule1** direkt rör mottagna meddelanden till Azure IoT Hub.

**IoTEdgeModule1** -projektet är ett .net Core 2,1-konsolprogram om det är C# en modul. Den innehåller obligatoriska Docker-filer som du behöver för din IoT Edge-enhet som körs med antingen en Windows-behållare eller Linux-behållare. `module.json` Filen beskriver metadata för en modul. Den faktiska modul koden, som använder Azure IoT-enhetens SDK som ett beroende, finns i `Program.cs` filen eller. `main.c`

## <a name="develop-your-module"></a>Utveckla din modell

Koden för standardmodulen som medföljer lösningen finns på **IoTEdgeModule1** > **program.cs** (for C#) eller **main. c** (c). Modulen och `deployment.template.json` filen har kon figurer ATS så att du kan skapa lösningen, push-överför den till behållar registret och distribuera den till en enhet för att starta testning utan att behöva röra någon kod. Modulen är byggd för att ta emot data från en källa (i det här fallet **SimulatedTemperatureSensor** -modulen som simulerar data) och rör den till Azure IoT Hub.

När du är redo att anpassa mallen med din egen kod använder du [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) : er för att bygga moduler som uppfyller nyckel behoven för IoT-lösningar som säkerhet, enhets hantering och pålitlighet.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Initiera iotedgehubdev med IoT Edge enhets anslutnings sträng

1. Kopiera anslutnings strängen för alla IoT Edge enheter från **primär anslutnings sträng** i Visual Studio Cloud Explorer. Se till att du inte kopierar anslutnings strängen för en icke-Edge-enhet, eftersom ikonen för en IoT Edge enhet skiljer sig från ikonen för en icke-Edge-enhet.

   ![Kopiera anslutningssträngen för Edge-enhet](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Gå till **verktyg** > **Azure IoT Edge verktyg** > **installations IoT Edge Simulator**, klistra in anslutnings strängen och klicka på **OK**.

   ![Öppna Set Edge-Anslutningsfönster sträng](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Ange anslutnings strängen från det första steget och välj sedan **OK**.

> [!NOTE]
> Du behöver bara följa de här stegen en gång på din utvecklings dator eftersom resultatet automatiskt tillämpas på alla efterföljande Azure IoT Edge lösningar. Den här proceduren kan följas igen om du behöver ändra till en annan anslutnings sträng.

## <a name="build-and-debug-single-module"></a>Bygga och felsöka en enskild modul

Normalt vill du testa och felsöka varje modul innan du kör den i en hel lösning med flera moduler.

1. Högerklicka på **IoTEdgeModule1** och välj **Ange som start projekt** i snabb menyn.

   ![Ange start-projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Tryck på **F5** eller klicka på knappen nedan för att köra modulen. Det kan ta 10&ndash;20 sekunder för första gången.

   ![Kör modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Du bör se en .NET Core console-app som startar om modulen har initierats korrekt.

   ![Modulen körs](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Om du utvecklar C#i, `PipeMessage()` ställer du in en Bryt punkt i funktionen i **program.cs**. om `InputQueue1Callback()` du använder C ställer du in en Bryt punkt i funktionen i **main. C**. Du kan sedan testa det genom att skicka ett meddelande genom att köra följande kommando i **git bash** eller **Wsl bash** Shell. (Du kan inte köra `curl` kommandot från PowerShell eller kommando tolken.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Felsöka enda modul](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Brytpunkten ska aktiveras. Du kan se variabler i fönstret för **lokala** Visual Studio-fönster.

   > [!TIP]
   > Du kan också använda [Postman](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden `curl`i stället för.

1. Tryck på **Ctrl + F5** eller klicka på stoppknappen att avsluta felsökningen.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Skapa och Felsök IoT Edge-lösning med flera moduler

När du är klar med att utveckla en enda modul kanske du vill köra och felsöka en hel lösning med flera moduler.

1. Lägg till en andra modul i lösningen genom att högerklicka på **AzureIoTEdgeApp1** och välja **Lägg till** > **ny IoT Edge modul**. Standard namnet för den andra modulen är **IoTEdgeModule2** och fungerar som en annan pipe-modul.

1. Öppna filen `deployment.template.json` så ser du att **IoTEdgeModule2** har lagts till i avsnittet **moduler** . Ersätt den **vägar** avsnittet med följande. Om du har anpassat dina Modulnamn, se till att du uppdaterar namnen så att de matchar.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Högerklicka på **AzureIoTEdgeApp1** och välj **Ange som start projekt** i snabb menyn.

1. Skapa dina Bryt punkter och tryck sedan på **F5** för att köra och felsöka flera moduler samtidigt. Du bör se flera .NET Core-konsolprogram för Windows, som varje fönster representerar en annan modul.

   ![Felsöka flera moduler](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Tryck på **CTRL + F5** eller Välj stopp-knappen för att stoppa fel sökningen.

## <a name="build-and-push-images"></a>Skapa och skicka avbildningar

1. Kontrol lera att **AzureIoTEdgeApp1** är start projektet. Välj antingen **Felsök** eller **släpp** som konfiguration för att bygga för dina modul-avbildningar.

    > [!NOTE]
    > När du väljer **Felsök**använder `Dockerfile.(amd64|windows-amd64).debug` Visual Studio för att bygga Docker-avbildningar. Detta inkluderar .NET Core kommandoradsverktyget felsökningsprogrammet VSDBG i en behållaravbildning när du skapar den. För produktions färdiga IoT Edge-moduler rekommenderar vi att du använder versions konfigurationen, som använder `Dockerfile.(amd64|windows-amd64)` utan VSDBG.

1. Om du använder ett privat register som Azure Container Registry använder du följande Docker-kommando för att logga in på den. Om du använder det lokala registret kan du [köra ett lokalt register](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Om du använder ett privat register som Azure Container Registry måste du lägga till din inloggnings information för registret i de körnings inställningar som finns i `deployment.template.json`filen. Ersätt plats hållarna med ditt faktiska ACR-administratörs användar namn, lösen ord och register namn.

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

1. Högerklicka på **AzureIoTEdgeApp1** och välj **bygge och push Edge-lösning** för att bygga och pusha Docker-avbildningen för varje modul.

   ![Skapa och skicka avbildningar](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Distribuera lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan också distribuera moduler med Cloud Explorer för Visual Studio. Du har redan ett distributions manifest som är för berett för `deployment.json` ditt scenario, filen och allt du behöver göra är att välja en enhet för att ta emot distributionen.

1. Öppna **Cloud Explorer** genom att klicka på **visa** > **Cloud Explorer**. Kontrol lera att du har loggat in i Visual Studio 2019.

1. I **Cloud Explorer**, expandera din prenumeration, hitta Azure IoT Hub och Azure IoT Edge-enheten som du vill distribuera.

1. Högerklicka på den IoT Edge enheten för att skapa en distribution för den. du måste välja distributions manifest filen under `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Du måste inte välja `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Klicka på Uppdatera om du vill se de nya modulerna som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**.

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka D2C-meddelandet för en speciell enhet väljer du enheten i listan och klickar sedan på **starta övervakning av inbyggd händelse slut punkt** i **Åtgärds** fönstret.

1. Om du vill stoppa övervakningen av data markerar du enheten i listan och väljer **stoppa övervakning av inbyggd händelse slut punkt** i **Åtgärds** fönstret.

## <a name="next-steps"></a>Nästa steg

För att utveckla anpassade moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md): er.
