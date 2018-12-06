---
title: Felsöka Azure plats-till-plats VPN kopplas från periodvis | Microsoft Docs
description: Lär dig mer om att felsöka problemet plats-till-plats-VPN-anslutningen frånkopplad regelbundet.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 2fdd82c2f0c96b3bd20231911bb88cf54c172931
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958022"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Felsökning: Azure plats-till-plats-VPN kopplas från periodvis

Du kan uppleva problem om en ny eller befintlig Microsoft Azure-plats-till-plats VPN-anslutning är stabil eller kopplar bort regelbundet. Den här artikeln innehåller felsökning för att hjälpa dig att identifiera och åtgärda orsaken till problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="prerequisite-step"></a>Innan du börjar:

Kontrollera vilken typ av gateway för virtuellt Azure-nätverk:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Kontrollera den **översikt** för den virtuella nätverksgatewayen för informationen.
    
    ![Översikt över gatewayen](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1 Kontrollera om den lokala VPN-enheten har verifierats

1. Kontrollera om du använder en [verifierade VPN-enhet- och operativsystemets version](vpn-gateway-about-vpn-devices.md#devicetable). Om VPN-enheten inte har verifierats kan behöva du kontakta enhetstillverkaren för att se om det finns några kompatibilitetsproblem.
2. Kontrollera att VPN-enheten är korrekt konfigurerad. Mer information finns i [redigera enhetens Konfigurationsexempel](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Steg 2 Kontrollera inställningarna för säkerhetsassociation (för principbaserad Azures virtuella nätverksgatewayar)

1. Se till att det virtuella nätverket, undernät och adressintervall i den **lokal nätverksgateway** definition i Microsoft Azure är samma som konfigurationen på den lokala VPN-enheten.
2. Kontrollera att det matchar säkerhetsassociation-inställningar.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Steg 3 söker efter användardefinierade vägar eller Nätverkssäkerhetsgrupper på Gateway-undernät

En användardefinierad väg på gateway-undernätet kan vara att begränsa vissa trafik och så att andra trafik. Detta gör det visas att VPN-anslutningen är instabilt för en del trafik och bra för andra. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Steg 4 Kontrollera ”en VPN-tunneln undernät par” (för principbaserade virtuella nätverksgatewayer)

Se till att den lokala VPN-enheten är inställda att ha **en VPN-tunnel undernät par** för principbaserade virtuella nätverksgatewayer.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Steg 5-kontroll för begränsning av Security Association (för principbaserade virtuella nätverksgatewayer)

Den principbaserade virtuella nätverksgatewayen har högst 200 undernät säkerhetsassociation par. Om antalet Azure-nätverk undernät multiplicerat gånger efter antal lokala undernät är större än 200 ser du sporadiska undernät som kopplar från.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Steg 6 Kontrollera lokala VPN-enhetens externa gränssnittet adress

- Om den Internetuppkopplade IP-adressen för VPN-enheten som ingår i den **lokal nätverksgateway** definition i Azure kan du uppleva sporadiska frånkopplingar.
- Enhetens externa gränssnittet måste vara direkt på Internet. Det bör finnas några Network Address Translation (NAT) eller en brandvägg mellan Internet och enheten.
-  Om du konfigurerar brandväggen klustring för att du har en virtuell IP-adress, måste du bryta klustret och exponera den VPN-installationen direkt till ett offentligt gränssnitt som gatewayen kan användas med.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Steg 7 Kontrollera om den lokala VPN-enheten har Perfect Forward Secrecy aktiverat

Den **Perfect Forward Secrecy** funktionen kan orsaka problem för frånkoppling. Om VPN-enhet har **förbättrar forward Secrecy** aktiverat, inaktiveras funktionen. Sedan [uppdatera den virtuella nätverksgatewayen IPsec-princip](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurera IPsec/IKE-princip för plats-till-plats-VPN-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md)

