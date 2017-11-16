---
title: "Azure DNS vanliga frågor och svar | Microsoft Docs"
description: "Vanliga frågor och svar om Azure DNS"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: subsarma
ms.openlocfilehash: 1a2a23fb5de5b5b491f061512a15f7acb6721446
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="azure-dns-faq"></a>Azure DNS vanliga frågor och svar

## <a name="about-azure-dns"></a>Om Azure DNS

### <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) en webbplats eller tjänst namn till dess IP-adress. Azure DNS är värdtjänsten för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

DNS-domäner i Azure DNS finns på Azures globalt nätverk av DNS-namnservrar. Vi använder Anycast nätverk så att varje DNS-frågan besvaras närmaste tillgängliga DNS-servern. Detta ger både snabb prestanda och hög tillgänglighet för din domän.

Azure DNS-tjänsten är baserad på Azure Resource Manager. Därför fördelar från Resource Manager-funktioner, till exempel rollbaserad åtkomstkontroll, granskningsloggar och låsa resurs. Dina domäner och poster kan hanteras via Azure-portalen, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med via REST-API och SDK-tjänsten.

### <a name="how-much-does-azure-dns-cost"></a>Hur mycket kostar Azure DNS?

Azure DNS fakturering modellen baseras på antalet DNS-zoner i Azure DNS och antal DNS-frågor som de får. Rabatter anges baserat på användning.

Mer information finns i [Azure DNS sida med priser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Vad finns det för serviceavtal för Azure DNS?

Vi garanterar att giltiga DNS-begäranden får svar från minst en Azure DNS-namnserver åtminstone 99,99 % av tiden.

Mer information finns i [Azure-serviceavtalet för DNS-sidan](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Vad är en DNS-zon? Är det detsamma som en DNS-domän? 

En domän är ett unikt namn i domain name system, till exempel ”contoso.com”.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen ”contoso.com” kan till exempel innehålla flera DNS-poster, till exempel ”mail.contoso.com” (för en e-postserver) och ”www.contoso.com” (för en webbplats). Dessa finns i DNS-zonen ”contoso.com”.

Ett domännamn *bara ett namn*, medan en DNS-zon är en Dataresurs som innehåller de DNS-posterna för ett domännamn. Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. Det ger också DNS-namnservrar för att besvara DNS-frågor från Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Måste jag köpa ett DNS-domännamn för att använda Azure DNS? 

Inte nödvändigtvis.

Du behöver inte köpa en domän för att tillhandahålla en DNS-zon i Azure DNS. Du kan skapa en DNS-zon när som helst utan att äga domännamnet. DNS-frågor för den här zonen löser endast om de dirigeras till Azure DNS-namnservrar tilldelas zonen.

Du behöver köpa domännamnet om du vill länka din DNS-zonen i den globala DNS-hierarkin – detta gör att DNS-frågor från var som helst i världen för att hitta DNS-zon och besvaras med DNS-poster.

## <a name="azure-dns-features"></a>Azure DNS-funktioner

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS som har stöd för DNS-baserade routning eller slutpunkt trafikredundans?

DNS-baserade Routning och slutpunkten trafikredundans tillhandahålls av Azure Traffic Manager. Detta är en separat Azure-tjänst som kan användas tillsammans med Azure DNS. Mer information finns i [Traffic Manager-översikt](../traffic-manager/traffic-manager-overview.md).

Azure DNS stöder endast värd 'statiska' DNS-domäner, där varje DNS-fråga för att DNS-posten alltid får samma DNS-svaret.

### <a name="does-azure-dns-support-domain-name-registration"></a>Stöder Azure DNS domännamnsregistrering?

Nej. Azure DNS stöder för närvarande inte köp av domännamn. Om du vill köpa domäner måste du använda en tredje parts domännamnsregistratorn. Registratorn debiterar vanligtvis en liten årlig avgift. Domänerna kan finnas i Azure DNS för för hantering av DNS-poster. Se [delegera en domän till Azure DNS](dns-domain-delegation.md) mer information.

Detta är en funktion som vi följer upp på vår eftersläpning. Du kan använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>Stöder Azure DNS ”privat” domäner?
Stöd för ”privat” domäner implementeras med hjälp av privata DNS-zoner.  Den här funktionen är tillgänglig som en förhandsgranskning.  Privata DNS-zoner hanteras med hjälp av samma verktyg som internet Azure DNS-zoner, men de kan bara lösas från inom de angivna virtuella nätverk.  Finns det [översikt](private-dns-overview.md) mer information.

Information om andra interna DNS-alternativ i Azure finns [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>Stöder Azure DNS DNSSEC?

Nej. Azure DNS stöder för närvarande inte DNSSEC.

Detta är en funktion som vi följer upp på vår eftersläpning. Du kan använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Stöder Azure DNS zonöverföringar (AXFR/IXFR)?

Nej. Azure DNS stöder för närvarande inte zonöverföringar. DNS-zoner kan vara [importeras till Azure DNS med hjälp av Azure CLI](dns-import-export.md). DNS-poster kan sedan hanteras den [Azure DNS-hanteringsportalen](dns-operations-recordsets-portal.md), vår [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-cmdlets](dns-operations-recordsets.md), eller [CLI verktyget](dns-operations-recordsets-cli.md).

Detta är en funktion som vi följer upp på vår eftersläpning. Du kan använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Stöder Azure DNS URL omdirigeringar?

Nej. URL: en omdirigering tjänster är inte faktiskt en DNS-tjänst - de fungerar med HTTP-nivå, i stället för DNS-nivå. Vissa DNS-leverantörer att paketera en URL omdirigera tjänsten som en del av deras övergripande erbjudande. Detta stöds inte för närvarande av Azure DNS.

Den här funktionen spåras på vår eftersläpning. Du kan använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Med hjälp av Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Kan jag Samkör en domän med hjälp av Azure DNS och en annan DNS-leverantör?

Ja. Azure DNS stöder samtidigt värd domäner med andra DNS-tjänster.

Om du vill göra det, måste du ändra NS-poster för domänen (som styr vilka providers får DNS-frågor för domänen) så att den pekar till namnservrar för båda providers. Dessa NS-poster som behöver ändras på 3 platser: i Azure DNS, i en annan provider och i den överordnade zonen (normalt konfigureras via domännamnsregistratorn). Mer information om DNS-delegering finns [DNS-delegering i domänen](dns-domain-delegation.md).

Dessutom måste du se till att DNS-poster för domänen är synkroniserat mellan både DNS-providers. Azure DNS stöder för närvarande inte DNS-zonöverföring. Du behöver synkronisera DNS-poster med antingen den [Azure DNS-hanteringsportalen](dns-operations-recordsets-portal.md), vår [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-cmdlets](dns-operations-recordsets.md), eller [CLI verktyget](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>Måste jag delegera min domän till alla 4 Azure DNS-namnservrar?

Ja. Azure DNS tilldelar varje DNS-zonen för isolering av fel och ökad återhämtning 4 namnservrar. För att kunna hantera DNS-Azure-serviceavtalet, behöver du delegera din domän så att alla 4 namnservrar.

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

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>Löser Azure DNS Nameservers via IPv6? 

Ja. Azure DNS Nameservers är dual stack (har både IPv4 och IPv6-adresser). För att hitta IPv6-adressen för Azure DNS-nameservers som tilldelats DNS-zonen, kan du använda ett verktyg, till exempel nslookup (till exempel `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Hur ställer jag in ett internationellt domännamn (IDN) i Azure DNS?

Internationella domännamn (IDN) fungerar genom att koda varje DNS-namn med hjälp av '[punycode](https://en.wikipedia.org/wiki/Punycode)'. DNS-frågor görs med hjälp av namnen punycode-kodat.

Du kan konfigurera internationella domännamn (IDN) i Azure DNS först konverterar zonnamnet eller postuppsättning namn till punycode. Azure DNS stöder för närvarande inte inbyggda konvertering till/från punycode.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Azure DNS](dns-overview.md)
<br>
[Lär dig mer om hur du använder Azure DNS för privata domäner](private-dns-overview.md)
<br>
[Mer information om DNS-zoner och poster](dns-zones-records.md)
<br>
[Kom igång med Azure DNS](dns-getstarted-portal.md)

