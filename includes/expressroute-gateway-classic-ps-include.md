---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0bf55d2353d3524e65602c7e67b7adbf80432043
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
Du måste skapa ett VNet och ett gateway-undernät, innan du arbetar med följande uppgifter.

> [!NOTE]
> De här exemplen gäller inte för S2S/ExpressRoute samexisterar konfigurationer.
> Mer information om hur du arbetar med gateways i en konfiguration för coexist finns [konfigurera samtidiga anslutningar](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Lägga till en gateway

Använd kommandot nedan för att skapa en gateway. Se till att ersätta alla värden för dina egna.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Kontrollera gatewayen som har skapats

Använd kommandot nedan för att kontrollera att gatewayen har skapats. Detta kommando hämtar också gateway-ID som du behöver för andra åtgärder.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway

Det finns ett antal [Gateway-SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra Gateway-SKU när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance gateway. Om du vill ändra din gateway till en gateway för UltraPerformance först ta bort den befintliga ExpressRoute-gatewayen och sedan skapa en ny UltraPerformance gateway. Om du vill konvertera din gateway från en UltraPerformance gateway tar du bort UltraPerformance gateway och sedan skapa en ny gateway. 
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