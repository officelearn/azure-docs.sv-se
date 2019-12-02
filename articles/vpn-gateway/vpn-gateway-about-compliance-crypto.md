---
title: 'Azure-VPN Gateway: kryptografiska krav'
description: Den här artikeln beskriver kryptografiska krav och Azure VPN-gatewayer
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/17/2019
ms.author: yushwang
ms.openlocfilehash: f1d30ef56350c3e1df796f20e3f9f39fdbc6428a
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666502"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Om kryptografiska krav och Azure VPN-gatewayer

Den här artikeln beskriver hur du kan konfigurera Azure VPN-gatewayer för att uppfylla dina kryptografiska krav för både lokala S2S VPN-tunnlar och VNet-till-VNet-anslutningar i Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Om IKEv1 och IKEv2 för Azure VPN-anslutningar

Traditionellt tillåtna endast IKEv1-anslutningar för grundläggande SKU: er och tillåtna IKEv2-anslutningar för alla VPN gateway-SKU: er som inte är grundläggande. De grundläggande SKU: erna tillåter endast 1 anslutning och tillsammans med andra begränsningar, till exempel prestanda, kunder som använder äldre enheter som endast stöder IKEv1-protokoll har begränsad erfarenhet. För att förbättra upplevelsen av kunder med IKEv1-protokoll tillåter vi nu IKEv1-anslutningar för alla VPN gateway-SKU: er, förutom Basic SKU. Mer information finns i [VPN gateway SKU: er](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Azure VPN Gateway IKEv1-och IKEv2-anslutningar](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

När IKEv1-och IKEv2-anslutningar används på samma VPN-gateway, är överföringen mellan dessa två anslutningar automatiskt aktive rad.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Om IPsec-och IKE-principinställningar för Azure VPN-gatewayer

IPsec-och IKE-protokoll standarden stöder en mängd olika krypteringsalgoritmer i olika kombinationer. Om du inte begär en speciell kombination av krypteringsalgoritmer och parametrar, använder Azure VPN-gatewayer en uppsättning standard förslag. Standard princip uppsättningarna valdes för att maximera samverkan med en mängd olika VPN-enheter från tredje part i standardkonfigurationerna. Därför kan principerna och antalet förslag inte avse alla möjliga kombinationer av tillgängliga krypteringsalgoritmer och viktiga styrkor.

Standard principen som har angetts för Azure VPN-gatewayen visas i artikeln: [om VPN-enheter och IPSec/IKE-parametrar för plats-till-plats-VPN gateway-anslutningar](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Krypterings krav

För kommunikation som kräver särskilda kryptografiska algoritmer eller parametrar, vanligt vis på grund av efterlevnad eller säkerhets krav, kan du nu konfigurera deras Azure VPN-gatewayer så att de använder en anpassad IPsec/IKE-princip med särskilda krypteringsalgoritmer och viktiga styrkor i stället för standard princip uppsättningarna i Azure.

IKEv2 huvud läges principer för Azure VPN-gatewayer använder till exempel bara Diffie-Hellman-grupp 2 (1024 bitar), men du kan behöva ange starkare grupper som ska användas i IKE, till exempel grupp 14 (2048-bitars), grupp 24 (2048-bitars MODP-grupp) eller ECP (Elliptic-kurva grupper) 256 eller 384 bit (grupp 19 respektive grupp 20 respektive). Liknande krav gäller även IPsec-principer för snabb läge.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Anpassad IPsec/IKE-princip med Azure VPN-gatewayer

Azure VPN-gatewayer stöder nu per anslutning, anpassad IPsec/IKE-princip. För en plats-till-plats-eller VNet-till-VNet-anslutning kan du välja en speciell kombination av krypteringsalgoritmer för IPsec och IKE med önskad nyckel längd, som du ser i följande exempel:

![IPSec-IKE – princip](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Du kan skapa en IPsec/IKE-princip och tillämpa den på en ny eller befintlig anslutning.

### <a name="workflow"></a>Arbetsflöde

1. Skapa virtuella nätverk, VPN-gatewayer eller lokala nätverksgateway för din anslutnings topologi enligt beskrivningen i andra instruktions dokument
2. Skapa en IPsec/IKE-princip
3. Du kan tillämpa principen när du skapar en S2S-eller VNet-till-VNet-anslutning
4. Om anslutningen redan har skapats kan du tillämpa eller uppdatera principen på en befintlig anslutning

## <a name="ipsecike-policy-faq"></a>Vanliga frågor och svar om IPsec/IKE-princip

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Nästa steg

Se [Konfigurera IPSec/IKE-princip](vpn-gateway-ipsecikepolicy-rm-powershell.md) för steg-för-steg-instruktioner om hur du konfigurerar anpassad IPsec/IKE-princip på en anslutning.

Se även [Anslut flera principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) om du vill veta mer om alternativet UsePolicyBasedTrafficSelectors.
