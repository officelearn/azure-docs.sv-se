---
title: Konvertera en äldre Exchange-peering till Azure-resurs med PowerShell
titleSuffix: Azure
description: Konvertera en äldre Exchange-peering till Azure-resurs med PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775399"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Konvertera en äldre Exchange-peering till Azure-resurs med PowerShell

I den här artikeln beskrivs hur du konverterar en befintlig äldre Exchange-peering till Azure-resurs med PowerShell-cmdletar.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) och [Exchange peer-genomgång](walkthrough-exchange-all.md) innan du börjar konfigurationen.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konvertera en äldre Exchange-peering till en Azure-resurs

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Få äldre Exchange-peering för konvertering
Nedan är exemplet för att få äldre Exchange peering på Seattle peering plats:

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
Nedan kan kommandot användas för att konvertera äldre Exchange-peering till Azure-resurs:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Observera att vid konvertering av äldre peer-peering till azure-resurs stöds inte ändringar&nbsp;

Nedan visas ett exempelsvar när etableringen från slutna till slutna har slutförts:

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
Mer information finns på [vanliga frågor om internet peering](faqs.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Exchange-peering med PowerShell](howto-exchange-powershell.md)
