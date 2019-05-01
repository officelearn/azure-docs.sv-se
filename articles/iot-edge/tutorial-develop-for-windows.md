---
title: Utveckla-modulen för Windows-enheter – Azure IoT Edge | Microsoft Docs
description: Den här självstudiekursen beskriver hur du konfigurerar din Utvecklingsresurser för dator och molnet att utveckla IoT Edge-moduler med hjälp av Windows-behållare för Windows-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6b629c7b8a6addbb2ef6f9ced58d4db656ad2480
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576798"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Självstudier: Utveckla IoT Edge-moduler för Windows-enheter

Använda Visual Studio 2017 för att utveckla och distribuera kod till Windows-enheter som kör IoT Edge.

I snabbstarten, skapas en IoT Edge-enhet med en Windows-dator och distribueras en färdiga modul från Azure Marketplace. Den här självstudiekursen beskriver vad som krävs för att utveckla och distribuera din egen kod till en IoT Edge-enhet. Den här självstudien är en användbar förutsättning för alla andra självstudier, som anger att mer information om specifika programmeringsspråk eller Azure-tjänster. 

Den här självstudien används ett exempel på distribution av en **C-modul till en Windows-enhet**. Det här exemplet har valts för dess enkelhet, så att du kan lära dig om utvecklingsverktygen utan att behöva bekymra dig om du har rätt bibliotek som är installerade. När du förstår begreppen som utveckling, kan du välja din det språk du föredrar eller Azure-tjänst som fördjupar. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ställ in din utvecklingsdator.
> * Använd IoT Edge-verktyg för Visual Studio 2017 för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra den i ett Azure container registry.
> * Distribuera din kod till en IoT Edge-enhet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudiekursen beskriver utvecklingen av IoT Edge-modul. En *IoT Edge-modul*, eller bara ibland *modulen* för kort, är en behållare som innehåller körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge-enhet. Moduler utför specifika uppgifter, t.ex. mata in data från sensorer, utföra dataanalys eller data rengöringen eller skicka meddelanden till en IoT-hubb. Mer information finns i [förstå Azure IoT Edge-moduler](iot-edge-modules.md).

När du utvecklar IoT Edge-moduler, är det viktigt att förstå skillnaden mellan utvecklingssystemet och målet IoT Edge-enhet där modulen så småningom ska distribueras. Behållaren som du skapar för att lagra din modul-kod måste matcha operativsystemet (OS) på den *målenheten*. Det här konceptet är enklare för utveckling för Windows-behållare, eftersom Windows-behållare endast köras på Windows-operativsystem. Men du kan till exempel använda en Windows-utvecklingsdator för att skapa moduler för Linux IoT Edge-enheter. I det här scenariot, skulle du behöva se till att utvecklingsdatorn kördes Linux-behållare. När du går igenom den här kursen, Tänk på skillnaden mellan *utvecklingsdator OS* och *behållare OS*.

Den här kursen riktar sig mot Windows-enheter som kör IoT Edge. Windows IoT Edge-enheter använder Windows-behållare. Vi rekommenderar att du använder Visual Studio 2017 för att utveckla för Windows-enheter, så det är den här självstudien använder. Du kan använda Visual Studio Code, även om det finns skillnader i stöd mellan de två verktyg.

I följande tabell visas stöds utvecklingsscenarier för **Windows-behållare** i Visual Studio Code och Visual Studio 2017.

|   | Visual Studio-koden | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics |   |
| **Språk** | C#(Felsökning stöds inte) | C <br> C# |
| **Mer information** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/itemdetails?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

Den här självstudien Lär hur utveckling för Visual Studio 2017. Om du föredrar att använda Visual Studio Code, läser du anvisningarna i [använda Visual Studio Code för att utveckla och felsöka moduler för Azure IoT Edge](how-to-vs-code-develop-module.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

En utvecklingsdator:

* Windows 10 med 1809 uppdateringen eller senare.
* Du kan använda din egen dator eller en virtuell dator, beroende på dina inställningar för utveckling.
* Installera [Git](https://git-scm.com/). 
* Installera Azure IoT C SDK för Windows x64 via vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

En Azure IoT Edge-enhet på Windows:

* Vi rekommenderar att du inte kör IoT Edge på utvecklingsdatorn, men i stället använda en separat enhet. Denna skillnad mellan utvecklingssystemet och IoT Edge-enhet mer korrekt speglar en SANT distributionsscenariot och hjälper till att hålla de olika begreppen direkt.
* Om du inte har en andra enhet som är tillgängliga kan använda snabbstartsartikeln för att skapa en IoT Edge-enhet i Azure med en [Windows VM](quickstart.md).

Molnresurser:

* En kostnadsfri eller standard-nivån [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure. 

## <a name="install-container-engine"></a>Installationsprogram för behållare

IoT Edge-moduler är paketerade som behållare, så du behöver en motor för behållare på en utvecklingsdator för att skapa och hantera behållare. Vi rekommenderar att du använder Docker Desktop för utveckling på grund av dess många funktioner och dess popularitet som en behållare. Med Docker Desktop på en Windows-dator kan växla du mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge-enheter. 

Använd Docker-dokumentation för att installera på utvecklingsdatorn: 

* [Installera Docker Desktop för Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows, blir du tillfrågad om du vill använda Linux eller Windows-behållare. Den här självstudien använder **Windows-behållare**. Mer information finns i [växla mellan Windows och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Konfigurera Visual Studio och verktyg

Använd IoT-tillägg för Visual Studio 2017 för att utveckla IoT Edge-moduler. Dessa tillägg ger projektmallar, automatisera skapandet av manifestet distributionen och gör att du kan övervaka och hantera IoT Edge-enheter. I det här avsnittet ska du installera Visual Studio och IoT Edge-tillägget och sedan ställa in ditt Azure-konto för att hantera IoT Hub-resurser från Visual Studio. 

1. Om du inte redan har Visual Studio på en utvecklingsdator [installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017) med följande arbetsbelastningar: 

   * Azure Development
   * Skrivbordsutveckling medC++
   * .NET Core plattformsoberoende utveckling

1. Om du redan har Visual Studio 2017 på utvecklingsdatorn, se till att dess version är 15.7 eller högre. Följ stegen i [ändra Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) att lägga till nödvändiga arbetsbelastningar om du inte har redan.

2. Ladda ned och installera den [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) tillägget för Visual Studio 2017. 

3. Öppna Visual Studio när installationen har slutförts.

4. Välj **visa** > **Cloud Explorer**. 

5. Välj profilikonen i cloud explorer och logga in på ditt Azure-konto om du inte redan loggat in. 

6. När du har loggat in visas dina Azure-prenumerationer. Välj de prenumerationer som du vill komma åt via cloud explorer och välj sedan **tillämpa**. 

7. Expandera din prenumeration, sedan **IoT-hubbar**, sedan din IoT-hubb. Du bör se en lista över dina IoT-enheter och kan använda den här explorer ska hanteras. 

   ![Få åtkomst till IoT Hub-resurser i Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt projekt i modulen

Verktyg för Azure IoT Edge-tillägget innehåller projektmallar för alla stöds IoT Edge modulen språk i Visual Studio 2017. Dessa mallar kan alla filer och kod som du behöver distribuera en fungerande-modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med egen affärslogik. 

1. Köra Visual Studio som administratör.

2. Välj **Arkiv** > **Nytt** > **Projekt**. 

3. I fönstret nytt projekt väljer du den **Azure IoT** programprojektet typ och väljer den **Azure IoT Edge** projekt. Byt namn på projektet och lösningen eller acceptera standardinställningen **AzureIoTEdgeApp1**. Klicka på **OK** för att skapa projektet. 

   ![Skapa ett nytt projekt i Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

4. Konfigurera ditt projekt i IoT Edge-programmet och modulfönstret, med följande värden: 

   | Fält | Värde |
   | ----- | ----- |
   | Programplattform | Avmarkera **Linux Amd64**, och kontrollera **WindowsAmd64**. |
   | Välj en mall | Välj **C modulen**. | 
   | Modulen projektnamn | Acceptera standardvärdet **IoTEdgeModule1**. | 
   | Lagringsplatsen för docker-avbildningen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen innehåller redan från modulen Projekt namn-värde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Sista avbildningslagringsplatsen ut \<registernamn\>.azurecr.io/iotedgemodule1. |

   ![Konfigurera ditt projekt för målenhet och Modultyp behållarregister](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. Välj **OK** att tillämpa ändringarna. 

När det nya projektet har lästs in i Visual Studio-fönstret kan du ta en stund att bekanta dig med de filer som den skapats: 

* Ett projekt med IoT Edge kallas **AzureIoTEdgeApp1.Windows.Amd64**.
    * Den **moduler** mappen finns länkar till de moduler som ingår i projektet. I det här fallet ska bara IoTEdgeModule1. 
    * Den **deployment.template.json** filen är en mall för att skapa ett manifest för distribution. En *distribution manifest* är en fil som definierar exakt vilka moduler som du vill distribuerad på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet. 
* Ett projekt med IoT Edge-modulen kallas **IoTEdgeModule1**.
    * Den **main.c** filen innehåller C modul-koden som medföljer projektmallen. Standardmodul hämtar indata från en källa och skickar dem vidare till IoT Hub. 
    * Den **module.json** hold filinformation om modulen, inklusive fullständig avbildningslagringsplatsen avbildningen version och vilka Dockerfile som ska användas för varje plattform som stöds.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange dina autentiseringsuppgifter för registret till IoT Edge-agenten

IoT Edge-körningen behöver dina autentiseringsuppgifter för registret att hämta dina behållaravbildningar till IoT Edge-enhet. Lägg till dessa autentiseringsuppgifter för distribution av mallen. 

1. Öppna den **deployment.template.json** fil.

2. Hitta den **registryCredentials** -egenskapen i $edgeAgent önskade egenskaper. 

3. Uppdatera egenskapen med dina autentiseringsuppgifter efter det här formatet: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

### <a name="build-and-push"></a>Skapa och skicka

Utvecklingsdatorn har nu åtkomst till ditt behållarregister och din IoT Edge-enheter kommer för. Det är dags att projektkoden i en behållaravbildning. 

1. Högerklicka på den **AzureIotEdgeApp1.Windows.Amd64** projektmappen, och välj **Build and Push IoT Edge-moduler**. 

   ![Skapa och skicka IoT Edge-moduler](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller fullständig distribution manifest, byggas ut information i distributionsmallen och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret. 

   Den här processen kan ta flera minuter första gången, men är snabbare nästa gång du kör kommandona. 

2. Öppna den **deployment.windows amd64.json** fil i den nyligen skapade config-mappen. (Den config-mappen kan inte förekomma i Solution Explorer i Visual Studio. Om så är fallet, väljer du den **visa alla filer** ikonen i Solution Explorer-verktygsfältet.)

3. Hitta den **bild** parameter i avsnittet IotEdgeModule1. Observera att bilden innehåller fullständiga avbildningslagringsplatsen med taggen namn, version och arkitektur från filen module.json.

4. Öppna den **module.json** filen i mappen IotEdgeModule1. 

5. Ändra det lägre versionsnumret för avbildningen modulen. (Versionen, inte $schema-version.) Till exempel öka versionsnumret patch till **0.0.2** som om vi hade gjort en liten korrigering i modulen koden. 

   >[!TIP]
   >Modulversioner aktivera versionskontroll och gör att du kan testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte öka Modulversion innan du skapar och push-överföring, över du lagringsplatsen i ditt behållarregister. 

6. Spara dina ändringar i filen module.json.

7. Högerklicka på den **AzureIotEdgeApp1.Windows.Amd64** projekt mappen igen och väljer du återigen **Build and Push IoT Edge-moduler**. 

8. Öppna den **deployment.windows amd64.json** filen igen. Observera att en ny fil inte har skapats när du körde versions- och push-kommandot igen. I stället har samma fil uppdaterats för att återspegla ändringarna. IotEdgeModule1 bild pekar nu på 0.0.2 version av behållaren. Den här ändringen i manifestet distribution är hur du berätta för IoT Edge-enhet att det finns en ny version av en modul för att hämta. 

9. Att ytterligare kontrollera det kommandot versions- och push gjorde, gå till den [Azure-portalen](https://portal.azure.com) och navigera till ditt behållarregister. 

10. Välj i ditt behållarregister **databaser** sedan **iotedgemodule1**. Kontrollera att båda versionerna av avbildningen pushats till registret.

    ![Visa båda bild-versioner i container registry](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Felsöka

Om det uppstår problem när du bygger och push-överföra avbildningen modulen har den ofta att göra med Docker-konfigurationen på utvecklingsdatorn. Använd följande kontroller för att granska konfigurationen: 

* Stötte du den `docker login` kommando med hjälp av de autentiseringsuppgifter som du kopierade från ditt behållarregister? Dessa autentiseringsuppgifter skiljer sig från de som används för att logga in på Azure. 
* Stämmer containerlagringsplatsen? Har den din rätt namnet på behållarregistret och din rätt Modulnamn? Öppna den **module.json** filen i mappen IotEdgeModule1 att kontrollera. Värdet för databasen bör se ut så  **\<registernamn\>.azurecr.io/iotedgemodule1**. 
* Om du har använt ett annat namn än **IotEdgeModule1** för, är det namnet konsekvent i hela lösningen?
* Din dator kör samma typ av behållare som du skapar? Den här självstudien är för Windows IoT Edge-enheter så att dina Visual Studio-filer ska ha den **windows amd64** tillägget och Docker Desktop bör köra Windows-behållare. 

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Du har verifierat att de inbyggda behållaravbildningarna lagras på ditt behållarregister, så är det dags att distribuera dem till en enhet. Se till att din IoT Edge-enhet är igång. 

1. Öppna Cloud Explorer i Visual Studio och expandera informationen för din IoT-hubb. 

2. Välj namnet på den enhet som du vill distribuera till. I den **åtgärder** väljer **skapa distribution**.

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-windows/create-deployment.png)


3. I Utforskaren, navigera till mappen konfiguration av dina projekt och välj den **deployment.windows amd64.json** fil. Den här filen finns ofta `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   Använd inte filen deployment.template.json, som inte har fullständig modulen bild värdena i den. 

4. Visa information för din IoT Edge-enhet i Cloud Explorer Se moduler på enheten.

5. Använd den **uppdatera** knappen för att uppdatera enhetens status för att se att tempSensor och IotEdgeModule1 moduler distribueras din enhet. 


   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enhet

IotEdgeModule1 koden tar emot meddelanden via dess inkommande kö och skickar dem via den utgående kön. Distribution av manifestet deklarerats vägar som skickas meddelanden från tempSensor till IotEdgeModule1 och sedan vidarebefordras meddelanden från IotEdgeModule1 till IoT Hub. Azure IoT Edge-verktyg för Visual Studio kan du se meddelanden när de anländer till IoT Hub från dina enskilda enheter. 

1. I Visual Studio cloud explorer, väljer du namnet på IoT Edge-enhet som du distribuerat till. 

2. I den **åtgärder** menyn och välj **börja övervaka D2C-meddelande**.

3. Titta på den **utdata** avsnittet i Visual Studio för att se meddelanden som inkommer på din IoT-hubb. 

   Det kan ta några minuter för båda modulerna att starta. IoT Edge-körningen behöver att ta emot nya distribution manifestet, hämta modulen bilder från behållaren runtime och starta sedan varje ny modul. Om du 

   ![Visa inkommande enhet till moln-meddelanden](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på din enhet själva kan du använda kommandon i det här avsnittet för att granska IoT Edge-körningen och moduler som körs på din enhet. 

Kommandona i det här avsnittet är för din IoT Edge-enhet, inte på din utvecklingsdator. Om du använder en virtuell dator för din IoT Edge-enhet kan ansluta till den nu. Gå till den virtuella datorns översiktssidan i Azure, och välj **Connect** att komma åt fjärrskrivbordsanslutningen. På enheten, öppnar du ett kommando eller PowerShell-fönster för att köra den `iotedge` kommandon.

* Visa alla moduler som har distribuerats till enheten och kontrollera deras status:

   ```cmd
   iotedge list
   ```

   Du bör se fyra moduler: de två moduler för IoT Edge-körning och tempSensor IotEdgeModule1. Alla fyra bör anges som körs.

* Granska loggarna för en specifik modul:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge-moduler är skiftlägeskänsliga. 

   TempSensor och IotEdgeModule1 loggar ska visa meddelanden som de bearbetning. Modulen edgeAgent ansvarar för att starta andra moduler så att loggar har information om hur du implementerar distribution manifestet. Om alla moduler visas inte i listan eller inte körs, har förmodligen edgeAgent loggarna felen. Modulen edgeHub ansvarar för kommunikationen mellan moduler och IoT Hub. Om modulerna som är igång och körs, men meddelanden inte anländer till din IoT-hubb, har förmodligen edgeHub loggarna felen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du ställer in Visual Studio 2017 på utvecklingsdatorn och distribuerat din första IoT Edge-modul från den. Nu när du vet de grundläggande begreppen kan du prova att lägga till funktioner till en modul så att den kan analysera informationen som passerar genom den. Välj det språk du föredrar: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)