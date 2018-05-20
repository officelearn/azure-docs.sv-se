---
title: Simulera Azure IoT kanten på Linux | Microsoft Docs
description: Installera Azure IoT kant körning på en simulerad enhet i Linux och distribuera din första modulen
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b7f0aa514cfffb8fb2ca76238a6a65a8e6443e56
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Distribuera Azure IoT kanten på en simulerad enhet i Linux eller MacOS - förhandsgranskning

Azure IoT-gräns kan du utföra analyser och databehandling på dina enheter utan att behöva skicka alla data till molnet. IoT kant självstudiekurser visar hur du distribuerar olika typer av moduler som skapats från Azure-tjänster eller anpassad kod, men du måste först en enhet för att testa. 

I den här självstudiekursen får du lära du dig att:

1. Skapa en IoT-hubb
2. Registrera en IoT-enhet
3. Starta IoT kant-körning
4. Distribuera en modul

![Självstudiekurs arkitektur][2]

Den simulerade enheten som du skapar i den här kursen är en Övervakare som genererar temperatur, fuktighet och tryck data. Andra kurser i Azure IoT kant bygger på det arbete som du gör här genom att distribuera moduler som analyserar data för affärsinsikter. 

## <a name="prerequisites"></a>Förutsättningar

Den här kursen använder din dator eller virtuell dator som en Sakernas Internet-enhet. Om du vill göra din dator till en IoT-enhet, krävs följande tjänster:

* Python pip att installera IoT kant-körningsmiljön.
   * Linux: `sudo apt-get install python-pip`.
     * _Observera att på vissa distributioner (t.ex. Raspbian), måste du kanske också vill uppgradera vissa pip-paket och installera ytterligare beroenden:_
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Docker att köra IoT-Edge moduler
   * [Installera Docker för Linux] [ lnk-docker-ubuntu] och se till att den körs. 
   * [Installera Docker för Mac] [ lnk-docker-mac] och se till att den körs. 

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta guiden genom att skapa din IoT-hubb.
![Skapa IoT-hubb][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrera en IoT-enhet

Registrera en IoT-enhet med din nya IoT-hubb.
![Registrera en enhet][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT kant-körning

Installera och starta Azure IoT kant-körningsmiljön på enheten. 
![Registrera en enhet][5]

IoT kant runtime distribueras på alla kant för IoT-enheter. Det består av två moduler. Den **IoT kant agent** underlättar distribution och övervakning av moduler på IoT gränsenheten. Den **kant för IoT-hubb** hanterar kommunikationen mellan moduler på IoT gränsenheten och mellan enheten och IoT-hubb. När du konfigurerar körningsmiljön på den nya enheten startar IoT kant agenten först. Gräns för IoT-hubb kommer senare när du distribuerar en modul. 

Hämta skriptet IoT kant kontroll på datorn där du kör IoT gränsenheten:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Konfigurera körningen med anslutningssträngen IoT kant enheten från föregående avsnitt:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Starta körningen:
```cmd
sudo iotedgectl start
```

Kontrollera Docker för att se att IoT kant-agenten körs som en modul:
```cmd
sudo docker ps
```

![Se edgeAgent i Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera dina Azure IoT insticksenhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT-hubb.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här självstudiekursen skapas en ny IoT Edge-enhet och installerat IoT kant-körningsmiljön. Sedan använde du Azure-portalen för att skicka en IoT kant-modul som ska köras på enheten utan att behöva göra ändringar i själva enheten. I det här fallet skapar den modul som du pushas miljödata som du kan använda för självstudierna. 

Öppna Kommandotolken på den dator som kör den simulerade enheten igen. Kontrollera att modulen distribueras från molnet körs på enheten IoT kant:

```cmd
sudo docker ps
```

![Visa tre moduler på enheten](./media/tutorial-simulate-device-linux/docker-ps2.png)

Visa meddelanden som skickas från modulen tempSensor till molnet:

```cmd
sudo docker logs -f tempSensor
```

![Visa data från en modul](./media/tutorial-simulate-device-linux/docker-logs.png)

Du kan också visa telemetri enheten skickar med hjälp av den [IoT-hubb explorer verktyget][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen, skapas en ny IoT Edge-enhet och används gränssnittet Azure IoT kant molnet för att distribuera kod på enheten. Nu har du en simulerad enhet genererar rådata om sin miljö. 

Den här kursen är förutsättning för alla andra kurser i IoT kant. Du kan fortsätta in på någon av de andra självstudierna Lär dig hur Azure IoT gräns kan du aktivera dessa data till affärsinsikter kant.

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
