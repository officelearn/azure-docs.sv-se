---
title: Vanliga frågor och svar med Azure DNS
description: Vanliga frågor och svar om Azure DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: bb5c4d508344f391d610aeaa7e0be54a93c997dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080034"
---
# <a name="azure-dns-faq"></a>Vanliga frågor och svar med Azure DNS

## <a name="about-azure-dns"></a>Om Azure DNS

### <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Domain Name System (DNS) översätter eller matchar namnet på en webbplats eller tjänst till dess IP-adress. Azure DNS är en värdtjänst för DNS-domäner. Det ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

DNS-domäner i Azure DNS finns på Azure globala nätverk av DNS-namnservrarna. Det här systemet använder Anycast-nätverk så att varje DNS-frågan besvaras av den närmast tillgängliga DNS-servern. Azure DNS ger snabb prestanda och hög tillgänglighet för din domän.

Azure DNS baseras på Azure Resource Manager. Fördelarna med Azure DNS från Resource Manager-funktioner som rollbaserad åtkomstkontroll, granskningsloggar och resurslåsning. Du kan hantera domäner och poster via Azure portal, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med tjänsten via REST API och SDK: er.

### <a name="how-much-does-azure-dns-cost"></a>Hur mycket kostar Azure DNS?

Faktureringsmodellen för Azure DNS baseras på antalet DNS-zoner som finns i Azure DNS. Det är också baserat på antalet DNS-frågor som de får. Rabatter lämnas baserat på användning.

Mer information finns i den [Azure DNS-sidan med priser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Vad finns det för serviceavtal för Azure DNS?

Azure garanterar att giltiga DNS-begäranden får svar från minst en Azure DNS-namnserver 100% av tiden.

Mer information finns i den [Azure DNS-SLA-sida](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Vad är en DNS-zon? Är det detsamma som en DNS-domän? 

En domän är ett unikt namn i domain name system. Ett exempel kan vara contoso.com.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen contoso.com kan till exempel innehålla flera DNS-poster. Poster kan innehålla mail.contoso.com för en e-postserver och en www\.contoso.com för en webbplats. Dessa poster finns i DNS-zonen contoso.com.

Ett domännamn är *bara ett namn*. En DNS-zon är en Dataresurs som innehåller DNS-poster för ett domännamn. Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. Det ger också DNS-namnservrar för att besvara DNS-frågor från Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Måste jag köpa ett DNS-domännamn för att använda Azure DNS? 

Inte nödvändigtvis.

Du behöver inte köpa en domän som värd för en DNS-zon i Azure DNS. Du kan skapa en DNS-zon när som helst utan att äga domännamnet. DNS-frågor för den här zonen lösa endast om de är riktade till Azure DNS-namnservrarna som tilldelats i zonen.

Om du vill länka DNS-zonen till den globala DNS-hierarkin, måste du köpa domännamnet. Sedan, DNS-frågor från var som helst i världen att hitta din DNS-zon och svar med dina DNS-poster.

## <a name="azure-dns-features"></a>Funktioner i Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Finns det några begränsningar när du använder alias poster för en domän namn apex med Traffic Manager?

Ja. Du måste använda statiska offentliga IP-adresser med Azure Traffic Manager. Konfigurera den **extern slutpunkt** mål med hjälp av en statisk IP-adress. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS som har stöd för DNS-baserad routning eller slutpunkt trafikredundans?

DNS-baserad Routning och slutpunkten trafikredundans tillhandahålls av Traffic Manager. Traffic Manager är en separat Azure-tjänst som kan användas med Azure DNS. Mer information finns i den [översikt över Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS stöder endast som är värd för statisk DNS-domäner, där varje DNS-fråga för att DNS-posten alltid får samma DNS-svaret.

### <a name="does-azure-dns-support-domain-name-registration"></a>Stöder domännamnsregistrering i Azure DNS?

Nej. Azure DNS stöder inte för närvarande kan köpa domännamn. Om du vill köpa domäner, måste du använda en tredjeparts-domännamnsregistrator. Registratorn oftast debiterar en liten årsavgift. Domänerna som kan sedan finnas i Azure DNS för hantering av DNS-poster. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

Funktionen för att köpa domännamn spåras i Azure eftersläpningen. Använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Stöder DNSSEC i Azure DNS?

Nej. Azure DNS stöder inte för närvarande Domain Name System Security Extensions (DNSSEC).

Funktionen DNSSEC spåras i Azure DNS-eftersläpning. Använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Stöder zonöverföringar (AXFR/IXFR) i Azure DNS?

Nej. Azure DNS stöder inte för närvarande zonöverföringar. DNS-zoner kan vara [importeras till Azure DNS med hjälp av Azure CLI](dns-import-export.md). DNS-poster som hanteras den [hanteringsportalen för Azure DNS](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [PowerShell-cmdletar](dns-operations-recordsets.md), eller [ CLI-verktyget](dns-operations-recordsets-cli.md).

Funktionen zon överföring spåras i Azure DNS-eftersläpning. Använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Stöder omdirigeringar URL: en i Azure DNS?

Nej. URL: en omdirigerings-tjänster är inte en DNS-tjänst. De fungerar med HTTP-nivå i stället för DNS-nivå. Vissa DNS-providrar Paketera en URL: en omdirigerings-tjänst som en del av sitt övergripande erbjudande. Den här tjänsten stöds inte för närvarande av Azure DNS.

Funktionen omdirigerings-URL spåras i Azure DNS-eftersläpning. Använda webbplatsen feedback till [registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Stöder utökad ASCII kodning (8-bitars)-set TXT postuppsättningar i Azure DNS?

Ja. Azure DNS stöder utökad ASCII kodning set TXT postuppsättningar. Men du måste använda den senaste versionen av Azure REST API: er, SDK: er, PowerShell och CLI. Versioner som är äldre än den 1 oktober 2017 eller SDK 2.1 stöder inte utökade ASCII-uppsättningen. 

Du kan till exempel ange en sträng som värde för en TXT-post som har utökade ASCII-tecken \128. Ett exempel är ”abcd\128efgh”. Azure DNS använder byte-värde för det här tecknet som är 128, i intern representation. Vid tidpunkten för DNS-matchning returneras bytevärdet i svaret. Observera också att ”abc” och ”\097\098\099” är utbytbara upplösning fråga. 

Vi följer [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zon filen master format escape-regler för TXT-poster. Till exempel `\` nu faktiskt Undertrycker allt per RFC. Om du anger `A\B` som poster värde TXT det representeras och löst som bara `AB`. Om du verkligen TXT-posten har `A\B` med upplösning som är du behöver att undvika den `\` igen. Till exempel ange `A\\B`.

Det här stödet är för närvarande inte tillgängligt för TXT-poster som skapats från Azure-portalen.

## <a name="alias-records"></a>Aliasposter

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Vilka är några scenarier där alias poster är användbar?

Se scenarier i avsnittet den [Azure DNS alias poster översikt](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Vilka typer av poster stöds alias postuppsättningar?

Alias postuppsättningar har stöd för följande typer av poster i en Azure DNS-zon:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Vilka resurser som stöds som mål för alias postuppsättningar?

- **Peka på en offentlig IP-adressresurs från DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och gör det till ett alias postuppsättningen så att den pekar till en offentlig IP-resurs.
- **Peka på en Traffic Manager-profil från en CNAME-DNS A/AAAA-postuppsättning.** Du kan peka till CNAME för en Traffic Manager-profil från en DNS CNAME-postuppsättning. Ett exempel är contoso.trafficmanager.net. Nu kan kan du också peka på en Traffic Manager-profil som har externa slutpunkter från ett A eller AAAA-postuppsättning i din DNS-zon.
- **Peka på en Azure Content Delivery Network (CDN) slutpunkt**. Detta är användbart när du skapar statiska webbplatser som använder Azure storage och Azure CDN.
- **Peka på en annan DNS-postuppsättning i samma zon.** Alias poster kan referera till andra postuppsättningar av samma typ. Du kan till exempel låta en DNS CNAME-postuppsättning vara ett alias för en annan CNAME-postuppsättning av samma typ. Det här är användbart om du vill att vissa postuppsättningar vara alias och vissa alias.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Kan jag skapa och uppdatera alias poster från Azure-portalen?

Ja. Du kan skapa eller hantera alias-poster i Azure-portalen tillsammans med Azure REST API: er, PowerShell, CLI och SDK: er.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Alias poster hjälper till att kontrollera att min DNS-postuppsättning tas bort när den underliggande offentliga IP-Adressen har tagits bort?

Ja. Den här funktionen är en av de viktigaste funktionerna i alias poster. Det hjälper dig att undvika eventuella avbrott för användare av ditt program.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Alias kommer innehåller hjälp om du vill kontrollera min DNS-postuppsättning uppdateras till rätt IP-adress när den underliggande offentliga IP-adressen ändras?

Ja. Den här funktionen är en av de viktigaste funktionerna i alias poster. Det hjälper dig att undvika eventuella avbrott eller säkerhetsrisker för ditt program.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Finns det några begränsningar när du använder aliaspost anger för A eller aaa-poster för att peka till Traffic Manager?

Ja. För att peka mot en Traffic Manager-profil som ett alias från ett A eller AAAA-postuppsättning måste Traffic Manager-måste profil använda endast externa slutpunkter. När du skapar de externa slutpunkterna i Traffic Manager kan du ange faktiska IP-adresserna för slutpunkterna.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Finns det en extra avgift för att använda alias poster?

Alias poster är en kvalifikation på en giltig DNS-postuppsättning. Det finns inga ytterligare faktureringen för alias poster.

## <a name="use-azure-dns"></a>Use Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Kan jag Samkör en domän med hjälp av Azure DNS och en annan DNS-provider?

Ja. Azure DNS stöder delad hosting domäner med andra DNS-tjänster.

Om du vill konfigurera delade hosting ändra NS-poster för domänen så att den pekar till namnservrarna för båda providers. Namnserver (NS) registrerar kontroll vilka leverantörer får DNS-frågor för domänen. Du kan ändra dessa NS-poster i Azure DNS, i en annan provider och i den överordnade zonen. Den överordnade zonen konfigureras vanligtvis via domännamnsregistratorn. Mer information om DNS-delegering finns i [DNS domändelegering](dns-domain-delegation.md).

Kontrollera också att DNS-poster för domänen är synkroniserade mellan båda DNS-providers. Azure DNS stöder inte för närvarande DNS-zonöverföring. DNS-poster måste synkroniseras genom att använda antingen den [hanteringsportalen för Azure DNS](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [PowerShell-cmdletar](dns-operations-recordsets.md), eller [CLI-verktyget](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Måste jag delegera min domän till alla fyra Azure DNS-namnservrarna?

Ja. Fyra namnservrarna tilldelar varje DNS-zon i Azure DNS. Den här ordningen är för felisolering och ökad flexibilitet. Delegera din domän till alla fyra namnservrarna för att kvalificera dig för serviceavtalet för Azure DNS.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Vilka är gränserna för användning för Azure DNS?

Följande standard begränsningar gäller när du använder Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan jag flytta en Azure DNS-zon mellan resursgrupper eller mellan prenumerationer?

Ja. DNS-zoner kan flyttas mellan resursgrupper eller prenumerationer.

Det finns ingen effekt på DNS-frågor när du flyttar en DNS-zon. Namnservrarna som tilldelats i zonen förblir densamma. DNS-frågorna bearbetas som vanligt i hela.

Mer information och anvisningar om hur du flyttar DNS-zoner finns i [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hur lång tid tar det för DNS-ändringarna ska börja gälla?

Ny DNS-zoner och DNS-poster vanligtvis visas i Azure DNS-namnservrarna snabbt. Tiden är några sekunder.

Ändringar av befintliga DNS-poster kan ta lite längre tid. Vanligtvis visas de i Azure DNS-namnservrarna inom 60 sekunder. DNS-cachen genom att DNS-klienter och rekursiv DNS-matchare utanför Azure DNS också kan påverka tidsinställning. Använd egenskapen Time To Live (TTL) för varje uppsättning av poster för att styra den här cachelagringens varaktighet.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hur kan jag skydda DNS-zoner mot oavsiktlig borttagning?

Azure DNS hanteras med hjälp av Azure Resource Manager. Azure DNS dra nytta av åtkomstkontrollfunktionerna som Azure Resource Manager tillhandahåller. Rollbaserad åtkomstkontroll styr vilka användare har läs- eller skrivbehörighet för DNS-zoner och uppsättningar av poster. Resurslås förhindra oavsiktlig ändring eller borttagning av DNS-zoner och uppsättningar av poster.

Mer information finns i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hur ställer jag in SPF-poster i Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Löser Azure DNS-namnservrarna via IPv6? 

Ja. Azure DNS-namnservrarna är dual stack. Dual stack innebär att de har IPv4 och IPv6-adresser. Använd ett verktyg som nslookup för att hitta IPv6-adress för Azure DNS-namnservrarna som tilldelats till din DNS-zon. Ett exempel är `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hur ställer jag in ett IDN i Azure DNS?

Internationaliserade domännamn (IDN) koda varje DNS-namn med hjälp av [punycode](https://en.wikipedia.org/wiki/Punycode). DNS-frågor görs med hjälp av dessa punycode-kodat namn.

Konvertera zonnamn eller postuppsättningsnamnet till punycode om du vill konfigurera IDN: er i Azure DNS. Azure DNS stöder inte för närvarande inbyggda konvertering till eller från punycode.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure DNS](dns-overview.md).

- [Mer information om hur du använder Azure DNS för privata domäner](private-dns-overview.md).

- [Mer information om DNS-zoner och poster](dns-zones-records.md).

- [Kom igång med Azure DNS](dns-getstarted-portal.md).
