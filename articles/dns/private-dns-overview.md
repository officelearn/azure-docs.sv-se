---
title: Vad är Azure Privat DNS?
description: En översikt över privata DNS som är värd för tjänsten på Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: a8548b4972d5853f09630ae3e9ded05ed6fee32b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076391"
---
# <a name="what-is-azure-private-dns"></a>Vad är Azure Privat DNS?

> [!IMPORTANT]
> Azure Private DNS är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) ett tjänstnamn till dess IP-adress.  Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Förutom att stöda mot internet DNS-domäner, stöder också privata DNS-zoner i Azure DNS.

Azure privata DNS är en tillförlitlig och säker DNS-tjänst för att hantera och matcha namn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Du kan använda dina egna anpassade domännamn i stället för de Azure-tillhandahållen namn som är tillgängliga idag genom att använda privata DNS-zoner. Använda anpassade domännamn hjälper dig att skräddarsy dina virtuella nätverksarkitekturen som bäst passar organisationens behov. Det ger namnmatchning för virtuella datorer (VM) inom ett virtuellt nätverk och mellan virtuella nätverk. Du kan också konfigurera zoner namn med vyn Dela vågrätt, vilket ger en privat och en offentlig DNS-zon kan dela namn.

För att lösa poster i en privat DNS-zon från ditt virtuella nätverk, måste du koppla det virtuella nätverket med zonen. Länkade virtuella nätverk har fullständig åtkomst och kan matcha alla DNS-poster som publicerats i den privata zonen. Du kan dessutom också aktivera autoregistrering på en länk för virtuellt nätverk. Om du aktiverar autoregistrering på en länk för virtuellt nätverk, är DNS-posterna för de virtuella datorerna i det virtuella nätverket registrerade i den privata zonen. När autoregistrering aktiveras, Azure DNS även uppdaterar zonposter när en virtuell dator skapas, ändringar dess ”IP-adress eller tas bort.

![Översikt över DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Du bör inte använda en *Local* domän för privata DNS-zonen. Inte alla operativsystem stöder detta.

## <a name="benefits"></a>Fördelar

Azure privata DNS ger följande fördelar:

* **Eliminerar behovet av anpassade DNS-lösningar**. Många kunder skapade tidigare, anpassade DNS-lösningar för att hantera DNS-zoner i sina virtuella nätverk. Du kan nu hantera DNS-zoner med intern Azure-infrastrukturen, vilket tar bort bördan med att skapa och hantera anpassade DNS-lösningar.

* **Använd alla vanliga DNS-posttyper**. Azure DNS supports A, AAAA, CNAME, MX, PTR, SOA, SRV, and TXT records.

* **Automatisk värdnamn Posthantering**. Tillsammans med som är värd för din anpassade DNS-poster, bibehålla Azure automatiskt värddatorposter för de virtuella datorerna i de angivna virtuella nätverken. I det här scenariot kan du optimera de domännamn som du använder utan att skapa anpassade DNS-lösningar eller ändra de program.

* **Värdnamnsmatchning mellan virtuella nätverk**. Till skillnad från Azure-tillhandahållen värdnamn, kan privata DNS-zoner delas mellan virtuella nätverk. Den här funktionen förenklar över flera nätverk och identifiering av tjänst-scenarier, till exempel vnet-peering.

* **Välbekanta verktyg och användarupplevelse**. Den här tjänsten använder för att minska inlärningskurvan välkänt Azure DNS-verktyg (Azure-portalen, Azure PowerShell, Azure CLI, Azure Resource Manager-mallar och REST API).

* **Dela vågrätt DNS-stöd**. Med Azure DNS kan skapa du zoner med samma namn som matchas till olika svar från inom ett virtuellt nätverk och från det offentliga internet. Ett typiskt scenario för split-horizon DNS är att tillhandahålla en särskild version av en tjänst för användning i ditt virtuella nätverk.

* **Tillgängliga i alla Azure-regioner**. Azure DNS privata zoner funktionen är tillgänglig i alla Azure-regioner i det offentliga Azure-molnet.

## <a name="capabilities"></a>Funktioner

Azure DNS tillhandahåller följande funktioner:

* **Automatisk registrering av virtuella datorer från ett virtuellt nätverk som är länkad till en privat zon med autoregistrering aktiverat**. De virtuella datorerna är registrerad (lades till) till den privata zonen som A-poster som pekar till sina privata IP-adresser. När en virtuell dator i en länk för virtuellt nätverk med autoregistrering aktiverad tas bort, bort Azure DNS också automatiskt motsvarande DNS-posten från länkade privat zon.

* **Vidarebefordra DNS-matchningen stöds mellan olika virtuella nätverk som är länkade till den privata zonen**. För flera virtuella nätverk DNS-matchning finns ingen explicit beroende så att de virtuella nätverken är peer-kopplade med varandra. Dock kanske du vill peerkoppla virtuella nätverk för andra scenarier (till exempel HTTP-trafik).

* **Omvänd DNS-sökning stöds inom omfånget för virtuellt nätverk**. Omvänd DNS-sökning för en privat IP-adress inom det virtuella nätverket som tilldelats en privat zon returnerar det FQDN som innehåller värden/postens namn och zonnamnet som suffix.

## <a name="other-considerations"></a>Annat att tänka på

Azure DNS har följande begränsningar:

* Endast ett virtuellt registreringsnätverk tillåts per privat zon.
* Upp till 10 lösningar tillåts virtuella nätverk per privat zon. Den här gränsen tas bort när den här funktionen blir allmänt tillgänglig.
* Ett specifikt virtuellt nätverk kan länkas till endast en privat zon som ett virtuellt nätverk för registrering.
* Ett specifikt virtuellt nätverk kan länkas till upp till 10 privata zoner som ett virtuellt nätverk för matchning. Den här gränsen tas bort när den här funktionen blir allmänt tillgänglig.
* Om du anger ett virtuellt nätverk för registrering är DNS-posterna för de virtuella datorer från det virtuella nätverket som är registrerade på den privata zonen inte kan visas eller hämtas från Azure Powershell och Azure CLI-API: er. VM-poster verkligen har registrerats och löser har.
* Omvänd DNS fungerar endast för privat IP-adressutrymme i det virtuella nätverket för registrering.
* Omvänd DNS för en privat IP-adress som inte är registrerade i den privata zonen (t.ex, en privat IP-adress för en virtuell dator i ett virtuellt nätverk som länkas som ett virtuellt nätverk för lösning till ett privat zon) returnerar *internal.cloudapp.net* som DNS-suffix. Detta suffix är dock inte matchas.
* Det virtuella nätverket måste vara helt tom första gången du länkar den till en privat zon som ett virtuellt nätverk för registrering eller matchning. Det virtuella nätverket kan dock sedan vara tomma för att framtida länka som en registrering eller matchning virtuellt nätverk till andra privata zoner.
* Villkorlig vidarebefordran stöds för närvarande inte (till exempel för att aktivera matchning mellan Azure och OnPrem-nätverk). Information om hur kunderna kan få det här scenariot via andra metoder finns i [namnmatchning för virtuella datorer och rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
* Omvänd DNS fungerar endast för privat IP-adressutrymme i länkade virtuella nätverk
* Omvänd DNS för en privat IP-adress för ett virtuellt nätverk som är länkade returnerar ”internal.cloudapp.net” som standard-suffixet för den virtuella datorn. För virtuella nätverk som är länkade till en privat zon med autoregistrering aktiverat omvänd DNS för en privat IP-adress som returnerar 2 fullständiga domännamn, en med standard suffixet *internal.cloudapp.net* och en annan med suffixet privat zon.
* Villkorlig vidarebefordran stöds inte. Till exempel för att aktivera matchning mellan Azure och lokala nätverk. Lär dig hur du kan aktivera det här scenariot med andra metoder. Se [namnmatchning för virtuella datorer och rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 


## <a name="pricing"></a>Prissättning

Information om priser finns i [priser för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med hjälp av [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs mer om några vanliga [privat zon scenarier](./private-dns-scenarios.md) som kan genomföras med privata zoner i Azure DNS.

* För vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteende kan du förväntar dig för vissa typer av åtgärder, finns i [privata DNS-vanliga frågor och svar](./dns-faq-private.md).

* Läs mer om DNS-zoner och poster genom att besöka [DNS-zoner och poster översikt](dns-zones-records.md).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
