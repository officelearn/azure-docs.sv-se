---
title: Använda Azure DNS för privata domäner | Microsoft Docs
description: En översikt av privata DNS-värdtjänsten på Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 0ee3b18b7f874c4f6b7b2c9c559aa7e393ad7d8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700612"
---
# <a name="use-azure-dns-for-private-domains"></a>Använd Azure DNS för privata domäner
Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) ett tjänstnamn till dess IP-adress. Värdtjänsten för DNS-domäner Azure DNS ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Förutom att mot internet DNS-domäner, stöder Azure DNS nu också privata DNS-domäner som en förhandsversion av funktionen. 
 
Azure DNS är en tillförlitlig, säker DNS-tjänst för att hantera och lösa domännamn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Du kan använda dina egna anpassade domännamn i stället för Azure-tillhandahållna namnen finns idag genom att använda privata DNS-zoner. Använda anpassade domännamn kan du anpassa din virtuella nätverksarkitekturen som bäst passar organisationens behov. Det ger namnmatchning för virtuella datorer (VM) inom ett virtuellt nätverk och mellan virtuella nätverk. Du kan dessutom konfigurera zoner namn med en delning vy, vilket gör att en privat och en offentlig DNS-zon med samma namn.

![Översikt över DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Fördelar

Azure DNS ger följande fördelar:

* **Tar bort behovet av anpassade lösningar som DNS-**. Många kunder skapade tidigare, anpassade DNS-lösningar för att hantera DNS-zoner i det virtuella nätverket. Du kan nu utföra DNS-zonen hantering med hjälp av inbyggda Azure-infrastrukturen, vilket tar bort för att skapa och hantera anpassade DNS-lösningar.

* **Använda alla vanliga DNS-posttyper**. Azure DNS stöder A, AAAA, CNAME, MX, NS, PTR, SOA, SRV och TXT-poster.

* **Automatisk hostname Posthantering**. Tillsammans med värd för din anpassade DNS-poster, underhåller Azure automatiskt hostname-poster för de virtuella datorerna i de angivna virtuella nätverk. Du kan optimera de domännamn som du använder utan att skapa anpassade lösningar som DNS eller ändra de program i det här scenariot.

* **Värdnamnsmatchning mellan virtuella nätverk**. Till skillnad från Azure-tillhandahållna värdnamn, kan privata DNS-zoner delas mellan virtuella nätverk. Den här funktionen gör det enklare för cross-nätverket och identifiering av tjänst scenarier, till exempel virtuella nätverk peering.

* **Välbekanta verktyg och användarupplevelsen**. Om du vill minska inlärningskurvan använder den här nya erbjudande etablerade Azure DNS-verktyg (PowerShell, Azure Resource Manager-mallar och REST-API).

* **Dela vågrätt DNS-stöd**. Du kan skapa zoner med samma namn att matcha olika svar från inom ett virtuellt nätverk och från internet med Azure DNS. Ett typiskt scenario för Dela vågrätt DNS är att tillhandahålla en särskild version av en tjänst för användning i ditt virtuella nätverk.

* **Tillgänglig i alla Azure-regioner**. Azure DNS-zoner för privata funktionen är tillgänglig i alla Azure-regioner i det offentliga Azure-molnet. 


## <a name="capabilities"></a>Funktioner

Azure DNS innehåller följande funktioner:
 
* **Automatisk registrering av virtuella datorer från ett enda virtuellt nätverk som är kopplad till en zon som ett virtuellt nätverk för registrering för privat**. De virtuella datorerna är registrerad (läggs till) till zonen privata A-poster pekar på sina privata IP-adresser. När en virtuell dator i en registrering av virtuellt nätverk raderas Azure också automatiskt bort motsvarande DNS från länkade privata zonen. 

  > [!NOTE]
  > Som standard fungera registrering virtuella nätverk också som upplösning virtuella nätverk, som DNS-matchning mot zonen fungerar från någon av de virtuella datorerna i det virtuella nätverket för registrering. 

* **Vanlig DNS-matchning stöds mellan virtuella nätverk som är länkade till den privata zonen som upplösning virtuella nätverk**. Mellan virtuella nätverk i DNS-matchning finns ingen explicit beroende så att de virtuella nätverken är peerkopplat med varandra. Kunder kan vill peer-virtuella nätverk för andra scenarier (till exempel HTTP-trafik).

* **Omvänd DNS-sökning stöds inom omfånget för virtuellt nätverk**. Omvänd DNS-sökning för en privat IP-adress i det virtuella nätverket som tilldelats en privat zon returneras det FQDN som innehåller värden/postnamnet samt zonnamnet som suffix. 


## <a name="limitations"></a>Begränsningar

Azure DNS regleras följande begränsningar:

* Bara en registrering virtuella nätverket tillåts per privata zonen.
* Upp till 10 lösningar tillåts virtuella nätverk per privata zon.
* Ett specifikt virtuellt nätverk kan länkas till endast en privat zon som ett virtuellt nätverk för registrering.
* Ett specifikt virtuellt nätverk kan länkas till upp till 10 privata zoner som ett virtuellt nätverk för namnmatchning.
* Om ett virtuellt nätverk för registrering har angetts DNS-posterna för de virtuella datorerna från det virtuella nätverket som är registrerade i zonen privata är inte synlig eller hämtas från Azure Powershell och Azure CLI-API: er, men VM-poster registreras verkligen och kommer Lös har.
* Omvänd DNS-fungerar endast för privata IP-adressutrymme i det virtuella nätverket för registrering.
* Omvänd DNS för en privat IP-adress som inte är registrerat i zonen privata (till exempel en privat IP-adress för en virtuell dator i ett virtuellt nätverk som är länkad som ett virtuellt nätverk lösning till ett privat zon) returnerar *internal.cloudapp.net* som DNS-suffix. Det här suffixet är dock inte matchas. 
* Det virtuella nätverket måste vara tom (det vill säga VM inte finns några poster) när den ursprungligen (det vill säga för första gången) länkar till en privat zon som ett virtuellt nätverk för registrering eller upplösning. Det virtuella nätverket kan dock sedan vara icke-tom för framtida länkning som ett registrerings- eller virtuellt nätverk till andra privata zoner. 
* För tillfället stöds inte villkorlig vidarebefordran (till exempel för att aktivera matchning mellan Azure och OnPrem nätverk). Information om hur kunder kan utnyttja det här scenariot via andra mekanismer finns [namnmatchning för virtuella datorer och rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Vanliga frågor och svar om privata zoner i Azure DNS inklusive specifika DNS-registrering och lösningar som du kan förvänta dig för vissa typer av åtgärder finns i avsnittet [vanliga frågor och svar](./dns-faq.md#private-dns).  


## <a name="pricing"></a>Prissättning

Funktionen privat DNS-zoner är kostnadsfritt under förhandsversion. Vid allmän tillgänglighet funktionen kommer att erbjuda en användningsbaserad prismodellen liknar den befintliga Azure DNS erbjudande. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en zon för privata i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

Läs om några vanliga [privata zonen scenarier](./private-dns-scenarios.md) som kan genomföras med privata zoner i Azure DNS.

Vanliga frågor och svar om privata zoner i Azure DNS inklusive specifikt beteende du kan förvänta dig för vissa typer av åtgärder finns i avsnittet [vanliga frågor och svar](./dns-faq.md#private-dns). 

Lär dig mer om DNS-zoner och poster genom att besöka [DNS-zoner och innehåller en översikt över](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure. 

