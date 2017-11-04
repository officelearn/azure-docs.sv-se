---
title: "Azure AD Domain Services: Nätverk riktlinjer | Microsoft Docs"
description: "Överväganden för nätverk för Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 5f9236c5cf660be00db6e09d61df617b64d978e9
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Överväganden för nätverk för Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Hur du väljer ett virtuellt Azure-nätverk
Följande riktlinjer hjälper dig att välja ett virtuellt nätverk som ska användas med Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Typ av virtuella Azure-nätverket
* **Hanteraren för virtuella nätverk**: Azure AD Domain Services kan aktiveras i virtuella nätverk som skapats med Azure Resource Manager.
* Du kan inte aktivera Azure AD Domain Services i Azure klassiska virtuella nätverk.
* Du kan ansluta andra virtuella nätverk till det virtuella nätverket som Azure AD Domain Services är aktiverat. Mer information finns i [nätverksanslutningar](active-directory-ds-networking.md#network-connectivity) avsnitt.
* **Regionala virtuella nätverk**: Om du planerar att använda ett befintligt virtuellt nätverk kontrollerar du att det är ett regionalt virtuellt nätverk.

  * Virtuella nätverk som använder mekanismen för äldre tillhörighetsgrupper kan inte användas med Azure AD Domain Services.
  * Att använda Azure AD Domain Services [migrera äldre virtuella nätverk till regionala virtuella nätverk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

### <a name="azure-region-for-the-virtual-network"></a>Azure-region för det virtuella nätverket
* Din Azure AD Domain Services hanterad domän distribueras i samma Azure-region som det virtuella nätverket som du vill aktivera tjänsten i.
* Välj ett virtuellt nätverk i en Azure-region som stöds av Azure AD Domain Services.
* På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.

### <a name="requirements-for-the-virtual-network"></a>Krav för det virtuella nätverket
* **Närhet till din Azure arbetsbelastningar**: Välj det virtuella nätverk som för närvarande är värd för/ska vara värd för virtuella datorer som behöver åtkomst till Azure AD Domain Services. Du kan också välja att ansluta virtuella nätverk om din arbetsbelastning distribueras i ett annat virtuellt nätverk än den hanterade domänen.
* **Anpassad/sätta egna DNS-servrar**: se till att det inte finns några anpassade DNS-servrar som konfigurerats för det virtuella nätverket. Ett exempel på en anpassad DNS-server är en instans av Windows Server DNS körs på en Windows Server-VM som du har distribuerat i det virtuella nätverket. Azure AD Domain Services är inte integrerat med några anpassade DNS-servrar som distribuerats i det virtuella nätverket.
* **Befintliga domäner med samma domännamn**: Kontrollera att du inte har en befintlig domän med samma domännamn tillgänglig i det virtuella nätverket. Anta exempelvis att det redan finns en domän som heter ”contoso.com” i det valda virtuella nätverket. Senare, försök att aktivera en Azure AD Domain Services-hanterad domän med samma domännamn (som ”contoso.com”) i det virtuella nätverket. Det uppstår ett fel vid försök att aktivera Azure AD Domain Services. Det här felet beror på namnkonflikter för domännamnet i det virtuella nätverket. I den här situationen måste du använda ett annat namn för att ställa in din Azure AD Domain Services-hanterade domän. Du kan också avetablera den befintliga domänen och sedan aktivera Azure AD Domain Services.

> [!WARNING]
> Du kan inte flytta Domain Services till ett annat virtuellt nätverk när du har aktiverat tjänsten.
>
>

## <a name="network-security-groups-and-subnet-design"></a>Nätverkssäkerhetsgrupper och undernät
En [Nätverkssäkerhetsgrupp (NSG)](../virtual-network/virtual-networks-nsg.md) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till VM-instanser i ett virtuellt nätverk. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Dessutom kan trafik till en enskild VM begränsas ytterligare genom att koppla en NSG direkt till den virtuella datorn.

![Rekommenderade undernät](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="guidelines-for-choosing-a-subnet"></a>Riktlinjer för att välja ett undernät
* Distribuera Azure AD Domain Services till ett **separata dedikerade undernät** i ditt virtuella Azure-nätverket.
* Gäller inte NSG: er till dedikerade undernätet för din hanterade domän. Om du måste tillämpa NSG: er till dedikerade undernätet, se till att du **inte blockera portar som krävs för att tjänsten och hantera din domän**.
* Begränsa inte antalet IP-adresser som är tillgängliga i det dedikerade undernätet för din hanterade domän alltför. Den här begränsningen förhindrar tjänsten från att två domänkontrollanter som är tillgänglig för din hanterade domän.
* **Aktivera inte Azure AD Domain Services i gateway-undernätet** av det virtuella nätverket.

> [!WARNING]
> När du kopplar en NSG med ett undernät som Azure AD Domain Services är aktiverat, kan det störa Microsofts möjlighet att underhålla och hantera domänen. Dessutom avbryts synkronisering mellan Azure AD-klienten och din hanterade domän. **SLA gäller inte för distributioner där en NSG har tillämpats som blockerar Azure AD Domain Services från uppdatering och hantering av din domän.**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Portar som krävs för Azure AD Domain Services
Följande portar krävs för Azure AD Domain Services till tjänsten och underhålla din hanterade domän. Kontrollera att portarna inte är blockerade för undernätet som du har aktiverat din hanterade domän.

| Portnummer | Syfte |
| --- | --- |
| 443 |Synkronisering med Azure AD-klient |
| 3389 |Hantering av din domän |
| 5986 |Hantering av din domän |
| 636 |Säker LDAP (LDAPS) åtkomst till din hanterade domän |

Port 5986 används för att utföra administrativa uppgifter med hjälp av PowerShell-fjärrkommunikation på din hanterade domän. Domänkontrollanterna för din hanterade domän lyssna vanligtvis inte på den här porten. Tjänsten öppnar den här porten på hanterade domänkontrollanter endast när en hanterings- eller åtgärden måste utföras för den hanterade domänen. När åtgärden har slutförts stängs tjänsten av den här porten på hanterade domänkontrollanter.

Port 3389 används för anslutning till fjärrskrivbord till din hanterade domän. Detta port finns kvar i stor utsträckning är inaktiverat på din hanterade domän. Tjänsten gör den här porten om vi behöver ansluta till din hanterade domän i felsökningssyfte initierade som svar på en tjänstbegäran som du har initierat. Den här mekanismen används inte kontinuerligt eftersom hantering och övervakning aktiviteter utförs med hjälp av PowerShell-fjärrkommunikation. Den här porten används endast i sällsynta händelsen som behövs för att fjärransluta till din hanterade domän för avancerad felsökning. Porten är stängd när felsökning åtgärden har slutförts.


### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Exempel NSG för virtuella nätverk med Azure AD Domain Services
I följande tabell visas ett exempel på en NSG som du kan konfigurera för ett virtuellt nätverk med en Azure AD Domain Services-hanterad domän. Den här regeln kan inkommande trafik över portarna som krävs för att säkerställa hanterad domän-förblir korrigeras, uppdateras och kan övervakas av Microsoft. 'DenyAll' Standardregeln gäller för inkommande trafik från internet.

Dessutom visas NSG: N också hur du låsa säker LDAP-åtkomst via internet. Hoppa över den här regeln om du inte har aktiverat säker LDAP-åtkomst till hanterade domänen via internet. NSG: N innehåller en uppsättning regler som tillåter inkommande LDAPS åtkomst via TCP-port 636 endast från en angiven mängd av IP-adresser. NSG-regel som tillåter LDAPS åtkomst via internet från den angivna IP-adresser har högre prioritet än DenyAll NSG-regeln.

![Exempel NSG till säker LDAPS åtkomst via internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mer information** - [skapar en Nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Nätverksanslutning
En Azure AD Domain Services-hanterad domän kan aktiveras endast inom ett enda virtuellt nätverk i Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scenarier för att ansluta Azure-nätverk
Anslut virtuella Azure-nätverk för att använda den hanterade domänen i något av följande scenarier för distribution:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Använd den hanterade domänen i mer än ett virtuellt Azure-nätverk
Du kan ansluta andra virtuella Azure-nätverk för virtuella Azure-nätverket som du har aktiverat Azure AD Domain Services. Den här peering VPN/VNet-anslutningen kan du använda den hanterade domänen med din arbetsbelastning distribueras på andra virtuella nätverk.

![Anslutningar för klassiska virtuella nätverk](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Använd den hanterade domänen i Resource Manager-baserade virtuella nätverk
Du kan ansluta en Resource Manager-baserat virtuellt nätverk till Azure klassiska virtuella nätverk som du har aktiverat Azure AD Domain Services. Den här anslutningen kan du använda den hanterade domänen med dina arbetsbelastningar som distribuerats i Resource Manager-baserade virtuella nätverk.

![Resurshanteraren för anslutningar för klassiska virtuella nätverk](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Alternativ för nätverksanslutning
* **VNet-till-VNet-anslutningar med virtuella nätverk peering**: peering virtuellt nätverk är en mekanism som ansluter två virtuella nätverk i samma region via Azure stamnät nätverket. När de två virtuella nätverken har peer-kopplats visas de som ett nätverk för alla anslutningsändamål. De hanteras fortfarande som separata resurser, men virtuella datorer i dessa virtuella nätverk kan kommunicera med varandra direkt med hjälp av privata IP-adresser.

    ![Anslutningar för virtuella nätverk med hjälp av peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Mer information om-virtuella nätverk peering](../virtual-network/virtual-network-peering-overview.md)
    
* **VNet-till-VNet-anslutningar med plats-till-plats VPN-anslutningar**: ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) liknar ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

    ![Anslutningar för virtuella nätverk med hjälp av VPN-Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Mer information – ansluta virtuella nätverk som använder VPN-gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Relaterat innehåll
* [Virtuella Azure-nätverket peering](../virtual-network/virtual-network-peering-overview.md)
* [Konfigurera ett VNet-till-VNet-anslutning för den klassiska distributionsmodellen](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Säkerhetsgrupper för Azure-nätverk](../virtual-network/virtual-networks-nsg.md)
* [Skapa en säkerhetsgrupp för nätverk](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
