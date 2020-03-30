---
title: Vad är Azure Privat DNS?
description: I den här artikeln kommer du igång med en översikt över den privata DNS-värdtjänsten på Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 97b266398b3ea46d09b04524dad34922f21b1a95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939287"
---
# <a name="what-is-azure-private-dns"></a>Vad är Azure Privat DNS?

Domännamnssystemet, eller DNS, ansvarar för att översätta (eller lösa) ett tjänstnamn till dess IP-adress.  Azure DNS är en värdtjänst för DNS-domäner som tillhandahåller namnmatchning med Microsoft Azure-infrastrukturen. Förutom att stödja INTERNET-vända DNS-domäner, Azure DNS stöder också privata DNS-zoner.

Azure Private DNS tillhandahåller en tillförlitlig och säker DNS-tjänst för att hantera och matcha domännamn i ett virtuellt nätverk utan att du behöver lägga till en anpassad DNS-lösning. Genom att använda privata DNS-zoner kan du använda dina egna egna domännamn i stället för de Azure-angivna namn som finns tillgängliga idag. Med hjälp av anpassade domännamn kan du skräddarsy din virtuella nätverksarkitektur så att den bäst passar din organisations behov. Det ger namnmatchning för virtuella datorer (VMs) i ett virtuellt nätverk och mellan virtuella nätverk. Dessutom kan du konfigurera zonnamn med en vy med delad horisont, vilket gör att en privat och en offentlig DNS-zon kan dela namnet.

Om du vill lösa posterna för en privat DNS-zon från det virtuella nätverket måste du länka det virtuella nätverket till zonen. Länkade virtuella nätverk har fullständig åtkomst och kan matcha alla DNS-poster som publiceras i den privata zonen. Dessutom kan du också aktivera autoregistration på en virtuell nätverkslänk. Om du aktiverar autoregistration på en virtuell nätverkslänk registreras DNS-posterna för de virtuella datorerna i det virtuella nätverket i den privata zonen. När autoregistration är aktiverat uppdaterar Azure DNS också zonposterna när en virtuell dator skapas, ändrar sin IP-adress eller tas bort.

![DNS-översikt](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Använd inte en *lokal* domän för din privata DNS-zon. Alla operativsystem stöder inte detta.

## <a name="benefits"></a>Fördelar

Azure Private DNS ger följande fördelar:

* **Tar bort behovet av anpassade DNS-lösningar**. Tidigare skapade många kunder anpassade DNS-lösningar för att hantera DNS-zoner i sitt virtuella nätverk. Du kan nu hantera DNS-zoner med den inbyggda Azure-infrastrukturen, som tar bort bördan av att skapa och hantera anpassade DNS-lösningar.

* **Använd alla vanliga DNS-postertyper**. Azure DNS stöder A-, AAAA-, CNAME-, MX-, PTR-, SOA-, SRV- och TXT-poster.

* **Hantering av automatisk värdnamnspost**. Tillsammans med att vara värd för dina anpassade DNS-poster underhåller Azure automatiskt värdnamnsposter för de virtuella datorerna i de angivna virtuella nätverken. I det här fallet kan du optimera de domännamn du använder utan att behöva skapa anpassade DNS-lösningar eller ändra program.

* **Värdnamnsmatchning mellan virtuella nätverk**. Till skillnad från Azure-angivna värdnamn kan privata DNS-zoner delas mellan virtuella nätverk. Den här funktionen förenklar scenarier för identifiering av nätverk och tjänster, till exempel virtuell nätverks peering.

* **Välbekanta verktyg och användarupplevelse**. För att minska inlärningskurvan använder den här tjänsten väletablerade Azure DNS-verktyg (Azure-portal, Azure PowerShell, Azure CLI, Azure Resource Manager-mallar och REST API).

* **DNS-stöd för delad horisont**. Med Azure DNS kan du skapa zoner med samma namn som matchar till olika svar från ett virtuellt nätverk och från det offentliga internet. Ett typiskt scenario för DNS med delad horisont är att tillhandahålla en dedikerad version av en tjänst för användning i det virtuella nätverket.

* **Tillgänglig i alla Azure-regioner**. Azure DNS-funktionen för privata zoner är tillgänglig i alla Azure-regioner i det offentliga Azure-molnet.

## <a name="capabilities"></a>Funktioner

Azure DNS innehåller följande funktioner:

* **Automatisk registrering av virtuella datorer från ett virtuellt nätverk som är länkat till en privat zon med autoregistration aktiverat**. De virtuella datorerna registreras (läggs till) i den privata zonen som A-poster som pekar på deras privata IP-adresser. När en virtuell dator i en virtuell nätverkslänk med autoregistration aktiverad tas bort, tar Azure DNS också automatiskt bort motsvarande DNS-post från den länkade privata zonen.

* **Framåt DNS-upplösning stöds över virtuella nätverk som är länkade till den privata zonen**. För DNS-lösning över virtuellt nätverk finns det inget explicit beroende så att de virtuella nätverken är peered med varandra. Du kanske vill peer virtuella nätverk för andra scenarier (till exempel HTTP-trafik).

* **Omvänd DNS-sökning stöds inom det virtuella nätverkets omfattning**. Omvänd DNS-sökning för en privat IP i det virtuella nätverket som tilldelats en privat zon returnerar FQDN som innehåller värd-/postnamnet och zonnamnet som suffix.

## <a name="other-considerations"></a>Andra överväganden

Azure DNS har följande begränsningar:

* Ett specifikt virtuellt nätverk kan bara länkas till en privat zon om automatisk registrering av VM DNS-poster är aktiverad. Du kan dock länka flera virtuella nätverk till en enda DNS-zon.
* Omvänd DNS fungerar bara för privat IP-utrymme i det länkade virtuella nätverket
* Omvänd DNS för en privat IP-adress för en länkad virtuell nätverksreturer *internal.cloudapp.net* som standardsuffix för den virtuella datorn. För virtuella nätverk som är länkade till en privat zon med autoregistration aktiverat returnerar omvänd DNS för en privat IP-adress två FQDN: ett med standardsuffixet *internal.cloudapp.net* och ett annat med suffixet för den privata zonen.
* Villkorad vidarebefordran stöds för närvarande inte internt. Så här aktiverar du lösning mellan Azure och lokala nätverk. Se [Namnmatchning för virtuella datorer och rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Prissättning

Prisinformation finns i [Azure DNS Pricing](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med hjälp av [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata zoner](./private-dns-scenarios.md) som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteenden som du kan förvänta dig för vissa typer av åtgärder, finns i [Vanliga frågor och svar om privata DNS.](./dns-faq-private.md)

* Lär dig mer om DNS-zoner och poster genom att besöka [DNS-zoner och poster översikt](dns-zones-records.md).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
