---
title: Om kryptografiska krav och Azure VPN-gatewayer | Microsoft Docs
description: Den här artikeln beskriver kryptografiska krav och Azure VPN-gatewayer
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 05/22/2017
ms.date: 10/01/2018
ms.author: v-jay
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648689"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Om kryptografiska krav och Azure VPN-gatewayer

Den här artikeln beskrivs hur du kan konfigurera Azure VPN-gatewayer för att uppfylla dina kryptografiska krav för både S2S VPN-tunnlar för flera platser och VNet-till-VNet-anslutningar i Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Om IPsec och IKE principparametrar för Azure VPN-gatewayer
IPsec och IKE protokollet som standard stöder en mängd olika kryptografiska algoritmer i olika kombinationer. Om kunderna inte begär en specifik kombination av kryptografiska algoritmer och parametrar, Använd en Azure VPN-gatewayer standard förslag. Uppsättningarna för standard-principen har valts för att maximera samverkan med ett brett utbud av tredje parts VPN-enheter på standardkonfigurationerna. Därför kan täcker principerna och antal förslag inte alla möjliga kombinationer av tillgängliga kryptografiska algoritmer och nyckellängder.

Standardprincipen för Azure VPN-gateway är anges i dokumentet: [Om VPN-enheter och IPsec/IKE-parametrar för anslutningar för plats-till-plats VPN-Gateway](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografiska krav
För kommunikation som kräver särskilda kryptografiska algoritmer eller parametrar, kan vanligtvis på grund av efterlevnad eller säkerhetskrav, kunder nu konfigurera sin Azure VPN-gatewayer för att använda en anpassad IPsec/IKE-princip med specifika kryptografiska algoritmer och nyckellängder i stället principuppsättningarna i Azure standard.

Till exempel använda IKEv2 huvudläge principerna för Azure VPN-gateways endast Diffie-Hellman-Grupp2 (1 024 bitar), medan kunder kan behöva ange starkare grupper som ska användas i IKE, till exempel gruppen 14 (2048-bitars), grupp 24 (2048-bitars MODP grupp) eller ECP (elliptic kurva grupper) 256 eller 384-bitars (gruppen 19 och 20 grupp, respektive). Samma krav som gäller för IPSec-snabbläge samt principer.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Anpassade IPsec/IKE-princip med Azure VPN-gatewayer
Azure VPN-gatewayer har nu stöd för varje anslutning, anpassad IPsec/IKE-princip. För en plats-till-plats eller VNet-till-VNet-anslutning, kan du välja en specifik kombination av kryptografiska algoritmer för IPsec och IKE med önskad nyckellängd som visas i följande exempel:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Du kan skapa en IPsec/IKE-princip och tillämpa till en ny eller befintlig anslutning. 

### <a name="workflow"></a>Arbetsflöde

1. Skapa virtuella nätverk, VPN-gatewayer och lokala nätverksgatewayer för anslutningstopologin enligt beskrivningen i andra instruktionsdokument
2. Skapa en IPsec/IKE-princip
3. Du kan tillämpa principen när du skapar en S2S- eller VNet-till-VNet-anslutning
4. Om anslutningen har redan skapats, kan du tillämpa eller uppdatera principen till en befintlig anslutning


## <a name="ipsecike-policy-faq"></a>Vanliga frågor och svar för IPsec/IKE-princip

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Nästa steg
Se [konfigurera IPsec/IKE-princip](vpn-gateway-ipsecikepolicy-rm-powershell.md) stegvisa instruktioner om hur du konfigurerar anpassade IPsec/IKE-principer på en anslutning.

Se även [ansluta flera principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) mer information om alternativet UsePolicyBasedTrafficSelectors.