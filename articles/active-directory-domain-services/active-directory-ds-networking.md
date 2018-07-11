---
title: 'Azure AD Domain Services: Riktlinjer för nätverk | Microsoft Docs'
description: Nätverksöverväganden för Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: maheshu
ms.openlocfilehash: 07f63eb9ed316f7798bd890bb6125617c6a1e886
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929645"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Nätverksöverväganden för Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Så här väljer du ett Azure-nätverk
Följande riktlinjer hjälper dig välja ett virtuellt nätverk ska användas med Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Typ av Azure-nätverk
* **Resource Manager-nätverk**: Azure AD Domain Services kan aktiveras i virtuella nätverk som skapats med Azure Resource Manager.
* Du kan inte aktivera Azure AD Domain Services i en klassisk Azure-nätverk.
* Du kan ansluta andra virtuella nätverk till det virtuella nätverket där Azure AD Domain Services är aktiverat. Mer information finns i den [nätverksanslutningar](active-directory-ds-networking.md#network-connectivity) avsnittet.

### <a name="azure-region-for-the-virtual-network"></a>Azure-region för det virtuella nätverket
* Din Azure AD Domain Services hanterade domänen har distribuerats i samma Azure-region som det virtuella nätverket som du vill aktivera tjänsten i.
* Välj ett virtuellt nätverk i en Azure-region som stöds av Azure AD Domain Services.
* På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.

### <a name="requirements-for-the-virtual-network"></a>Krav för det virtuella nätverket
* **Närhet till dina Azure-arbetsbelastningar**: Välj det virtuella nätverk som för närvarande är värd för/ska vara värd för virtuella datorer som behöver åtkomst till Azure AD Domain Services. Om dina arbetsbelastningar har distribuerats i ett annat virtuellt nätverk än den hanterade domänen, kan du också välja att ansluta virtuella nätverk.
* **Anpassad/bring your own DNS-servrar**: se till att det inte finns några anpassade DNS-servrar som konfigurerats för det virtuella nätverket. Ett exempel på en anpassad DNS-server är en instans av Windows Server-DNS som körs på en Windows Server VM som du har distribuerat i det virtuella nätverket. Azure AD Domain Services är inte integrerat med eventuella anpassade DNS-servrar som distribuerats i det virtuella nätverket.
* **Befintliga domäner med samma domännamn**: Kontrollera att du inte har en befintlig domän med samma domännamn i det virtuella nätverket. Anta exempelvis att det redan finns en domän som heter ”contoso.com” i det valda virtuella nätverket. Senare kan försöker du aktivera en Azure AD Domain Services-hanterad domän med samma domännamn (det vill säga ”contoso.com”) i det virtuella nätverket. Det uppstår ett fel vid försök att aktivera Azure AD Domain Services. Det här felet beror på namnkonflikter för domännamnet i det virtuella nätverket. I den här situationen måste du använda ett annat namn för att ställa in din Azure AD Domain Services-hanterade domän. Du kan också avetablera den befintliga domänen och sedan aktivera Azure AD Domain Services.

> [!WARNING]
> Du kan inte flytta Domain Services till ett annat virtuellt nätverk när du har aktiverat tjänsten.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Riktlinjer för att välja ett undernät

![Rekommenderade undernät](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Distribuera Azure AD Domain Services till en **separata dedikerade undernät som** inom Azure-nätverk.
* Gäller inte NSG: er för dedikerat undernät för din hanterade domän. Om du måste tillämpa Nätverkssäkerhetsgrupper på det dedikerade undernätet, se till att du **inte blockera de portar som krävs till tjänsten och hantera din domän**.
* Begränsa inte antalet IP-adresser som är tillgängliga i det dedikerade undernätet för din hanterade domän alltför. Den här begränsningen förhindrar att tjänsten gör två domänkontrollanter som är tillgängliga för din hanterade domän.
* **Aktivera inte Azure AD Domain Services i gateway-undernätet** av det virtuella nätverket.

> [!WARNING]
> När du kopplar en NSG med ett undernät där Azure AD Domain Services är aktiverad, du kan störa Microsofts förmåga att tjänsten och hantera domänen. Dessutom avbryts synkronisering mellan din Azure AD-klient och din hanterade domän. **SERVICEAVTALET gäller inte för distributioner där en NSG har tillämpats som blockerar Azure AD Domain Services från uppdatering och hantering av din domän.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Portar som krävs för Azure AD Domain Services
Följande portar krävs för Azure AD Domain Services till tjänsten och underhålla din hanterade domän. Kontrollera att portarna inte är blockerad för undernätet som du har aktiverat din hanterade domän.

| Portnummer | Krävs? | Syfte |
| --- | --- | --- |
| 443 | Obligatorisk |Synkronisering med Azure AD-klienten |
| 5986 | Obligatorisk | Hantering av din domän |
| 3389 | Obligatorisk | Hantering av din domän |
| 636 | Valfri | Åtkomst med säkert LDAP (LDAPS) till din hanterade domän |

**Port 443 (synkronisering med Azure AD)**
* Den används för att synkronisera Azure AD-katalogen med din hanterade domän.
* Det är obligatoriskt att tillåta åtkomst till den här porten för din NSG. Den hanterade domänen är inte synkroniserade med Azure AD-katalogen inte har tillgång till den här porten. Användare kanske inte kan logga in eftersom ändringar i sina lösenord inte synkroniseras till din hanterade domän.
* Du kan begränsa inkommande åtkomst till den här porten till IP-adresser som tillhör Azure IP-adressintervall. Observera att Azure-IP-adressintervallet är ett annat intervall än PowerShell intervallet enligt regeln nedan.

**Port 5986 (PowerShell-fjärrkommunikation)**
* Den används för att utföra administrativa uppgifter med hjälp av PowerShell-fjärrkommunikation på din hanterade domän.
* Det är obligatoriskt att tillåta åtkomst via den här porten för din NSG. Den hanterade domänen får inte vara uppdaterade, konfigurerade, säkerhetskopieras eller övervakade inte har tillgång till den här porten.
* För att nya domäner och domäner med en Azure Resource Manager-nätverk, du kan begränsa inkommande åtkomst till den här porten till följande IP-adresser för källa: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141 , 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* För domäner med ett klassiskt virtuellt nätverk kan du begränsa inkommande åtkomsten till den här porten till följande IP-adresser för källa: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209
* Domänkontrollanterna för den hanterade domänen lyssna vanligtvis inte på den här porten. Tjänsten öppnas den här porten på hanterade domänkontrollanter endast när en hantering eller underhåll åtgärden måste utföras för den hanterade domänen. När åtgärden har slutförts stänger tjänsten av den här porten på hanterade domänkontrollanter.

**Port 3389 (fjärrskrivbord)**
* Den används för anslutning till fjärrskrivbord till domänkontrollanter för din hanterade domän.
* Du kan begränsa inkommande åtkomst till följande IP-adresser för källa: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Detta port finns kvar i stort sett inaktiverat på din hanterade domän. Den här mekanismen används inte med jämna mellanrum eftersom hantering och övervakning av uppgifter utförs med hjälp av PowerShell-fjärrkommunikation. Den här porten används endast i om Microsoft behöver fjärransluta till din hanterade domän för avancerad felsökning. Porten är stängd när felsökning åtgärden har slutförts.

**Port 636 (säker LDAP)**
* Den används för att aktivera åtkomst med säkert LDAP till din hanterade domän via internet.
* Det är valfritt att öppna den här porten via din NSG. Öppna porten bara om du har åtkomst med säkert LDAP via internet aktiverat.
* Du kan begränsa inkommande åtkomst till den här porten till käll-IP-adresser som du förväntar dig att ansluta via säkert LDAP.


## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En [Nätverkssäkerhetsgrupp (NSG)](../virtual-network/virtual-networks-nsg.md) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till dina VM-instanser i ett virtuellt nätverk. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Dessutom kan trafik till en enskild VM begränsas ytterligare genom att koppla en NSG direkt till den virtuella datorn.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Exempel på NSG för virtuella nätverk med Azure AD Domain Services
I följande tabell visar ett exempel NSG som du kan konfigurera för ett virtuellt nätverk med en Azure AD Domain Services-hanterad domän. Den här regeln tillåter inkommande trafik över portarna som krävs för att säkerställa att dina hanterade domänen är kvar korrigeringar, uppdateras och kan övervakas av Microsoft. 'DenyAll' Standardregeln gäller för all annan inkommande trafik från internet.

Dessutom visar NSG: N också hur man låser åtkomst med säkert LDAP via internet. Hoppa över den här regeln om du inte har aktiverat åtkomst med säkert LDAP till din hanterade domän via internet. NSG: N innehåller en uppsättning regler som tillåter inkommande LDAPS åtkomst via TCP-port 636 bara från en angiven mängd av IP-adresser. NSG-regel som tillåter LDAPS via internet från den angivna IP-adresser har högre prioritet än DenyAll NSG-regel.

![Exemplet NSG till säker LDAPS åtkomst via internet](.\media\active-directory-domain-services-alerts\default-nsg.png)

**Mer information** - [skapa en Nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md).


## <a name="network-connectivity"></a>Nätverksanslutning
En Azure AD Domain Services-hanterad domän kan aktiveras endast inom ett enda virtuellt nätverk i Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scenarier för att ansluta Azure-nätverk
Anslut Azure-nätverk om du vill använda den hanterade domänen i någon av följande scenarier:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Använda den hanterade domänen i mer än en Azure-nätverk
Du kan ansluta andra Azure-nätverk till Azure-nätverket där du har aktiverat Azure AD Domain Services. Den här VPN-/ VNet peering-anslutningen kan du använda den hanterade domänen med dina arbetsbelastningar som distribuerats i andra virtuella nätverk.

![Anslutningar för klassiska virtuella nätverk](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Använda den hanterade domänen i ett virtuellt nätverk med Resource Manager-baserade
Du kan ansluta ett virtuellt nätverk med Resource Manager-baserade till Azure klassiskt virtuellt nätverk som du har aktiverat Azure AD Domain Services. Den här anslutningen kan du använda den hanterade domänen med dina arbetsbelastningar som distribuerats i Resource Manager-baserade virtuella nätverk.

![Resource Manager till klassisk virtuell nätverksanslutning](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Alternativ för nätverksanslutning
* **VNet-till-VNet-anslutningar som använder virtuell nätverks-peering**: virtuell nätverkspeering är en mekanism som ansluter två virtuella nätverk i samma region via Azures stamnätverk. När de två virtuella nätverken har peer-kopplats visas de som ett nätverk för alla anslutningsändamål. De hanteras fortfarande som separata resurser, men virtuella datorer i dessa virtuella nätverk kan kommunicera med varandra direkt med hjälp av privata IP-adresser.

    ![Anslutningar för virtuella nätverk med hjälp av peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Mer information – virtual network-peering](../virtual-network/virtual-network-peering-overview.md)

* **VNet-till-VNet-anslutningar med plats-till-plats VPN-anslutningar**: ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet till VNet) liknar ansluter ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

    ![Anslutningar för virtuella nätverk med VPN-Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Mer information – ansluta virtuella nätverk med VPN-gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Relaterat innehåll
* [Azure virtuell nätverkspeering](../virtual-network/virtual-network-peering-overview.md)
* [Konfigurera en VNet-till-VNet-anslutning för den klassiska distributionsmodellen](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Säkerhetsgrupper för Azure-nätverk](../virtual-network/security-overview.md)
* [Skapa en Nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md)
