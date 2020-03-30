---
title: Skapa en Azure IoT-hubb med en PowerShell-cmdlet | Microsoft-dokument
description: Lär dig hur du använder PowerShell-cmdletar för att skapa en resursgrupp och sedan skapa en IoT-hubb i resursgruppen. Lär dig också hur du tar bort navet.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890591"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Skapa en IoT-hubb med cmdleten New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure PowerShell-cmdletar för att skapa och hantera Azure IoT-hubbar. Den här självstudien visar hur du skapar en IoT-hubb med PowerShell.

För att slutföra den här how-to behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Om du använder Cloud Shell är du redan inloggad på din prenumeration. Om du kör PowerShell lokalt i stället anger du följande kommando för att logga in på din Azure-prenumeration:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du behöver en resursgrupp för att distribuera en IoT-hubb. Du kan använda en befintlig resursgrupp eller skapa en ny.

Om du vill skapa en resursgrupp för IoT-hubben använder du kommandot [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) I det här exemplet skapas en resursgrupp med namnet **MyIoTRG1** i regionen **Östra USA:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du vill skapa en IoT-hubb i resursgruppen som du skapade i föregående steg använder du kommandot [Ny-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) I det här exemplet skapas en **S1-hubb** som heter **MyTestIoTHub** i regionen **östra USA:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Namnet på IoT-hubben måste vara globalt unikt.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Du kan lista alla IoT-hubbar i din prenumeration med kommandot [Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

I det här exemplet visas den S1 Standard IoT Hub som du skapade i föregående steg.

Du kan ta bort IoT-hubben med kommandot [Ta bort AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Du kan också ta bort en resursgrupp och alla resurser som den innehåller med kommandot [Ta bort AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT-hubb med en PowerShell-cmdlet, om du vill utforska ytterligare, kolla in följande artiklar:

* [PowerShell-cmdletar för att arbeta med din IoT-hubb](https://docs.microsoft.com/powershell/module/az.iothub/).

* [IoT Hub-resursprovider REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK:er](iot-hub-devguide-sdks.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
