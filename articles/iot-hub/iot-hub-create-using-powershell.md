---
title: "Skapa en Azure IoT-hubb med hjälp av en PowerShell-cmdlet | Microsoft Docs"
description: "Hur du använder en PowerShell-cmdlet för att skapa en IoT-hubb."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 02227adeb8a9a7463506efa44ddc2977f8aae65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Skapa en IoT-hubb med hjälp av cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure PowerShell-cmdlets för att skapa och hantera Azure IoT-hubbar. Den här kursen visar hur du skapar en IoT-hubb med PowerShell.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker Azure Resource Manager-distributionsmodellen.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure PowerShell-cmdlets][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration
Ange följande kommando för att logga in på Azure-prenumerationen i PowerShell-Kommandotolken:

```powershell
Login-AzureRmAccount
```

Om du har flera Azure-prenumerationer, ger logga in på Azure åtkomst till alla de Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

```powershell
Get-AzureRMSubscription
```

Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan använda prenumerationsnamn eller ID från utdata från det föregående kommandot:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Du behöver en resursgrupp och distribuera en IoT-hubb. Du kan använda en befintlig resursgrupp eller skapa en ny.

Du kan använda följande kommando för att identifiera de platser där du kan distribuera en IoT-hubb:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Använd följande kommando för att skapa en resursgrupp för din IoT-hubb i någon av platserna som stöds för IoT-hubb. Det här exemplet skapar en resursgrupp med namnet **MyIoTRG1** i den **östra USA** region:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Använd följande kommando för att skapa en IoT-hubb i resursgruppen du skapade i föregående steg. Det här exemplet skapas en **S1** hubb kallas **MyTestIoTHub** i den **östra USA** region:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Namnet på IoT-hubben måste vara unika.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Du kan ange IoT-hubbar i din prenumeration med hjälp av följande kommando:

```powershell
Get-AzureRmIotHub
```

I föregående exempel läggs en S1 Standard IoT-hubb du debiteras. Du kan ta bort IoT-hubb med följande kommando:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Du kan också ta bort en resursgrupp och alla resurser som den innehåller med följande kommando:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Nästa steg

Nu du har distribuerat en IoT-hubb med hjälp av en PowerShell-cmdlet, kanske du vill utforska vidare:

* Identifiera andra [PowerShell-cmdlets för att arbeta med din IoT-hubb][lnk-iothub-cmdlets].
* Läs mer om funktionerna i den [IoT-hubb resursprovidern REST API][lnk-rest-api].

Mer information om hur du utvecklar för IoT-hubb finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Simulera en enhet med IoT kant][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
