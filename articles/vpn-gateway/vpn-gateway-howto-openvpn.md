---
title: 'Så här konfigurerar du OpenVPN på Azure VPN Gateway: PowerShell'
description: Steg för att konfigurera OpenVPN för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: de8d03467b5e44df1b9069c6db31d496785ff32e
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983859"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurera OpenVPN för Azure-punkt-till-plats-VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN® protokoll** på Azure VPN gateway. Artikeln förutsätter att du redan har en fungerande punkt-till-plats-miljö. Om du inte gör det använder du instruktionerna i steg 1 för att skapa en punkt-till-plats-VPN.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. skapa en punkt-till-plats-VPN

Om du inte redan har en fungerande punkt-till-plats-miljö, följer du anvisningarna för att skapa en. Se [skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att skapa och konfigurera en punkt-till-plats-VPN-gateway med intern Azure-certifikatautentisering. 

> [!IMPORTANT]
> Bas-SKU: n stöds inte för OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Aktivera OpenVPN på gatewayen

Aktivera OpenVPN på din gateway. Kontrol lera att gatewayen redan har kon figurer ATS för punkt-till-plats (IKEv2 eller SSTP) innan du kör följande kommandon:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar klienter för OpenVPN finns i [konfigurera OpenVPN-klienter](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
