---
title: Om Azure punkt-till-plats-VPN-anslutningar | Microsoft Docs
description: Den här artikeln hjälper dig att förstå punkt-till-plats-anslutningar och hjälper dig att bestämma vilken P2S VPN-gatewaytyp av autentisering du använder.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8cdc80e8e4f8d3feb36ca82740d5610e60716ec6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003367"
---
# <a name="about-point-to-site-vpn"></a>Om punkt-till-plats-VPN

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. Den här artikeln gäller distributionsmodellen i Resource Manager.

## <a name="protocol"></a>Vilket protokoll använder P2S?

Punkt-till-plats-VPN kan använda något av följande protokoll:

* Secure Socket Tunneling Protocol (SSTP), en SSL-baserad VPN-protokoll. En SSL VPN-lösning kan ta sig förbi brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443, som använder SSL. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

Om du har en blandad klientmiljö med Windows och Mac-enheter kan du konfigurera både SSTP och IKEv2.

>[!NOTE]
>IKEv2 för P2S är tillgänglig för endast distributionsmodellen Resource Manager. Det är inte tillgänglig för den klassiska distributionsmodellen.
>

## <a name="authentication"></a>Hur autentiseras P2S VPN-klienter?

Innan Azure tar emot en P2S VPN-anslutning, måste användaren först autentiseras. Det finns två sätt som Azure erbjuder att autentisera en anslutande användaren.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autentisera med hjälp av Azures interna certifikatautentisering

När du använder Azures interna certifikatautentisering används ett klientcertifikat som finns på enheten för att autentisera den anslutande användaren. Klientcertifikat genereras från ett betrott rotcertifikat och sedan installeras på varje klientdator. Du kan använda ett rotcertifikat som genererades med en företagslösning eller du kan generera ett självsignerat certifikat.

Valideringen av klientcertifikatet kan utföras av VPN-gatewayen och sker vid upprättandet av P2S VPN-anslutningen. Rotcertifikatet krävs för verifieringen och måste laddas upp till Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autentisera med hjälp av Active Directory (AD) domänserver

AD-Domain-autentisering kan användarna ansluta till Azure med hjälp av domänautentiseringsuppgifterna organisation. Det krävs en RADIUS-server som är integrerad med AD-servern. Organisationer kan även använda sina befintliga RADIUS-distributionen.   
  RADIUS-servern kan distribueras lokalt eller i ditt Azure VNET. Azure VPN Gateway fungerar som en och vidarebefordrar autentiseringsmeddelanden fram och tillbaka mellan RADIUS-servern och enheten som ansluter under autentiseringen. Gateway-nätverksåtkomst till RADIUS-servern är så viktigt. Om RADIUS-servern är tillgänglig lokalt, måste en S2S VPN-anslutning från Azure till den lokala platsen anges för nätverksåtkomst.  
  RADIUS-servern kan också integreras med AD-Certifikattjänster. På så sätt kan du använda RADIUS-servern och distributionen av enterprise-certifikat för P2S-certifikatautentisering som ett alternativ till Azure-certifikatautentisering. Fördelen är att du inte behöver överföra rotcertifikat och återkallade certifikat till Azure.

En RADIUS-server kan också integreras med andra system för extern Identitetshantering. Detta öppnar massor av alternativ för autentisering för P2S VPN, inklusive alternativ för multifaktorautentisering.

![punkt-till-plats](./media/point-to-site-about/p2s.png "punkt-till-plats")

## <a name="what-are-the-client-configuration-requirements"></a>Vilka är konfigurationskrav som klienten?

>[!NOTE]
>För Windows-klienter, måste du ha administratörsbehörighet på klientenheten för att initiera VPN-anslutning från klientenheten till Azure.
>

Användare kan du använda de inbyggda VPN-klienterna på Windows och Mac-enheter för P2S. Azure tillhandahåller en VPN-klient configuration zip-fil som innehåller inställningar som krävs för dessa interna klienter att ansluta till Azure.

* VPN-klientkonfiguration består av en installer-paketet som användarna installerar på sina enheter för Windows-enheter.
* För Mac-enheter består den av den mobileconfig-fil som användarna installerar på sina enheter.

Zip-filen innehåller också värdena för vissa viktiga inställningar på Azure-sidan som du kan använda för att skapa din egen profil för dessa enheter. Några av värdena är adressen för VPN-gateway, konfigurerade tunneltyper, vägar och rotcertifikatet för gateway-verifiering.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Vilken Gateway-SKU: er stöd för P2S VPN?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte garanterad genomströmning på grund av villkor för internet-trafik och dina program.
* Information om priser finns på sidan med priser 
* Information om SLA (serviceavtal) finns på SLA-sida.

>[!NOTE]
>Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering.
>

## <a name="configure"></a>Hur konfigurerar jag en P2S-anslutning?

En P2S-konfiguration kräver ett ganska stort antal specifika steg. I följande artiklar innehåller stegen för att vägleder dig genom P2S-konfiguration och länkar för att konfigurera enheter för VPN-klienten:

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azures interna certifikatautentisering](vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="faqcert"></a>Vanliga frågor och svar för Azures interna certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Vanliga frågor och svar för RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azures interna certifikatautentisering](vpn-gateway-howto-point-to-site-rm-ps.md)
