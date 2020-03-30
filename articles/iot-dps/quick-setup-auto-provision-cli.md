---
title: Konfigurera Azure IoT Hub Device Provisioning Service med Azure CLI
description: Snabbstart – konfigurera DS (Azure IoT Hub Device Provisioning Service) med Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ea1cae1f5a30d4cd76df39fec43f3818178fc213
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77484204"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Snabbstart: Konfigurera etableringstjänsten för IoT Hub Device med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstartsinformationen med Azure CLI för att skapa en IoT-hubb och en IoT Hub Device Provisioning Service och för att länka de två tjänsterna tillsammans. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!IMPORTANT]
> Både IoT-hubben och etableringstjänsten som du skapar i den här snabbstarten kan identifieras offentligt som DNS-slutpunkter. Undvik känslig information om du vill ändra de namn som används för dessa resurser.
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

I följande exempel skapas en IoT-hubb med namnet *my-sample-hub* på platsen *usavästra*. Ett IoT-hubbnamn måste vara globalt unikt i Azure, så du kanske vill lägga till ett unikt prefix eller suffix i exempelnamnet eller välja ett nytt namn helt och hållet. Kontrollera att ditt namn följer korrekta namngivningskonventioner för en IoT-hubb: det ska vara 3-50 tecken långt och kan bara innehålla alfanumeriska tecken eller bindestreck ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Skapa en enhetsetableringstjänst

Skapa en enhetsetableringstjänst med kommandot [az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) 

I följande exempel skapas en etableringstjänst med namnet *my-sample-dps* på *westus-platsen.* Du måste också välja ett globalt unikt namn för din egen etableringstjänst. Kontrollera att den följer korrekta namngivningskonventioner för en IoT Hub Device Provisioning Service: den ska vara 3-64 tecken lång och kan bara innehålla versaler eller gemener alfanumeriska tecken eller bindestreck ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en etableringstjänst på platsen USA, västra. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett ord- eller flerordsformat. till exempel: westus, västra USA, västra USA, etc. Värdet är inte skiftlägeskänsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

Du behöver IoT-hubbens anslutningssträng för att kunna länka den till enhetsetableringstjänsten. Använd kommandot [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) för att hämta anslutningssträngen. Använd sedan dess utdata för att ställa in en variabel som ska användas när du länkar de två resurserna. 

I följande exempel anges *variabeln hubConnectionString* till värdet för anslutningssträngen för primärnyckeln för `--policy-name` hubbens *iothubowner-princip* (parametern kan användas för att ange en annan princip). Handla ut *min-prov-hubb* för den unika IoT hub namn du valde tidigare. Kommandot använder Azure CLI-[frågan](/cli/azure/query-azure-cli) och [utdata](/cli/azure/format-output-azure-cli#tsv-output-format)alternativ till att extrahera anslutningssträngen från kommandots utdata.

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

I följande exempel länkas en IoT-hubb med namnet *my-sample-hub* på *westus-platsen* och en enhetsetableringstjänst med namnet *my-sample-dps*. Handla ut dessa namn för de unika IoT-hubben och enhetsetableringstjänstnamn som du valde tidigare. Kommandot använder anslutningssträngen för IoT-hubben som lagrades i *variabeln hubConnectionString* i föregående steg.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Kommandot kan ta några minuter att slutföra.

## <a name="verify-the-provisioning-service"></a>Kontrollera etableringstjänsten

Hämta information om etableringstjänsten med kommandot [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

I följande exempel hämtas information om en etableringstjänst med namnet *my-sample-dps*. Handla det här namnet mot ditt eget enhetsetableringstjänstnamn.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Den länkade IoT-hubben visas i samlingen *properties.iotHubs*.

![Verifiera etableringstjänsten](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte tänker fortsätta kan du använda följande kommandon för att ta bort etableringstjänsten, IoT-hubben eller resursgruppen och alla dess resurser. Ersätt namnen på de resurser som skrivs nedan med namnen på dina egna resurser.

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

I den här snabbstarten har du distribuerat en IoT-hubb och en enhetsetableringstjänstinstans och länkat de två resurserna. Om du vill veta hur du använder den här inställningen för att etablera en simulerad enhet fortsätter du till snabbstarten för att skapa en simulerad enhet.

> [!div class="nextstepaction"]
> [Snabbstart för att skapa en simulerad enhet](./quick-create-simulated-device.md)
