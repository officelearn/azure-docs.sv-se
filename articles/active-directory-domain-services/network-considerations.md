---
title: Nätverksplanering och anslutningar för Azure AD Domain Services | Microsoft-dokument
description: Lär dig mer om några av de överväganden om virtuell nätverksdesign och resurser som används för anslutning när du kör Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e610bf94dfdee4e2765e4fae4259f18a9f1036b5
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639982"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Hänsyn till virtual network design och konfigurationsalternativ för Azure AD Domain Services

Eftersom AZURE Active Directory Domain Services (AD DS) tillhandahåller autentiserings- och hanteringstjänster till andra program och arbetsbelastningar är nätverksanslutning en nyckelkomponent. Utan korrekt konfigurerade virtuella nätverksresurser kan program och arbetsbelastningar inte kommunicera med och använda de funktioner som tillhandahålls av Azure AD DS. Planera dina krav på virtuella nätverk för att se till att Azure AD DS kan betjäna dina program och arbetsbelastningar efter behov.

I den här artikeln beskrivs designöverväganden och krav för ett virtuellt Azure-nätverk för att stödja Azure AD DS.

## <a name="azure-virtual-network-design"></a>Azure virtuellt nätverk design

Om du vill tillhandahålla nätverksanslutning och tillåta program och tjänster att autentisera mot Azure AD DS använder du ett virtuellt Azure-nätverk och undernät. Helst bör Azure AD DS distribueras till sitt eget virtuella nätverk. Du kan inkludera ett separat programundernät i samma virtuella nätverk som värd för din hantering VM eller lätta programarbetsbelastningar. Ett separat virtuellt nätverk för större eller komplexa programarbetsbelastningar, peered till Azure AD DS virtuella nätverk, är oftast den lämpligaste designen. Andra designalternativ är giltiga, förutsatt att du uppfyller kraven i följande avsnitt för det virtuella nätverket och undernätet.

När du utformar det virtuella nätverket för Azure AD DS gäller följande överväganden:

* Azure AD DS måste distribueras till samma Azure-region som ditt virtuella nätverk.
    * För närvarande kan du bara distribuera en Azure AD DS-hanterad domän per Azure AD-klientorganisation. Den Azure AD DS-hanterade domänen distribueras till en enda region. Se till att du skapar eller väljer ett virtuellt nätverk i en [region som stöder Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Tänk på närheten till andra Azure-regioner och de virtuella nätverk som är värd för dina programarbetsbelastningar.
    * För att minimera svarstiden, hålla dina kärnprogram nära, eller i samma region som, det virtuella nätverksundernätet för din Azure AD DS-hanterade domän. Du kan använda vpn-anslutningar (Virtual Network Peering eller VPN) mellan virtuella Azure-nätverk. Dessa anslutningsalternativ beskrivs i följande avsnitt.
* Det virtuella nätverket kan inte förlita sig på andra DNS-tjänster än de som tillhandahålls av Azure AD DS.
    * Azure AD DS tillhandahåller en egen DNS-tjänst. Det virtuella nätverket måste konfigureras för att använda dessa DNS-tjänstadresser. Namnmatchning för ytterligare namnområden kan utföras med hjälp av villkorliga vidarebefordrare.
    * Du kan inte använda anpassade DNS-serverinställningar för att dirigera frågor från andra DNS-servrar, även på virtuella datorer. Resurser i det virtuella nätverket måste använda DNS-tjänsten som tillhandahålls av Azure AD DS.

> [!IMPORTANT]
> Du kan inte flytta Azure AD DS till ett annat virtuellt nätverk när du har aktiverat tjänsten.

En Azure AD DS-hanterad domän ansluter till ett undernät i ett virtuellt Azure-nätverk. Utforma det här undernätet för Azure AD DS med följande överväganden:

* Azure AD DS måste distribueras i sitt eget undernät. Använd inte ett befintligt undernät eller ett gateway-undernät.
* En nätverkssäkerhetsgrupp skapas under distributionen av en Azure AD DS-hanterad domän. Den här nätverkssäkerhetsgruppen innehåller de regler som krävs för korrekt servicekommunikation.
    * Skapa eller använd inte en befintlig nätverkssäkerhetsgrupp med dina egna anpassade regler.
* Azure AD DS kräver 3-5 IP-adresser. Kontrollera att ditt IP-adressintervall för undernätet kan ange det här antalet adresser.
    * Om du begränsar tillgängliga IP-adresser kan azure AD-domäntjänsterna förhindra att två domänkontrollanter underhålls.

I följande exempeldiagram beskrivs en giltig design där Azure AD DS har ett eget undernät, det finns ett gateway-undernät för extern anslutning och programarbetsbelastningar finns i ett anslutet undernät i det virtuella nätverket:

![Rekommenderad nätdesign](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Anslutningar till det virtuella Azure AD DS-nätverket

Som anges i föregående avsnitt kan du bara skapa en Azure AD Domain Services-hanterad domän i ett enda virtuellt nätverk i Azure, och endast en hanterad domän kan skapas per Azure AD-klientorganisation. Baserat på den här arkitekturen kan du behöva ansluta ett eller flera virtuella nätverk som är värd för dina programarbetsbelastningar till ditt virtuella Azure AD DS-nätverk.

Du kan ansluta programarbetsbelastningar som finns i andra virtuella Azure-nätverk med någon av följande metoder:

* Virtuell nätverkspeering
* Virtuellt privat nätverk (VPN)

### <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Virtuell nätverks peering är en mekanism som ansluter två virtuella nätverk i samma region via Azure-stamnätet. Global virtuell nätverks peering kan ansluta virtuella nätverk över Azure-regioner. När de två virtuella nätverken har peer-varandra kan resurserna, till exempel virtuella datorer, kommunicera med varandra direkt med privata IP-adresser. Med hjälp av virtuell nätverks peering kan du distribuera en Azure AD DS hanterad domän med dina programarbetsbelastningar som distribueras i andra virtuella nätverk.

![Virtuell nätverksanslutning med peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Mer information finns i [Översikt över Azure-peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Virtuella privata nätverk (VPN)

Du kan ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på samma sätt som du kan konfigurera ett virtuellt nätverk till en lokal plats. Båda anslutningarna använder en VPN-gateway för att skapa en säker tunnel med IPsec/IKE. Med den här anslutningsmodellen kan du distribuera Azure AD DS till ett virtuellt Azure-nätverk och sedan ansluta lokala platser eller andra moln.

![Virtuell nätverksanslutning med en VPN-gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Mer information om hur du använder virtuella privata nätverk finns i [Konfigurera en VNet-till-VNet VPN-gateway-anslutning med hjälp av Azure-portalen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Namnmatchning vid anslutning av virtuella nätverk

Virtuella nätverk som är anslutna till det virtuella nätverket Azure AD Domain Services har vanligtvis sina egna DNS-inställningar. När du ansluter virtuella nätverk konfigurerar det inte automatiskt namnmatchning för det anslutande virtuella nätverket för att lösa tjänster som tillhandahålls av azure AD DS-hanterade domänen. Namnmatchning för de anslutande virtuella nätverken måste konfigureras för att programarbetsbelastningar ska kunna hitta Azure AD Domain Services.

Du kan aktivera namnmatchning med villkorlig DNS-vidarebefordrare på DNS-servern som stöder anslutande virtuella nätverk eller genom att använda samma DNS-IP-adresser från det virtuella azure AD Domain Service-nätverket.

## <a name="network-resources-used-by-azure-ad-ds"></a>Nätverksresurser som används av Azure AD DS

En Azure AD DS-hanterad domän skapar vissa nätverksresurser under distributionen. Dessa resurser behövs för en lyckad drift och hantering av Azure AD DS-hanterad domän och bör inte konfigureras manuellt.

| Azure-resurs                          | Beskrivning |
|:----------------------------------------|:---|
| Nätverkskort                  | Azure AD DS är värd för den hanterade domänen på två domänkontrollanter (DC) som körs på Windows Server som virtuella Azure-datorer. Varje virtuell dator har ett virtuellt nätverksgränssnitt som ansluter till ditt virtuella nätverksundernät. |
| Dynamisk standard-IP-adress      | Azure AD DS kommunicerar med synkroniserings- och hanteringstjänsten med hjälp av en vanlig SKU-offentlig IP-adress. Mer information om offentliga IP-adresser finns i [IP-adresstyper och allokeringsmetoder i Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Azure-standardbelastningsbalanser            | Azure AD DS använder en standard SKU belastningsutjämnare för nätverksadressöversättning (NAT) och belastningsutjämning (när den används med säker LDAP). Mer information om Azure-belastningsutjämnare finns i [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| NAT-regler (Network Address Translation) | Azure AD DS skapar och använder tre NAT-regler för belastningsutjämnaren - en regel för säker HTTP-trafik och två regler för säker PowerShell-ommotning. |
| Regler för lastbalanserare                     | När en Azure AD DS-hanterad domän har konfigurerats för säker LDAP på TCP-port 636 skapas och används tre regler på en belastningsutjämnare för att distribuera trafiken. |

> [!WARNING]
> Ta inte bort eller ändra någon av de nätverksresurser som skapats av Azure AD DS, till exempel konfigurera belastningsutjämnaren eller reglerna manuellt. Om du tar bort eller ändrar någon av nätverksresurserna kan ett avbrott i Azure AD DS-tjänsten uppstå.

## <a name="network-security-groups-and-required-ports"></a>Nätverkssäkerhetsgrupper och nödvändiga portar

En [nätverkssäkerhetsgrupp (NSG)](../virtual-network/virtual-networks-nsg.md) innehåller en lista över regler som tillåter eller nekar nätverkstrafik till trafik i ett virtuellt Azure-nätverk. En nätverkssäkerhetsgrupp skapas när du distribuerar Azure AD DS som innehåller en uppsättning regler som gör att tjänsten kan tillhandahålla autentiserings- och hanteringsfunktioner. Den här standardnätverkssäkerhetsgruppen är associerad med det virtuella nätverksundernätet som din Azure AD DS-hanterade domän distribueras till.

Följande regler för nätverkssäkerhetsgrupper krävs för att Azure AD DS ska kunna tillhandahålla autentiserings- och hanteringstjänster. Redigera eller ta inte bort dessa regler för nätverkssäkerhetsgrupp för det virtuella nätverksundernätet som din Azure AD DS-hanterade domän distribueras till.

| Portnummer | Protokoll | Källa                             | Mål | Åtgärd | Krävs | Syfte |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Alla         | Tillåt  | Ja      | Synkronisering med din Azure AD-klientorganisation. |
| 3389        | TCP      | CorpNetSaw (2000/90                         | Alla         | Tillåt  | Ja      | Hantering av din domän. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Alla         | Tillåt  | Ja      | Hantering av din domän. |

> [!WARNING]
> Redigera inte dessa nätverksresurser och konfigurationer manuellt. När du associerar en felkonfigurerad nätverkssäkerhetsgrupp eller en användardefinierad vägtabell med undernätet där Azure AD DS distribueras kan du störa Microsofts möjlighet att serva och hantera domänen. Synkroniseringen mellan din Azure AD-klientorganisation och din Azure AD DS-hanterade domän störs också.
>
> Om du använder säker LDAP kan du lägga till den TCP-port 636-regeln som krävs för att tillåta extern trafik om det behövs. Om du lägger till den här regeln får du inte ett tillstånd av nätverkssäkerhetsgruppen. Mer information finns i [Låsa säker LDAP-åtkomst via internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Standardregler för *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*och *DenyAllOutBound* finns också för nätverkssäkerhetsgruppen. Redigera eller ta inte bort dessa standardregler.
>
> Azure SLA gäller inte för distributioner där en felaktigt konfigurerad nätverkssäkerhetsgrupp och/eller användardefinierade vägtabeller har tillämpats som blockerar Azure AD DS från att uppdatera och hantera din domän.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 – synkronisering med Azure AD

* Används för att synkronisera din Azure AD-klient med din Azure AD DS-hanterade domän.
* Utan åtkomst till den här porten kan din Azure AD DS-hanterade domän inte synkroniseras med din Azure AD-klientorganisation. Användare kanske inte kan logga in eftersom ändringar i sina lösenord inte skulle synkroniseras med din Azure AD DS-hanterade domän.
* Inkommande åtkomst till den här porten till IP-adresser är begränsad som standard med hjälp av **azureactiveDirectoryDomainServices-tjänsttaggen.**
* Begränsa inte utgående åtkomst från den här porten.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 – hantering med fjärrskrivbord

* Används för fjärrskrivbordsanslutningar till domänkontrollanter i din Azure AD DS-hanterade domän.
* Standardregeln för nätverkssäkerhetsgrupp använder *CorpNetSaw-tjänsttaggen* för att ytterligare begränsa trafiken.
    * Med det här tjänstmärket tillåts endast arbetsstationer för säker åtkomst i Microsofts företagsnätverk för att använda fjärrskrivbord till den Hanterade Azure AD DS-domänen.
    * Åtkomst tillåts endast med affärsmotivering, till exempel för hanterings- eller felsökningsscenarier.
* Den här regeln kan ställas in *på Neka*och endast ställas in *på Tillåt* vid behov. De flesta hanterings- och övervakningsuppgifter utförs med PowerShell-ommotning. RDP används endast i den sällsynta händelsen att Microsoft behöver ansluta på distans till din hanterade domän för avancerad felsökning.

> [!NOTE]
> Du kan inte manuellt välja *CorpNetSaw-tjänstmärket* från portalen om du försöker redigera den här nätverkssäkerhetsgruppsregeln. Du måste använda Azure PowerShell eller Azure CLI för att manuellt konfigurera en regel som använder *CorpNetSaw-tjänsttaggen.*

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 – hantering med PowerShell-ommotning

* Används för att utföra hanteringsuppgifter med PowerShell-ommotning i din Azure AD DS-hanterade domän.
* Utan åtkomst till den här porten kan din Azure AD DS-hanterade domän inte uppdateras, konfigureras, säkerhetskopieras eller övervakas.
* För Azure AD DS-hanterade domäner som använder ett Resource Manager-baserat virtuellt nätverk kan du begränsa inkommande åtkomst till den här porten till *AzureActiveDirectoryDomainServices-tjänsttaggen.*
    * För äldre Azure AD DS-hanterade domäner med ett klassiskt virtuellt nätverk Du kan begränsa inkommande åtkomst till den här porten till följande käll-IP-adresser: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83,* *52.161.13.95*, *104.40.156.18*och *104.40.87.209*.

    > [!NOTE]
    > Under 2017 blev Azure AD Domain Services tillgängliga för värd i ett Azure Resource Manager-nätverk. Sedan dess har vi kunnat skapa en säkrare tjänst med hjälp av Azure Resource Manager moderna funktioner. Eftersom Azure Resource Manager-distributioner helt ersätter klassiska distributioner, kommer Azure AD DS klassiska virtuella nätverksdistributioner att dras tillbaka den 1 mars 2023.
    >
    > För mer information, se det [officiella meddelandet om utfasning](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Användardefinierade vägar

Användardefinierade vägar skapas inte som standard och behövs inte för att Azure AD DS ska fungera korrekt. Om du måste använda rutttabeller bör du undvika att göra några ändringar i vägen *0.0.0.0.* Ändringar i den här vägen stör Azure AD Domain Services och placerar den hanterade domänen i ett tillstånd som inte stöds.

Du måste också dirigera inkommande trafik från IP-adresserna som ingår i respektive Azure-tjänsttaggar till undernätet Azure AD Domain Services. Mer information om tjänsttaggar och tillhörande IP-adress från finns i [Azure IP Ranges and Service Tags - Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Dessa AZURE datacenter IP-intervall kan ändras utan föregående meddelande. Se till att du har processer för att validera att du har de senaste IP-adresserna.

## <a name="next-steps"></a>Nästa steg

Mer information om några av de nätverksresurser och anslutningsalternativ som används av Azure AD DS finns i följande artiklar:

* [Azure virtuell nätverks peering](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN-gateways](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure-nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)
