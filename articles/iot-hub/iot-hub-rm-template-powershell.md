---
title: Skapa en Azure IoT-hubb med en mall (PowerShell) | Microsoft-dokument
description: Så här använder du en Azure Resource Manager-mall för att skapa en IoT-hubb med Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976610"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Skapa en IoT-hubb med Azure Resource Manager-mall (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Lär dig hur du använder en Azure Resource Manager-mall för att skapa en IoT-hubb och en konsumentgrupp. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. Mer information om hur du utvecklar Resource Manager-mallar finns i [Dokumentationen till Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Resource Manager-mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Här är en kopia av mallen:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Mallen skapar en Azure Iot-hubb med tre slutpunkter (eventhub, moln-till-enhet och meddelanden) och en konsumentgrupp. Fler mallexempel finns i [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Iot Hub-mallschemat hittar du [här](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Det finns flera metoder för att distribuera en mall.  Du använder Azure PowerShell i den här självstudien.

Om du vill köra PowerShell-skriptet väljer du **Prova det** för att öppna Azure Cloud-gränssnittet. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan Klistra in:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Som du kan se i PowerShell-skriptet är mallen som används från Azure Quickstart-mallar. Om du vill använda din egen måste du först ladda upp `-TemplateFile` mallfilen till cloud-skalet och sedan använda växeln för att ange filnamnet.  Ett exempel finns i [Distribuera mallen](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en IoT-hubb med hjälp av en Azure Resource Manager-mall kanske du vill utforska vidare:

* Läs om funktionerna i [IoT Hub-resursprovidern REST API][lnk-rest-api].
* Läs [översikten över Azure Resource Manager][lnk-azure-rm-overview] om du vill veta mer om funktionerna i Azure Resource Manager.
* JSON-syntax och egenskaper som ska användas i mallar finns i [Microsoft.Devices resurstyper](/azure/templates/microsoft.devices/iothub-allversions).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK:er][lnk-sdks]

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
