---
title: Vanliga frågor och svar med Azure DNS | Microsoft Docs
description: Vanliga frågor och svar om Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: victorh
ms.openlocfilehash: 747b2e2499a9bafcf7a7b03bc2ce144828c55c75
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172508"
---
# <a name="azure-dns-faq"></a>Vanliga frågor och svar med Azure DNS

## <a name="about-azure-dns"></a>Om Azure DNS

### <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) namnet på en webbplats eller tjänst till dess IP-adress. Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

DNS-domäner i Azure DNS finns på Azures globala nätverk av DNS-namnservrarna. Detta använder Anycast-nätverk så att varje DNS-frågan besvaras av den närmast tillgängliga DNS-servern. Azure DNS innehåller både snabb prestanda och hög tillgänglighet för din domän.

Azure DNS-tjänsten bygger på Azure Resource Manager. Därmed kan dra nytta av Resource Manager-funktioner som rollbaserad åtkomstkontroll, granskningsloggar och resurslåsning. Dina domäner och poster kan hanteras via Azure portal, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med tjänsten via REST API och SDK: er.

### <a name="how-much-does-azure-dns-cost"></a>Hur mycket kostar Azure DNS?

Faktureringsmodellen för Azure DNS baseras på antalet DNS-zoner som finns i Azure DNS och antalet DNS-frågor som de får. Rabatter lämnas baserat på användning.

Mer information finns i den [Azure DNS-sidan med priser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Vad finns det för serviceavtal för Azure DNS?

Azure garanterar att giltiga DNS-begäranden får svar från minst en server i Azure DNS-namnet på minst 99,99% av tiden.

Mer information finns i den [Azure DNS-SLA-sida](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Vad är en DNS-zon? Är det detsamma som en DNS-domän? 

En domän är ett unikt namn i domännamnssystemet, till exempel ”contoso.com”.


En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen ”contoso.com” kan till exempel innehålla flera DNS-poster, till exempel ”mail.contoso.com” (för en e-postserver) och ”www.contoso.com” (för en webbplats). Dessa poster finns i DNS-zonen ”contoso.com”.

Ett domännamn är *bara ett namn*, medan en DNS-zon är en Dataresurs som innehåller DNS-poster för ett domännamn. Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. Det ger också DNS-namnservrar för att besvara DNS-frågor från Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Måste jag köpa ett DNS-domännamn för att använda Azure DNS? 

Inte nödvändigtvis.

Du behöver inte köpa en domän för att tillhandahålla en DNS-zon i Azure DNS. Du kan skapa en DNS-zon när som helst utan att äga domännamnet. DNS-frågor för den här zonen löser endast om de dirigeras till Azure DNS-namnservrarna som tilldelats i zonen.

Du måste köpa domännamnet om du vill länka DNS-zonen till den globala DNS-hierarkin, den här länkar kan DNS-frågor från var som helst i världen kan hitta din DNS-zon och besvaras med dina DNS-poster.

## <a name="azure-dns-features"></a>Azure DNS-funktioner

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS som har stöd för DNS-baserad routning eller slutpunkt trafikredundans?

DNS-baserad Routning och slutpunkten trafikredundans tillhandahålls av Azure Traffic Manager. Azure Traffic Manager är en separat Azure-tjänst som kan användas tillsammans med Azure DNS. Mer information finns i den [översikt över Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS stöder endast som är värd för ”statiska” DNS-domäner, där varje DNS-fråga för att DNS-posten alltid får samma DNS-svaret.

### <a name="does-azure-dns-support-domain-name-registration"></a>Stöder domännamnsregistrering i Azure DNS?

Nej. Azure DNS stöder för närvarande inte köpa domännamn. Om du vill köpa domäner kan behöva du använda en tredjeparts-domännamnsregistrator. Registratorn oftast debiterar en liten årsavgift. Domänerna som kan finnas i Azure DNS för hantering av DNS-poster. Se [delegera en domän till Azure DNS](dns-domain-delegation.md) mer information.

Köp domän är en funktion som spåras i Azure kvarvarande uppgifter. Du kan använda feedback-plats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Stöder DNSSEC i Azure DNS?

Nej. Azure DNS stöder för närvarande inte DNSSEC.

DNSSEC är en funktion som spåras i Azure DNS eftersläpning. Du kan använda feedback-plats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Stöder zonöverföringar (AXFR/IXFR) i Azure DNS?

Nej. Azure DNS stöder för närvarande inte zonöverföringar. DNS-zoner kan vara [importeras till Azure DNS med Azure CLI](dns-import-export.md). DNS-poster kan sedan hanteras den [hanteringsportalen för Azure DNS](dns-operations-recordsets-portal.md), våra [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-cmdletar](dns-operations-recordsets.md), eller [CLI-verktyget](dns-operations-recordsets-cli.md).

Överföring av zoner är en funktion som spåras i Azure DNS eftersläpning. Du kan använda feedback-plats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Stöder omdirigeringar URL: en i Azure DNS?

Nej. URL: en omdirigerings-tjänster är inte faktiskt en DNS-tjänst – de fungerar med HTTP-nivå i stället för DNS-nivå. Vissa DNS-providrar för att sammanställa en URL omdirigera service som en del av sitt övergripande erbjudande. Detta stöds inte för närvarande av Azure DNS.

URL-omdirigering funktionen spåras i Azure DNS eftersläpning. Du kan använda feedback-plats till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Stöder utökad ASCII kodning (8-bitars) set för TXT-postuppsättning i Azure DNS?

Ja. Azure DNS stöder utökade ASCII-kodning uppsättningen för TXT-postuppsättningar, om du använder den senaste versionen av Azure REST API: er, SDK: er, PowerShell och CLI (versioner som är äldre än 2017-10-01 eller SDK 2.1 gör inte hantera utökade ASCII). Till exempel om användaren anger en sträng som värde för en TXT-post som har utökade ASCII-tecken \128 (t.ex.: ”abcd\128efgh”), Azure DNS använder byte-värde för det här tecknet (vilket är 128) i intern representation. Vid tidpunkten för DNS-matchningen returneras samt byte-värde i svaret. Observera också att ”abc” och ”\097\098\099” är utbytbara upplösning fråga. 

Vi följer [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zon filen master format escape-regler för TXT-poster. Exempel ”: \” nu faktiskt Undertrycker allt per RFC. Om du anger ”A\B” som värde för TXT-post, representeras och Lös som bara ”AB”. Om du verkligen TXT-posten att ha ”A\B” på lösning måste du escape-de ”\" igen, dvs Ange som ”A\\B”. 

Observera att det här stödet inte är för närvarande tillgänglig för TXT-poster som skapats från Azure Portal. 

## <a name="using-azure-dns"></a>Med Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Kan jag Samkör en domän med hjälp av Azure DNS och en annan DNS-provider?

Ja. Azure DNS stöder delad hosting domäner med andra DNS-tjänster.

Om du vill göra det måste du ändra NS-poster för domänen (som styr vilka leverantörer får DNS-frågor för domänen) så att den pekar till namnservrarna för båda providers. Du kan ändra dessa NS-poster på tre platser: i Azure DNS, i en annan provider och i den överordnade zonen (vanligtvis konfigureras via domännamnsregistratorn). Mer information om DNS-delegering finns i [DNS domändelegering](dns-domain-delegation.md).

Dessutom måste du se till att DNS-poster för domänen är synkroniserade mellan båda DNS-providers. Azure DNS stöder för närvarande inte DNS-zonöverföring. DNS-poster måste synkroniseras med antingen den [hanteringsportalen för Azure DNS](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-cmdletar](dns-operations-recordsets.md), eller [CLI-verktyget](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Måste jag delegera min domän till alla fyra Azure DNS-namnservrarna?

Ja. Azure DNS tilldelar varje DNS-zon för felisolering och ökad återhämtning fyra namnservrarna. För att kvalificera dig för serviceavtalet för Azure DNS, måste du delegera din domän till alla fyra namnservrarna.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Vilka är gränserna för användning för Azure DNS?

Följande standard begränsningar gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan jag flytta en Azure DNS-zon mellan resursgrupper eller mellan prenumerationer?

Ja. DNS-zoner kan flyttas mellan resursgrupper eller prenumerationer.

Det finns ingen inverkan på DNS-frågor när du flyttar en DNS-zon. Namnservrarna som tilldelats i zonen oförändrade och DNS-frågorna bearbetas som vanligt i hela.

Mer information och anvisningar om hur du flyttar DNS-zoner finns i [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hur lång tid tar det för DNS-ändringarna ska börja gälla?

Ny DNS-zoner och DNS-poster vanligtvis återspeglas på Azure DNS-namnservrarna snabbt, inom några sekunder.

Ändringar av befintliga DNS-poster kan det ta lite längre, men bör fortfarande återspeglas på Azure DNS-namnservrarna inom 60 sekunder. I det här fallet DNS-cachelagring utanför Azure DNS (av DNS-klienter och DNS-rekursiva matchare) kan även att påverka den tid det tar innan en DNS-ändringen börjar gälla. Den här cachevaraktighet kan kontrolleras med hjälp av egenskapen Time To Live (TTL) för varje uppsättning av poster.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hur kan jag skydda DNS-zoner mot oavsiktlig borttagning?

Azure DNS hanteras med Azure Resource Manager och fördelar med åtkomstkontrollen funktioner som Azure Resource Manager tillhandahåller. Rollbaserad åtkomstkontroll kan användas för att styra vilka användare har läs- eller skrivbehörighet för DNS-zoner och uppsättningar av poster. Resurslås kan användas för att förhindra oavsiktlig ändring eller borttagning av DNS-zoner och uppsättningar av poster.

Mer information finns i [Protecting DNS-zoner och poster](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hur ställer jag in SPF-poster i Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Löser Azure DNS-namnservrar över IPv6? 

Ja. Azure DNS-namnservrar är dual-stack (har både IPv4 och IPv6-adresser). För att hitta IPv6-adress för Azure DNS-namnservrar som tilldelats DNS-zonen, kan du använda ett verktyg som nslookup (till exempel `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Hur ställer jag in ett internationellt domännamn (IDN) i Azure DNS?

Internationella domännamn (IDN) fungerar genom att koda varje DNS-namn med ”[punycode](https://en.wikipedia.org/wiki/Punycode)”. DNS-frågor görs med hjälp av dessa punycode-kodat namn.

Du kan konfigurera internationella domännamn (IDN) i Azure DNS genom att först konverterar zonnamnet eller postuppsättningen till punycode. Azure DNS stöder för närvarande inte inbyggda konvertering till och från punycode.

## <a name="private-dns"></a>Privata DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Stöder ”privat” domäner i Azure DNS?
Stöd för ”privat” domäner implementeras med hjälp av funktionen för privata zoner.  Den här funktionen är för närvarande tillgängligt som offentlig förhandsversion.  Privata zoner som hanteras med samma verktyg som internet-ansluten Azure DNS-zoner, men de är bara matchas från i de angivna virtuella nätverken.  Se den [översikt](private-dns-overview.md) mer information.

Privata zoner stöds inte på Azure portal just nu. 

Information om andra interna DNS-alternativ i Azure finns i [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Vad är skillnaden mellan virtuella nätverk för registrering och virtuella matchningsnätverk i samband med privata zoner? 
Du kan länka virtuella nätverk till en privat DNS-zon på två sätt – som ett virtuellt nätverk för registrering eller som ett virtuellt nätverk för matchning. Virtuella datorer i det virtuella nätverket kommer att kunna matcha mot poster i den privata zonen i båda fallen. Men om du anger ett virtuellt nätverk som virtuellt registreringsnätverk Azure kommer automatiskt att registreras (dynamisk registrering) DNS-poster i zonen för de virtuella datorerna i det virtuella nätverket. Dessutom när en virtuell dator i en registrering av virtuellt nätverk tas bort, tar Azure automatiskt bort motsvarande DNS-posten från länkade privat zon. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones fungerar i Azure-regioner?
Ja. Privata zoner har stöd för DNS-matchning mellan virtuella nätverk i Azure-regioner, även om du inte uttryckligen peering i virtuella nätverk, så länge som de virtuella nätverken har angetts som virtuella matchningsnätverk för privat zon. Kunder kan måste de virtuella nätverken är peer-kopplad för TCP/HTTP-trafik kan flöda från en region till en annan. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Är anslutningen till Internet från virtuella nätverk som krävs för privata zoner?
Nej. Privata zoner fungerar tillsammans med virtuella nätverk och låt kunderna hantera domäner för virtuella datorer eller andra resurser inom och mellan virtuella nätverk. Ingen Internetanslutning krävs för namnmatchning. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Samma privat zon användas för flera virtuella nätverk för matchning av? 
Ja. Kunder kan associera upp till 10 virtuella matchningsnätverk med en privat zon.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Kan ett virtuellt nätverk som hör till en annan prenumeration läggas till som ett virtuellt nätverk för lösning till en privat zon? 
Ja, så länge användaren har behörighet att skriva åtgärden på såväl de virtuella nätverk som den privata DNS-zonen. Observera att skrivbehörighet kan allokeras till flera RBAC-roller. Till exempel har klassiska nätverk deltagare RBAC-roller skrivbehörighet till virtuella nätverk. Mer information om RBAC-roller finns i [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Automatiskt registrerade VM DNS-poster i en privat zon raderas automatiskt när de virtuella datorerna har tagits bort av kunden?
Ja. Om du tar bort en virtuell dator inom ett virtuellt nätverk för registrering, kommer vi automatiskt ta bort DNS-poster som registrerades i zonen på grund av att ett virtuellt nätverk för registrering. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Kan en automatiskt registrerade VM-post i en privat zon (från ett virtuellt nätverk för registrering) tas bort manuellt? 
Nej. Just nu, virtuell dator DNS-posterna som registreras automatiskt i en privat zon från ett virtuellt nätverk för registrering är inte synlig eller redigerbar av kunder. Men du kan ersätta (Skriv över) sådana automatiskt registrerade DNS-poster med en manuellt skapad DNS-post i zonen. Se följande frågor och svar som åtgärdar detta.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Vad händer om vi försöker manuellt skapa en ny DNS-post till en privat zon som har samma värdnamn som en (automatiskt registrerade) befintlig virtuell dator i ett virtuellt nätverk för registrering? 
Om du försöker skapa en ny DNS-post manuellt i en privat zon som har samma värdnamn som en befintlig virtuell dator (automatiskt registrerade) i ett virtuellt registreringsnätverk tillåter vi den nya DNS-posten att skriva över det automatiskt registrerade VM-post. Dessutom om du därefter försöker ta bort den här manuellt skapade DNS-post från zonen det kommer att radera och automatisk registrering kommer att hända igen (DNS-posten kommer att återskapas automatiskt i zonen) så länge som den virtuella datorn fortfarande finns och har en privat IP kopplade till den. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Vad händer när vi ta bort länken till ett virtuellt registreringsnätverk från en privat zon? Automatiskt registrerade VM-poster från det virtuella nätverket tas bort från zonen samt?
Ja. Om du ta bort länken till ett virtuellt nätverk för registrering (uppdatera DNS-zonen för att ta bort det associerade virtuella nätverket för registrering) från en privat zon, tas eventuella poster som ska registreras automatiskt virtuella datorer från zonen i Azure. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Vad händer om vi tar bort ett registrering (eller lösning) virtuellt nätverk som är länkad till en privat zon? Har vi uppdatera den privata zonen manuellt FN: s-länk det virtuella nätverket för virtuella nätverk som en registrering (eller lösning) från zonen?
Ja. När du tar bort ett virtuellt nätverk för registrering (eller lösning) utan att bryta länken till den från en privat zon först, Azure gör att din borttagning åtgärden ska lyckas, men det virtuella nätverket är inte automatiskt avlänkas från din privata zonen eventuellt. Du måste manuellt ta bort länken till det virtuella nätverket från den privata zonen. Därför är det bäst att först ta bort länken till ditt virtuella nätverk från din privata zonen innan den tas bort.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>Skulle DNS-matchningen med FQDN (internal.cloudapp.net) att fungera även om en privat zon (till exempel: contoso.local) är länkad till ett virtuellt nätverk? 
Ja. Privata zoner funktionen ersätter inte standard DNS-lösningar med hjälp av Azure-tillhandahållen internal.cloudapp.net zonen och erbjuds som en ytterligare funktion eller förbättring. För båda fallen (om förlita dig på Azure-tillhandahållen internal.cloudapp.net eller på din egen privat zon) är det bäst att använda det fullständiga Domännamnet för zonen som du vill matcha mot. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>DNS-suffix på virtuella datorer inom ett virtuellt nätverk som är länkade ändras med privat zon? 
Nej. Just nu, förblir DNS-suffix på de virtuella datorerna i ditt länkade virtuella nätverk som standard medföljer Azure suffix (”*. internal.cloudapp.net”). Du kan dock manuellt ändra den här DNS-suffix på dina virtuella datorer med den privata zonen. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Finns det några begränsningar för privata zoner för den här förhandsversionen?
Ja. Allmänt tillgängliga förhandsversionen finns följande begränsningar:
* 1 virtuella registreringsnätverk per privat zon
* Upp till 10 virtuella matchningsnätverk per privat zon
* Ett givet virtuellt nätverk kan endast kopplas till en privat zon som ett virtuellt nätverk för registrering
* Ett givet virtuellt nätverk kan bara kopplas till upp till 10 privata zoner som ett virtuellt nätverk för lösning
* Om ett virtuellt nätverk för registrering har angetts, DNS-posterna för de virtuella datorerna från det virtuella nätverket som är registrerade i privata zonen kommer inte att visas eller hämtas från Powershell/CLI/API: erna, men VM-poster verkligen har registrerats och löser har
* Omvänd DNS fungerar endast för privata IP-adressutrymme i det virtuella nätverket för registrering
* Omvänd DNS för en privat IP som inte är registrerat i den privata zonen (till exempel: privat IP för en virtuell dator i ett virtuellt nätverk som länkas som ett virtuellt nätverk lösning till en privat zon) returnerar ”internal.cloudapp.net” som DNS-suffix men Detta suffix kan inte matchas.   
* Virtuellt nätverk måste vara tomt (dvs.) Inga virtuella datorer med ett nätverkskort som är ansluten) om från början (dvs.) för första gången) länkar till en privat zon som registrering eller matchning virtuella nätverket. Det virtuella nätverket kan dock sedan vara tomma för att framtida länka som en registrering eller matchning virtuellt nätverk till andra privata zoner. 
* För tillfället stöds villkorlig vidarebefordran inte, till exempel för att aktivera matchning mellan Azure och OnPrem-nätverk. Dokumentation om hur kunderna kan få det här scenariot via andra metoder finns i [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Finns det någon kvoter eller gränser för zoner eller poster för privata zoner?
Det finns ingen separat gränser för antalet zoner som tillåts per prenumeration eller antalet postuppsättningar per zon för privata zoner. Både offentliga och privata zoner räknas mot de övergripande DNS-gränserna som beskrevs [här](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Finns det Portal stöd för privata zoner?
Privata zoner som redan har skapats via mekanismer för icke-Portal (API/PowerShell/CLI/SDK: er) som ska visas på Azure portal, men kunder kommer inte att skapa nya privata zoner eller hantera associationer med virtuella nätverk. Dessutom för virtuella nätverk som är associerade som virtuellt registreringsnätverk syns automatiskt registrerade VM-poster inte från portalen. 

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure DNS](dns-overview.md)
<br>
[Läs mer om hur du använder Azure DNS för privata domäner](private-dns-overview.md)
<br>
[Mer information om DNS-zoner och poster](dns-zones-records.md)
<br>
[Kom igång med Azure DNS](dns-getstarted-portal.md)

