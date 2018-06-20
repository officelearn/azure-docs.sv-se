---
title: Simulera Azure IoT Edge på Linux | Microsoft Docs
description: Installera Azure IoT Edge-körning på en simulerad enhet i Linux och distribuera din första modul
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0b8b2658af9173cea6a7cdcb0147c7b0dc13a455
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631004"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Distribuera Azure IoT Edge på en simulerad enhet i Linux eller MacOS – förhandsversion

Med Azure IoT Edge kan du utföra analyser och databearbetning på dina enheter utan att behöva push-överföra alla data till molnet. I självstudierna för IoT Edge visar vi hur du distribuerar olika typer av moduler som skapats från Azure-tjänster eller anpassad kod, men du måste först ha en enhet som ska testas. 

I den här självstudiekursen får du lära du dig att:

1. Skapa en IoT Hub
2. Registrera en IoT Edge-enhet
3. Starta IoT Edge-körningen
4. Distribuera en modul

![Självstudiens arkitektur][2]

Den simulerade enheten som du skapar i den här självstudien är en övervakare som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar datan för verksamhetsinsyn. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien använder din dator eller en virtuell dator som en ”Sakernas Internet”-enhet. Om du vill omvandla din dator till en IoT Edge-enhet måste du ha följande tjänster:

* Python-pip som installerar IoT Edge-körningen.
   * Linux: `sudo apt-get install python-pip`.
     * _Observera att i vissa distributioner (t.ex. Raspbian) kan du också behöva uppgradera vissa pip-paket och installera fler beroenden:_
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Docker, för att köra IoT Edge-modulerna
   * [Installera Docker för Linux][lnk-docker-ubuntu] och kontrollera att den körs. 
   * [Installera Docker för Mac][lnk-docker-mac] och kontrollera att den körs. 

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta självstudien genom att skapa din IoT Hub.
![Skapa IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Registrera en enhet][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera och starta Azure IoT Edge-körningen på enheten. 
![Registrera en enhet][5]

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den består av två moduler. **IoT Edge-agenten** underlättar distribution och övervakning av moduler på IoT Edge-enheten. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub. När du konfigurerar körningen på den nya enheten, är det bara IoT Edge-agenten som startar till att börja med. IoT Edge-hubben startas senare när du distribuerar en modul. 

Hämta skriptet för IoT Edge-kontroll på datorn där du kommer att köra IoT Edge-enheten:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Konfigurera körningen med anslutningssträngen för IoT Edge-enheten från föregående avsnitt:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Starta körningen:
```cmd
sudo iotedgectl start
```

Kontrollera Docker för att se att IoT Edge-agenten körs som en modul:
```cmd
sudo docker ps
```

![Se edgeAgent i Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT Hub.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här självstudien skapade du en ny IoT Edge-enhet och installerade IoT Edge-körning på den. Sedan använde du Azure Portal till att push-överföra en IoT Edge-modul som ska köras på enheten, utan att du behövde göra några ändringar på själva enheten. I det här fallet skapar modulen som du överförde de miljödata som du använder i självstudierna. 

Öppna kommandotolken igen på den dator som kör den simulerade enheten. Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten:

```cmd
sudo docker ps
```

![Visa tre moduler på enheten](./media/tutorial-simulate-device-linux/docker-ps2.png)

Visa meddelanden som skickas från modulen tempSensor till molnet:

```cmd
sudo docker logs -f tempSensor
```

![Visa data från modulen](./media/tutorial-simulate-device-linux/docker-logs.png)

Du kan också visa den telemetri som enheten skickar med hjälp av [IoT Hubs utforskarverktyg][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en ny IoT Edge-enhet och du använde molngränssnittet i Azure IoT Edge till att distribuera kod på enheten. Nu har du en simulerad enhet som genererar rådata om sin miljö. 

Den här självstudien är förutsättning för alla andra IoT Edge-självstudier. Du kan fortsätta med någon av de andra självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Utveckla och distribuera C#-kod som en modul](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
