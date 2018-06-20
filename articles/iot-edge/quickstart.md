---
title: Snabbstart Azure IoT Edge + Windows | Microsoft Docs
description: Testa Azure IoT Edge genom att köra analys på en simulerad Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2fd16ab4ade61b1a08f93294051f4246e47839b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631742"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Snabbstart: Distribuera din första IoT Edge-modul från Azure Portal till en Windows.enhet – förhandsgranskning

Använd Azure IoT Edge-molnsgränssnittet när du ska fjärrdistribuera färdig kod till en IoT-enhet i den här snabbstarten. Utför den här uppgiften genom att först använda din Windows-enhet för att simulera en IoT Edge-enhet, varefter du kan distribuera en modul till den.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto][lnk-account] innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att du använder en dator eller virtuell dator som kör Windows för att simulera en Sakernas Internet-enhet. Om du kör Windows på en virtuell dator, aktiverar du [kapslad virtualisering][lnk-nested] och tilldelar minst 2 GB minne. 

1. Kontrollera att du använder en Windows-version som stöds:
   * Windows 10 
   * Windows Server
2. [Installera Docker för Windows][lnk-docker] och kontrollera att den körs.
3. Installera [Python på Windows][lnk-python] och kontrollera att du kan använda kommandot pip. Den här snabbstarten har testats med Python-versionerna >=2.7.9 och >=3.5.4.  
4. Hämta skriptet för IoT Edge-kontroll genom att köra följande kommando.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge kan köra Windows-behållare eller Linux-behållare. Om du vill använda Windows-behållare måste du köra:
>    * Windows 10 Fall Creators Update eller
>    * Windows Server 1709 (Build 16299) eller
>    * Windows IoT Core (Build 16299) på en x64-baserad enhet
>
> När det gäller Windows IoT Core följer du anvisningarna i [Installera IoT Edge-körning på Windows IoT Core][lnk-install-iotcore]. I annat fall [konfigurerar du bara Docker för användning av Windows behållare][lnk-docker-containers] och kan välja att verifiera kraven med följande PowerShell-kommando:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Skapa en IoT Hub med Azure CLI

Skapa en IoT Hub i din Azure-prenumeration. Den kostnadsfria nivån för IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en kostnadsfri hubb kan du hoppa över det här avsnittet och gå till [Registrera en IoT Edge-enhet][anchor-register]. Varje prenumeration kan bara ha en kostnadsfri IoT Hub. 

1. Logga in på [Azure Portal][lnk-portal]. 
1. Välj **Cloud Shell**-knappen. 

   ![Cloud Shell-knapp][1]

1. Skapa en resursgrupp. I följande exempel skapas en resursgrupp med namnet **IoTEdge** i regionen **västra USA**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Skapa en IoT Hub i din nya resursgrupp. Följande kod skapar en kostnadsfri **F1**-hubb med namnet **MyIotHub** i resursgruppen **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Det består av två moduler. För det första underlättar IoT Edge-agenten distribution och övervakning av moduler på IoT Edge-enheten. För det andra hanterar IoT Edge-hubben kommunikationen mellan moduler på IoT Edge-enheten och mellan enheten och IoT Hub. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure Portal för att skicka en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten. I det här fallet skapar den modul som du pushade miljödata som du kan använda för självstudierna. 

Öppna kommandotolken på den dator som kör den simulerade enheten igen. Kontrollera att modulen distribueras från molnet som körs på IoT Edge-enheten. 

```cmd
docker ps
```

![Visa tre moduler på enheten](./media/tutorial-simulate-device-windows/docker-ps2.png)

Visa meddelanden som skickas från modulen tempSensor till molnet. 

```cmd
docker logs -f tempSensor
```

![Visa data från modulen](./media/tutorial-simulate-device-windows/docker-logs.png)

Du kan också visa telemetri som enheten skickar med [IoT Hub-utforskarverktyget][lnk-iothub-explorer]. 
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den simulerade enheten som du skapade, tillsammans med de Docker-behållare som har startats för varje modul, använder du följande kommando: 

```cmd
iotedgectl uninstall
```

När du inte längre behöver den IoT Hub som du skapade kan du använda kommandot [az iot-hubb delete][lnk-delete] för att ta bort resursen och eventuella enheter som är kopplade till den:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en IoT Edge-modul till en IoT Edge-enhet. Testa nu att distribuera olika typer av Azure-tjänster som moduler, så att du kan analysera data vid gränsen. 

* [Distribuera Azure Function som en modul](tutorial-deploy-function.md)
* [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)
* [Distribuera din egen kod som en modul](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
