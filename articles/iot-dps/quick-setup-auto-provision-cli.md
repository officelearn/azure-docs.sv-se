---
title: Set up Azure IoT Hub Device Provisioning Service using Azure CLI
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service using Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6406929c3abc3612da2c27edc45e10fd84883d73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228551"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. This quickstart details using the Azure CLI to create an IoT hub and an IoT Hub Device Provisioning Service, and to link the two services together. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Both the IoT hub and the provisioning service you create in this quickstart will be publicly discoverable as DNS endpoints. Undvik känslig information om du vill ändra de namn som används för dessa resurser.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *my-sample-resource-group* på platsen *usavästra*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en resursgrupp på platsen USA, västra. Du kan visa en lista över tillgängliga platser genom att köra kommandot `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Skapa en IoT-hubb med kommandot [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

I följande exempel skapas en IoT-hubb med namnet *my-sample-hub* på platsen *usavästra*. An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Create a Device Provisioning Service

Create a Device Provisioning Service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. 

The following example creates a provisioning service named *my-sample-dps* in the *westus* location. You will also need to choose a globally unique name for your own provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en etableringstjänst på platsen USA, västra. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

Du behöver IoT-hubbens anslutningssträng för att kunna länka den till enhetsetableringstjänsten. Använd kommandot [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) för att hämta anslutningssträngen. Använd sedan dess utdata för att ställa in en variabel som ska användas när du länkar de två resurserna. 

The following example sets the *hubConnectionString* variable to the value of the connection string for the primary key of the hub's *iothubowner* policy (the `--policy-name` parameter can be used to specify a different policy). Trade out *my-sample-hub* for the unique IoT hub name you chose earlier. Kommandot använder Azure CLI-[frågan](/cli/azure/query-azure-cli) och [utdata](/cli/azure/format-output-azure-cli#tsv-output-format)alternativ till att extrahera anslutningssträngen från kommandots utdata.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Du kan använda kommandot `echo` om du vill se anslutningssträngen.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Dessa två kommandon är giltiga för en värd som körs under Bash. Om du använder ett lokalt Windows-/CMD-gränssnitt eller en PowerShell-värd, måste du ändra kommandona till korrekt syntax för den miljön.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Länka IoT-hubben och etableringstjänsten

Länka IoT-hubben och etableringstjänsten med kommandot [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

The following example links an IoT hub named *my-sample-hub* in the *westus* location and a Device Provisioning Service named *my-sample-dps*. Trade out these names for the unique IoT hub and Device Provisioning Service names you chose earlier. The command uses the connection string for your IoT hub that was stored in the *hubConnectionString* variable in the previous step.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

The command may take a few minutes to complete.

## <a name="verify-the-provisioning-service"></a>Kontrollera etableringstjänsten

Hämta information om etableringstjänsten med kommandot [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

I följande exempel hämtas information om en etableringstjänst med namnet *my-sample-dps*. Trade out this name for your own Device Provisioning Service name.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Den länkade IoT-hubben visas i samlingen *properties.iotHubs*.

![Verify Provisioning Service](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte tänker fortsätta kan du använda följande kommandon för att ta bort etableringstjänsten, IoT-hubben eller resursgruppen och alla dess resurser. Replace the names of the resources written below with the names of your own resources.

Ta bort etableringstjänsten genom att köra kommandot [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Ta bort IoT-hubben genom att köra kommandot [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Ta bort en resursgrupp och alla dess resurser genom att köra kommandot [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Nästa steg

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
