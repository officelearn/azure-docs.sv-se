---
title: Skapa en Azure IoT Hub med hjälp av en mall (PowerShell) | Microsoft Docs
description: Hur du använder en Azure Resource Manager-mall för att skapa en IoT Hub med PowerShell.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0b033121890ea8c347642f64922113505f39efc9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436543"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Skapa en IoT hub med Azure Resource Manager-mall (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda Azure Resource Manager för att skapa och hantera Azure-IoT-hubbar programmässigt. Den här självstudien visar hur du använder en Azure Resource Manager-mall för att skapa en IoT hub med PowerShell.

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver distributionsmodellen Azure Resource Manager.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure PowerShell 1.0] [ lnk-powershell-install] eller senare.

> [!TIP]
> Artikeln [med hjälp av Azure PowerShell med Azure Resource Manager] [ lnk-powershell-arm] innehåller mer information om hur du använder PowerShell och Azure Resource Manager-mallar för att skapa Azure-resurser.

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Ange följande kommando för att logga in på Azure-prenumerationen i en PowerShell-Kommandotolken:

```powershell
Connect-AzureRmAccount
```

Om du har flera Azure-prenumerationer kan får logga in på Azure du åtkomst till alla Azure-prenumerationer som är associerade med dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

```powershell
Get-AzureRMSubscription
```

Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

Du kan använda följande kommandon för att identifiera där du kan distribuera en IoT-hubb och de API-versionerna som stöds för tillfället:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Skapa en resursgrupp som innehåller din IoT-hubb med följande kommando i en av platserna som stöds för IoT Hub. Det här exemplet skapas en resursgrupp med namnet **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Skicka in en mall för att skapa en IoT-hubb

Använd en JSON-mall för att skapa en IoT-hubb i resursgruppen. Du kan också använda en Azure Resource Manager-mall för att göra ändringar i en befintlig IoT-hubb.

1. Använd en textredigerare för att skapa en Azure Resource Manager-mall som kallas **template.json** med följande resursdefinition att skapa en ny standard IoT hub. Det här exemplet lägger till IoT Hub i den **USA, östra** region, skapar två konsumentgrupper (**cg1** och **cg2**) i Event Hub-kompatibla slutpunkten och använder den  **2016-02-03** API-versionen. Den här mallen förväntar dig att skicka in namnet för IoT-hubb som en parameter kallas **hubName**. Den aktuella listan över platser som har stöd för IoT Hub finns [Azure-Status][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Spara mallfilen Azure Resource Manager på den lokala datorn. Det här exemplet förutsätts att du sparar den i en mapp med namnet **c:\templates**.

3. Kör följande kommando för att distribuera ny IoT hub, skicka namnet på din IoT-hubb som en parameter. I det här exemplet är namnet på IoT-hubben `abcmyiothub`. Namnet på din IoT hub måste vara globalt unikt:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Utdata visar nycklarna för IoT-hubben som du skapade.

5. Kontrollera att ditt program har lagts till den nya IoT-hubben genom att gå till den [Azure-portalen] [ lnk-azure-portal] och visa din lista över resurser. Du kan också använda den **Get-AzureRmResource** PowerShell-cmdlet.

> [!NOTE]
> Det här exempelprogrammet lägger till en S1 Standard IoT-hubb som du faktureras. Du kan ta bort IoT-hubben via den [Azure-portalen] [ lnk-azure-portal] eller genom att använda den **Remove-AzureRmResource** PowerShell-cmdlet när du är klar.

## <a name="next-steps"></a>Nästa steg

Nu du har distribuerat en IoT hub med en Azure Resource Manager-mall med PowerShell, kanske du vill utforska ytterligare:

* Läs mer om funktionerna i den [IoT Hub REST API för resursprovider][lnk-rest-api].
* Läs [översikt över Azure Resource Manager] [ lnk-azure-rm-overview] att lära dig mer om funktionerna för Azure Resource Manager.
* JSON-syntax och egenskaper som ska användas i mallar finns i [Microsoft.Devices resurstyper](/azure/templates/microsoft.devices/iothub-allversions).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK: er][lnk-sdks]

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/azurerm/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
