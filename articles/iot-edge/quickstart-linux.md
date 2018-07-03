---
title: Snabbstart Azure IoT Edge + Linux | Microsoft Docs
description: I den här snabbstarten lär du dig hur du fjärrdistribuerar färdig kod till en IoT Edge-enhet.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 86bf28249321a705e8855de35121611b05009854
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063501"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Snabbstart: Distribuera din första IoT Edge-modul till en Linux x64-enhet

Azure IoT Edge flyttar kraften i molnet till IoT-enheter. I den här snabbstarten får du lära dig hur du använder molngränssnittet för att fjärrdistribuera färdig kod till en IoT Edge-enhet.

I den här snabbstarten lär du dig att:

1. Skapa en IoT Hub.
2. Registrera en IoT Edge-enhet till din IoT Hub.
3. Installera och starta IoT Edge-körningen på enheten.
4. Fjärrdistribuera en modul till en IoT Edge-enhet.

![Snabbstartsarkitektur][2]

Denna snabbstart gör din Linux-dator eller virtuella dator till en IoT Edge-enhet. Du kan sedan distribuera en modul från Azure Portal till din enhet. Modulen som du distribuerar i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar simulerade data för verksamhetsinsyn. 

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto][lnk-account] innan du börjar.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kommer använda Azure CLI för att slutföra många av stegen i den här snabbstarten och Azure IoT har ett tillägg för att aktivera ytterligare funktioner. 

Lägg till Azure IoT-tillägget till Cloud Shell-instansen.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa din IoT Hub i Azure Portal.
![Skapa IoT Hub][3]

Den kostnadsfria nivån för IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en kostnadsfri hubb kan du den. Varje prenumeration kan bara ha en kostnadsfri IoT Hub. 

1. Skapa en resursgrupp i Azure Cloud Shell. I följande exempel skapas en resursgrupp med namnet **TestResources** i regionen **USA, västra**. Genom att lägga alla resurser för snabbstarten och självstudierna i en grupp, kan du hantera dem tillsammans. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. Skapa en IoT Hub i din nya resursgrupp. Följande kod skapar en kostnadsfri **F1**-hubb i resursgruppen **TestResources**. Ersätt *{hub_name}* med ett unikt namn för din IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Registrera en enhet][4]

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Eftersom IoT Edge-enheterna fungerar och kan hanteras annorlunda än vanliga IoT-enheter, kan du ange att detta ska vara en IoT Edge-enhet från början. 

1. Ange följande kommando i Azure Cloud Shell för att skapa en enhet med namnet **myEdgeDevice** i din hubb.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. Hämta anslutningssträngen för din enhet som länkar den fysiska enheten med dess identitet i IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Kopiera anslutningssträngen och spara den. Du behöver det här värdet för att konfigurera IoT Edge-körningen i nästa avsnitt. 


## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera och starta Azure IoT Edge-körningen på enheten. 
![Registrera en enhet][5]

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. **IoT Edge säkerhetsdaemon** startas varje gång en Edge-enhet startar. Enheten startas genom att IoT Edge-agenten startas. **IoT Edge-agenten** underlättar distribution och övervakning av moduler på IoT Edge-enheten, inklusive IoT Edge-hubb. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub. 

### <a name="register-your-device-to-use-the-software-repository"></a>Registrera din enhet för att använda programvarudatabasen

De paket som du behöver för att köra IoT Edge-körningen hanteras i en programvarudatabas. Konfigurera din IoT Edge-enhet för att få åtkomst till den här databasen. 

Stegen i det här avsnittet gäller enheter som kör **Ubuntu 16.04**. För att komma åt programvarudatabasen i andra versioner av Linux kan du läsa [Installera Azure IoT Edge-körning på Linux (x64)](how-to-install-iot-edge-linux.md) eller [Installera Azure IoT Edge-körning på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

1. Installera databaskonfigurationen på den dator som du använder som IoT Edge-enhet.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Installera en offentlig nyckel för att komma åt databasen.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Installera en körmiljö för behållare

IoT Edge-körning är en uppsättning behållare, och den logik som du distribuerar till IoT Edge-enheten är paketerad som behållare. Förbered din enhet för dessa komponenter genom att installera en körmiljö för behållare.

Uppdatera **apt-get**.

   ```bash
   sudo apt-get update
   ```

Installera Moby (en körmiljö för behållare) och dess CLI-kommandon. 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Installera och konfigurera IoT Edge säkerhetsdaemon

Denna säkerhetsdaemon installeras som en systemtjänst så att IoT Edge-körningen startar varje gång enheten startas. Installationen innehåller även en version av **hsmlib** som gör att säkerhetsdaemon kan interagera med enhetens maskinvarusäkerhet. 

1. Ladda ned och installera IoT Edge säkerhetsdaemon. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Öppna konfigurationsfilen för IoT Edge. Konfigurationsfilen är skyddad, så du kan behöva använda förhöjd behörighet att komma åt den.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Lägg till anslutningssträngen för IoT Edge-enheten som du kopierade när du registrerade din enhet. Ersätt värdet för variabeln **device_connection_string** som du kopierade tidigare i den här snabbstarten.

4. Starta om Edge säkerhetsdaemon:

   ```bash
   sudo systemctl restart iotedge
   ```

5. Kontrollera att Edge säkerhetsdaemon körs som en systemtjänst:

   ```bash
   sudo systemctl status iotedge
   ```

   ![Se att Edge Daemon körs som en systemtjänst](./media/quickstart-linux/iotedged-running.png)

   Du kan också visa loggar från Edge säkerhetsdaemon genom att köra följande kommando:

   ```bash
   journalctl -u iotedge
   ```

6. Visa de moduler som körs på din enhet: 

   ```bash
   iotedge list
   ```

   ![Visa en modul på din enhet](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT Hub.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure Portal för att skicka en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten. I det här fallet skapar modulen som du överförde de miljödata som du använder i självstudierna. 

Öppna kommandotolken igen på den dator som kör den simulerade enheten. Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten:

   ```bash
   iotedge list
   ```

   ![Visa tre moduler på enheten](./media/quickstart-linux/iotedge-list-2.png)

Visa meddelanden som skickas från modulen tempSensor:

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
Efter en utloggning och inloggning krävs inte *sudo* för kommandot ovan.

![Visa data från modulen](./media/quickstart-linux/iotedge-logs.png)

Temperatursensormodulen kan vänta på att ansluta till Edge Hub om den sista raden i loggen är `Using transport Mqtt_Tcp_Only`. Avbryt modulen och låt Edge-agenten starta om den. Du kan avsluta den med kommandot `sudo docker stop tempSensor`.

Du kan visa telemetri som enheten skickar med hjälp av [verktyget IoT Hub Explorer][lnk-iothub-explorer] eller [Azure IoT Toolkit-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta vidare med IoT Edge-självstudierna kan du använda den enhet som du registrerade och konfigurerade i den här snabbstarten. Om du vill ta bort installationerna från enheten kan du använda följande kommandon.  

Ta bort IoT Edge-körningen.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Ta bort de behållare som har skapats på enheten. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

Ta bort körmiljön för behållaren.

   ```bash
   sudo apt-get remove --purge moby
   ```

När du inte längre behöver de Azure-resurser som du har skapat kan du ta bort resursgruppen och alla associerade resurser med följande kommando:

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Nästa steg

Den här snabbstarten är en förutsättning för alla andra IoT Edge-självstudier. Du kan fortsätta med någon av de andra självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av en Azure-funktion](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png


<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
