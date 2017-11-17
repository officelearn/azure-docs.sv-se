---
title: Snabbstart Azure IoT kant + Windows | Microsoft Docs
description: "Testa Azure IoT kant genom att köra analytics på en simulerad edge-enhet"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e232b4cdb62b7bf212808bd380119482ee88b077
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Snabbstart: Distribuera din första IoT kant-modul från Azure portal till en windowsenhet - förhandsgranskning

I den här snabbstarten att använda Azure IoT kant molnet gränssnittet för att distribuera färdiga kod via en fjärranslutning till en IoT-enhet. Om du vill utföra den här uppgiften först använda din Windows-enhet för att simulera en IoT-enhet, kan sedan du distribuera en modul till den.

Om du inte har en aktiv Azure-prenumeration kan du skapa en [kostnadsfritt konto] [ lnk-account] innan du börjar.

## <a name="prerequisites"></a>Krav

Den här kursen förutsätter att du använder en dator eller virtuell dator som kör Windows för att simulera en Sakernas Internet-enhet. Om du kör Windows på en virtuell dator, aktivera [kapslade virtualisering] [ lnk-nested] och tilldela minst 2 GB minne. 

1. Kontrollera att du använder en Windows-version som stöds:
   * Windows 10 
   * Windows Server
2. Installera [Docker för Windows] [ lnk-docker] och se till att den körs.
3. Installera [Python 2.7 på Windows] [ lnk-python] och kontrollera att du kan använda kommandot pip.
4. Kör följande kommando för att hämta skriptet IoT kant kontroll.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT-kant kan köra Windows behållare eller Linux-behållare. Du måste köra för att använda Windows-behållare:
>    * Uppdatering för Windows 10 faller skapare, eller
>    * Windows Server 1709 (Build 16299), eller
>    * Windows IoT Core (skapa 16299) på en enhet med x64
>
> För Windows IoT kärnor, följer du instruktionerna i [installera IoT kant-körningsmiljön på Windows IoT Core] [lnk-installation-iotcore]. Annars bara [konfigurera Docker om du vill använda Windows behållare][lnk-docker-containers], och du kan också kontrollera dina krav med följande powershell-kommando:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Skapa en IoT-hubb med Azure CLI

Skapa en IoT-hubb i din Azure-prenumeration. Kostnadsfria nivån för IoT-hubb fungerar för denna Snabbstart. Om du har använt tidigare IoT-hubb och redan har en kostnadsfri hubb skapas, hoppa över det här avsnittet och gå till [registrera en insticksenhet för IoT][anchor-register]. Varje prenumeration kan bara ha en kostnadsfri IoT-hubb. 

1. Logga in på [Azure Portal][lnk-portal]. 
1. Välj den **moln Shell** knappen. 

   ![Molnet Shell-knappen][1]

1. Skapa en resursgrupp. Följande kod skapar en resursgrupp med namnet **IoTEdge** i den **västra USA** region:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Skapa en IoT-hubb i din nya resursgrupp. Följande kod skapar ett kostnadsfritt **F1** hubb kallas **MyIotHub** i resursgruppen **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrera en IoT-enhet

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT kant-körning

IoT kant runtime distribueras på alla kant för IoT-enheter. Det består av två moduler. Först underlättar IoT kant agenten distribution och övervakning av moduler på IoT gränsenheten. Gräns för IoT-hubb hanterar andra, kommunikation mellan moduler på IoT gränsenheten och mellan enheten och IoT-hubb. 

Konfigurera körningen med anslutningssträngen IoT kant enheten från föregående avsnitt.

```
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Starta körningen.

```
iotedgectl start
```

Kontrollera Docker för att se att IoT kant-agenten körs som en modul.

```
docker ps
```

![Se edgeAgent i Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Distribuera en modul

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I Snabbstart, skapas en ny IoT Edge-enhet och installerat IoT kant-körningsmiljön. Sedan använde du Azure-portalen för att skicka en IoT kant-modul som ska köras på enheten utan att behöva göra ändringar i själva enheten. I det här fallet skapar den modul som du pushas miljödata som du kan använda för självstudierna. 

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
## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver IoT-hubb som du har skapat kan du använda den [az iot-hubb delete] [ lnk-delete] för att ta bort resursen och alla enheter som är kopplade till den:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en IoT-Edge-modul till en IoT-enhet. Prova nu distribuera olika typer av Azure-tjänster i moduler, så att du kan analysera data vid gränsen. 

* [Distribuera Azure-funktion som en modul](tutorial-deploy-function.md)
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

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
