---
title: 'Fjärrarbete med P2S: Azure VPN Gateway'
description: På den här sidan beskrivs hur du kan utnyttja Azure Bastion för att fjärrstyra arbetet på grund av COVID-19-pandemin.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337112"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Fjärrarbete med Azure VPN Gateway Point-to-site

>[!NOTE]
>I den här artikeln beskrivs hur du kan utnyttja Azure VPN Gateway, Azure, Microsoft-nätverket och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du står inför på grund av COVID-19-krisen.
>

I den här artikeln beskrivs de alternativ som är tillgängliga för organisationer för att konfigurera fjärråtkomst för sina användare eller för att komplettera sina befintliga lösningar med ytterligare kapacitet under COVID-19-epidemin.

Azure point-to-site-lösningen är molnbaserad och kan etableras snabbt för att tillgodose den ökade efterfrågan hos användare att arbeta hemifrån. Den kan skalas upp enkelt och stängas av lika enkelt och snabbt när den ökade kapaciteten inte behövs längre.

## <a name="about-point-to-site-vpn"></a>Om VPN från punkt till plats

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är användbar för distansarbetare som vill ansluta till Azure-virtuella nätverk eller lokala datacenter från en fjärrplats, till exempel hemifrån eller en konferens. I den här artikeln beskrivs hur du gör det möjligt för användare att arbeta på distans baserat på olika scenarier.

Tabellen nedan visar klientoperativsystemen och de autentiseringsalternativ som är tillgängliga för dem. Det skulle vara bra att välja autentiseringsmetoden baserat på klientoperativsystemet som redan används. Välj till exempel OpenVPN med certifikatbaserad autentisering om du har en blandning av klientoperativsystem som behöver ansluta. Observera också att punkt-till-plats-VPN endast stöds på ruttbaserade VPN-gateways.

![punkt-till-plats](./media/working-remotely-support/ostable.png "Operativsystem")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>Scenario 1 - Användare behöver endast åtkomst till resurser i Azure

I det här fallet behöver fjärranvändarna bara komma åt resurser som finns i Azure.

![punkt-till-plats](./media/working-remotely-support/scenario1.png "Scanario 1")

På en hög nivå krävs följande steg för att användarna ska kunna ansluta till Azure-resurser på ett säkert sätt:

1. Skapa en virtuell nätverksgateway (om det inte finns någon sådan)
2. Konfigurera punkt-till-plats-VPN på gatewayen
    3. [Följ den här länken](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)för certifikatautentisering .
    2.  [För OpenVPN följer du den här länken](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn).
    3.  [För Azure AD-autentisering följer du den här länken](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant).
    4.  [Om du vill felsöka point-to-site-anslutningar följer du den här länken](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems).
3. Hämta och distribuera VPN-klientkonfigurationen
4. Distribuera certifikaten (om certifikatautentisering är markerad) till klienterna
5. Ansluta till Azure VPN

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>Scenario 2 – Användare behöver åtkomst till resurser i Azure- och/eller on-prem-resurser

I det här fallet måste fjärranvändarna komma åt resurser som finns i Azure och i lokala datacenter.

![punkt-till-plats](./media/working-remotely-support/scenario2.png "Scenario 2")

På en hög nivå krävs följande steg för att användarna ska kunna ansluta till Azure-resurser på ett säkert sätt:

1. Skapa en virtuell nätverksgateway (om det inte finns någon sådan)
2. Konfigurera punkt-till-plats-VPN på gatewayen (se scenario 1 ovan)
3. Konfigurera tunnel från plats till plats på Azure Virtual Network Gateway med BGP aktiverat
4. Konfigurera lokal enhet för anslutning till Azure Virtual Network Gateway
5. Hämta point-to-site-profilen från Azure-portalen och distribuera till klienter

[Följ den här länken om du vill lära dig hur du konfigurerar en VPN-tunnel från plats till plats](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Vanliga frågor och svar om inbyggd Azure-certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Vanliga frågor och svar om RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Efterföljande moment

* [Konfigurera en P2S-anslutning - Azure AD-autentisering](openvpn-azure-ad-tenant.md)

* [Konfigurera en P2S-anslutning - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azure-autentisering av inbyggt certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**