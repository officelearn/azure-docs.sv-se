---
title: Skapa en Azure IoT Hub med hjälp av en mall (PowerShell) | Microsoft Docs
description: Hur du använder en Azure Resource Manager-mall för att skapa en IoT Hub med Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609189"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Skapa en IoT hub med Azure Resource Manager-mall (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Lär dig hur du använder en Azure Resource Manager-mall för att skapa en IoT-hubb och en konsumentgrupp. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. Mer information om hur du utvecklar Resource Manager-mallar finns i [dokumentation om Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Resource Manager-mallen som används i den här snabbstarten är från [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Här är en kopia av mallen:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Mallen skapar en Azure-Iot-hubb med tre slutpunkter (eventhub, moln-till-enhet och meddelanden) och en konsumentgrupp. Läs mer mallexempel [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Mallsschemat Iot Hub finns [här](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Det finns flera metoder för att distribuera en mall.  I den här självstudien använder du Azure PowerShell.

Om du vill köra PowerShell-skriptet, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan klistra in:

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

Som du ser i PowerShell-skriptet är den mall som användes från Azure Quickstart-mallar. Om du vill använda din egen, måste du först överföra mallfilen till cloudshell och sedan använda den `-TemplateFile` växel för att ange namnet på filen.  Ett exempel finns i [distribuera mallen](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Nästa steg

Nu du har distribuerat en IoT-hubb med hjälp av en Azure Resource Manager-mall kan du utforska ytterligare:

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
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
