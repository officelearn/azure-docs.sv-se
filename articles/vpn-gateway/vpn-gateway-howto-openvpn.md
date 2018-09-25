---
title: 'Hur du konfigurerar OpenVPN på Azure VPN Gateway: PowerShell | Microsoft Docs'
description: Steg för att konfigurera OpenVPN för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: b915a56b14332bfa885eaccf9a151d08c58dc5b1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973702"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurera OpenVPN för Azure punkt-till-plats VPN-Gateway (förhandsversion)

Den här artikeln kan du ställa in OpenVPN på Azure VPN Gateway. Artikeln förutsätter att du redan har en punkt-till-plats arbetsmiljö. Om du inte gör det, följ instruktionerna i steg 1 för att skapa en punkt-till-plats-VPN.

> [!IMPORTANT]
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

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