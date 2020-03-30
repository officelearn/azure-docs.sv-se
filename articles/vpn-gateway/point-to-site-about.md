---
title: Om VPN-anslutningar för Azure Point-to-Site | VPN-gateway
description: Den här artikeln hjälper dig att förstå Point-to-Site-anslutningar och hjälper dig att avgöra vilken P2S VPN-gatewayautentiseringstyp som ska användas.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: cherylmc
ms.openlocfilehash: 78ed07560fdb15efb2de13c194549f5b433b775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500603"
---
# <a name="about-point-to-site-vpn"></a>Om VPN från punkt till plats

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. Den här artikeln gäller distributionsmodellen i Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Vilket protokoll använder P2S?

Point-to-site VPN kan använda något av följande protokoll:

* **OpenVPN® Protocol**, ett SSL/TLS-baserat VPN-protokoll. En SSL VPN-lösning kan penetrera brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443 utgående, som SSL använder. OpenVPN kan användas för att ansluta från Android, iOS (version 11.0 och högre), Windows, Linux och Mac-enheter (OSX-versioner 10.13 och senare).

* Secure Socket Tunneling Protocol (SSTP), ett eget SSL-baserat VPN-protokoll. En SSL VPN-lösning kan penetrera brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443 utgående, som SSL använder. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).


>[!NOTE]
>IKEv2 och OpenVPN för P2S är endast tillgängliga för resurshanterarens distributionsmodell. De är inte tillgängliga för den klassiska distributionsmodellen.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Hur autentiseras P2S VPN-klienter?

Innan Azure accepterar en P2S VPN-anslutning måste användaren autentiseras först. Det finns två mekanismer som Azure erbjuder för att autentisera en anslutande användare.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autentisera med inbyggd Azure-certifikatautentisering

När du använder den inbyggda Azure-certifikatautentiseringen används ett klientcertifikat som finns på enheten för att autentisera den anslutande användaren. Klientcertifikat genereras från ett betrott rotcertifikat och installeras sedan på varje klientdator. Du kan använda ett rotcertifikat som har genererats med en Enterprise-lösning eller skapa ett självsignerat certifikat.

Valideringen av klientcertifikatet utförs av VPN-gatewayen och sker under etableringen av P2S VPN-anslutningen. Rotcertifikatet krävs för valideringen och måste överföras till Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Autentisera med inbyggd Azure Active Directory-autentisering

Azure AD-autentisering gör det möjligt för användare att ansluta till Azure med sina Azure Active Directory-autentiseringsuppgifter. Inbyggd Azure AD-autentisering stöds endast för OpenVPN-protokollet och Windows 10 och kräver användning av [Azure VPN-klienten](https://go.microsoft.com/fwlink/?linkid=2117554).

Med inbyggd Azure AD-autentisering kan du utnyttja Azure AD:s villkorliga åtkomst samt MFA-funktioner (Multi Factor Authentication(MFA) för VPN.

På en hög nivå måste du utföra följande steg för att konfigurera Azure AD-autentisering:

1. [Konfigurera en Azure AD-klientorganisation](openvpn-azure-ad-tenant.md)

2. [Aktivera Azure AD-autentisering på gatewayen](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Hämta och konfigurera Azure VPN-klient](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autentisera med Active Directory (AD) domänserver

AD-domänautentisering tillåter användare att ansluta till Azure med hjälp av sina autentiseringsuppgifter för organisationsdomäner. Det kräver en RADIUS-server som integreras med AD-servern. Organisationer kan också utnyttja sin befintliga RADIUS-distribution.
  
RADIUS-servern kan distribueras lokalt eller i ditt Virtuella Azure-nätverk. Under autentiseringen fungerar Azure VPN Gateway som en genomfart och vidarebefordrar autentiseringsmeddelanden fram och tillbaka mellan RADIUS-servern och den anslutande enheten. Så gateway-nåbarhet till RADIUS-servern är viktigt. Om RADIUS-servern finns lokalt krävs en VPN S2S-anslutning från Azure till den lokala platsen för att nå.  
  
RADIUS-servern kan också integreras med AD-certifikattjänster. På så sätt kan du använda RADIUS-servern och företagscertifikatdistributionen för P2S-certifikatautentisering som ett alternativ till Azure-certifikatautentiseringen. Fördelen är att du inte behöver överföra rotcertifikat och återkallade certifikat till Azure.

En RADIUS-server kan också integreras med andra externa identitetssystem. Detta öppnar upp massor av autentiseringsalternativ för P2S VPN, inklusive multifaktoralternativ.

![punkt-till-plats](./media/point-to-site-about/p2s.png "Punkt-till-plats")

## <a name="what-are-the-client-configuration-requirements"></a>Vilka är klientkonfigurationskraven?

>[!NOTE]
>För Windows-klienter måste du ha administratörsrättigheter på klientenheten för att kunna initiera VPN-anslutningen från klientenheten till Azure.
>

Användare använder de inbyggda VPN-klienterna på Windows- och Mac-enheter för P2S. Azure tillhandahåller en ZIP-fil för VPN-klientkonfiguration som innehåller inställningar som krävs av dessa inbyggda klienter för att ansluta till Azure.

* För Windows-enheter består VPN-klientkonfigurationen av ett installationspaket som användarna installerar på sina enheter.
* För Mac-enheter består den av mobileconfig-filen som användarna installerar på sina enheter.

Zip-filen innehåller också värdena för några av de viktiga inställningarna på Azure-sidan som du kan använda för att skapa din egen profil för dessa enheter. Några av värdena inkluderar VPN-gateway-adressen, konfigurerade tunneltyper, vägar och rotcertifikatet för gateway-validering.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Vilka gateway-SKU:er har stöd för P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Rekommendationer för Gateway SKU finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Vilka IKE/IPsec-principer är konfigurerade för VPN-gateways för P2S?


**IKEv2 (på andra sätt)**

|**Chiffer** | **Integritet** | **PRF (första)** | **DH-grupp** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**Ipsec**

|**Chiffer** | **Integritet** | **PFS-grupp** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Vilka TLS-principer är konfigurerade för VPN-gateways för P2S?
**TLS**

|**Principer** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hur konfigurerar jag en P2S-anslutning?

En P2S-konfiguration kräver en hel del specifika steg. Följande artiklar innehåller stegen för att gå igenom P2S-konfigurationen och länkar för att konfigurera VPN-klientenheterna:

* [Konfigurera en P2S-anslutning - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azure-autentisering av inbyggt certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurera OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Så här tar du bort konfigurationen av en P2S-anslutning

För steg, se [vanliga frågor](#removeconfig)och svar nedan.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Vanliga frågor och svar om inbyggd Azure-certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Vanliga frågor och svar om RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Efterföljande moment

* [Konfigurera en P2S-anslutning - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azure-autentisering av inbyggt certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
