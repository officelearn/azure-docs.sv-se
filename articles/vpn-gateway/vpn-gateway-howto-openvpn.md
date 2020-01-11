---
title: 'Så här konfigurerar du OpenVPN på Azure VPN Gateway: PowerShell'
description: Steg för att konfigurera OpenVPN för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 02514285570b764c6f7392b789f2ff7b427bb3a6
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863765"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurera OpenVPN för Azure-punkt-till-plats-VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN® protokoll** på Azure VPN gateway. Artikeln förutsätter att du redan har en fungerande punkt-till-plats-miljö. Om du inte gör det använder du instruktionerna i steg 1 för att skapa en punkt-till-plats-VPN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. skapa en punkt-till-plats-VPN

Om du inte redan har en fungerande punkt-till-plats-miljö, följer du anvisningarna för att skapa en. Se [skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att skapa och konfigurera en punkt-till-plats-VPN-gateway med intern Azure-certifikatautentisering. 

> [!IMPORTANT]
> Bas-SKU: n stöds inte för OpenVPN.

## <a name="enable"></a>2. Aktivera OpenVPN på gatewayen

Aktivera OpenVPN på din gateway. Kontrol lera att gatewayen redan har kon figurer ATS för punkt-till-plats (IKEv2 eller SSTP) innan du kör följande kommandon:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar klienter för OpenVPN finns i [konfigurera OpenVPN-klienter](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
