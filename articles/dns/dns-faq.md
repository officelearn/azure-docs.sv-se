---
title: Azure DNS vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor och svar om Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: f07f914ccf8ea6df216e3f571e38d7628b2d7fb6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-dns-faq"></a>Azure DNS vanliga frågor och svar

## <a name="about-azure-dns"></a>Om Azure DNS

### <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) en webbplats eller tjänst namn till dess IP-adress. Azure DNS är värdtjänsten för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

DNS-domäner i Azure DNS finns på Azures globalt nätverk av DNS-namnservrar. Här används Anycast nätverk så att varje DNS-frågan besvaras närmaste tillgängliga DNS-servern. Azure DNS innehåller både snabb prestanda och hög tillgänglighet för din domän.

Azure DNS-tjänsten är baserad på Azure Resource Manager. Därför fördelar från Resource Manager-funktioner, till exempel rollbaserad åtkomstkontroll, granskningsloggar och låsa resurs. Dina domäner och poster kan hanteras via Azure-portalen, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med via REST-API och SDK-tjänsten.

### <a name="how-much-does-azure-dns-cost"></a>Hur mycket kostar Azure DNS?

Azure DNS fakturering modellen baseras på antalet DNS-zoner i Azure DNS och antal DNS-frågor som de får. Rabatter anges baserat på användning.

Mer information finns i [Azure DNS sida med priser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Vad finns det för serviceavtal för Azure DNS?

Azure garanterar att giltiga DNS-förfrågningar får ett svar från minst en server i Azure DNS-namnet minst 99,99% av tiden.

Mer information finns i [Azure-serviceavtalet för DNS-sidan](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Vad är en DNS-zon? Är det detsamma som en DNS-domän? 

En domän är ett unikt namn i domain name system, till exempel ”contoso.com”.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen ”contoso.com” kan till exempel innehålla flera DNS-poster, till exempel ”mail.contoso.com” (för en e-postserver) och ”www.contoso.com” (för en webbplats). Dessa poster finns i DNS-zonen ”contoso.com”.

Ett domännamn *bara ett namn*, medan en DNS-zon är en Dataresurs som innehåller de DNS-posterna för ett domännamn. Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. Det ger också DNS-namnservrar för att besvara DNS-frågor från Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Måste jag köpa ett DNS-domännamn för att använda Azure DNS? 

Inte nödvändigtvis.

Du behöver inte köpa en domän för att tillhandahålla en DNS-zon i Azure DNS. Du kan skapa en DNS-zon när som helst utan att äga domännamnet. DNS-frågor för den här zonen löser endast om de dirigeras till Azure DNS-namnservrar tilldelas zonen.

Du behöver köpa domännamnet om du vill länka din DNS-zonen i den globala DNS-hierarkin – den här länkar kan DNS-frågor från var som helst i världen för att hitta DNS-zon och besvaras med DNS-poster.

## <a name="azure-dns-features"></a>Azure DNS-funktioner

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS som har stöd för DNS-baserade routning eller slutpunkt trafikredundans?

DNS-baserade Routning och slutpunkten trafikredundans tillhandahålls av Azure Traffic Manager. Azure Traffic Manager är en separat Azure-tjänst som kan användas tillsammans med Azure DNS. Mer information finns i [Traffic Manager-översikt](../traffic-manager/traffic-manager-overview.md).

Azure DNS stöder endast värd 'statiska' DNS-domäner, där varje DNS-fråga för att DNS-posten alltid får samma DNS-svaret.

### <a name="does-azure-dns-support-domain-name-registration"></a>Stöder Azure DNS domännamnsregistrering?

Nej. Azure DNS stöder för närvarande inte köp av domännamn. Om du vill köpa domäner måste du använda en tredje parts domännamnsregistratorn. Registratorn debiterar vanligtvis en liten årlig avgift. Domänerna kan finnas i Azure DNS för för hantering av DNS-poster. Se [delegera en domän till Azure DNS](dns-domain-delegation.md) mer information.

Domän inköp är en funktion som spåras i Azure eftersläpning. Du kan använda feedbackwebbplats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Stöder Azure DNS DNSSEC?

Nej. Azure DNS stöder för närvarande inte DNSSEC.

DNSSEC är en funktion som spåras på Azure DNS eftersläpning. Du kan använda feedbackwebbplats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Stöder Azure DNS zonöverföringar (AXFR/IXFR)?

Nej. Azure DNS stöder för närvarande inte zonöverföringar. DNS-zoner kan vara [importeras till Azure DNS med hjälp av Azure CLI](dns-import-export.md). DNS-poster kan sedan hanteras den [Azure DNS-hanteringsportalen](dns-operations-recordsets-portal.md), vår [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-cmdlets](dns-operations-recordsets.md), eller [CLI verktyget](dns-operations-recordsets-cli.md).

Överföring av zoner är en funktion som spåras på Azure DNS eftersläpning. Du kan använda feedbackwebbplats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Stöder Azure DNS URL omdirigeringar?

Nej. URL: en omdirigering tjänster är inte faktiskt en DNS-tjänst - de fungerar med HTTP-nivå, i stället för DNS-nivå. Vissa DNS-leverantörer att paketera en URL omdirigera tjänsten som en del av deras övergripande erbjudande. Detta stöds inte för närvarande av Azure DNS.

Omdirigerings-URL: en funktion spåras på Azure DNS eftersläpning. Du kan använda feedbackwebbplats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Med hjälp av Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Kan jag Samkör en domän med hjälp av Azure DNS och en annan DNS-leverantör?

Ja. Azure DNS stöder samtidigt värd domäner med andra DNS-tjänster.

Om du vill göra det, måste du ändra NS-poster för domänen (som styr vilka providers får DNS-frågor för domänen) så att den pekar till namnservrar för båda providers. Du kan ändra dessa NS-poster på tre platser: i Azure DNS, i en annan provider och i den överordnade zonen (normalt konfigureras via domännamnsregistratorn). Mer information om DNS-delegering finns [DNS-delegering i domänen](dns-domain-delegation.md).

Dessutom måste du se till att DNS-poster för domänen är synkroniserat mellan både DNS-providers. Azure DNS stöder för närvarande inte DNS-zonöverföring. DNS-poster måste synkroniseras med antingen den [Azure DNS-hanteringsportalen](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-cmdlets](dns-operations-recordsets.md), eller [CLI verktyget](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Måste jag delegera min domän till alla fyra Azure DNS-namnservrar?

Ja. Azure DNS tilldelar varje DNS-zonen för isolering av fel och ökad återhämtning fyra namnservrar. För att kunna hantera DNS-Azure-serviceavtalet, behöver du delegera din domän så att alla fyra namnservrar.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Vad är gränserna för Resursanvändning för Azure DNS?

Följande standard begränsningar gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan jag flytta en Azure DNS-zon mellan resursgrupper eller mellan prenumerationer?

Ja. DNS-zoner kan flyttas mellan resursgrupper eller mellan prenumerationer.

Det finns ingen inverkan på DNS-frågor när du flyttar en DNS-zon. Namnservrar tilldelas zonen är desamma och DNS-frågor bearbetas i.

Mer information och anvisningar om hur du flyttar DNS-zoner finns [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hur lång tid tar det för DNS-ändringarna ska börja gälla?

Den nya DNS-zoner och DNS-poster vanligtvis återspeglas i Azure DNS-namnservrar snabbt inom några sekunder.

Ändringar av befintliga DNS-poster kan ta lite längre tid, men fortfarande visas på Azure DNS-namnservrar inom 60 sekunder. DNS-cachelagring utanför Azure DNS (genom DNS-klienter och rekursiva DNS-matchare) kan då också påverka den tid det tar för en DNS-ändringarna ska gälla. Cachevaraktigheten kan kontrolleras med hjälp av egenskapen Time To Live (TTL) för varje uppsättning av poster.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hur kan jag skydda DNS-zoner mot oavsiktlig borttagning?

Azure DNS hanteras med Azure Resource Manager och fördelar från åtkomstkontrollen funktioner som Azure Resource Manager tillhandahåller. Rollbaserad åtkomstkontroll kan användas för att styra vilka användare som har läs- eller skrivbehörighet till DNS-zoner och postuppsättningar. Resurslås kan användas för att förhindra oavsiktlig ändring eller borttagning av DNS-zoner och postuppsättningar.

Mer information finns i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hur ställer jag in SPF-poster i Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Löser Azure DNS Nameservers via IPv6? 

Ja. Azure DNS Nameservers är dual stack (har både IPv4 och IPv6-adresser). För att hitta IPv6-adressen för Azure DNS-nameservers som tilldelats DNS-zonen, kan du använda ett verktyg, till exempel nslookup (till exempel `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Hur ställer jag in ett internationellt domännamn (IDN) i Azure DNS?

Internationella domännamn (IDN) fungerar genom att koda varje DNS-namn med hjälp av '[punycode](https://en.wikipedia.org/wiki/Punycode)'. DNS-frågor görs med hjälp av namnen punycode-kodat.

Du kan konfigurera internationella domännamn (IDN) i Azure DNS först konverterar zonnamnet eller postuppsättning namn till punycode. Azure DNS stöder för närvarande inte inbyggda konvertering till/från punycode.

## <a name="private-dns"></a>Privata DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Stöder Azure DNS ”privat” domäner?
Stöd för ”privat” domäner implementeras med hjälp av funktionen för privat zoner.  Den här funktionen är för närvarande tillgänglig som förhandsversion.  Privata zoner hanteras med hjälp av samma verktyg som internet Azure DNS-zoner, men de kan bara lösas från inom de angivna virtuella nätverk.  Finns det [översikt](private-dns-overview.md) mer information.

Privat zoner stöds inte på Azure-portalen just nu. 

Information om andra interna DNS-alternativ i Azure finns [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Vad är skillnaden mellan virtuella nätverk för registrering och matchning av virtuella nätverk i samband med privata zoner? 
Du kan länka virtuella nätverk till en DNS-privata zon på två sätt – registrering av virtuellt nätverk eller ett virtuellt nätverk för matchning. Virtuella datorer i det virtuella nätverket kommer att kunna matcha mot poster i zonen privata i båda fallen. Men om du anger ett virtuellt nätverk som ett virtuellt nätverk för registrering av registreras Azure automatiskt (dynamisk registrering) DNS-poster i zonen för de virtuella datorerna i det virtuella nätverket. Dessutom när en virtuell dator i en registrering av virtuellt nätverk hämtar bort Azure kommer också automatiskt ta bort motsvarande DNS-posten från länkade privata zonen. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Fungerar Azure privata DNS-zoner i Azure-regioner
Ja. Privat zoner stöds för DNS-matchning mellan virtuella nätverk i Azure-regioner, även om du inte uttryckligen peering virtuella nätverk, så länge som de virtuella nätverk som har angetts som upplösning virtuella nätverk för privata zonen. Kunder kan måste de virtuella nätverken att peerkoppla för TCP/HTTP-trafik från en region till en annan. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Finns en anslutning till Internet från virtuella nätverk som krävs för privat zoner?
Nej. Privat zoner fungerar tillsammans med virtuella nätverk och informera kunderna hantera domäner för virtuella datorer eller andra resurser inom och mellan virtuella nätverk. Ingen Internetanslutning krävs för namnmatchning. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Samma privata zon användas för flera virtuella nätverk för matchning av? 
Ja. Kunder kan koppla upp till 10 upplösning virtuella nätverk med en privat zon.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Kan läggas ett virtuellt nätverk som tillhör en annan prenumeration som ett virtuellt nätverk lösning till en zon för privat? 
Ja, så länge användaren har behörighet att skriva igen på både virtuella nätverk samt privata DNS-zonen. Observera att skrivbehörighet kan tilldelas flera RBAC-roller. Till exempel har rollen klassiska Network-deltagare RBAC skrivbehörighet till virtuella nätverk. Mer information om RBAC-roller finns [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-what-is.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Automatiskt registrerade virtuella DNS-poster i en privat zon raderas automatiskt när de virtuella datorerna tas bort av kunden?
Ja. Vi tar automatiskt bort DNS-poster som har registrerats i zonen på grund av den här ett virtuellt nätverk för registrering om du tar bort en virtuell dator inom ett virtuellt nätverk för registrering. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Kan en post automatiskt registrerade virtuell dator i en privat zon (från ett virtuellt nätverk för registrering) tas bort manuellt? 
Nej. För tillfället virtuella DNS-posterna som registreras automatiskt i en privat zon från ett virtuellt nätverk för registrering är inte synlig eller redigerbar av kunder. Du kan dock ersätta (Skriv över) sådana automatiskt registrerade DNS-poster med en manuellt skapad DNS-post i zonen. Se följande fråga och svar som detta.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Vad händer när vi försökte skapa en ny DNS-post manuellt till ett privat område som har samma värdnamn som en (automatiskt registrerade) befintlig virtuell dator i ett virtuellt nätverk för registrering? 
Om du försöker skapa en ny DNS-post manuellt till ett privat område som har samma värdnamn som en befintlig virtuell dator (automatiskt registrerade) i ett virtuellt nätverk för registrering tillåter vi den nya DNS-posten att skriva över det automatiskt registrerade logg för virtuell dator. Dessutom, om du därefter försöker ta bort den här manuellt skapade DNS-post från zonen lyckas ta bort och automatisk registrering sker igen (DNS-posten kommer att återskapas automatiskt i zonen) så länge som den virtuella datorn fortfarande finns och har en privat IP kopplade till den. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Vad händer när vi Avlänka registrering av virtuellt nätverk från en privat zon? Automatiskt registrerade virtuella poster från det virtuella nätverket tas bort från zonen samt?
Ja. Om du Avlänka ett virtuellt nätverk för registrering (uppdatera DNS-zon att ta bort det virtuella nätverket som har associerade registrering) från en zon för privat tar Azure bort alla poster som ska registreras automatiskt virtuella från zonen. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Vad händer när vi tar bort ett registrering (eller upplösning) virtuellt nätverk som är kopplad till en zon för privat? Har vi manuellt uppdatera privata zonen un-länk det virtuella nätverket för virtuella nätverk som registrering (eller upplösning) från zonen?
Ja. När du tar bort ett virtuellt nätverk för registrering (eller upplösning) utan att bryta länken till den från ett privat zonen först, Azure kan tas bort åtgärden ska lyckas, men det virtuella nätverket är inte automatiskt olänkade från zonen privata eventuella. Du måste manuellt ta bort länk det virtuella nätverket från zonen privata. Därför är det bäst att Avlänka först ditt virtuella nätverk från zonen privata innan den tas bort.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>Skulle DNS-matchning med FQDN (internal.cloudapp.net) fortfarande fungera även om en zon för privat (till exempel: contoso.local) är kopplad till ett virtuellt nätverk? 
Ja. Privat zoner funktionen ersätter inte standard DNS-lösningar med hjälp av Azure-tillhandahållna internal.cloudapp.net zonen och erbjuds som en ytterligare funktion eller förbättring. För båda fallen (om förlita dig på Azure-tillhandahållna internal.cloudapp.net eller på din egen privata zon) är det bäst att använda det fullständiga Domännamnet för zonen som du vill matcha mot. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>DNS-suffix på virtuella datorer i ett virtuellt nätverk som är länkade ändras som för zonen privat? 
Nej. Just nu, förblir DNS-suffix på de virtuella datorerna i ditt länkade virtuella nätverk som standard-Azure-tillhandahållna suffix (”*. internal.cloudapp.net”). Du kan dock manuellt ändra den här DNS-suffix på virtuella datorer som för zonen privata. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Finns det några begränsningar för privat zoner under den här förhandsgranskningen?
Ja. Under Public Preview finns följande begränsningar:
* 1 registrering virtuella nätverk per privata zon
* Matchning av upp till 10 virtuella nätverk per privata zon
* Ett givet virtuellt nätverk kan bara kopplas till en privat zon som ett virtuellt nätverk för registrering
* Ett givet virtuellt nätverk kan länkas till upp till 10 privata zoner som ett virtuellt nätverk upplösning
* Om ett virtuellt nätverk för registrering har angetts DNS-posterna för de virtuella datorerna från det virtuella nätverket som är registrerade i zonen för privat kommer inte att visas eller hämtas från Powershell/CLI/API: erna, men VM-poster registreras verkligen och löser har
* Omvänd DNS fungerar endast för privata IP-adressutrymme i det virtuella nätverket för registrering
* Omvänd DNS för en privat IP som inte är registrerat i zonen privat (till exempel: privat IP för en virtuell dator i ett virtuellt nätverk som är länkad som ett virtuellt nätverk lösning till en privat zon) returnerar ”internal.cloudapp.net” som DNS-suffix men Det här suffixet kommer inte kunna matchas.   
* Virtuella nätverk måste vara tom (dvs Inga virtuella datorer med ett nätverkskort anslutet) när initialt (dvs för första gången) länka till en privat zon som registreringen eller matchningen virtuella nätverk. Det virtuella nätverket kan dock sedan vara icke-tom för framtida länkning som ett registrerings- eller virtuellt nätverk till andra privata zoner. 
* För tillfället stöds villkorlig vidarebefordran inte, till exempel för att aktivera matchning mellan Azure och OnPrem nätverk. Dokumentation om hur kunder kan utnyttja det här scenariot via andra mekanismer finns [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Finns det någon kvoter eller gränserna för zoner eller poster för privat zoner?
Det finns ingen separat begränsning på antalet zoner som tillåts per prenumeration eller Antal uppsättningar av poster per zon för privat zoner. Både offentliga och privata zoner räknas in i de övergripande DNS-begränsningar enligt [här](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Finns det stöd för portalen för privat zoner?
Privat zoner som redan har skapats via externt metoder (API/PowerShell/CLI/SDK) visas på Azure-portalen, men kunder kommer inte kunna skapa nya privata zoner eller hantera associationer med virtuella nätverk. Dessutom för virtuella nätverk som är associerade som registrering virtuella nätverk, visas automatiskt registrerade VM-poster inte från portalen. 

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Azure DNS](dns-overview.md)
<br>
[Lär dig mer om hur du använder Azure DNS för privata domäner](private-dns-overview.md)
<br>
[Mer information om DNS-zoner och poster](dns-zones-records.md)
<br>
[Kom igång med Azure DNS](dns-getstarted-portal.md)

