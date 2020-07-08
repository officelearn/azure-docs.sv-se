---
title: 'Azure VPN Gateway: Konfigurera paket fångster'
description: Lär dig mer om paket avbildnings funktioner som du kan använda på VPN-gatewayer.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 6edfe0228ce4cbe21ad4ae0eb8b7316a92f1da31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987158"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurera paket avbildningar för VPN-gatewayer

Anslutnings-och prestandarelaterade problem är ofta komplexa och tar betydande tid och ansträngning för att bara minska orsaken till problemet. Möjligheten att fånga paket minskar avsevärt tiden för att begränsa problemet till vissa delar av nätverket, t. ex. om problemet finns på kund sidan av nätverket, på Azure-sidan av nätverket eller någonstans i mellan. När problemet har begränsats är det mycket mer effektivt att felsöka och vidta åtgärder.

Det finns några vanliga tillgängliga verktyg för paket fångst. Att hämta relevanta paket avbildningar med hjälp av dessa verktyg är dock ofta besvärligt i synnerhet när du arbetar med stora volym trafik scenarier. Filtrerings funktioner som tillhandahålls av en VPN-gateway paket fångst blir en större differentiering. Du kan använda en VPN gateway-paket avbildning utöver de vanligaste verktygen för paket insamling.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Filtrerings funktioner för paket insamling i VPN gateway

Paket avbildningar för VPN gateway kan köras på gatewayen eller på en specifik anslutning beroende på kundernas behov. Du kan också köra paket fångster på flera tunnlar samtidigt. Du kan fånga enkel trafik, IKE-och ESP-trafik och interna paket tillsammans med filtrering på en VPN-gateway.

Användning av 5 tupler-filter (käll under nät, mål under nät, källport, målport, protokoll) och TCP-flaggor (SYN, ACK, FIN, URG, PSH, per volym) är användbart när du isolerar problem med hög volym trafik.

Du kan bara använda ett alternativ per egenskap när du kör paket fångsten.

## <a name="setup-packet-capture-using-powershell"></a>Konfigurera paket avbildning med PowerShell

Se exemplen nedan för PowerShell-kommandon för att starta och stoppa paket fångster. Mer information om parameter alternativ (till exempel hur du skapar filter) finns i det här PowerShell- [dokumentet](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Starta paket fångst för en VPN-gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Valfri parameter **– katalogen** kan användas för att tillämpa filter.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Stoppa paket fångst för en VPN-gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Starta paket fångst för en VPN gateway-anslutning

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Valfri parameter **– katalogen** kan användas för att tillämpa filter.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Stoppa paket fångst på en VPN gateway-anslutning

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Viktiga överväganden

- Att köra paket dum par kan påverka prestandan. Kom ihåg att stoppa paket fångsten när det inte behövs.
- Föreslagen minsta paket hämtnings tid är 600 sekunder. Att ha kortare hämtnings tid för paketet kanske inte ger fullständiga data på grund av synkroniseringsproblem mellan flera komponenter på sökvägen.
- Paket insamlings data genereras i PCAP-format. Använd wireshark eller andra program som ofta är tillgängliga för att öppna PCAP-filer.

## <a name="next-steps"></a>Nästa steg

Mer information om VPN Gateway finns i [about VPN gateway](vpn-gateway-about-vpngateways.md)
