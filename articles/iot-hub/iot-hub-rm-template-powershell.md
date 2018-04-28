---
title: Skapa en Azure IoT-hubb med en mall (PowerShell) | Microsoft Docs
description: Hur du använder en Azure Resource Manager-mall för att skapa en IoT-hubb med PowerShell.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0e5f95d98f772b226e162f601939bc94bf8fb78b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Skapa en IoT-hubb med hjälp av Azure Resource Manager-mall (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda Azure Resource Manager för att skapa och hantera Azure IoT-hubbar programmässigt. Den här kursen visar hur du använder en Azure Resource Manager-mall för att skapa en IoT-hubb med PowerShell.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker Azure Resource Manager-distributionsmodellen.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure PowerShell 1.0] [ lnk-powershell-install] eller senare.

> [!TIP]
> Artikeln [med hjälp av Azure PowerShell med Azure Resource Manager] [ lnk-powershell-arm] ger mer information om hur du använder PowerShell och Azure Resource Manager-mallar för att skapa Azure-resurser.

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Ange följande kommando för att logga in på Azure-prenumerationen i PowerShell-Kommandotolken:

```powershell
Connect-AzureRmAccount
```

Om du har flera Azure-prenumerationer, ger logga in på Azure åtkomst till alla de Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

```powershell
Get-AzureRMSubscription
```

Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

Du kan använda följande kommandon för att identifiera där du kan distribuera en IoT-hubb och API-versioner som för närvarande stöds:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Skapa en resursgrupp att innehålla din IoT-hubb med följande kommando i en av platserna som stöds för IoT-hubb. Det här exemplet skapar en resursgrupp med namnet **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Skicka en mall för att skapa en IoT-hubb

Använda en JSON-mall för att skapa en IoT-hubb i resursgruppen. Du kan också använda en Azure Resource Manager-mall för att göra ändringar i en befintlig IoT-hubb.

1. Använda en textredigerare för att skapa en mall med Azure Resource Manager namnet **template.json** med följande resursdefinitionen att skapa en ny standard IoT-hubb. Det här exemplet lägger till IoT-hubb i den **östra USA** region, skapar två konsumentgrupper (**cg1** och **cg2**) på den Event Hub-kompatibel slutpunkt och använder den **2016-02-03** API-versionen. Den här mallen förväntar du överföra i IoT hub-namn som en parameter anropas **hubName**. Den aktuella listan över platser som har stöd för IoT-hubb finns [Azure Status][lnk-status].

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

2. Spara filen för Azure Resource Manager-mallen på den lokala datorn. Det här exemplet förutsätter att du sparar den i en mapp med namnet **c:\templates**.

3. Kör följande kommando för att distribuera din nya IoT-hubb som skickar namnet på din IoT-hubb som en parameter. I det här exemplet IoT-hubben heter `abcmyiothub`. Namnet på din IoT-hubb måste vara globalt unika:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Utdata visar nycklarna för IoT-hubb som du skapade.

5. För att verifiera ditt program läggs ny IoT-hubben, finns det [Azure-portalen] [ lnk-azure-portal] och visa en lista över resurser. Du kan också använda den **Get-AzureRmResource** PowerShell-cmdlet.

> [!NOTE]
> Det här exempelprogrammet lägger till en S1 Standard IoT-hubb du debiteras. Du kan ta bort IoT-hubb via den [Azure-portalen] [ lnk-azure-portal] eller genom att använda den **ta bort AzureRmResource** PowerShell-cmdlet när du är klar.

## <a name="next-steps"></a>Nästa steg

Nu du har distribuerat en IoT-hubb med en Azure Resource Manager-mall med PowerShell kan du vill utforska vidare:

* Läs mer om funktionerna i den [IoT-hubb resursprovidern REST API][lnk-rest-api].
* Läs [översikt över Azure Resource Manager] [ lnk-azure-rm-overview] att lära dig mer om funktionerna i Azure Resource Manager.

Mer information om hur du utvecklar för IoT-hubb finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
