---
title: Vanliga frågor och svar om Azure Private Link (Vanliga frågor och svar)
description: Läs mer om Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349929"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Private Link (Vanliga frågor och svar)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Vad är Azure Private Endpoint och Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Du kan använda privata slutpunkter för att ansluta till en Azure PaaS-tjänst som stöder privat länk eller till din egen private link-tjänst.
- **[Azure Private Link Service](private-link-service-overview.md)**: Azure Private Link-tjänsten är en tjänst som skapats av en tjänsteleverantör. För närvarande kan en private link-tjänst kopplas till ip-konfigurationen för klientdel för en standardbelastningsutjämningsapparat. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hur skickas trafiken när privat länk används?
Trafiken skickas privat med Microsofts stamnät. Det korsar inte internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Vad är skillnaden mellan en tjänstslutpunkter och en privat slutpunkter?
- När du använder privata slutpunkter beviljas nätverksåtkomst till specifika resurser bakom en viss tjänst som tillhandahåller detaljerad segmentering, även trafik kan nå tjänstresursen från lokala utan att använda offentliga slutpunkter.
- En tjänstslutpunkt förblir en offentligt dirigerbar IP-adress.  En privat slutpunkt är en privat IP i adressutrymmet i det virtuella nätverket där den privata slutpunkten är konfigurerad.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Vad är relationen mellan private link-tjänsten och den privata slutpunkten?
Privat slutpunkt ger åtkomst till flera privata länkresurstyper, inklusive Azure PaaS-tjänster och din egen Private Link Service. Det är en en-till-många relation. En private link-tjänst kan ta emot anslutningar från flera privata slutpunkter. Å andra sidan kan en privat slutpunkt bara ansluta till en Private Link-tjänst.    

## <a name="private-endpoint"></a>Privat slutpunkt 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Kan jag skapa flera privata slutpunkter i samma virtuella nätverk? Kan de ansluta till olika tjänster? 
Ja. Du kan ha flera privata slutpunkter i samma virtuella nätverk eller undernät. De kan ansluta till olika tjänster.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Behöver jag ett dedikerat undernät för privata slutpunkter? 
Nej. Du behöver inte ett dedikerat undernät för privata slutpunkter. Du kan välja en privat slutpunkts-IP från vilket undernät som helst från det virtuella nätverket där tjänsten distribueras.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Kan Private Endpoint ansluta till tjänsten Private Link i Azure Active Directory-klienter? 
Ja. Privata slutpunkter kan ansluta till Private Link-tjänster eller Azure PaaS över AD-klienter.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Kan privat slutpunkt ansluta till Azure PaaS-resurser i Azure-regioner?
Ja. Privata slutpunkter kan ansluta till Azure PaaS-resurser i Azure-regioner.

## <a name="private-link-service"></a>Private Link-tjänst
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Vilka är de förutsättningar för att skapa en private link-tjänst? 
Dina serverenheter för tjänsten ska finnas i ett virtuellt nätverk och bakom en standardbelastningsutjämningsapparat.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hur kan jag skala min private link-tjänst? 
Du kan skala din private link-tjänst på några olika sätt: 
- Lägga till startstödsdor i poolen bakom standardbelastningsutjämningsapparaten 
- Lägg till en IP-adress i tjänsten Private Link. Vi tillåter upp till 8 IPs per Private Link-tjänst.  
- Lägg till ny privat länktjänst i Standard load balancer. Vi tillåter upp till åtta Private Link-tjänster per belastningsutjämnare.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kan jag ansluta min tjänst till flera privata slutpunkter?
Ja. En privat länktjänst kan ta emot anslutningar från flera privata slutpunkter. En privat slutpunkt kan dock bara ansluta till en private link-tjänst.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hur ska jag kontrollera exponeringen av min private link-tjänst?
Du kan styra exponeringen med hjälp av synlighetskonfigurationen på private link-tjänsten. Synligheten stöder tre inställningar:

- **Ingen** - Endast prenumerationer med RBAC-åtkomst kan hitta tjänsten. 
- **Restriktiv** - Endast prenumerationer som är vitlistade och med RBAC-åtkomst kan hitta tjänsten. 
- **Alla** - Alla kan hitta tjänsten. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kan jag skapa en private link-tjänst med grundläggande belastningsutjämnare? 
Nej. Privat länk-tjänst via en grundläggande belastningsutjämnare stöds inte.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Krävs ett dedikerat undernät för private link-tjänsten? 
Nej. Private Link-tjänsten kräver inget dedikerat undernät. Du kan välja vilket undernät som helst i ditt virtuella nätverk där tjänsten distribueras.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jag är en tjänsteleverantör som använder Azure Private Link. Måste jag se till att alla mina kunder har unikt IP-utrymme och inte överlappar mitt IP-utrymme? 
Nej. Azure Private Link innehåller den här funktionen för dig. Därför behöver du inte ha icke-överlappande adressutrymme med kundens adressutrymme. 

##  <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Private Link](private-link-overview.md)
