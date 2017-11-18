---
title: "Simulera Azure IoT kanten på Linux | Microsoft Docs"
description: "Installera Azure IoT kant körning på en simulerad enhet i Linux och distribuera din första modulen"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 11353ef93455a47f9f1c252fc5e192c111d87dd7
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Distribuera Azure IoT kanten på en simulerad enhet i Linux - förhandsgranskning

Azure IoT-gräns kan du utföra analyser och databehandling på dina enheter utan att behöva skicka alla data till molnet. IoT kant självstudiekurser visar hur du distribuerar olika typer av moduler som skapats från Azure-tjänster eller anpassad kod, men du måste först en enhet för att testa. 

Den här självstudiekursen vägleder dig genom att skapa en simulerad IoT insticksenhet, distribuera en modul som genererar sensordata. Lär dig att:

![Självstudiekurs arkitektur][2]

Den simulerade enheten som du skapar i den här kursen är en Övervakare som genererar temperatur, fuktighet och tryck data. Andra kurser i Azure IoT kant bygger på det arbete som du gör här genom att distribuera moduler som analyserar data för affärsinsikter. 

## <a name="prerequisites"></a>Krav

Den här kursen förutsätter att du använder en dator eller virtuell dator som kör Linux för att simulera en Sakernas Internet-enhet. Följande tjänster krävs för att distribuera en IoT-enhet:

- [Installera Docker för Linux] [ lnk-docker-ubuntu] och se till att den körs. 
- De flesta Linux-distributioner, inklusive Ubuntu, har redan Python 2.7 installerad. Använder du följande kommando för att se till att pip är installerad: `sudo apt-get install python-pip`.

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
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
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
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
