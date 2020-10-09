---
title: Skapa en Azure-IoT Hub med hjälp av en PowerShell-cmdlet | Microsoft Docs
description: Lär dig hur du använder PowerShell-cmdletar för att skapa en resurs grupp och sedan skapa en IoT-hubb i resurs gruppen. Lär dig också hur du tar bort hubben.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890591"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Skapa en IoT-hubb med hjälp av New-AzIotHub cmdlet

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure PowerShell-cmdletar för att skapa och hantera Azure IoT-hubbar. Den här självstudien visar hur du skapar en IoT Hub med PowerShell.

För att slutföra den här instruktionen behöver du en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Om du använder Cloud Shell är du redan inloggad i din prenumeration. Om du kör PowerShell lokalt i stället anger du följande kommando för att logga in på din Azure-prenumeration:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du behöver en resurs grupp för att distribuera en IoT-hubb. Du kan använda en befintlig resurs grupp eller skapa en ny.

Om du vill skapa en resurs grupp för din IoT-hubb använder du kommandot [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) . I det här exemplet skapas en resurs grupp med namnet **MyIoTRG1** i regionen **USA, östra** :

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Skapa en IoT-hubb

Om du vill skapa en IoT-hubb i resurs gruppen som du skapade i föregående steg, använder du kommandot [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) . I det här exemplet skapas en **S1** -hubb med namnet **MYTESTIOTHUB** i regionen **USA, östra** :

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Namnet på IoT-hubben måste vara globalt unikt.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Du kan lista alla IoT-hubbar i din prenumeration med kommandot [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) :

```azurepowershell-interactive
Get-AzIotHub
```

I det här exemplet visas S1-standardIoT Hub som du skapade i föregående steg.

Du kan ta bort IoT-hubben med kommandot [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) :

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Du kan också ta bort en resurs grupp och alla resurser som den innehåller med kommandot [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT-hubb med en PowerShell-cmdlet, om du vill utforska ytterligare, kan du läsa följande artiklar:

* [PowerShell-cmdletar för att arbeta med din IoT Hub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [IoT Hub Resource provider REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
