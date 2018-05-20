---
title: Felsöka Azure plats-till-plats VPN kopplar från periodvis | Microsoft Docs
description: Lär dig att felsöka problemet plats-till-plats VPN-anslutningen frånkopplad regelbundet.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c827469080195054d4ff70ab72fc123365a73df
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Felsöka: Azure plats-till-plats-VPN kopplar från periodvis

Du kan få problem att en ny eller befintlig Microsoft Azure-plats-till-plats VPN-anslutning inte är stabilt eller kopplar från regelbundet. Den här artikeln innehåller felsökning som hjälper dig att identifiera och åtgärda orsaken till problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="prerequisite-step"></a>Innan du börjar:

Kontrollera vilken typ av Azure virtuella nätverkets gateway:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Kontrollera den **översikt** sida av den virtuella nätverksgatewayen för informationen.
    
    ![Översikt över gatewayen](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1 Kontrollera om lokala VPN-enhet har verifierats

1. Kontrollera om du använder en [verifiera VPN-enhet och operativsystemversion](vpn-gateway-about-vpn-devices.md#devicetable). Om VPN-enheten inte har verifierats, kan du behöva kontakta tillverkaren av enheten för att se om det finns några kompatibilitetsproblem.
2. Kontrollera att VPN-enheten är korrekt konfigurerad. Mer information finns i [redigera enheten configuration exempel](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Steg 2 Kontrollera inställningarna för säkerhetsassociation (för gruppolicy-baserad Azure virtuella nätverksgatewayer)

1. Se till att det virtuella nätverket, undernät och områden i den **lokal nätverksgateway** definition i Microsoft Azure är samma som konfigurationen på den lokala VPN-enheten.
2. Kontrollera att säkerhetsassociation stämmer.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Steg 3-kontroll för användardefinierade vägar eller Nätverkssäkerhetsgrupper på Gateway-undernät

En användardefinierad väg på gateway-undernätet kan begränsa viss trafik och tillåta andra trafik. Detta gör det visas att VPN-anslutningen är instabilt för viss trafik och bra för andra. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Steg 4 Kontrollera ”en VPN-tunneln undernät par” (för principbaserad virtuella nätverks-gateway)

Se till att den lokala VPN-enheten har **en VPN-tunnel undernät par** för principbaserad virtuella nätverksgatewayer.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Steg 5 Kontrollera säkerhet Association begränsningen (för principbaserad virtuella nätverksgatewayer)

Policy-baserad virtuell nätverksgateway har högst 200 undernät säkerhetsassociation par. Om antalet virtuella Azure-nätverket undernät multiplicerat med antalet lokala undernät är större än 200 ser du sporadiska undernät som kopplar från.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Steg 6 Kontrollera lokalt externa gränssnittet för VPN-enhetsadress

- Om IP-adressen för VPN-enhet för Internetuppkopplad ingår i den **lokal nätverksgateway** definition i Azure, sporadiska frånkopplingar kan uppstå.
- Enhetens externa gränssnittet måste vara direkt på Internet. Det bör finnas några NAT (Network Address Translation) eller en brandvägg mellan Internet och enheten.
-  Om du konfigurerar brandväggen kluster om du vill att en virtuell IP-adress, måste du bryta klustret och exponera VPN-enhet direkt till ett offentligt gränssnitt som gatewayen kan samverka med.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Steg 7 Kontrollera om lokala VPN-enhet har Perfect Forward Secrecy aktiverad

Den **Perfect Forward Secrecy** funktion kan orsaka frånkoppling problem. Om VPN-enhet har **perfekt forward Secrecy** aktiverat, inaktiveras funktionen. Sedan [uppdatera den virtuella nätverksgatewayen IPsec-princip](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurera IPsec/IKE-princip för plats-till-plats VPN-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md)

