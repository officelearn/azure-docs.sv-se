---
title: 'Azure VPN Gateway: Cryptographic requirements'
description: This article discusses cryptographic requirements and Azure VPN gateways
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902828"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>About cryptographic requirements and Azure VPN gateways

This article discusses how you can configure Azure VPN gateways to satisfy your cryptographic requirements for both cross-premises S2S VPN tunnels and VNet-to-VNet connections within Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>About IKEv1 and IKEv2 for Azure VPN connections

Traditionally we allowed IKEv1 connections for Basic SKUs only and allowed IKEv2 connections for all VPN gateway SKUs other than Basic SKUs. The Basic SKUs allow only 1 connection and along with other limitations such as performance, customers using legacy devices that support only IKEv1 protocols were having limited experience. In order to enhance the experience of customers using IKEv1 protocols, we are now allowing IKEv1 connections for all of the VPN gateway SKUs, except Basic SKU. For more information, see [VPN Gateway SKUs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Azure VPN Gateway IKEv1 and IKEv2 connections](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

When IKEv1 and IKEv2 connections are applied to the same VPN gateway, the transit between these two connections is auto-enabled.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Om IPsec-och IKE-principinställningar för Azure VPN-gatewayer

IPsec-och IKE-protokoll standarden stöder en mängd olika krypteringsalgoritmer i olika kombinationer. Om du inte begär en speciell kombination av krypteringsalgoritmer och parametrar, använder Azure VPN-gatewayer en uppsättning standard förslag. Standard princip uppsättningarna valdes för att maximera samverkan med en mängd olika VPN-enheter från tredje part i standardkonfigurationerna. Därför kan principerna och antalet förslag inte avse alla möjliga kombinationer av tillgängliga krypteringsalgoritmer och viktiga styrkor.

Standard principen som har angetts för Azure VPN-gatewayen visas i artikeln: [om VPN-enheter och IPSec/IKE-parametrar för plats-till-plats-VPN gateway-anslutningar](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Krypterings krav

För kommunikation som kräver särskilda kryptografiska algoritmer eller parametrar, vanligt vis på grund av efterlevnad eller säkerhets krav, kan du nu konfigurera deras Azure VPN-gatewayer så att de använder en anpassad IPsec/IKE-princip med särskilda krypteringsalgoritmer och viktiga styrkor, i stället för Azures standard princip uppsättningar.

IKEv2 huvud läges principer för Azure VPN-gatewayer använder till exempel bara Diffie-Hellman-grupp 2 (1024 bitar), men du kan behöva ange starkare grupper som ska användas i IKE, till exempel grupp 14 (2048-bitars), grupp 24 (2048-bitars MODP-grupp) eller ECP (Elliptic-kurva grupper) 256 eller 384 bit (grupp 19 respektive grupp 20 respektive). Liknande krav gäller även IPsec-principer för snabb läge.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Anpassad IPsec/IKE-princip med Azure VPN-gatewayer

Azure VPN-gatewayer stöder nu per anslutning, anpassad IPsec/IKE-princip. För en plats-till-plats-eller VNet-till-VNet-anslutning kan du välja en speciell kombination av krypteringsalgoritmer för IPsec och IKE med önskad nyckel längd, som du ser i följande exempel:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

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
