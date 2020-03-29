---
title: Felsöka Azure Site-to-Site VPN kopplas från periodvis
description: Lär dig hur du felsöker problemet där VPN-anslutningen från dag till plats kopplas från regelbundet.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862568"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Felsökning: Azure Site-to-Site VPN kopplas från ibland

Det kan bero på problemet att en ny eller befintlig Microsoft Azure-plats-till-plats-VPN-anslutning inte är stabil eller kopplas från regelbundet. Den här artikeln innehåller felsökningssteg som hjälper dig att identifiera och lösa orsaken till problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="prerequisite-step"></a>Förutsättningssteg

Kontrollera typen av Azure-gateway för virtuella nätverk:

1. Gå till [Azure Portal](https://portal.azure.com).
2. Sök efter typinformation på sidan **Översikt** för den virtuella nätverksgatewayen.
    
    ![Översikten över gatewayen](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1 Kontrollera om den lokala VPN-enheten har validerats

1. Kontrollera om du använder en [validerad VPN-enhet och operativsystemversion](vpn-gateway-about-vpn-devices.md#devicetable). Om VPN-enheten inte är validerad kan du behöva kontakta enhetstillverkaren för att se om det finns något kompatibilitetsproblem.
2. Kontrollera att VPN-enheten är korrekt konfigurerad. Mer information finns i [Redigera exempel på enhetskonfiguration](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Steg 2 Kontrollera säkerhetsassociationens inställningar(för principbaserade Azure-gateways för virtuella nätverk)

1. Kontrollera att det virtuella nätverket, undernäten och intervallen i definitionen **för lokal nätverksgateway** i Microsoft Azure är samma som konfigurationen på den lokala VPN-enheten.
2. Kontrollera att säkerhetsassociationens inställningar matchar.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Steg 3 Sök efter användardefinierade vägar eller nätverkssäkerhetsgrupper i gatewayundernätet

En användardefinierad väg i gateway-undernätet kan begränsa viss trafik och tillåta annan trafik. Detta gör att det verkar som om VPN-anslutningen är opålitlig för viss trafik och bra för andra. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Steg 4 Kontrollera inställningen "en VPN-tunnel per undernätspar" (för principbaserade gateways för virtuella nätverk)

Kontrollera att den lokala VPN-enheten är inställd på att ha **en VPN-tunnel per undernätspar** för principbaserade virtuella nätverksgatewayer.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Steg 5 Sök efter begränsning av säkerhetsassociationen (för principbaserade gateways för virtuella nätverk)

Den principbaserade virtual network gateway har gränsen på 200 säkerhetsassociationspar. Om antalet Azure virtuella nätverk undernät multiplicerat gånger med antalet lokala undernät är större än 200, ser du sporadiska undernät frånkoppling.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Steg 6 Kontrollera extern vpn-enhets adress för VPN-enheten

- Om VPN-enhetens IP-adresss IP-adress inkluderas i definitionen för **lokal nätverksgateway** i Azure kan det uppstå sporadiska frånkopplingar.
- Enhetens externa gränssnitt måste vara direkt på Internet. Nat (Network Address Translation) eller brandväggen mellan Internet och enheten bör inte finnas någon NAT (Network Address Translation).
-  Om du konfigurerar brandväggskluster för att ha en virtuell IP måste du bryta klustret och exponera VPN-enheten direkt till ett offentligt gränssnitt som gatewayen kan samverka med.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Steg 7 Kontrollera om den lokala VPN-enheten har perfect forward secrecy aktiverat

Funktionen **Perfect Forward Secrecy** kan orsaka problem med frånkopplingen. Om VPN-enheten har **Perfect forward Secrecy** aktiverat inaktiverar du funktionen. Uppdatera sedan [IPsec-principen för virtuell nätverksgateway](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurera IPsec/IKE-principen för VPN-anslutningar från plats till plats](vpn-gateway-ipsecikepolicy-rm-powershell.md)

