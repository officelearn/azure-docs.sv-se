---
title: Simulera Azure IoT Edge i Windows | Microsoft Docs
description: Installera Azure IoT Edge-körningen på en simulerad enhet i Windows och distribuera din första modul
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7ad99a49a578de4997a2d76d48da33aba6847f3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631198"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Distribuera Azure IoT Edge på en simulerad enhet i Windows – förhandsversion

Med Azure IoT Edge kan du utföra analyser och databearbetning på dina enheter utan att behöva push-överföra alla data till molnet. I självstudierna för IoT Edge visar vi hur du distribuerar olika typer av moduler som skapats från Azure-tjänster eller anpassad kod, men du måste först ha en enhet som ska testas. 

I den här självstudiekursen får du lära du dig att:

1. Skapa en IoT Hub
2. Registrera en IoT Edge-enhet
3. Starta IoT Edge-körningen
4. Distribuera en modul

![Självstudiens arkitektur][2]

Den simulerade enhet som du skapar i den här självstudien är en övervakare för en vindturbin som genererar temperatur-, fuktighets- och lufttrycksdata. Du är intresserad av den här informationen eftersom dina turbiner körs med olika effektivitetsnivåer beroende på väderförhållanden. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar datan för verksamhetsinsyn. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att du använder en dator eller en virtuell dator som kör Windows när du simulerar en ”Sakernas Internet-enhet. 

>[!TIP]
>Om du kör Windows på en virtuell dator aktiverar du [kapslad virtualisering][lnk-nested] och allokerar minst 2 GB minne. 

1. Kontrollera att du använder en Windows-version som stöds:
   * Windows 10 
   * Windows Server
2. Installera [Docker för Windows][lnk-docker] och kontrollera att den körs.
3. Installera [Python i Windows][lnk-python] och kontrollera att du kan använda pip-kommandot. Den här självstudien har testats med Python-versionerna >=2.7.9 och >=3.5.4.  
4. Kör följande kommando för att ladda ned skriptet för IoT Edge-kontroll.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge kan köra Windows-behållare eller Linux-behållare. Du kan använda Windows-behållare om du kör någon av följande Windows-versioner:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (Build 16299)
>    * Windows IoT Core (Build 16299) på en x64-baserad enhet
>
> För Windows IoT Core följer du anvisningarna i [Installera IoT Edge-körning på Windows IoT Core][lnk-install-iotcore]. Annars [konfigurerar du enbart Docker till att använda Windows-behållare][lnk-docker-containers]. Använd följande kommando för att verifiera dina krav:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta självstudien genom att skapa din IoT Hub.
![Skapa IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Registrera en enhet][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT Edge-körningen

Installera och starta Azure IoT Edge-körningen på enheten. 
![Registrera en enhet][5]

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den består av två moduler. **IoT Edge-agenten** underlättar distribution och övervakning av moduler på IoT Edge-enheten. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub. När du konfigurerar körningen på den nya enheten, är det bara IoT Edge-agenten som startar till att börja med. IoT Edge-hubben startas senare när du distribuerar en modul. 


Konfigurera körningen med anslutningssträngen för IoT Edge-enheten från föregående avsnitt.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Starta körningen.

```cmd
iotedgectl start
```

Kontrollera Docker för att se att IoT Edge-agenten körs som en modul.

```cmd
docker ps
```

![Se edgeAgent i Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT Hub.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visa genererade data

I den här självstudien skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningen på den. Sedan använde du Azure Portal till att push-överföra en IoT Edge-modul som ska köras på enheten, utan att du behövde göra några ändringar på själva enheten. I det här fallet skapar modulen som du överförde de miljödata som du använder i självstudierna. 

Öppna kommandotolken igen på den dator som kör den simulerade enheten. Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten. 

```cmd
docker ps
```

![Visa tre moduler på enheten](./media/tutorial-simulate-device-windows/docker-ps2.png)

Visa meddelanden som skickas från modulen tempSensor till molnet. 

```cmd
docker logs -f tempSensor
```

![Visa data från modulen](./media/tutorial-simulate-device-windows/docker-logs.png)

Du kan också visa den telemetri som enheten skickar med hjälp av [IoT Hubs utforskarverktyg][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en ny IoT Edge-enhet och du använde molngränssnittet i Azure IoT Edge till att distribuera kod på enheten. Nu har du en simulerad enhet som genererar rådata om sin miljö. 

Den här självstudien är förutsättning för alla andra IoT Edge-självstudier. Du kan fortsätta med någon av de andra självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

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