---
title: Vanliga frågor och svar - Azure DNS
description: I den här artikeln får du lära dig mer om vanliga frågor om Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 76b19cfb3c00a26d81eab81f67d8e156a520f377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77121715"
---
# <a name="azure-dns-faq"></a>Vanliga frågor och svar om Azure DNS

## <a name="about-azure-dns"></a>Om Azure DNS

### <a name="what-is-azure-dns"></a>Vad är Azure DNS?

DNS (Domain Name System) översätter eller löser ett webbplats- eller tjänstnamn till dess IP-adress. Azure DNS är en värdtjänst för DNS-domäner. Den innehåller namnmatchning med hjälp av Microsoft Azure-infrastruktur. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

DNS-domäner i Azure DNS finns i Azures globala nätverk av DNS-namnservrar. Det här systemet använder Anycast-nätverk så att varje DNS-fråga besvaras av den närmaste tillgängliga DNS-servern. Azure DNS ger snabb prestanda och hög tillgänglighet för din domän.

Azure DNS baseras på Azure Resource Manager. Azure DNS drar nytta av Resource Manager-funktioner som rollbaserad åtkomstkontroll, granskningsloggar och resurslåsning. Du kan hantera domäner och poster via Azure-portalen, Azure PowerShell-cmdlets och Azure CLI som inte är plattformen. Program som kräver automatisk DNS-hantering kan integreras med tjänsten via REST API och SDK:er.

### <a name="how-much-does-azure-dns-cost"></a>Hur mycket kostar Azure DNS?

Azure DNS-faktureringsmodellen baseras på antalet DNS-zoner som finns i Azure DNS. Det är också baserat på antalet DNS-frågor de får. Rabatter tillhandahålls baserat på användning.

Mer information finns på [sidan Azure DNS-prissättning](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Vad är serviceavtalet för Azure DNS?

Azure garanterar att giltiga DNS-begäranden får ett svar från minst en Azure DNS-namnserver 100 % av tiden.

Mer information finns på [sidan Azure DNS SLA](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Vad är en DNS-zon? Är det samma sak som en DNS-domän? 

En domän är ett unikt namn i domännamnssystemet. Ett exempel kan vara contoso.com.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen contoso.com kan till exempel innehålla flera DNS-poster. Posterna kan innehålla mail.contoso.com för en e-postserver och www\.contoso.com för en webbplats. Dessa poster finns i DNS-zonen contoso.com.

Ett domännamn är *bara ett namn*. En DNS-zon är en dataresurs som innehåller DNS-posterna för ett domännamn. Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. Det ger också DNS-namnservrar för att svara på DNS-frågor från Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Måste jag köpa ett DNS-domännamn för att kunna använda Azure DNS? 

Inte nödvändigtvis.

Du behöver inte köpa en domän som värd för en DNS-zon i Azure DNS. Du kan skapa en DNS-zon när som helst utan att äga domännamnet. DNS-frågor för den här zonen löser endast om de dirigeras till Azure DNS-namnservrar som tilldelats zonen.

Om du vill länka DNS-zonen till den globala DNS-hierarkin måste du köpa domännamnet. Sedan hittar DNS-frågor från var som helst i världen din DNS-zon och svarar med dina DNS-poster.

## <a name="azure-dns-features"></a>Azure DNS-funktioner

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Finns det några begränsningar när du använder aliasposter för ett domännamn apex med Traffic Manager?

Ja. Du måste använda statiska offentliga IP-adresser med Azure Traffic Manager. Konfigurera det **externa slutpunktsmålet** med hjälp av en statisk IP-adress. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Stöder Azure DNS DNS-baserad trafikroutning eller redundans för slutpunkt?

DNS-baserad trafikroutning och redundans för slutpunkt tillhandahålls av Traffic Manager. Traffic Manager är en separat Azure-tjänst som kan användas med Azure DNS. Mer information finns i [översikten Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS stöder endast värd för statiska DNS-domäner, där varje DNS-fråga för en viss DNS-post alltid får samma DNS-svar.

### <a name="does-azure-dns-support-domain-name-registration"></a>Stöder Azure DNS registrering av domännamn?

Nej. Azure DNS stöder för närvarande inte alternativet att köpa domännamn. För att köpa domäner måste du använda en domännamnsregistrare från tredje part. Registratorn tar vanligtvis ut en liten årsavgift. Domänerna kan sedan finnas i Azure DNS för hantering av DNS-poster. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

Funktionen för att köpa domännamn spåras i Azure-eftersläpningen. Använd feedbackwebbplatsen för att [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Stöder Azure DNS DNSSEC?

Nej. Azure DNS stöder för närvarande inte DNSSEC (Domain Name System Security Extensions).

DNSSEC-funktionen spåras i Azure DNS-eftersläpningen. Använd feedbackwebbplatsen för att [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Stöder Azure DNS zonöverföringar (AXFR/IXFR)?

Nej. Azure DNS stöder för närvarande inte zonöverföringar. DNS-zoner kan [importeras till Azure DNS med hjälp av Azure CLI](dns-import-export.md). DNS-poster hanteras via [Azure DNS-hanteringsportalen,](dns-operations-recordsets-portal.md) [REST API](https://docs.microsoft.com/powershell/module/az.dns), [SDK,](dns-sdk.md) [PowerShell-cmdlets](dns-operations-recordsets.md)eller [CLI-verktyget](dns-operations-recordsets-cli.md).

Zonöverföringsfunktionen spåras i Azure DNS-eftersläpningen. Använd feedbackwebbplatsen för att [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Har Azure DNS support-URL-omdirigeringar?

Nej. URL-omdirigeringstjänster är inte en DNS-tjänst. De fungerar på HTTP-nivå i stället för DNS-nivå. Vissa DNS-leverantörer paketerar en url-omdirigeringstjänst som en del av sitt övergripande erbjudande. Den här tjänsten stöds för närvarande inte av Azure DNS.

URL-omdirigeringsfunktionen spåras i Azure DNS-eftersläpningen. Använd feedbackwebbplatsen för att [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Stöder Azure DNS den utökade ASCII-kodningen (8-bitars) uppsättning för TXT-postuppsättningar?

Ja. Azure DNS stöder den utökade ASCII-kodningsuppsättningen för TXT-postuppsättningar. Men du måste använda den senaste versionen av Azure REST API:er, SDK:er, PowerShell och CLI. Versioner som är äldre än den 1 oktober 2017 eller SDK 2.1 stöder inte den utökade ASCII-uppsättningen. 

Du kan till exempel ange en sträng som värde för en TXT-post som har det utökade ASCII-tecknet \128. Ett exempel är "abcd\128efgh". Azure DNS använder bytevärdet för det här tecknet, som är 128, i intern representation. Vid tidpunkten för DNS-matchning returneras det här bytevärdet i svaret. Observera också att "abc" och "\097\098\099" är utbytbara när det gäller upplösning. 

Vi följer [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zon fil master format escape regler för TXT poster. Till exempel, `\` nu faktiskt undgår allt per RFC. Om du `A\B` anger som TXT-postvärde representeras det `AB`och matchas som bara . Om du verkligen vill att TXT-posten ska `A\B` ha `\` på upplösning, måste du fly igen. Ange som ett `A\\B`exempel .

Det här stödet är för närvarande inte tillgängligt för TXT-poster som skapats från Azure-portalen.

## <a name="alias-records"></a>Aliasposter

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Vilka scenarier är de där aliasposter är användbara?

Se avsnittet scenarier i [översikten över Azure DNS-aliasposter](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Vilka posttyper stöds för aliaspostuppsättningar?

Aliaspostuppsättningar stöds för följande posttyper i en Azure DNS-zon:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Vilka resurser stöds som mål för aliaspostuppsättningar?

- **Peka på en offentlig IP-resurs från en DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och göra den till en aliaspostuppsättning för att peka på en offentlig IP-resurs.
- **Peka på en Traffic Manager-profil från en DNS A/AAAA/CNAME-postuppsättning.** Du kan peka på CNAME för en Traffic Manager-profil från en DNS CNAME-postuppsättning. Ett exempel är contoso.trafficmanager.net. Nu kan du också peka på en Traffic Manager-profil som har externa slutpunkter från en A- eller AAAA-post i DNS-zonen.
- **Peka på en CDN-slutpunkt (Azure Content Delivery Network).** Detta är användbart när du skapar statiska webbplatser med Azure storage och Azure CDN.
- **Peka på en annan DNS-postuppsättning inom samma zon.** Aliasposter kan referera till andra postuppsättningar av samma typ. Du kan till exempel låta en DNS CNAME-postuppsättning vara ett alias för en annan CNAME-postuppsättning av samma typ. Det här arrangemanget är användbart om du vill att vissa postuppsättningar ska vara alias och vissa icke-alias.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Kan jag skapa och uppdatera aliasposter från Azure-portalen?

Ja. Du kan skapa eller hantera aliasposter i Azure-portalen tillsammans med Azure REST API:er, PowerShell, CLI och SDK:er.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Hjälper aliasposter till att se till att min DNS-postuppsättning tas bort när den underliggande offentliga IP-adressen tas bort?

Ja. Den här funktionen är en av de viktigaste funktionerna i aliasposter. Det hjälper dig att undvika potentiella avbrott för användare av ditt program.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Hjälper aliasposter till att se till att min DNS-postuppsättning uppdateras till rätt IP-adress när den underliggande offentliga IP-adressen ändras?

Ja. Den här funktionen är en av de viktigaste funktionerna i aliasposter. Det hjälper dig att undvika potentiella avbrott eller säkerhetsrisker för ditt program.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Finns det några begränsningar när du använder aliaspostuppsättningar för A- eller AAAA-poster för att peka på Traffic Manager?

Ja. Om du vill peka på en Traffic Manager-profil som ett alias från en A- eller AAAA-postuppsättning får Traffic Manager-profilen endast använda externa slutpunkter. När du skapar de externa slutpunkterna i Traffic Manager anger du slutpunkternas faktiska IP-adresser.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Finns det en extra avgift för att använda aliasposter?

Aliasposter är en kvalificering för en giltig DNS-postuppsättning. Det finns ingen ytterligare fakturering för aliasposter.

## <a name="use-azure-dns"></a>Använda Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Kan jag vara värd för en domän med hjälp av Azure DNS och en annan DNS-provider?

Ja. Azure DNS stöder co-hosting domäner med andra DNS-tjänster.

Om du vill konfigurera co-hosting ändrar du NS-posterna för domänen så att de pekar på båda leverantörernas namnservrar. Namnserverposter (NS) styr vilka leverantörer som får DNS-frågor för domänen. Du kan ändra dessa NS-poster i Azure DNS, i den andra providern och i den överordnade zonen. Den överordnade zonen konfigureras vanligtvis via domännamnsregistraren. Mer information om DNS-delegering finns i [DNS-domändelegering](dns-domain-delegation.md).

Kontrollera också att DNS-posterna för domänen är synkroniserade mellan båda DNS-providersna. Azure DNS stöder för närvarande inte DNS-zonöverföringar. DNS-poster måste synkroniseras med hjälp av [antingen Azure DNS-hanteringsportalen,](dns-operations-recordsets-portal.md) [REST API](https://docs.microsoft.com/rest/api/dns/), [SDK,](dns-sdk.md) [PowerShell-cmdlets](dns-operations-recordsets.md)eller [CLI-verktyget](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Måste jag delegera min domän till alla fyra Azure DNS-namnservrar?

Ja. Azure DNS tilldelar fyra namnservrar till varje DNS-zon. Detta arrangemang är för felisolering och ökad motståndskraft. Om du vill kvalificera dig för Azure DNS SLA delegerar du domänen till alla fyra namnservrarna.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Vilka är användningsgränserna för Azure DNS?

Följande standardgränser gäller när du använder Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan jag flytta en Azure DNS-zon mellan resursgrupper eller mellan prenumerationer?

Ja. DNS-zoner kan flyttas mellan resursgrupper eller mellan prenumerationer.

Det finns ingen effekt på DNS-frågor när du flyttar en DNS-zon. De namnservrar som tilldelats zonen förblir desamma. DNS-frågor bearbetas som vanligt hela tiden.

Mer information och instruktioner om hur du flyttar DNS-zoner finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hur lång tid tar det innan DNS-ändringarna börjar gälla?

Nya DNS-zoner och DNS-poster visas vanligtvis snabbt i Azure DNS-namnservrarna. Tidpunkten är några sekunder.

Ändringar i befintliga DNS-poster kan ta lite längre tid. De visas vanligtvis i Azure DNS-namnservrarna inom 60 sekunder. DNS-cachelagring av DNS-klienter och DNS-rekursiva resolvers utanför Azure DNS kan också påverka tidpunkten. Om du vill styra cachens varaktighet använder du egenskapen Time-To-Live (TTL) för varje postuppsättning.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hur kan jag skydda mina DNS-zoner mot oavsiktlig borttagning?

Azure DNS hanteras med hjälp av Azure Resource Manager. Azure DNS drar nytta av de åtkomstkontrollfunktioner som Azure Resource Manager tillhandahåller. Rollbaserade åtkomstkontrollkontroller som användare har läs- eller skrivåtkomst till DNS-zoner och postuppsättningar. Resurslås förhindrar oavsiktlig ändring eller borttagning av DNS-zoner och postuppsättningar.

Mer information finns i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hur konfigurerar jag SPF-poster i Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Matchar Azure DNS-namnservrar över IPv6? 

Ja. Azure DNS-namnservrar är dual stack. Dual stack innebär att de har IPv4- och IPv6-adresser. Om du vill hitta IPv6-adressen för Azure DNS-namnservrar som tilldelats din DNS-zon använder du ett verktyg som nslookup. Ett exempel är `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hur konfigurerar jag ett IDN i Azure DNS?

Internationaliserade domännamn (IDN) kodar varje DNS-namn med [punycode](https://en.wikipedia.org/wiki/Punycode). DNS-frågor görs med hjälp av dessa punktkodkodade namn.

Om du vill konfigurera IDN:er i Azure DNS konverterar du zonnamnet eller postuppsättningsnamnet till punycode. Azure DNS stöder för närvarande inte inbyggd konvertering till eller från punktkod.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure DNS](dns-overview.md).

- [Läs mer om hur du använder Azure DNS för privata domäner](private-dns-overview.md).

- [Läs mer om DNS-zoner och poster](dns-zones-records.md).

- [Kom igång med Azure DNS](dns-getstarted-portal.md).
