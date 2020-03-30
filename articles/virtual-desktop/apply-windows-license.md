---
title: Använda Windows-licens för virtuella datorer för sessionsvärdar – Azure
description: I artikeln beskrivs hur du anvÃ¤nder Windows-licensen för virtuella datorer med virtuella datorer i Windows.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254240"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Använda Windows-licens för virtuella datorer för sessionsvärd

Kunder som har rätt tillstånd att köra Windows Virtual Desktop-arbetsbelastningar kan tillämpa en Windows-licens på sina virtuella datorer för sessionvärden och köra dem utan att betala för en annan licens. Mer information finns i [Priser för Virtuellt skrivbord i Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/)Virtual Desktop .

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Olika sätt att använda windows virtual desktop-licens
Med Windows Virtual Desktop-licensiering kan du använda en licens på alla virtuella Windows- eller Windows Server-datorer som är registrerade som sessionsvärd i en värdpool och tar emot användaranslutningar. Den här licensen gäller inte för virtuella datorer som körs som filresursservrar, domänkontrollanter och så vidare.

Det finns några sätt att använda Licensen för Windows Virtual Desktop:
- Du kan skapa en värdpool och dess virtuella datorer för session värd med azure [marketplace-erbjudandet](./create-host-pools-azure-marketplace.md). Virtuella datorer som skapats på det här sättet automatiskt har licensen tillämpas.
- Du kan skapa en värdpool och dess virtuella datorer för sessionsvärd med [GitHub Azure Resource Manager-mallen](./create-host-pools-arm-template.md). Virtuella datorer som skapats på det här sättet automatiskt har licensen tillämpas.
- Du kan använda en licens på en befintlig virtuell dator för sessionsvärd. Det gör du genom att först följa instruktionerna i [Skapa en värdpool med PowerShell](./create-host-pools-powershell.md) för att skapa en värdpool och associerade virtuella datorer och sedan gå tillbaka till den här artikeln för att lära dig hur du använder licensen.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Använda en Windows-licens på en virtuell sessionsvärd
Kontrollera att du har [installerat och konfigurerat den senaste Azure PowerShell](/powershell/azure/overview). Kör följande PowerShell-cmdlet för att tillämpa Windows-licensen:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verifiera att den virtuella datorn för sessionsvärden använder licensförmånen
När du har distribuerat den virtuella datorn kör du den här cmdlet ot verifiera licenstypen:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

En virtuell sessionsvärd med den tillämpade Windows-licensen visar dig ungefär så här:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Virtuella datorer utan tillämpad Windows-licens visar dig ungefär så här:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Kör följande cmdlet om du vill visa en lista över alla virtuella datorer för sessionsvärdar som har Windows-licensen tillämpad i din Azure-prenumeration:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
