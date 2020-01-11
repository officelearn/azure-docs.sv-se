---
title: Om Azure punkt-till-plats VPN-anslutningar | Microsoft Docs
description: Den här artikeln hjälper dig att förstå punkt-till-plats-anslutningar och hjälper dig att bestämma vilken P2S VPN gateway-autentiseringstyp som ska användas.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 6084eca4f7d2d6040eb3f32a9b28cfc50922d200
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896102"
---
# <a name="about-point-to-site-vpn"></a>Om punkt-till-plats-VPN

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. Den här artikeln gäller distributionsmodellen i Resource Manager.

## <a name="protocol"></a>Vilket protokoll används av P2S?

Punkt-till-plats-VPN kan använda något av följande protokoll:

* **OpenVPN® protokoll**, ett SSL/TLS-baserat VPN-protokoll. En SSL VPN-lösning kan tränga in brand väggar, eftersom de flesta brand väggar öppnar TCP-port 443 utgående, som SSL använder. OpenVPN kan användas för att ansluta från Android, iOS (version 11,0 och senare), Windows-, Linux-och Mac-enheter (OSX-versioner 10,13 och senare).

* SSTP (Secure Socket Tunneling Protocol), ett patentskyddat SSL-baserat VPN-protokoll. En SSL VPN-lösning kan tränga in brand väggar, eftersom de flesta brand väggar öppnar TCP-port 443 utgående, som SSL använder. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).


>[!NOTE]
>IKEv2-och OpenVPN för P2S är endast tillgängliga för distributions modellen Resource Manager. De är inte tillgängliga för den klassiska distributions modellen.
>

## <a name="authentication"></a>Hur autentiseras P2S VPN-klienter?

Innan Azure accepterar en P2S VPN-anslutning måste användaren autentiseras först. Det finns två mekanismer som Azure erbjuder för att autentisera en ansluten användare.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autentisera med intern Azure-certifikatautentisering

När du använder den interna autentiseringen med Azure-certifikat används ett klient certifikat som finns på enheten för att autentisera den anslutande användaren. Klient certifikat genereras från ett betrott rot certifikat och installeras sedan på varje klient dator. Du kan använda ett rot certifikat som har genererats med en företags lösning, eller så kan du skapa ett självsignerat certifikat.

Verifieringen av klient certifikatet utförs av VPN-gatewayen och sker när P2S VPN-anslutningen upprättas. Rot certifikatet krävs för verifieringen och måste laddas upp till Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Autentisera med intern Azure Active Directory autentisering

Med Azure AD-autentisering kan användare ansluta till Azure med sina Azure Active Directory autentiseringsuppgifter. Intern Azure AD-autentisering stöds bara för OpenVPN-protokoll och Windows 10 och kräver användning av [Azure VPN-klienten (för hands version)](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab).

Med inbyggd Azure AD-autentisering kan du utnyttja Azure ADs villkorliga åtkomst och Multi-Factor Authentication (MFA) funktioner för VPN.

Du måste utföra följande steg för att konfigurera Azure AD-autentisering på en hög nivå:

1. [Konfigurera en Azure AD-klient](openvpn-azure-ad-tenant.md)

2. [Aktivera Azure AD-autentisering på gatewayen](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant#enable-authentication)

3. [Hämta och konfigurera Azure VPN-klienten (för hands version)](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autentisera med hjälp av Active Directory (AD) domän Server

AD Domain Authentication gör att användare kan ansluta till Azure med sina organisations domän uppgifter. Den kräver en RADIUS-server som integreras med AD-servern. Organisationer kan också utnyttja sin befintliga RADIUS-distribution.   
  
RADIUS-servern kan distribueras lokalt eller i ditt Azure VNET. Under autentiseringen fungerar Azure VPN Gateway som ett pass och vidarebefordrar autentiseringsbegäranden fram och tillbaka mellan RADIUS-servern och den anslutande enheten. Det är viktigt att gatewayens tillgänglighet till RADIUS-servern är viktigt. Om RADIUS-servern finns lokalt krävs en S2S-anslutning från Azure till den lokala platsen för nåbarhet.  
  
RADIUS-servern kan också integreras med AD Certificate Services. På så sätt kan du använda RADIUS-servern och distributionen av företags certifikat för P2S certifikatautentisering som ett alternativ till Azure-certifikatautentisering. Fördelen är att du inte behöver ladda upp rot certifikat och återkallade certifikat till Azure.

En RADIUS-server kan också integreras med andra externa identitets system. Detta öppnar massor av autentiseringsalternativ för P2S VPN, inklusive Multi-Factor Options.

![punkt-till-plats](./media/point-to-site-about/p2s.png "Point-to-Site")

## <a name="what-are-the-client-configuration-requirements"></a>Vilka är klient konfigurations kraven?

>[!NOTE]
>För Windows-klienter måste du ha administratörs behörighet på klient enheten för att kunna initiera VPN-anslutningen från klient enheten till Azure.
>

Användarna använder inbyggda VPN-klienter på Windows-och Mac-enheter för P2S. Azure tillhandahåller en zip-fil för VPN-klientkonfiguration som innehåller inställningar som krävs av dessa interna klienter för att ansluta till Azure.

* VPN-klientens konfiguration för Windows-enheter består av ett installations paket som användarna installerar på sina enheter.
* För Mac-enheter består den av den mobileconfig-fil som användarna installerar på sina enheter.

Zip-filen innehåller också värdena för några av de viktiga inställningarna på Azure-sidan som du kan använda för att skapa din egen profil för dessa enheter. Några av värdena är VPN gateway-adressen, konfigurerade tunnel typer, vägar och rot certifikatet för gateway-verifiering.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Vilka Gateway-SKU: er stöder P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Rekommendationer för gateway-SKU finns i [om VPN Gateway inställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering.
>

## <a name="IKE/IPsec policies"></a>Vilka IKE/IPsec-principer har kon figurer ATS på VPN-gatewayer för P2S?


**IKEv2**

|**Chiffer** | **Äkthet** | **PRF** | **DH-grupp** |
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

**IPsec**

|**Chiffer** | **Äkthet** | **PFS-grupp** |
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

## <a name="TLS policies"></a>Vilka TLS-principer är konfigurerade på VPN-gatewayer för P2S?
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




## <a name="configure"></a>Hur gör jag för att konfigurerar du en P2S-anslutning?

En P2S-konfiguration kräver ett par olika steg. Följande artiklar innehåller stegen för att vägleda dig genom P2S-konfigurationen och länkar till att konfigurera VPN-klient enheter:

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – autentisering med internt Azure-certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurera OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a>Hur gör jag för att bort konfigurationen av en P2S-anslutning?

En P2S-konfiguration kan tas bort med hjälp av AZ CLI och följande kommando: 

`az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"`
 
## <a name="faqcert"></a>Vanliga frågor och svar om intern Azure-certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Vanliga frågor och svar om RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Efterföljande moment

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – autentisering med internt Azure-certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
