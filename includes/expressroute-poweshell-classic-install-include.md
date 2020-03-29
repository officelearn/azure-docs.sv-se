---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926221"
---
Installera de senaste versionerna av Azure Service Management (SM) PowerShell-modulerna och ExpressRoute-modulen. Du kan inte använda Azure CloudShell-miljön för att köra SM-moduler.

1. Installera modulen [Installera modulen Installera modulen För servicehantering](/powershell/azure/servicemanagement/install-azure-ps) för att installera Azure Service Management Module. Om du redan har Az- eller RM-modulen, se till att använda "-AllowClobber".
2. Importera de installerade modulerna. När du använder följande exempel justerar du sökvägen så att den återspeglar platsen och versionen av dina installerade PowerShell-moduler.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Om du vill logga in på ditt Azure-konto öppnar du PowerShell-konsolen med förhöjda rättigheter och ansluter till ditt konto. Använd följande exempel för att ansluta med modulen Service Management:

   ```powershell
   Add-AzureAccount
   ```