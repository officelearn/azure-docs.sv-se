---
title: Hantera IoT Central från Azure PowerShell | Microsoft Docs
description: I den här artikeln beskrivs hur du skapar och hanterar dina IoT Central-program från Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: a95b59c6cc0d486c1d4b10f39d0d272dd4b34f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019000"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Hantera IoT Central från Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) för att hantera dina program.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att köra Azure PowerShell på den lokala datorn kan du läsa [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps). När du kör Azure PowerShell lokalt använder du cmdleten **Connect-AzAccount** för att logga in på Azure innan du provar cmdletarna i den här artikeln.

## <a name="install-the-iot-central-module"></a>Installera IoT Central-modulen

Kör följande kommando för att kontrol lera att [IoT Central-modulen](https://docs.microsoft.com/powershell/module/az.iotcentral/) är installerad i din PowerShell-miljö:

```powershell
Get-InstalledModule -name Az.I*
```

Om listan över installerade moduler inte innehåller **AZ. IotCentral**kör du följande kommando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Skapa ett program

Använd cmdleten [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) för att skapa ett IoT Central-program i din Azure-prenumeration. Ett exempel:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skriptet skapar först en resurs grupp i regionen USA, östra för programmet. I följande tabell beskrivs de parametrar som används med kommandot **New-AzIotCentralApp** :

|Parameter         |Beskrivning |
|------------------|------------|
|ResourceGroupName |Resurs gruppen som innehåller programmet. Den här resurs gruppen måste redan finnas i din prenumeration. |
|Location |Som standard använder denna cmdlet platsen från resurs gruppen. För närvarande kan du skapa ett IoT Central-program i regionerna **östra USA**, **västra USA**, **Nord Europa**eller Västeuropa **, Västeuropa eller** i regionerna **Australien** eller **Asien och Stillahavsområdet** .  |
|Namn              |Namnet på programmet i Azure Portal. |
|under domän         |Under domänen i programmets URL. I exemplet är programmets URL https://mysubdomain.azureiotcentral.com. |
|Sku               |För närvarande kan du använda antingen **ST1** eller **ST2**. Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Mall          | Den program mal len som ska användas. Mer information finns i följande tabell: |
|DisplayName       |Namnet på programmet som det visas i användar gränssnittet. |

**Programmallar**

| Mallens namn            | Beskrivning |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter.
| iotc-pnp-preview@1.0.0   | Skapar ett tomt Plug and Play-program (för hands version) som du kan fylla i med dina egna enhets mallar och enheter. |
| iotc-condition@1.0.0     | Skapar ett program med en mall för övervakning av villkor i butiken. Använd den här mallen för att ansluta och övervaka lagrings miljön. |
| iotc-consumption@1.0.0   | Skapar ett program med en övervaknings mall för vattenkonsumtion. Använd den här mallen för att övervaka och kontrol lera vatten flödet. |
| iotc-distribution@1.0.0  | Skapar ett program med en mall för digital distribution. Använd den här mallen för att förbättra lagrets uteffekts effektivitet genom att digitala nyckel till gångar och åtgärder. |
| iotc-inventory@1.0.0     | Skapar ett program med en mall för smart inventerings hantering. Använd den här mallen för att automatisera mottagning, produkt förflyttning, cyklisk inventering och spårning av sensorer. |
| iotc-logistics@1.0.0     | Skapar ett program med en ansluten logistik mall. Använd den här mallen för att spåra din försändelse i real tid över luft, vatten och mark med plats-och villkors övervakning. |
| iotc-meter@1.0.0         | Skapar ett program med en mall för övervakning av smarta mätare. Använd den här mallen för att övervaka energi förbrukning, nätverks status och identifiera trender för att förbättra kund support och hantering av Smart avläsning.  |
| iotc-patient@1.0.0       | Skapar ett program med en mall för kontinuerliga patient övervakning. Använd den här mallen för att utöka patient vård, åter användning och hantering av sjukdomar. |
| iotc-power@1.0.0         | Skapar ett program med en övervaknings mall för sol panelen. Använd den här mallen för att övervaka status för sol panelen, utveckling av energi produktion. |
| iotc-quality@1.0.0       | Skapar ett program med en mall för övervakning av vatten kvalitet. Använd den här mallen om du vill övervaka vatten kvaliteten digitalt.|
| iotc-store@1.0.0         | Skapar ett program med en mall för analys i butiken – check. Använd den här mallen för att övervaka och hantera kassa flödet inuti ditt lager. |
| iotc-waste@1.0.0         | Skapar ett program med en mall för anslutet avfalls hantering. Använd den här mallen för att övervaka avfalls platser och operatörer för sändnings fält. |

## <a name="view-your-iot-central-applications"></a>Visa dina IoT Central-program

Använd cmdleten [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) för att lista dina IoT Central-program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd cmdleten [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) för att uppdatera metadata för ett IoT Central program. Om du till exempel vill ändra visnings namnet för ditt program:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd cmdleten [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) för att ta bort ett IoT Central-program. Ett exempel:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure PowerShell, är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
