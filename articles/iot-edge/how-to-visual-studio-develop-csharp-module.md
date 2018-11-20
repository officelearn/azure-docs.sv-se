---
title: Utveckla och felsöka C#-moduler för Azure IoT Edge i Visual Studio 2017 | Microsoft Docs
description: Använd Visual Studio 2017 för att utveckla och felsöka C#-modul för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 86f0867389ce6ee1c83190e03ba17362db29d6f7
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975881"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Använd Visual Studio 2017 för att utveckla och felsöka C#-moduler för Azure IoT Edge (förhandsversion)

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio 2017 som huvudsakliga verktyget för att utveckla och felsöka C#-moduler.

Azure IoT Edge-verktyg för Visual Studio ger följande fördelar:

- Skapa, redigera, skapa, köra och felsöka Azure IoT Edge-lösningar och moduler på din lokala utvecklingsdator.
- Distribuera din Azure IoT Edge-lösning till Azure IoT Edge-enhet via Azure IoT Hub.
- Koda din Azure IoT-moduler i C# samtidigt som du har alla fördelarna med Visual Studio-utveckling.
- Hantera Azure IoT Edge-enheter och moduler med Användargränssnittet. 

Den här artikeln visar hur du använder Azure IoT Edge-verktyg för Visual Studio 2017 för att utveckla IoT Edge-moduler i C#. Du också lära dig hur du distribuerar ditt projekt till din Azure IoT Edge-enhet.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows som en utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet.

Eftersom den här artikeln används Visual Studio 2017 som det huvudsakliga utvecklingsverktyg, installera Visual Studio. Och se till att du inkluderar **arbetsbelastningen Azure development** i Visual Studio 2017-installationen. Du kan [ändra Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) och Lägg till utvecklingsarbetsbelastningar för Azure.

När din Visual Studio 2017 är klar kan behöver du också:

- Ladda ned och installera [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) från Visual Studio marketplace och skapa IoT Edge-projekt i Visual Studio 2017.
- [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn för att skapa och köra dina avbildningar för modulen. Du måste ange korrekt Docker CE som körs i Linux-behållare eller Windows-behållare läge.
- Om du vill konfigurera lokal utvecklingsmiljö för att felsöka, köra och testa din IoT Edge-lösning du behöver [utvecklingsverktyg för Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installera [Python (2.7/3.6) och Pip](https://www.python.org/). Installera sedan **iotedgehubdev** genom att köra nedanstående kommando i terminalen. Kontrollera att din Azure IoT EdgeHub utvecklingsverktyg version är större än 0.3.0-betaversionen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

- Testa din modul, behöver du en aktiv IoT-hubb med minst en IoT Edge enhets-ID som skapats. Om du kör IoT Edge security daemon på utvecklingsdator, kan du behöva stoppa EdgeHub och EdgeAgent innan du börjar utveckling i Visual Studio. 

### <a name="check-your-tools-version"></a>Kontrollera vilken version av verktyg

1. Från den **verktyg** menyn, Välj **tillägg och uppdateringar**. Expandera **installerad > Verktyg** och du hittar **Azure IoT Edge** och **Cloud Explorer**.

2. Observera den installerade versionen. Du kan jämföra den här versionen med den senaste versionen på Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge))

3. Om din version är äldre uppdatera dina verktyg i Visual Studio som du ser i följande avsnitt.

### <a name="update-your-tools"></a>Uppdatera dina verktyg

1. I den **tillägg och uppdateringar** dialogrutan Expandera **uppdateringar > Visual Studio Marketplace**, Välj **Azure IoT Edge** eller **Cloud Explorer**och välj **uppdatering**.

2. När verktyg uppdateringen laddas ned, stänger du Visual Studio till utlösaren verktygen uppdatera med VSIX installationsprogrammet.

3. I installationsprogrammet, väljer **OK** att starta och sedan ändra för att uppdatera verktygen.

4. När uppdateringen är klar, Välj Stäng och starta om Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Skapa ett Azure IoT Edge-projekt

Azure IoT Edge-projektmallen i Visual Studio skapar ett projekt som kan distribueras till Azure IoT Edge-enheter i Azure IoT Hub. Först skapar du en Azure IoT Edge-lösning och sedan skapa den första C#-modulen i lösningen. Varje IoT Edge-lösning kan innehålla mer än en modul. 

1. Välj **Nytt** > **Projekt** från **Arkiv**-menyn i Visual Studio.

2. I den **nytt projekt** dialogrutan **installerad**, expandera **Visual C# > molnet**väljer **Azure IoT Edge**, ange ett  **Namnet** för ditt projekt och anger platsen och klickar på **OK**. Standardnamnet för projektet är **AzureIoTEdgeApp1**. 

   ![Nytt projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. I den **IoT Edge-Modulkonfiguration** väljer **C#-modul** skriver och ange din Modulnamn och modulen avbildningslagringsplatsen.  VS autopopulates modulen namnet med **localhost:5000**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, är det bra med localhost. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **<registry name>. azurecr.io**. Ersätt endast localhost-delen av strängen; ta inte bort modulens namn. Standardnamnet för modulen är **IoTEdgeModule1**

4. Klicka på **OK** att skapa Azure IoT Edge-projekt med en C#-modul.

Nu har en **AzureIoTEdgeApp1** projekt och en **IoTEdgeModule1** projektet i vår lösning. Den **AzureIoTEdgeApp1** projektet har en **deployment.template.json** fil. Den här filen definierar de moduler som du vill skapa och distribuera för din IoT Edge-lösning, och definierar rutter mellan moduler. Standardlösningen har en **tempSensor** modulen och en **IoTEdgeModule1** modulen. Den **tempSensor** modulen genererar simulerade data till **IoTEdgeModule1** modulen, samtidigt som standardkoden i **IoTEdgeModule1** modul är en pipe meddelande-modul som direkt pipe emot meddelanden till IoT Hub.

Den **IoTEdgeModule1** project är en .net Core 2,1 konsolprogram. Den innehåller nödvändig **Dockerfiles** du behöver för din IoT Edge-enhet som körs med behållare för Windows eller Linux-behållare. Den **module.json** filen beskriver metadata för en modul. Den **program.cs** är den faktiska modul-koden som tar Azure IoT Device SDK som ett beroende.

## <a name="develop-your-module"></a>Utveckla din modell

C#-modul som medföljer lösningen standardkoden finns på **IoTEdgeModule1** > **Program.cs**. Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub. 

När du är redo att anpassa mallen C# med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>Initiera **iotegehubdev** med anslutningssträngen för IoT Edge-enhet

1. Du behöver anslutningssträngen för alla IoT Edge-enhet, kan du kopiera värdet för ”primär anslutningssträng” från Cloud Explorer i Visual Studio 2017 följer. Kopiera inte anslutningssträngen för icke-Edge-enhet, ikonen för IoT Edge-enhet skiljer sig från det icke-Edge-enhet.

   ![Kopiera anslutningssträngen för Edge-enhet](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. Du behöver högerklickar du på **AzureIoTEdgeApp1** projektet för att öppna snabbmenyn och klicka sedan på **ange Edge Enhetsanslutningssträngen**, visas fönstret för Azure IoT Edge-inställningar.

   ![Öppna Set Edge-Anslutningsfönster sträng](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. I fönstret Inställningar ange anslutningssträngen som du fick i det första steget och klicka på **OK** knappen.

>[!NOTE]
>Detta är endast arbete, du bara behöver köra det här steget en gång på en dator, alla efterföljande Azure IoT-Edge lösningar får om kostnadsfritt. Naturligtvis kan du ställa det här steget om anslutningssträngen är ogiltig eller om du behöver ändra till ytterligare en anslutningssträng.

## <a name="build-and-debug-single-c-module"></a>Skapa och Felsök enda C#-modul

Vanligtvis vill vi test/debug varje modul innan vi gör det som körs i en hel lösning med flera moduler.

1. Välj **IoTEdgeModule1** som start-projekt i snabbmenyn.

   ![Ange start-projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Tryck på **F5** eller klicka på knappen nedan för att köra modulen, det kan ta 10 ~ 20 sekunder för första gången.

   ![Kör modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. Du bör se en .net Core-konsolapp igång om modulen har initierats.

   ![Modulen körs](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Nu kan du konfigurera en brytpunkt i **PipeMessage** i **Program.cs**, skickar meddelande genom att köra följande kommando i din **Git Bash** eller **WSL Bash**  (kör inte det i CMD eller Powershell) (du kan också hitta det här kommandot i VS utdatafönstret):

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Felsöka enda modul](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Brytpunkten ska aktiveras. Du kan se variabler i lokala variabler Visual Studio-fönstret.

   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden via i stället för `curl`.

5. Tryck på **Ctrl + F5** eller klicka på stoppknappen att avsluta felsökningen.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Skapa och Felsök IoT Edge-lösning med flera moduler

När vi har slutfört utveckla enda modulen nu ska vill vi köra och felsöka hela lösningen med flera moduler.

1. Lägg till din andra C#-modul i lösningen. Högerklicka på **AzureIoTEdgeApp1** och välj **Lägg till** -> **nya IoT Edge-modul**. Standardnamnet för den andra modulen är **IoTEdgeModule2** och det är fortfarande en pipe-modul.

2. Gå till **deployment.template.json**. Du kommer att se **IoTEdgeModule2** har lagts till i **moduler** avsnittet. Ersätt den **vägar** avsnittet med följande. Om du har anpassat din Modulnamnen, kontrollera att du uppdatera namnen i nedanstående efter ersättning.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Ange **AzureIoTEdgeApp1** projekt som start-projekt i snabbmenyn.

4. Ange brytpunkter och tryck på F5, och du kan köra och felsöka flera moduler samtidigt. Du bör se flera .net Core-app konsolfönster, varje fönster anger din C#-modul. 

   ![Felsöka flera moduler](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Tryck på **Ctrl + F5** eller klicka på stoppknappen att avsluta felsökningen.

## <a name="build-and-push-images"></a>Skapa och skicka avbildningar

1. Se till att **AzureIoTEdgeApp1** är Start-projekt. Välj **felsöka** eller **versionen** konfiguration för modulen bilderna för att skapa.

    > [!NOTE]
    > När du väljer **felsöka**, VS använder `Dockerfile.debug` att skapa Docker-avbildningar. Detta inkluderar .NET Core kommandoradsverktyget felsökningsprogrammet VSDBG i en behållaravbildning när du skapar den. Vi rekommenderar att du använder **versionen** konfiguration som använder `Dockerfile` utan VSDBG för produktionsklara IoT Edge-moduler.

2. Om du använder privat register som Azure Container Registry, kör du Docker logga in med följande kommando i terminalen. Om du använder lokala registret, kan du [kör lokala registret](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Om du använder privat register som Azure Container Registry, måste du placera registreringsanvändarnamn och lösenord i `deployment.template.json` under körningsinställningar med följande innehåll. Kom ihåg att ersätta platshållaren med din faktiska administratörens användarnamn och lösenord.

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

4. Högerklicka på **AzureIoTEdgeApp1** och välj snabbmenyn för **Build and Push lösning**, den skapar och överför docker-avbildningen för varje modul.

   ![Skapa och skicka avbildningar](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Distribuera lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan också distribuera moduler med Cloud Explorer för Visual Studio. Du redan har ett manifest för distribution som förberedd för ditt scenario, den `deployment.json` filen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

1. Öppna **Cloud Explorer** genom att klicka på **visa** > **Cloud Explorer**. Kontrollera att du har loggat in Visual Studio 2017 med ditt konto.

2. I **Cloud Explorer**, expandera din prenumeration, hitta Azure IoT Hub och Azure IoT Edge-enheten som du vill distribuera.

3. Högerklicka på IoT Edge-enhet för att skapa distribution för den, måste du välja distribution manifestfilen under den `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

>>[!NOTE]
>>Du måste inte välja `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

4. Klicka på uppdateringsknappen. Du bör se de nya modulerna som körs tillsammans med den **TempSensor** modulen och **$edgeAgent** och **$edgeHub**.

## <a name="view-generated-data"></a>Visa genererade data

1. För att övervaka D2C-meddelande för en specifik enhet, klickar du på enheten i listan och klicka på **börja övervaka D2C meddelanden** i **åtgärd** fönster.

2. Om du vill stoppa dataövervakningen, klickar du på enheten i listan och välj **stoppa övervakning av D2C meddelanden** i **åtgärd** fönster.

## <a name="next-steps"></a>Nästa steg

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
