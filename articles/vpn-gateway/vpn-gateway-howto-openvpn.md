---
title: 'Hur du konfigurerar OpenVPN på Azure VPN Gateway: PowerShell | Microsoft Docs'
description: Steg för att konfigurera OpenVPN för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: cherylmc
ms.openlocfilehash: 958f4f46ec6ba407df7c739b7c62aa1489458485
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408284"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurera OpenVPN för Azure punkt-till-plats VPN-Gateway (förhandsversion)

Den här artikeln kan du ställa in OpenVPN på Azure VPN Gateway. Artikeln förutsätter att du redan har en punkt-till-plats arbetsmiljö. Om du inte gör det, följ instruktionerna i steg 1 för att skapa en punkt-till-plats-VPN.

> [!IMPORTANT]
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>Registrera den här funktionen

Klicka på den **TryIt** i dessa steg för att registrera den här funktionen enkelt med Azure Cloud Shell.

>[!NOTE]
>Om du inte registrerar den här funktionen kan att du inte kunna använda den.
>

När du klickar på **TryIt** för att öppna Azure Cloud Shell, kopiera och klistra in följande kommandon:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

När funktionen visas som registrerad, registrera prenumerationen till Microsoft.Network-namnområde.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Skapa en punkt-till-plats-VPN

Om du inte redan har en fungerande punkt-till-plats-miljö, följer du anvisningarna för att skapa en. Se [skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) att skapa och konfigurera en punkt-till-plats VPN-gateway med Azures interna certifikatautentisering.

## <a name="cmdlets"></a>2. Installera PowerShell-cmdletar

Installera den senaste versionen av PowerShell-cmdletarna för Resource Manager. Mer information om hur du installerar PowerShell-cmdlets finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Detta är viktigt eftersom tidigare versioner av cmdletarna inte innehåller de aktuella värden som du behöver för den här övningen.

## <a name="enable"></a>3. Aktivera OpenVPN på gatewayen

Aktivera OpenVPN på din gateway. Kontrollera att gatewayen har redan konfigurerats för punkt-till-plats (IKEv2 eller SSTP) innan du kör följande kommandon:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Nästa steg

För att konfigurera klienter för OpenVPN Se [konfigurera OpenVPN klienter](vpn-gateway-howto-openvpn-clients.md).