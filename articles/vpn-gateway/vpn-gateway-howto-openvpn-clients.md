---
title: Så här konfigurerar du OpenVPN-klienter för Azure VPN Gateway | Microsoft Docs
description: Lär dig hur du konfigurerar OpenVPN-protokoll klienter för Azure VPN Gateway, inklusive Windows-, Mac-och Linux-klienter.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926220"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurera OpenVPN-klienter för Azure VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN- &reg; protokoll** klienter.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har slutfört stegen för att konfigurera OpenVPN för din VPN-gateway. Mer information finns i [konfigurera OpenVPN för Azure VPN gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill att VPN-klienter ska kunna komma åt resurser i ett annat VNet följer du anvisningarna i artikeln [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) för att konfigurera en VNet-till-VNET-anslutning. Se till att aktivera BGP på gatewayarna och anslutningarna, annars flödar inte trafiken.

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
