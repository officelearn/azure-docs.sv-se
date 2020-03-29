---
title: 'Azure VPN Gateway: Konfigurera paketinsamlingar'
description: Lär dig mer om paketinspelningsfunktioner som du kan använda på VPN-gateways.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353508"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurera paketinsamlingar för VPN-gateways

Anslutnings- och prestandarelaterade problem är ofta tider komplexa och tar avsevärd tid och ansträngning bara för att begränsa orsaken till problemet. Möjligheten att paketinsamling avsevärt bidrar till att minska tiden för att begränsa problemets omfattning till vissa delar av nätverket, till exempel om problemet finns på kundsidan av nätverket, Azure-sidan av nätverket eller någonstans däremellan. När problemet har begränsats är det mycket effektivare att felsöka och vidta korrigerande åtgärder.

Det finns några allmänt tillgängliga verktyg för paketfångst. Men att få relevanta paketinsamlingar med hjälp av dessa verktyg är ofta gånger besvärligt, särskilt när man arbetar med hög volym trafikscenarier. Filtreringsfunktioner som tillhandahålls av en VPN-gateway paketfångst blir en viktig differentiator. Du kan använda en VPN-gateway paketfångst utöver allmänt tillgängliga paketinspelningsverktyg.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Filtrering av VPN-gateway-paketfiltrering

VPN gateway-paketinsamlingar kan köras på gatewayen eller på en viss anslutning beroende på kundens behov. Du kan också köra paketinsamlingar på flera tunnlar samtidigt. Du kan fånga en- eller tvåriktad trafik, IKE- och ESP-trafik och inre paket tillsammans med filtrering på en VPN-gateway.

Använda 5 tupplar filter (källa undernät, mål undernät, källport, målport, protokoll) och TCP flaggor (SYN, ACK, FIN, URG, PSH, RST) är till hjälp när isolera problem på en hög volym trafik.

Du kan bara använda ett alternativ per egenskap när du kör paketfångsten.

## <a name="setup-packet-capture-using-powershell"></a>Installera pakethämtning med PowerShell

Se exemplen nedan för PowerShell-kommandon för att starta och stoppa paketinsamlingar. Mer information om parameteralternativ (till exempel hur du skapar filter) finns i det här [PowerShell-dokumentet](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Starta paketinsamling för en VPN-gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Valfri parameter **-FilterData** kan användas för att använda filtret.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Stoppa pakethämtning för en VPN-gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Starta paketinsamling för en VPN-gatewayanslutning

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Valfri parameter **-FilterData** kan användas för att använda filtret.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Stoppa pakethämtning på en VPN-gatewayanslutning

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Viktiga överväganden

- Att köra paketinfångningar kan påverka prestanda. Kom ihåg att stoppa paketfångsten när det inte behövs.
- Den föreslagna minsta pakethämtningstiden är 600 sekunder. Om du har kortare varaktighet för paketinsamling kanske det inte ger fullständiga data på grund av synkroniseringsproblem mellan flera komponenter på sökvägen.
- Datafiler för paketinsamling genereras i PCAP-format. Använd Wireshark eller andra allmänt tillgängliga program för att öppna PCAP-filer.

## <a name="next-steps"></a>Nästa steg

Mer information om VPN Gateway finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md)
