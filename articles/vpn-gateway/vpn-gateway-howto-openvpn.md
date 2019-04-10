---
title: 'Hur du konfigurerar OpenVPN på Azure VPN Gateway: PowerShell| Microsoft Docs'
description: Steg för att konfigurera OpenVPN för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281972"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurera OpenVPN för Azure punkt-till-plats VPN-Gateway (förhandsversion)

Den här artikeln visar hur du konfigurerar **OpenVPN® protokollet** på Azure VPN Gateway. Artikeln förutsätter att du redan har en punkt-till-plats arbetsmiljö. Om du inte gör det, följ instruktionerna i steg 1 för att skapa en punkt-till-plats-VPN.

> [!IMPORTANT]
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>Registrera funktionen

Klicka på den **TryIt** i dessa steg för att registrera den här funktionen enkelt med Azure Cloud Shell.

>[!NOTE]
>Om du inte registrerar den här funktionen kan att du inte kunna använda den.
>

När du har klickat på **TryIt** för att öppna Azure Cloud Shell kopierar du och klistrar in följande kommandon:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

När funktionen visas som registrerad registrerar du prenumerationen på nytt i Microsoft.Network-namnrymden.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Skapa en punkt-till-plats-VPN

Om du inte redan har en fungerande punkt-till-plats-miljö, följer du anvisningarna för att skapa en. Se [skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) att skapa och konfigurera en punkt-till-plats VPN-gateway med Azures interna certifikatautentisering. 

> [!IMPORTANT]
> Grundläggande SKU stöds inte för OpenVPN.

## <a name="enable"></a>2. Aktivera OpenVPN på gatewayen

Aktivera OpenVPN på din gateway. Kontrollera att gatewayen har redan konfigurerats för punkt-till-plats (IKEv2 eller SSTP) innan du kör följande kommandon:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Nästa steg

För att konfigurera klienter för OpenVPN Se [konfigurera OpenVPN klienter](vpn-gateway-howto-openvpn-clients.md).

**”OpenVPN” är ett varumärke som tillhör OpenVPN Inc.**