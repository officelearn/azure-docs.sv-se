---
title: Om kryptografiska krav och Azure VPN-gatewayer | Microsoft Docs
description: "Den här artikeln beskrivs kraven för kryptografiska och Azure VPN-gatewayer"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: d2f3da47f1d4eebe1b81964790ff6612dd78155d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Om kryptografiska krav och Azure VPN-gatewayer

Den här artikeln beskrivs hur du kan konfigurera Azure VPN-gatewayer för att uppfylla dina kryptografiska krav för både anslutningar mellan lokala S2S VPN-tunnlar och VNet-till-VNet-anslutningar i Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Om IPsec och IKE principparametrar för Azure VPN-gatewayer
IPsec och IKE protokollet som standard stöder en mängd olika krypteringsalgoritmer i olika kombinationer. Om kunder inte begär en specifik kombination av kryptografiska algoritmer och parametrar, Använd Azure VPN-gatewayer för förslag som standard. Standard principen anger har valt att maximera samverkan med en mängd olika tredje parts VPN-enheter i standardkonfigurationerna. Därför kan inte principer och antalet förslag täcka alla möjliga kombinationer av tillgängliga kryptografiska algoritmer och viktiga fördelar.

Standardprincipen som angetts för Azure VPN-gateway finns i dokumentet: [om VPN-enheter och IPsec/IKE-parametrar för plats-till-plats VPN-Gateway-anslutningar](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografiska krav
För kommunikation som kräver särskilda kryptografiska algoritmer eller parametrar, kan vanligtvis på grund av kompatibilitet eller säkerhetskrav, kunder nu konfigurera sina Azure VPN-gatewayer för att använda en anpassad IPsec/IKE-princip med specifika kryptografiska algoritmer och viktiga styrkor i stället Azure standard principen anger.

Till exempel använda IKEv2 huvudlägesprinciper för Azure VPN-gatewayer endast Diffie-Hellman-Grupp2 (1024 bitar) medan kunder kan behöva ange starkare grupper som ska användas i IKE, till exempel grupp 14 (2048-bitars), grupp 24 (2048-bitars MODP grupp) eller ECP (elliptic kurva grupper) 256 eller 384 bitar (gruppen 19 och grupp 20 respektive). Samma krav gäller för IPSec-snabbläge samt principer.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Anpassad IPsec/IKE-princip med Azure VPN-gatewayer
Azure VPN-gatewayer har nu stöd för varje anslutning, anpassad IPsec/IKE-princip. För en plats-till-plats eller VNet-till-VNet-anslutningen, kan du välja en specifik kombination av kryptografiska algoritmer för IPSec- och IKE med den önskade nyckellängd som visas i följande exempel:

![IPSec-princip](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Du kan skapa en princip för IPsec/IKE och gäller för en ny eller befintlig anslutning. 

### <a name="workflow"></a>Arbetsflöde

1. Skapa virtuella nätverk, VPN-gatewayer och lokala nätverksgatewayer för anslutningstopologin enligt beskrivningen i andra instruktionsdokument
2. Skapa en IPsec/IKE-princip
3. Du kan tillämpa principen när du skapar en S2S eller VNet-till-VNet-anslutning
4. Om anslutningen redan har skapats kan du tillämpa eller uppdatera principen till en befintlig anslutning


## <a name="ipsecike-policy-faq"></a>IPSec-/ princip vanliga frågor och svar

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Nästa steg
Se [konfigurera IPSec-/ princip](vpn-gateway-ipsecikepolicy-rm-powershell.md) stegvisa instruktioner om hur du konfigurerar anpassade IPsec/IKE-principen på en anslutning.

Se även [ansluta flera principbaserad VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) vill veta mer om alternativet UsePolicyBasedTrafficSelectors.