---
title: Hantera IoT Central från Azure PowerShell | Microsoft Docs
description: Hantera IoT Central från Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8d432d588cfd563ee48b93f9042339e9590bb5e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533267"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Hantera IoT Central från Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program från IoT Central [Programhanterare](https://aka.ms/iotcentral) kan du använda [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) att hantera dina program.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du vill köra Azure PowerShell på den lokala datorn finns [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps). När du kör Azure PowerShell lokalt måste du använda den **Connect AzAccount** cmdlet för att logga in på Azure innan du försöker cmdletarna i den här artikeln.

## <a name="install-the-iot-central-module"></a>Installera modulen IoT Central

Kör följande kommando för att kontrollera den [IoT Central-modulen](https://docs.microsoft.com/powershell/module/az.iotcentral/) har installerats i din PowerShell-miljö:

```powershell
Get-InstalledModule -name Az.I*
```

Om listan över installerade moduler inte omfattar **Az.IotCentral**, kör du följande kommando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Skapa ett program

Använd den [New AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet för att skapa ett IoT Central-program i Azure-prenumerationen. Exempel:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skriptet skapar först en resursgrupp i östra USA för programmet. I följande tabell beskrivs de parametrar som används med den **New AzIotCentralApp** kommando:

|Parameter         |Beskrivning |
|------------------|------------|
|ResourceGroupName |Den resursgrupp som innehåller programmet. Den här resursgruppen måste redan finnas i din prenumeration. |
|Location |Som standard använder denna cmdlet platsen från resursgruppen. För närvarande kan du skapa en IoT Central-program i den **USA, östra**, **västra USA**, **Nordeuropa**, eller **Västeuropa** regioner. |
|Namn              |Namnet på programmet i Azure-portalen. |
|Underdomän         |Underdomänen i URL: en för programmet. I det här exemplet programmets URL är https://mysubdomain.azureiotcentral.com. |
|Sku               |Det enda värdet som är för närvarande **S1** (standard-nivån). Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Mall          | Mall för program att använda. Mer information finns i följande tabell: |
|DisplayName       |Namnet på appen som visas i Användargränssnittet. |

**Programmallar**

|Mallens namn  |Beskrivning |
|---------------|------------|
|iotc-default@1.0.0 |Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |
|iotc-demo@1.0.0    |Skapar ett program som innehåller en mall för enheter som redan har skapats för en kylande varuautomat. Använd den här mallen för att börja utforska Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Skapar ett program med enhetsmallar där du kan ansluta en MXChip- eller Raspberry Pi-enhet. Använd den här mallen om du utvecklar en enhet experimentera med någon av dessa enheter. |

## <a name="view-your-iot-central-applications"></a>Visa dina IoT Central-program

Använd den [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet för att lista med IoT Central-program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd den [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet för att uppdatera metadata för ett program med IoT Central. Till exempel vill ändra visningsnamnet för ditt program:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd den [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet för att ta bort ett IoT Central-program. Exempel:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central program från Azure PowerShell, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
