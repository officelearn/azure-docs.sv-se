---
title: 'Konfigurera OpenVPN på Azure VPN Gateway: PowerShell'
description: Steg för att konfigurera OpenVPN för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162415"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurera OpenVPN för Azure point-to-site VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN® Protocol** på Azure VPN Gateway. Artikeln förutsätter att du redan har en arbetspunkt-till-plats-miljö. Om du inte gör det använder du instruktionerna i steg 1 för att skapa en punkt-till-plats-VPN.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Skapa en punkt-till-plats-VPN

Om du inte redan har en fungerande point-to-site-miljö följer du instruktionen för att skapa en. Se [Skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att skapa och konfigurera en punkt-till-plats-VPN-gateway med inbyggd Azure-certifikatautentisering. 

> [!IMPORTANT]
> Basic SKU stöds inte för OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Aktivera OpenVPN på gatewayen

Aktivera OpenVPN på din gateway. Kontrollera att gatewayen redan är konfigurerad för point-to-site (IKEv2 eller SSTP) innan du kör följande kommandon:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera klienter för OpenVPN finns i [Konfigurera OpenVPN-klienter](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
