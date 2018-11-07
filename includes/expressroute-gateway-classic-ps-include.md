---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219825"
---
Du måste skapa ett virtuellt nätverk och ett gateway-undernät först innan du arbetar med följande uppgifter.

> [!NOTE]
> De här exemplen gäller inte för S2S/ExpressRoute samexistera konfigurationer.
> Läs mer om hur du arbetar med gatewayer i en konfiguration med coexist [konfigurerar du samexisterande anslutningar](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Lägga till en gateway

Använd kommandot nedan för att skapa en gateway. Glöm inte att ersätta alla värden för dina egna.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Kontrollera att gatewayen har skapats

Använd kommandot nedan för att verifiera att gatewayen har skapats. Detta kommando hämtar också gateway-ID som du behöver för andra åtgärder.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway

Det finns ett antal [Gateway SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra den Gateway-SKU när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance-gateway. Om du vill ändra din gateway till en UltraPerformance-gateway, först ta bort den befintliga ExpressRoute-gatewayen och skapa sedan en ny UltraPerformance-gateway. Ta först bort UltraPerformance-gateway för att nedgradera din gateway från en UltraPerformance-gateway och sedan skapa en ny gateway. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Ta bort en gateway

Använd kommandot nedan för att ta bort en gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
