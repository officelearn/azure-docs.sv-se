---
title: Konfigurera OpenVPN-klienter för Azure VPN Gateway| Microsoft-dokument
description: Steg för att konfigurera OpenVPN-klienter för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066117"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurera OpenVPN-klienter för Azure VPN Gateway

Den här artikeln hjälper dig att konfigurera ** &reg; OpenVPN Protocol-klienter.**

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har slutfört stegen för att konfigurera OpenVPN för din VPN-gateway. Mer information finns i [Konfigurera OpenVPN för Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill att VPN-klienterna ska kunna komma åt resurser i ett annat virtuella nätverk följer du instruktionerna i [VNet-till-VNet-artikeln](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) för att konfigurera en vnet-till-vnet-anslutning. Var noga med att aktivera BGP på gateways och anslutningar, annars trafiken kommer inte att flöda.

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
