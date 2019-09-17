---
title: Vanliga frågor och svar om Azure Private Link
description: Lär dig mer om Azures privata länk.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9239917599b40ac159e1cdbd4d3db6f6e70e12d2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019038"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Private Link

## <a name="private-link"></a>Privat länk

### <a name="what-is-azure-private-link-service-and-private-endpoint"></a>Vad är Azure Private Link service och privat slut punkt?

- **Privat Azure-slutpunkt**: Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Du kan använda privata slut punkter för att ansluta till en Azure PaaS-tjänst som stöder privat länk eller till en egen privat länk tjänst.
- **Azure Private Link-tjänst**: Azure Private Link service är en tjänst som skapats av en tjänst leverantör. För närvarande kan en privat länk tjänst kopplas till klient delens IP-konfiguration för en Standard Load Balancer. 

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kan jag ansluta min tjänst till flera privata slut punkter?
Ja. En privat länk-tjänst kan ta emot anslutningar från flera privata slut punkter. En privat slut punkt kan dock bara ansluta till en privat länk tjänst.  
 
### <a name="is-data-transferred-over-private-link-always-private"></a>Är data som överförs via privat länk alltid privata?
Ja. Alla data via privat Azure-länk stannar kvar på Microsofts stamnät. Den passerar inte Internet.  
 
### <a name="what-is-the-difference-between-a-vnet-service-endpoint-and-a-private-endpoint"></a>Vad är skillnaden mellan en VNet-slutpunkt och en privat slut punkt?
- VNet-tjänstens slut punkter utökar det virtuella nätverkets privata adress utrymme och identiteten för ditt VNet till Azure-tjänsterna, via en direkt anslutning. Med tjänstens slut punkter kan du skydda dina kritiska Azure-tjänsteresurser enbart till dina virtuella nätverk, medan trafiken dirigeras och hålls kvar i Microsofts stamnät nätverk det sdestined till tjänstens offentliga IP-adress.
- Privat slut punkt är en nätverks resurs som fungerar som en start punkt i det virtuella nätverket och använder en privat IP-adress för att uppnå de tjänster som drivs av privat länk. Trafiken dirigeras till en optimerad och finns kvar i Microsoft stamnät nätverket.   

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Vad är förhållandet mellan privat länk tjänst och privat slut punkt?
Det är en en-till-många-relation. En privat länk-tjänst kan ta emot anslutningar från flera privata slut punkter. Å andra sidan kan en privat slut punkt bara ansluta till en privat länk tjänst.    
 
### <a name="will-vnet-service-endpoints-be-deprecated-once-private-endpoints-are-available"></a>Kommer VNet-tjänstens slut punkter att bli föråldrade när privata slut punkter är tillgängliga? 
Nej. VNet-tjänstens slut punkter och privata slut punkter är oberoende teknik/resurser. De kan komplettera varandra och båda kommer att finnas tillsammans. Vissa funktioner och användnings fall kan överlappa varandra, du kan välja den modell som passar dina behov.  
 
### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jag är en tjänst leverantör som använder Azures privata länk. Måste jag se till att alla mina kunder har ett unikt IP-utrymme och inte överlappar mitt IP-utrymme? 
Nej. Azures privata länk tillhandahåller den här funktionen åt dig. Därför behöver du inte ha icke-överlappande adress utrymme med kundens adress utrymme. 
 
## <a name="private-link-service"></a>Privat länk tjänst
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Vilka är kraven för att skapa en privat länk tjänst? 
Tjänstens Server delar bör finnas i ett virtuellt nätverk och bakom en Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hur kan jag skala min privata länk tjänst? 
Du kan skala din privata länk tjänst på ett par olika sätt: 
- Lägg till virtuella server delar till poolen bakom Standard Load Balancer 
- Lägg till en IP-adress i den privata länk tjänsten. Vi tillåter upp till 8 IP-adresser per privat länk-tjänst.  
- Lägg till en ny privat länk-tjänst i Standard Load Balancer. Vi tillåter upp till åtta privata länk tjänster per belastningsutjämnare.   


### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hur styr jag exponeringen för min privata länk tjänst?
Du kan styra exponeringen med hjälp av Synlighets konfigurationen i Private Link-tjänsten. Synligheten stöder tre inställningar:

- **Ingen** – endast prenumerationer med RBAC-åtkomst kan hitta tjänsten. 
- Endast **begränsade** prenumerationer som är vit listas och med RBAC-åtkomst kan hitta tjänsten. 
- **Alla** – alla kan hitta tjänsten. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kan jag skapa en privat länk tjänst med Basic Load Balancer? 
Nej. Tjänsten för privata länkar via en grundläggande Load Balancer stöds inte.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Krävs ett dedikerat undernät för tjänsten för privat länk? 
Nej. Tjänsten för privata länkar kräver inte ett dedikerat undernät. Du kan välja valfritt undernät i ditt VNet där tjänsten distribueras.   

## <a name="private-endpoint"></a>Privat slutpunkt 
 
### <a name="cani-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>CanI skapar du flera privata slut punkter i samma VNet? Kan de ansluta till olika tjänster? 
Ja. Du kan ha flera privata slut punkter i samma VNet eller undernät. De kan ansluta till olika tjänster.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Behöver jag ett dedikerat undernät för privata slut punkter? 
Nej. Du behöver inte ett dedikerat undernät för privata slut punkter. Du kan välja en privat slut punkts-IP från valfritt undernät från det virtuella nätverk där tjänsten distribueras.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Kan privat slut punkt ansluta till Private Link service mellan Azure Active Directory klienter? 
Ja. Privata slut punkter kan ansluta till privata länk tjänster eller Azure-PaaS över AD-klienter.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Kan privat slut punkt ansluta till Azure PaaS-resurser i Azure-regioner?
Ja. Privata slut punkter kan ansluta till Azure PaaS-resurser i Azure-regioner.

##  <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azures privata länk](private-link-overview.md)
