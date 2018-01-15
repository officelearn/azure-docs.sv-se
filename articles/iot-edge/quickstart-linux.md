---
title: Snabbstart Azure IoT kant + Linux | Microsoft Docs
description: "Testa Azure IoT kant genom att köra analytics på en simulerad edge-enhet"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 440b70f4d04728973d77e54e7f6303e1ad7fcd89
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-or-mac-device---preview"></a>Snabbstart: Distribuera din första IoT kant-modulen till en enhet med Linux- eller Mac - Förhandsgranska

Azure IoT-Edge flyttar kraften i molnet till Internet of Things-enheter. I det här avsnittet lär dig att använda molnet gränssnittet för att distribuera färdiga kod via en fjärranslutning till en IoT-enhet.

Om du inte har en aktiv Azure-prenumeration kan du skapa en [kostnadsfritt konto] [ lnk-account] innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Denna Snabbstart använder din dator eller virtuell dator som en Sakernas Internet-enhet. Om du vill göra din dator till en IoT-enhet, krävs följande tjänster:

* Python pip att installera IoT kant-körningsmiljön.
   * Linux: `sudo apt-get install python-pip`.
   * MacOS: `sudo easy_install pip`.
* Docker att köra IoT-Edge moduler
   * [Installera Docker för Linux] [ lnk-docker-ubuntu] och se till att den körs. 
   * [Installera Docker för Mac] [ lnk-docker-mac] och se till att den körs. 

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

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT-hubb. Eftersom IoT-gränsenheterna fungerar och kan hanteras annorlunda än typisk IoT-enheter, kan du ange detta ska vara en insticksenhet för IoT från början. 

1. Navigera till din IoT-hubb i Azure-portalen.
1. Välj **IoT kant (förhandsgranskning)**.
1. Välj **lägga till IoT gränsenheten**.
1. Ge den simulerade enheten ett unikt enhets-ID.
1. Välj **spara** lägga till enheten.
1. Välj den nya enheten i listan över enheter. 
1. Kopiera värdet för **anslutningssträngen--primärnyckel** och spara den. Så här konfigurerar du IoT kant-körningsmiljön i nästa avsnitt ska du använda det här värdet. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT kant-körning

IoT kant runtime distribueras på alla kant för IoT-enheter. Det består av två moduler. Först underlättar IoT kant agenten distribution och övervakning av moduler på IoT gränsenheten. Gräns för IoT-hubb hanterar andra, kommunikation mellan moduler på IoT gränsenheten och mellan enheten och IoT-hubb. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I Snabbstart, skapas en ny IoT Edge-enhet och installerat IoT kant-körningsmiljön. Sedan använde du Azure-portalen för att skicka en IoT kant-modul som ska köras på enheten utan att behöva göra ändringar i själva enheten. I det här fallet skapar den modul som du pushas miljödata som du kan använda för självstudierna. 

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

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver IoT-hubb som du har skapat kan du använda den [az iot-hubb delete] [ lnk-delete] för att ta bort resursen och alla enheter som är kopplade till den:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en IoT-Edge-modul till en IoT-enhet. Prova nu distribuera olika typer av Azure-tjänster i moduler, så att du kan analysera data vid gränsen. 

* [Distribuera din egen kod som en modul](tutorial-csharp-module.md)
* [Distribuera Azure-funktion som en modul](tutorial-deploy-function.md)
* [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
