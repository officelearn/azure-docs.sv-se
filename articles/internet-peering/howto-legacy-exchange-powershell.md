---
title: Konvertera en äldre Exchange-peering till Azure-resurs med hjälp av PowerShell
titleSuffix: Azure
description: Konvertera en äldre Exchange-peering till Azure-resurs med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775399"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Konvertera en äldre Exchange-peering till Azure-resurs med hjälp av PowerShell

Den här artikeln beskriver hur du konverterar en befintlig äldre Exchange-peering till Azure-resurser med hjälp av PowerShell-cmdletar.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [krav](prerequisites.md) och [genom gång av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konvertera en äldre Exchange-peering till Azure Resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Hämta äldre Exchange-peering för konvertering
Nedan visas ett exempel på hur du kan hämta äldre Exchange-peering på Seattle-peering-platsen:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Svaret liknar följande exempel:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Konvertera äldre peering
Kommandot nedan kan användas för att konvertera äldre Exchange-peering till Azure-resursen:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Observera att när du konverterar äldre peering till Azure-resurs, stöds inte ändringar &nbsp;

Nedan visas ett exempel på ett svar när end-to-end-etableringen har slutförts:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```
Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Exchange-peering med PowerShell](howto-exchange-powershell.md)
