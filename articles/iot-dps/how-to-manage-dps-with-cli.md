---
title: Hantera IoT Hub Device Provisioning Service med Azure CLI & IoT-tillägget
description: Lär dig hur du använder Azure CLI och IoT-tillägget för att hantera IoT Hub Device Provisioning Service (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: 020b0dbc937885923b26c115f91147437b7a0f9b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964736"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Så här använder du Azure CLI och IoT-tillägget för att hantera IoT Hub Device Provisioning Service

[Azure CLI](/cli/azure?view=azure-cli-latest) är ett kommando rads verktyg med öppen källkod, plattforms oberoende för hantering av Azure-resurser som IoT Edge. Azure CLI är tillgängligt på Windows, Linux och macOS. Med Azure CLI kan du hantera Azure IoT Hub-resurser, enhets etablerings tjänst instanser och länkade nav i rutan.

IoT-tillägget berikar Azure CLI med funktioner som enhets hantering och fullständig IoT Edge funktion.

I den här självstudien utför du först stegen för att konfigurera Azure CLI och IoT-tillägget. Sedan får du lära dig hur du kör CLI-kommandon för att utföra grundläggande åtgärder för enhets etablering. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Installation 

### <a name="install-python"></a>Installera Python

[Python 2.7x eller Python 3.x](https://www.python.org/downloads/) krävs.

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ [installations anvisningarna](/cli/azure/install-azure-cli?view=azure-cli-latest) för att konfigurera Azure CLI i din miljö. Som minst måste din Azure CLI-version vara 2.0.70 eller högre. Validera med `az –version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. Ett enkelt sätt att installera i Windows är att hämta och installera [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Installera IoT-tillägg

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-iot` för att se information om IoT-tillägget. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Grundläggande åtgärder för enhets etablerings tjänsten

Exemplet visar hur du loggar in på ditt Azure-konto, skapar en Azure-resurs grupp (en behållare som innehåller relaterade resurser för en Azure-lösning), skapar en IoT Hub, skapar en enhets etablerings tjänst, listar befintliga enhets etablerings tjänster och skapar en länkad IoT Hub med CLI-kommandon. 

Slutför installationsstegen som beskrivs ovan innan du börjar. Om du inte har ett Azure-konto än kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) nu. 


### <a name="1-log-in-to-the-azure-account"></a>1. Logga in på Azure-kontot
  
```azurecli
az login
```

![Skärm bild som visar ett kommando tolks fönster som kör kommandot AZ login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. skapa en resurs grupp IoTHubBlogDemo i öster

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Skapa resursgrupp](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. skapa två enhets etablerings tjänster

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Skapa enhets etablerings tjänst](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. lista alla befintliga enhets etablerings tjänster under den här resurs gruppen

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Visa lista med enhets etablerings tjänster](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. skapa en IoT Hub-blogDemoHub under den nyligen skapade resurs gruppen

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![Skapa IoT Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. länka en befintlig IoT Hub till en enhets etablerings tjänst

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Länka hubb](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa självstudiekurs för att lära dig att etablera flera enheter över belastningsutjämnade hubbar. 

> [!div class="nextstepaction"]
> [Etablera enheter till flera belastningsutjämnade IoT-hubbar](./tutorial-provision-multiple-hubs.md)