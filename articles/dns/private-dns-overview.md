---
title: Använda Azure DNS för privata domäner
description: En översikt över privata DNS som är värd för tjänsten på Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/1/2019
ms.author: victorh
ms.openlocfilehash: 7f5f377f34a43dfb01ea516e023bb98f118d0dd4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247230"
---
# <a name="use-azure-dns-for-private-domains"></a>Använda Azure DNS för privata domäner

Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) ett tjänstnamn till dess IP-adress. En värdtjänst för DNS-domäner med Azure DNS ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Förutom att stöda mot internet DNS-domäner, Azure DNS stöder nu också privata DNS-domäner som en förhandsversion av funktionen.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Azure DNS är en tillförlitlig och säker DNS-tjänst för att hantera och matcha namn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Du kan använda dina egna anpassade domännamn i stället för de Azure-tillhandahållen namn som är tillgängliga idag genom att använda privata DNS-zoner. Använda anpassade domännamn hjälper dig att skräddarsy dina virtuella nätverksarkitekturen som bäst passar organisationens behov. Det ger namnmatchning för virtuella datorer (VM) inom ett virtuellt nätverk och mellan virtuella nätverk. Du kan också konfigurera zoner namn med vyn Dela vågrätt, vilket ger en privat och en offentlig DNS-zon kan dela namn.

Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen. De här kallas *virtuella lösningsnätverk*. Du kan även ange ett virtuellt nätverk där Azure DNS ska bibehålla värddatorposter varje gång en virtuell dator skapas, ändrar IP-adress eller tas bort. Det här kallas *virtuellt registreringsnätverk*.

Om du anger ett virtuellt nätverk för registrering, DNS-posterna för de virtuella datorer från det virtuella nätverket som är registrerade på den privata zonen är inte kan visas eller hämtas från Azure Powershell och Azure CLI-API: er, men VM-poster verkligen har registrerats och kommer Lös har.

![Översikt över DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> As a best practice, do not use a .local domain for your private DNS zone. Inte alla operativsystem stöder detta.

## <a name="benefits"></a>Fördelar

Azure DNS ger följande fördelar:

* **Eliminerar behovet av anpassade DNS-lösningar**. Många kunder skapade tidigare, anpassade DNS-lösningar för att hantera DNS-zoner i sina virtuella nätverk. Du kan nu utföra hantering av DNS-zon med hjälp av interna Azure-infrastrukturen, vilket tar bort bördan med att skapa och hantera anpassade DNS-lösningar.

* **Använd alla vanliga DNS-posttyper**. Azure DNS supports A, AAAA, CNAME, MX, PTR, SOA, SRV, and TXT records.

* **Automatisk värdnamn Posthantering**. Tillsammans med som är värd för din anpassade DNS-poster, bibehålla Azure automatiskt värddatorposter för de virtuella datorerna i de angivna virtuella nätverken. I det här scenariot kan du optimera de domännamn som du använder utan att skapa anpassade DNS-lösningar eller ändra de program.

* **Värdnamnsmatchning mellan virtuella nätverk**. Till skillnad från Azure-tillhandahållen värdnamn, kan privata DNS-zoner delas mellan virtuella nätverk. Den här funktionen förenklar över flera nätverk och identifiering av tjänst-scenarier, till exempel vnet-peering.

* **Välbekanta verktyg och användarupplevelse**. För att minska inlärningskurvan, använder den här nya möjligheten välkänt Azure DNS-verktyg (PowerShell, Azure Resource Manager-mallar och REST API).

* **Dela vågrätt DNS-stöd**. Med Azure DNS kan skapa du zoner med samma namn som matchas till olika svar från inom ett virtuellt nätverk och från det offentliga internet. Ett typiskt scenario för split-horizon DNS är att tillhandahålla en särskild version av en tjänst för användning i ditt virtuella nätverk.

* **Tillgängliga i alla Azure-regioner**. Azure DNS privata zoner funktionen är tillgänglig i alla Azure-regioner i det offentliga Azure-molnet.

## <a name="capabilities"></a>Funktioner

Azure DNS tillhandahåller följande funktioner:

* **Automatisk registrering av virtuella datorer från ett enda virtuellt nätverk som är länkad till en privat zon som virtuellt registreringsnätverk**. De virtuella datorerna är registrerad (lades till) till den privata zonen som A-poster som pekar på sina privata IP-adresser. När en virtuell dator i en registrering av virtuellt nätverk har tagits bort, Azure också automatiskt att ta bort motsvarande DNS från den länkade privata zonen. 

  Som standard fungera virtuella registreringsnätverk också som lösning virtuella nätverk som DNS-matchning mot zonen fungerar från någon av de virtuella datorerna i det virtuella nätverket för registrering.

  > [!NOTE]
  > Om du anger ett virtuellt nätverk för registrering är DNS-posterna för de virtuella datorer från det virtuella nätverket som är registrerade på den privata zonen inte kan visas eller hämtas från Azure Powershell och Azure CLI-API: er. VM-poster verkligen har registrerats och löser har.

* **Vidarebefordra DNS-matchningen stöds mellan olika virtuella nätverk som är länkade till den privata zonen som virtuella matchningsnätverk**. För flera virtuella nätverk DNS-matchning finns ingen explicit beroende så att de virtuella nätverken är peer-kopplade med varandra. Kunder kanske vill peerkoppla virtuella nätverk för andra scenarier (till exempel HTTP-trafik).

* **Omvänd DNS-sökning stöds inom omfånget för virtuellt nätverk**. Omvänd DNS-sökning för en privat IP-adress inom det virtuella nätverket som tilldelats en privat zon returnerar det FQDN som innehåller värden/postens namn och zonnamnet som suffix.

## <a name="limitations"></a>Begränsningar

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

För vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika DNS-registrering och upplösning som du kan förvänta dig för vissa typer av åtgärder, finns i [vanliga frågor och svar](./dns-faq.md#private-dns).  

## <a name="pricing"></a>Prissättning

Privata DNS-zoner funktionen är kostnadsfritt under den offentliga förhandsversionen. Under allmänt tillgängliga erbjuder funktionen en användningsbaserad Prismodell liknande den i den befintliga Azure DNS erbjuder. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en privat zon i Azure DNS med hjälp av [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

Läs mer om några vanliga [privat zon scenarier](./private-dns-scenarios.md) som kan genomföras med privata zoner i Azure DNS.

För vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteende kan du förväntar dig för vissa typer av åtgärder, finns i [vanliga frågor och svar](./dns-faq.md#private-dns). 

Läs mer om DNS-zoner och poster genom att besöka [DNS-zoner och poster översikt](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
