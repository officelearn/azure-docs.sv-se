---
title: Om Azure punkt-till-plats VPN-anslutningar | Microsoft Docs
description: "Den här artikeln hjälper dig att förstå punkt-till-plats-anslutningar och hjälper dig att bestämma vilka P2S VPN gateway-autentiseringstyp som ska användas."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2018
ms.author: cherylmc
ms.openlocfilehash: 74cfa8f54c52463ac0b42c5cc6abab7b0366ac29
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="about-point-to-site-vpn"></a>Om punkt-till-plats VPN

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. Den här artikeln gäller distributionsmodellen i Resource Manager.

## <a name="protocol"></a>Vilket protokoll använder P2S?

Punkt-till-plats-VPN kan använda någon av följande protokoll:

* Secure Socket Tunneling Protocol (SSTP), ett SSL-baserad VPN-protokoll. En SSL VPN-lösning kan genom brandväggar, eftersom de flesta brandväggar öppna TCP-port 443, som använder SSL. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

Om du har en blandad klientmiljö som består av Windows och Mac-enheter kan du konfigurera både SSTP och IKEv2.

>[!NOTE]
>IKEv2 för P2S är tillgängligt för Resource Manager-distributionsmodellen. Det är inte tillgänglig för den klassiska distributionsmodellen.
>

## <a name="authentication"></a>Hur autentiseras P2S VPN-klienter?

Innan Azure tar emot en P2S VPN-anslutning, måste användaren först autentiseras. Det finns två metoder som Azure erbjuder att autentisera en anslutande användaren.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autentisera med hjälp av inbyggda Azure certifikatautentisering

När du använder den interna Azure certifikatautentiseringen används ett klientcertifikat som finns på enheten för att autentisera den anslutande användaren. Klientcertifikat genereras från ett betrott rotcertifikat och sedan installeras på varje klientdator. Du kan använda ett rotcertifikat som skapades med en företagslösning eller du kan generera ett självsignerat certifikat.

Verifieringen av klientcertifikatet utförs av VPN-gateway och sker under etablering av P2S VPN-anslutningen. Rotcertifikatet krävs för validering och måste överföras till Azure. 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autentisera med hjälp av Active Directory (AD) domänserver

AD-domänautentisering tillåter användare att ansluta till Azure med sina domänautentiseringsuppgifter för organisationen. Det krävs en RADIUS-server som kan integreras med AD-server. Organisationer kan också använda deras befintliga RADIUS-distribution.   
 RADIUS-server kan vara distribuerade lokalt eller i ditt Azure VNET. Azure VPN-Gateway fungerar som en och vidarebefordrar autentiseringsmeddelanden fram och tillbaka mellan RADIUS-servern och enheten som ansluter under autentiseringen. Gateway-anslutningsbarhet till RADIUS-servern är så viktigt. Om RADIUS-server finns lokalt, måste det anges för reachability med en S2S VPN-anslutning från Azure till den lokala platsen.  
 RADIUS-servern kan även integreras med AD-Certifikattjänster. På så sätt kan du använda RADIUS-server och dina företagsdistribution certifikat för P2S certifikatautentisering som ett alternativ till Azure certifikatautentisering. Fördelen är att du inte behöver överföra rotcertifikat och återkallade certifikat till Azure.

En RADIUS-server kan också integreras med andra system för extern Identitetshantering. Detta öppnar gott om autentiseringsalternativ för P2S-VPN, inklusive alternativ för multi-Factor.

! [punkt-till-plats]] (./media/point-to-site-about/p2s.png ”punkt-till-plats”)

### <a name="configuration-requirements-for-client-devices"></a>Konfigurationskrav för klientenheter

Användare kan du använda de inbyggda VPN-klienterna på Windows och Mac-enheter för P2S. Azure tillhandahåller en VPN-klient configuration zip-fil som innehåller inställningar som krävs för dessa interna klienter att ansluta till Azure.

* VPN-klientkonfiguration består av ett installationspaket som användarna installera på sina enheter för Windows-enheter.
* För Mac-enheter består det av den mobileconfig-fil som användare installera på sina enheter.

Zip-filen innehåller också värdena för vissa av de viktiga inställningarna på Azure-sidan som du kan använda för att skapa din egen profil för dessa enheter. Vissa värden innehåller VPN-gateway-adress, konfigurerade tunneltyper, vägar och rotcertifikatet för gateway-verifiering.

### <a name="gwsku"></a>Vilka Gateway-SKU: er stöd P2S VPN?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte en garanterad genomströmning på grund av villkor för internet-trafik och dina program beteenden.
* Information om priser finns på sidan priser 
* SLA (serviceavtal) information finns på sidan SLA.

>[!NOTE]
>Grundläggande SKU: N har inte stöd för IKEv2 eller RADIUS-autentisering.
>

## <a name="configure"></a>Hur konfigurerar jag en P2S-anslutning

En P2S-konfigurationen kräver ganska några särskilda åtgärder. I följande artiklar innehåller stegen för att vägleder dig genom P2S konfiguration och länkar till Konfigurera enheter för VPN-klienten:

* [Konfigurera en anslutning för P2S - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en anslutning för P2S - Azure inbyggd certifikatautentisering](vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="faqcert"></a>Vanliga frågor och svar för interna Azure certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Vanliga frågor och svar för RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en anslutning för P2S - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en anslutning för P2S - Azure inbyggd certifikatautentisering](vpn-gateway-howto-point-to-site-rm-ps.md)