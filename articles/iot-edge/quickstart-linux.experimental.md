---
title: Simulera Azure IoT Edge på Linux | Microsoft Docs
description: I den här snabbstarten lär du dig hur du fjärrdistribuerar färdig kod till en IoT Edge-enhet.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0e0d22b3363b00c81be5091fd12773f9e486c09e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099193"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Snabbstart: Distribuera din första IoT Edge-modul till en Linux x64-enhet

Med Azure IoT Edge kan du utföra analyser och databearbetning på dina enheter utan att behöva push-överföra alla data till molnet. I självstudierna för IoT Edge visar vi hur du distribuerar olika typer av moduler, men du måste först ha en enhet som ska testas. 

I den här snabbstarten lär du dig att:

1. Skapa en IoT Hub.
2. Registrera en IoT Edge-enhet till din IoT Hub.
3. Starta IoT Edge-körningen.
4. Fjärrdistribuera en modul till en IoT Edge-enhet.

![Självstudiens arkitektur][2]

Denna snabbstart gör din Linux-dator eller virtuella dator till en IoT Edge-enhet. Du kan sedan distribuera en modul från Azure Portal till din enhet. Modulen som du distribuerar i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar simulerade data för verksamhetsinsyn. 

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto][Ink-konto] innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa din IoT Hub i Azure Portal.
![Skapa IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Registrera en enhet][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera och starta Azure IoT Edge-körningen på enheten. 
![Registrera en enhet][5]

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. **IoT Edge säkerhetsdaemon** startas varje gång en Edge-enhet startar. Enheten startas genom att IoT Edge-agenten startas. **IoT Edge-agenten** underlättar distribution och övervakning av moduler på IoT Edge-enheten, inklusive IoT Edge-hubb. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub. 

### <a name="register-your-device-to-use-the-software-repository"></a>Registrera din enhet för att använda programvarudatabasen

De paket som du behöver för IoT Edge-körningen hanteras i en programvarudatabas. Konfigurera din IoT Edge-enhet för att få åtkomst till den här databasen. 

Stegen i det här avsnittet gäller enheter som kör **Ubuntu 16.04**. För att komma åt programvarudatabasen i andra versioner av Linux kan du läsa [Installera Azure IoT Edge-körning på Linux (x64)](how-to-install-iot-edge-linux.md) eller [Installera Azure IoT Edge-körning på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

1. Installera databaskonfigurationen på den dator du använder som IoT Edge-enhet.

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

   ![Kontrollera att Edge Daemon körs som en systemtjänst](./media/quickstart-linux/iotedged-running.png)

   Du kan också visa loggar från Edge säkerhetsdaemon genom att köra följande kommando:

   ```bash
   journalctl -u iotedge
   ```

6. Visa de moduler som körs på din enhet: 

   ```bash
   sudo iotedge list
   ```
Efter en utloggning och inloggning krävs inte *sudo* för kommandot ovan.

   ![Visa en modul på din enhet](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT Hub.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure Portal för att skicka en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten. I det här fallet skapar modulen som du överförde de miljödata som du använder i självstudierna. 

Öppna kommandotolken igen på den dator som kör den simulerade enheten. Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten:

   ```bash
   sudo iotedge list
   ```
Efter en utloggning och inloggning krävs inte *sudo* för kommandot ovan.

   ![Visa tre moduler på enheten](./media/quickstart-linux/iotedge-list-2.png)

Visa meddelanden som skickas från modulen tempSensor:

  ```bash
   sudo iotedge logs tempSensor -f 
   ```

Efter en utloggning och inloggning krävs inte *sudo* för kommandot ovan.

![Visa data från modulen](./media/quickstart-linux/iotedge-logs.png)

Temperatursensormodulen kanske väntar på att ansluta till Edge Hub om den sista raden i loggen är `Using transport Mqtt_Tcp_Only`. Avbryt modulen och låt Edge-agenten starta om den. Du kan avsluta den med kommandot `sudo docker stop tempSensor`.

Du kan visa telemetri som enheten skickar med hjälp av [verktyget IoT Hub Explorer][lnk-iothub-explorer] eller [Azure IoT Toolkit-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med IoT Edge-självstudierna kan du använda enheten du registrerade och konfigurerade i den här snabbstarten. Om du vill ta bort installationerna från din enhet kan du använda följande kommandon.  

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

När du inte längre behöver den Azure IoT Hub- eller IoT Edge-enhet du skapade i den här snabbstarten, kan du ta bort dem i Azure Portal. Gå till översiktssidan för din IoT Hub och välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny IoT Edge-enhet och du använde molngränssnittet i Azure IoT Edge för att distribuera kod på enheten. Nu har du en simulerad enhet som genererar rådata om sin miljö. 

Den här snabbstarten är en förutsättning för alla andra IoT Edge-självstudier. Du kan fortsätta med någon av självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av en Azure-funktion](tutorial-deploy-function.md)


<!-- Images -->
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
