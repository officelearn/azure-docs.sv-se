---
title: 'Azure VPN Gateway: Kryptografiska krav'
description: I den här artikeln beskrivs kryptografiska krav och Azure VPN-gateways
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902828"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Om kryptografiska krav och Azure VPN-gateways

I den här artikeln beskrivs hur du kan konfigurera Azure VPN-gateways för att uppfylla dina kryptografiska krav för både korslokala S2S VPN-tunnlar och VNet-till-VNet-anslutningar i Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Om IKEv1- och IKEv2 för Azure VPN-anslutningar

Traditionellt tillät vi endast IKEv1-anslutningar för grundläggande SKU:er och tillät IKEv2-anslutningar för alla ANDRA VPN-gateway-SKU:er än grundläggande SKU:er. Basic SKU:er tillåter endast 1 anslutning och tillsammans med andra begränsningar, till exempel prestanda, kunder som använder äldre enheter som endast stöder IKEv1-protokoll hade begränsad erfarenhet. För att förbättra upplevelsen för kunder som använder IKEv1-protokoll tillåter vi nu IKEv1-anslutningar för alla SKU:er för VPN-gateway, förutom Basic SKU. Mer information finns i [SKU:er för VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Azure VPN Gateway IKEv1- och IKEv2-anslutningar](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

När IKEv1- och IKEv2-anslutningar tillämpas på samma VPN-gateway aktiveras överföringen mellan dessa två anslutningar automatiskt.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Om IPsec- och IKE-principparametrar för Azure VPN-gateways

IPsec- och IKE-protokollstandard stöder ett brett utbud av kryptografiska algoritmer i olika kombinationer. Om du inte begär en specifik kombination av kryptografiska algoritmer och parametrar använder Azure VPN-gateways en uppsättning standardförslag. Standardprincipuppsättningarna valdes för att maximera interoperabiliteten med ett brett utbud av VPN-enheter från tredje part i standardkonfigurationer. Därför kan policyerna och antalet förslag inte omfatta alla möjliga kombinationer av tillgängliga kryptografiska algoritmer och nyckelstyrkor.

Standardprincipuppsättningen för Azure VPN-gateway visas i artikeln: [Om VPN-enheter och IPsec/IKE-parametrar för plats-till-plats VPN Gateway-anslutningar](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografiska krav

För kommunikation som kräver specifika kryptografiska algoritmer eller parametrar, vanligtvis på grund av efterlevnads- eller säkerhetskrav, kan du nu konfigurera deras Azure VPN-gateways för att använda en anpassad IPsec/IKE-princip med specifika kryptografiska algoritmer och nyckelstyrkor, i stället för Azure-standardprincipuppsättningarna.

IKEv2-huvudlägesprinciperna för Azure VPN-gateways använder till exempel endast Diffie-Hellman Group 2 (1024 bitar), medan du kan behöva ange starkare grupper som ska användas i IKE, till exempel Grupp 14 (2048-bitars), Grupp 24 (2048-bitars MODP-grupp) eller ECP (elliptisk kurva grupper) 256 respektive 384 bitar (grupp 19 respektive grupp 20). Liknande krav gäller även för IPsec-snabblägesprinciper.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Anpassad IPsec/IKE-princip med Azure VPN-gateways

Azure VPN-gateways stöder nu per anslutning, anpassad IPsec/IKE-princip. För en site-to-site- eller VNet-till-VNet-anslutning kan du välja en specifik kombination av kryptografiska algoritmer för IPsec och IKE med önskad nyckelstyrka, som visas i följande exempel:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Du kan skapa en IPsec/IKE-princip och ansöka om en ny eller befintlig anslutning.

### <a name="workflow"></a>Arbetsflöde

1. Skapa virtuella nätverk, VPN-gateways eller lokala nätverksgateways för anslutningstopologin enligt beskrivningen i andra instruktioner
2. Skapa en IPsec/IKE-princip
3. Du kan tillämpa principen när du skapar en S2S- eller VNet-till-VNet-anslutning
4. Om anslutningen redan har skapats kan du tillämpa eller uppdatera principen till en befintlig anslutning

## <a name="ipsecike-policy-faq"></a>Vanliga frågor och svar om IPsec/IKE-principen

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Nästa steg

Se [Konfigurera IPsec/IKE-principen](vpn-gateway-ipsecikepolicy-rm-powershell.md) för steg-för-steg-instruktioner om hur du konfigurerar anpassad IPsec/IKE-princip på en anslutning.

Se även [Anslut flera principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) om du vill veta mer om alternativet UsePolicyBasedTrafficSelectors.
