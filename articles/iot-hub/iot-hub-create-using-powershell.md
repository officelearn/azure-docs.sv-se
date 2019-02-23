---
title: Skapa en Azure IoT Hub med hjälp av en PowerShell-cmdlet | Microsoft Docs
description: Hur du använder en PowerShell-cmdlet för att skapa en IoT hub.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 6462673f6c3992aacbaee168eafc6bdb1b2fa944
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733478"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Skapa en IoT hub med hjälp av cmdleten New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure PowerShell-cmdletar för att skapa och hantera Azure-IoT-hubbar. Den här självstudien visar hur du skapar en IoT hub med PowerShell.

För att slutföra den här anvisningen, behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Om du använder Cloud Shell är du redan inloggad till din prenumeration. Om du kör PowerShell lokalt i stället, anger du följande kommando för att logga in på Azure-prenumerationen:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du behöver en resursgrupp att distribuera en IoT-hubb. Du kan använda en befintlig resursgrupp eller skapa en ny.

Du kan skapa en resursgrupp för din IoT-hubb med den [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) kommando. Det här exemplet skapas en resursgrupp med namnet **MyIoTRG1** i den **USA, östra** region:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Du kan skapa en IoT-hubb i resursgruppen du skapade i föregående steg med den [New AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) kommando. Det här exemplet skapar en **S1** hub kallas **MyTestIoTHub** i den **USA, östra** region:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Namnet på IoT-hubben måste vara globalt unikt.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Du kan lista alla IoT-hubbar i din prenumeration med hjälp av den [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) kommando:

```azurepowershell-interactive
Get-AzIotHub
```

Det här exemplet visar S1 Standard IoT-hubben som du skapade i föregående steg.

Du kan ta bort en IoT hub med hjälp av den [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) kommando:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternativt kan du ta bort en resursgrupp och alla resurser den innehåller med hjälp av den [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT hub med en PowerShell-cmdlet om du vill utforska ytterligare, ta en titt i följande artiklar:

* [PowerShell-cmdletar för att arbeta med din IoT-hubb](https://docs.microsoft.com/powershell/module/az.iothub/).

* [IoT Hub REST API för resursprovider](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
