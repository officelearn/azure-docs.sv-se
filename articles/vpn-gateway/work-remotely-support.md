---
title: 'Fjärrarbete med P2S: Azure VPN Gateway'
description: På den här sidan beskrivs hur du kan utnyttja VPN Gateway för att möjliggöra fjärrarbete på grund av COVID-19-pandemin.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 2d07a13c654f30e48c37d2e8d3e801166e26f4f4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886593"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Fjärrarbete med Azure VPN Gateway Point-to-site

>[!NOTE]
>I den här artikeln beskrivs hur du kan utnyttja Azure VPN Gateway, Azure, Microsoft-nätverket och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du står inför på grund av COVID-19-krisen.
>

I den här artikeln beskrivs de alternativ som är tillgängliga för organisationer för att konfigurera fjärråtkomst för sina användare eller för att komplettera sina befintliga lösningar med ytterligare kapacitet under COVID-19-epidemin.

Azure point-to-site-lösningen är molnbaserad och kan etableras snabbt för att tillgodose den ökade efterfrågan hos användare att arbeta hemifrån. Den kan skalas upp enkelt och stängas av lika enkelt och snabbt när den ökade kapaciteten inte behövs längre.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Om VPN från punkt till plats

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är användbar för distansarbetare som vill ansluta till Azure-virtuella nätverk eller lokala datacenter från en fjärrplats, till exempel hemifrån eller en konferens. I den här artikeln beskrivs hur du gör det möjligt för användare att arbeta på distans baserat på olika scenarier.

Tabellen nedan visar klientoperativsystemen och de autentiseringsalternativ som är tillgängliga för dem. Det skulle vara bra att välja autentiseringsmetoden baserat på klientoperativsystemet som redan används. Välj till exempel OpenVPN med certifikatbaserad autentisering om du har en blandning av klientoperativsystem som behöver ansluta. Observera också att punkt-till-plats-VPN endast stöds på ruttbaserade VPN-gateways.

![punkt-till-plats](./media/working-remotely-support/ostable.png "Operativsystem")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenario 1 - Användare behöver endast åtkomst till resurser i Azure

I det här fallet behöver fjärranvändarna bara komma åt resurser som finns i Azure.

![punkt-till-plats](./media/working-remotely-support/scenario1.png "Scenario 1")

På en hög nivå krävs följande steg för att användarna ska kunna ansluta till Azure-resurser på ett säkert sätt:

1. Skapa en virtuell nätverksgateway (om det inte finns någon).
2. Konfigurera punkt-till-plats-VPN på gatewayen.

   * Följ [den här länken](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)för certifikatautentisering .
   * För OpenVPN följer du [den här länken](vpn-gateway-howto-openvpn.md).
   * För Azure AD-autentisering följer du [den här länken](openvpn-azure-ad-tenant.md).
   * Om du vill felsöka point-to-site-anslutningar följer du [den här länken](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Ladda ned och distribuera VPN-klientkonfigurationen.
4. Distribuera certifikaten (om certifikatautentisering är markerat) till klienterna.
5. Anslut till Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenario 2 – Användare behöver åtkomst till resurser i Azure- och/eller on-prem-resurser

I det här fallet måste fjärranvändarna komma åt resurser som finns i Azure och i lokala datacenter.

![punkt-till-plats](./media/working-remotely-support/scenario2.png "Scenario 2")

På en hög nivå krävs följande steg för att användarna ska kunna ansluta till Azure-resurser på ett säkert sätt:

1. Skapa en virtuell nätverksgateway (om det inte finns någon).
2. Konfigurera punkt-till-plats-VPN på gatewayen (se [scenario 1](#scenario1)).
3. Konfigurera en plats-till-plats-tunnel på Azure-gatewayen för virtuella nätverk med BGP aktiverat.
4. Konfigurera den lokala enheten för att ansluta till Azure virtuell nätverksgateway.
5. Hämta point-to-site-profilen från Azure-portalen och distribuera till klienter

Mer information om hur du konfigurerar en VPN-tunnel från plats till plats finns i [den här länken](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Vanliga frågor och svar om inbyggd Azure-certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Vanliga frågor och svar om RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Efterföljande moment

* [Konfigurera en P2S-anslutning - Azure AD-autentisering](openvpn-azure-ad-tenant.md)

* [Konfigurera en P2S-anslutning - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azure-autentisering av inbyggt certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**