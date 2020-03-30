---
title: Övergång till OpenVPN eller IKEv2 från SSTP | Azure VPN-gateway
description: Den här artikeln hjälper dig att förstå olika sätt att övervinna 128 samtidig anslutningsgränsen för SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: alzam
ms.openlocfilehash: 60c3a7f4f12993f475b3d03fd0915971d7673a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80143110"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Övergång till OpenVPN-protokoll eller IKEv2 från SSTP

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här artikeln gäller resurshanterarens distributionsmodell och beskriver olika sätt att övervinna anslutningsgränsen på 128 samtidiga anslutningar för SSTP genom att övergå till OpenVPN-protokoll eller IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Vilket protokoll använder P2S?

Point-to-site VPN kan använda något av följande protokoll:

* **OpenVPN&reg; Protocol**, ett SSL/TLS-baserat VPN-protokoll. En SSL VPN-lösning kan penetrera brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443 utgående, som SSL använder. OpenVPN kan användas för att ansluta från Android, iOS (version 11.0 och högre), Windows, Linux och Mac-enheter (OSX-versioner 10.13 och senare).

* **Secure Socket Tunneling Protocol (SSTP)**, ett eget SSL-baserat VPN-protokoll. En SSL VPN-lösning kan penetrera brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443 utgående, som SSL använder. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare). **SSTP stöder upp till 128 samtidiga anslutningar endast oavsett gateway SKU**.

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).


>[!NOTE]
>IKEv2 och OpenVPN för P2S är endast tillgängliga för resurshanterarens distributionsmodell. De är inte tillgängliga för den klassiska distributionsmodellen. Grundläggande gateway SKU stöder inte IKEv2- eller OpenVPN-protokoll. Om du använder den grundläggande SKU måste du ta bort och återskapa en produktion SKU Virtual Network Gateway.
>

## <a name="migrating-from-sspt-to-ikev2-or-openvpn"></a>Migrera från SSPT till IKEv2 eller OpenVPN

Det kan finnas fall när du vill stödja mer än 128 samtidigA P2S-anslutning till en VPN-gateway men använder SSTP. I så fall måste du gå till IKEv2 eller OpenVPN-protokollet.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Alternativ 1 - Lägg till IKEv2 utöver SSTP på gatewayen

Detta är det enklaste alternativet. SSTP och IKEv2 kan samexistera på samma gateway och ge dig ett högre antal samtidiga anslutningar. Du kan helt enkelt aktivera IKEv2 på den befintliga gatewayen och hämta om klienten.

Att lägga till IKEv2 i en befintlig SSTP VPN-gateway påverkar inte befintliga klienter och du kan konfigurera dem så att de använder IKEv2 i små batchar eller bara konfigurera de nya klienterna för att använda IKEv2. Om en Windows-klient är konfigurerad för både SSTP och IKEv2, kommer den att försöka ansluta med IKEV2 först och om det misslyckas, kommer det att falla tillbaka till SSTP.

**IKEv2 använder icke-standardiserade UDP-portar så du måste se till att dessa portar inte blockeras på användarens brandvägg. De portar som används är UDP 500 och 4500.**

Om du vill lägga till IKEv2 i en befintlig gateway går du bara till fliken "point-to-site configuration" under Virtual Network Gateway i portalen och väljer **IKEv2 och SSTP (SSL)** i listrutan.

![punkt-till-plats](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Alternativ 2 - Ta bort SSTP och aktivera OpenVPN på gatewayen

Eftersom SSTP och OpenVPN båda är TLS-baserade protokoll kan de inte samexistera på samma gateway. Om du bestämmer dig för att flytta från SSTP till OpenVPN måste du inaktivera SSTP och aktivera OpenVPN på gatewayen. Den här åtgärden gör att de befintliga klienterna förlorar anslutningen till VPN-gatewayen tills den nya profilen har konfigurerats på klienten.

Du kan aktivera OpenVPN tillsammans med IKEv2 om du vill. OpenVPN är TLS-baserad och använder standard-TCP 443-porten. Om du vill växla till OpenVPN går du till fliken "point-to-site configuration" under Virtual Network Gateway i portalen och väljer **OpenVPN (SSL)** eller **IKEv2 och OpenVPN (SSL)** från listrutan.

![punkt-till-plats](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

När gatewayen har konfigurerats kan befintliga klienter inte ansluta förrän du [distribuerar och konfigurerar OpenVPN-klienterna](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Om du använder Windows 10 kan du också använda [Azure VPN-klienten för Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="what-are-the-client-configuration-requirements"></a>Vilka är klientkonfigurationskraven?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Vilka gateway-SKU:er har stöd för P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Rekommendationer för Gateway SKU finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Vilka IKE/IPsec-principer är konfigurerade för VPN-gateways för P2S?


**IKEv2 (på andra sätt)**

|**Chiffer** | **Integritet** | **PRF (första)** | **DH-grupp** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**Ipsec**

|**Chiffer** | **Integritet** | **PFS-grupp** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Vilka TLS-principer är konfigurerade för VPN-gateways för P2S?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hur konfigurerar jag en P2S-anslutning?

En P2S-konfiguration kräver en hel del specifika steg. Följande artiklar innehåller stegen för att gå igenom P2S-konfigurationen och länkar för att konfigurera VPN-klientenheterna:

* [Konfigurera en P2S-anslutning - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azure-autentisering av inbyggt certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurera OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en P2S-anslutning - RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – Azure-autentisering av inbyggt certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
