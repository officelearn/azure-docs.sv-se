---
title: "Simulera Azure IoT kanten på Windows | Microsoft Docs"
description: "Installera Azure IoT kant körning på en simulerad enhet i Windows och distribuera din första modulen"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9c9bf2df078bfebb23ad549c7053f12b62fab59b
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Distribuera Azure IoT kanten på en simulerad enhet i Windows - förhandsgranskning

Azure IoT-Edge flyttar kraften i molnet till Sakernas Internet (IoT)-enheter. Den här självstudiekursen vägleder dig genom att skapa en simulerad IoT insticksenhet som genererar sensordata. Lär dig att:

![Självstudiekurs arkitektur][2]

Den simulerade enheten som du skapar i den här kursen är en Övervakare på en vind turbin som genererar temperatur, fuktighet och tryck data. Du är intresserad av den här informationen eftersom din turbiner utföras på olika nivåer av effektivitet beroende på väder. Andra kurser i Azure IoT kant bygger på det arbete som du gör här genom att distribuera moduler som analyserar data för affärsinsikter. 

## <a name="prerequisites"></a>Krav

Den här kursen förutsätter att du använder en dator eller virtuell dator som kör Windows för att simulera en Sakernas Internet-enhet. 

>[!TIP]
>Om du kör Windows på en virtuell dator, aktivera [kapslade virtualisering] [ lnk-nested] och tilldela minst 2 GB minne. 

1. Kontrollera att du använder en Windows-version som stöds:
   * Windows 10 
   * Windows Server
2. Installera [Docker för Windows] [ lnk-docker] och se till att den körs.
3. Installera [Python 2.7 på Windows] [ lnk-python] och kontrollera att du kan använda kommandot pip.
4. Kör följande kommando för att hämta skriptet IoT kant kontroll.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT-kant kan köra Windows behållare eller Linux-behållare. Om du kör något av följande Windows-versioner måste använda du Windows-behållare:
>    * Uppdatering för Windows 10 faller skapare
>    * Windows Server 1709 (Build 16299)
>    * Windows IoT Core (skapa 16299) på en enhet med x64
>
> För Windows IoT Core, följer du instruktionerna i [installera runtime IoT kanten på Windows IoT Core][lnk-install-iotcore]. Annars bara [konfigurera Docker om du vill använda Windows behållare][lnk-docker-containers]. Använd följande kommando för att validera dina krav:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta guiden genom att skapa din IoT-hubb.
![Skapa IoT-hubb][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrera en IoT-enhet

Registrera en IoT-enhet med din nya IoT-hubb.
![Registrera en enhet][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT kant-körning

Installera och starta Azure IoT kant-körningsmiljön på enheten. 
![Registrera en enhet][5]

IoT kant runtime distribueras på alla kant för IoT-enheter. Det består av två moduler. Den **IoT kant agent** underlättar distribution och övervakning av moduler på IoT gränsenheten. Den **kant för IoT-hubb** hanterar kommunikationen mellan moduler på IoT gränsenheten och mellan enheten och IoT-hubb. När du konfigurerar körningsmiljön på den nya enheten startar IoT kant agenten först. Gräns för IoT-hubb kommer senare när du distribuerar en modul. 


Konfigurera körningen med anslutningssträngen IoT kant enheten från föregående avsnitt.

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Starta körningen.

```cmd
iotedgectl start
```

Kontrollera Docker för att se att IoT kant-agenten körs som en modul.

```cmd
docker ps
```

![Se edgeAgent i Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera dina Azure IoT insticksenhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT-hubb.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visa genererade data

I den här självstudiekursen skapas en ny IoT Edge-enhet och installerat IoT kant-körningsmiljön. Sedan använde du Azure-portalen för att skicka en IoT kant-modul som ska köras på enheten utan att behöva göra ändringar i själva enheten. I det här fallet skapar den modul som du pushas miljödata som du kan använda för självstudierna. 

Öppna Kommandotolken på den dator som kör den simulerade enheten igen. Kontrollera att modulen distribueras från molnet körs på enheten IoT kant. 

```cmd
docker ps
```

![Visa tre moduler på enheten](./media/tutorial-simulate-device-windows/docker-ps2.png)

Visa meddelanden som skickas från modulen tempSensor till molnet. 

```cmd
docker logs -f tempSensor
```

![Visa data från en modul](./media/tutorial-simulate-device-windows/docker-logs.png)

Du kan också visa telemetri enheten skickar med hjälp av den [IoT-hubb explorer verktyget][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen, skapas en ny IoT Edge-enhet och används gränssnittet Azure IoT kant molnet för att distribuera kod på enheten. Nu har du en simulerad enhet genererar rådata om sin miljö. 

Den här kursen är förutsättning för alla andra kurser i IoT kant. Du kan fortsätta in på någon av de andra självstudierna Lär dig hur Azure IoT gräns kan du aktivera dessa data till affärsinsikter kant.

> [!div class="nextstepaction"]
> [Utveckla och distribuera C#-kod som en modul](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md