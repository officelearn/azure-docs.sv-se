---
title: Så här fungerar Azure Traffic Manager | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Traffic Manager dirigerar trafik för hög prestanda och tillgänglighet för dina webbprogram
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: rohink
ms.openlocfilehash: 4863ffd383cfcd46bad462156e26293d145fd418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294856"
---
# <a name="how-traffic-manager-works"></a>Så här fungerar Traffic Manager

Med Azure Traffic Manager kan du styra distributionen av trafik mellan programslutpunkter. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager ger två viktiga fördelar:

- Distribution av trafik enligt en av flera [trafik-routing metoder](traffic-manager-routing-methods.md)
- [Kontinuerlig övervakning av slutpunktshälsa](traffic-manager-monitoring.md) och automatisk redundans vid felpunkter

När en klient försöker ansluta till en tjänst måste den först matcha tjänstens DNS-namn till en IP-adress. Klienten ansluter sedan till den IP-adressen för att få åtkomst till tjänsten.

**Den viktigaste punkten att förstå är att Traffic Manager fungerar på DNS-nivå.**  Traffic Manager använder DNS för att dirigera klienter till specifika tjänstslutpunkter baserat på reglerna för trafikroutningsmetoden. Klienter ansluter till den valda slutpunkten **direkt**. Traffic Manager är inte en proxy eller en gateway. Traffic Manager ser inte trafiken som passerar mellan klienten och tjänsten.

## <a name="traffic-manager-example"></a>Exempel på Traffic Manager

Contoso Corp har utvecklat en ny partnerportal. URL:en för `https://partners.contoso.com/login.aspx`den här portalen är . Programmet finns i tre regioner i Azure. För att förbättra tillgängligheten och maximera globala prestanda använder de Traffic Manager för att distribuera klienttrafik till närmaste tillgängliga slutpunkt.

För att uppnå den här konfigurationen utför de följande steg:

1. Distribuera tre instanser av deras tjänst. DNS-namnen på dessa distributioner är "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" och "contoso-asia.cloudapp.net".
1. Skapa en Traffic Manager-profil med namnet "contoso.trafficmanager.net" och konfigurera den så att den använder trafikroutningsmetoden "Prestanda" över de tre slutpunkterna.
1. Konfigurera deras fåfänga domännamn, "partners.contoso.com", så att det pekar på "contoso.trafficmanager.net", med hjälp av en DNS CNAME-post.

![DNS-konfiguration för Traffic Manager][1]

> [!NOTE]
> När du använder en fåfängadomän med Azure Traffic Manager måste du använda ett CNAME för att peka ditt fåfänga domännamn på ditt Traffic Manager-domännamn. DNS-standarder tillåter inte att du skapar ett CNAME på "apex" (eller root) för en domän. Därför kan du inte skapa ett CNAME för "contoso.com" (kallas ibland en "naken" domän). Du kan bara skapa ett CNAME för en domän under "contoso.com", till exempel "www.contoso.com". För att komma runt den här begränsningen rekommenderar vi att du är värd för DNS-domänen på [Azure DNS](../dns/dns-overview.md) och använder [Alias-poster](../dns/tutorial-alias-tm.md) för att peka på din traffic manager-profil. Alternativt kan du använda en enkel HTTP-omdirigering för att rikta begäranden om "contoso.com" till ett alternativt namn som "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Så här ansluter klienter med Traffic Manager

Fortsätter från föregående exempel, när en `https://partners.contoso.com/login.aspx`klient begär sidan, utför klienten följande steg för att matcha DNS-namnet och upprätta en anslutning:

![Anslutningsanläggning med Traffic Manager][2]

1. Klienten skickar en DNS-fråga till sin konfigurerade rekursiva DNS-tjänst för att matcha namnet "partners.contoso.com". En rekursiv DNS-tjänst, som ibland kallas en lokal DNS-tjänst, är inte värd för DNS-domäner direkt. I stället laddar klienten bort arbetet med att kontakta de olika auktoritära DNS-tjänsterna över Internet som behövs för att matcha ett DNS-namn.
2. För att matcha DNS-namnet hittar den rekursiva DNS-tjänsten namnservrarna för domänen "contoso.com". Den kontaktar sedan dessa namnservrar för att begära "partners.contoso.com" DNS-post. De contoso.com DNS-servrarna returnerar CNAME-posten som pekar på contoso.trafficmanager.net.
3. Därefter hittar den rekursiva DNS-tjänsten namnservrarna för domänen "trafficmanager.net", som tillhandahålls av Azure Traffic Manager-tjänsten. Den skickar sedan en begäran om DNS-posten för "contoso.trafficmanager.net" till dessa DNS-servrar.
4. Traffic Manager-namnservrarna får begäran. De väljer en slutpunkt baserat på:

    - Det konfigurerade tillståndet för varje slutpunkt (inaktiverade slutpunkter returneras inte)
    - Den aktuella hälsotillståndet för varje slutpunkt, enligt hälsokontrollerna av Traffic Manager. Mer information finns i [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md).
    - Den valda trafikroutningsmetoden. Mer information finns i [Routningsmetoder för Trafikhanteraren](traffic-manager-routing-methods.md).

5. Den valda slutpunkten returneras som en annan DNS CNAME-post. Låt oss i så fall anta att contoso-us.cloudapp.net returneras.
6. Därefter hittar den rekursiva DNS-tjänsten namnservrarna för domänen "cloudapp.net". Den kontaktar dessa namnservrar för att begära "contoso-us.cloudapp.net" DNS-post. En DNS A-post som innehåller IP-adressen för den USA-baserade tjänstslutpunkten returneras.
7. Den rekursiva DNS-tjänsten konsoliderar resultaten och returnerar ett enda DNS-svar till klienten.
8. Klienten tar emot DNS-resultaten och ansluter till den angivna IP-adressen. Klienten ansluter till programtjänstens slutpunkt direkt, inte via Traffic Manager. Eftersom det är en HTTPS-slutpunkt utför klienten det nödvändiga SSL/TLS-handskakningen och gör sedan en HTTP GET-begäran för sidan '/login.aspx.

Den rekursiva DNS-tjänsten cachelagrar de DNS-svar som tas emot. DNS-matcharen på klientenheten cachelagrar också resultatet. Cachelagring gör att efterföljande DNS-frågor kan besvaras snabbare med hjälp av data från cacheminnet i stället för att fråga andra namnservrar. Cachens varaktighet bestäms av egenskapen "time-to-live" (TTL) för varje DNS-post. Kortare värden resulterar i snabbare cacheutgång och därmed fler tur- och returresor till Traffic Manager-namnservrarna. Längre värden innebär att det kan ta längre tid att dirigera trafik från en misslyckad slutpunkt. Med Traffic Manager kan du konfigurera TTL som används i Traffic Manager DNS-svar så att den är så låg som 0 sekunder och så hög som 2 147 483 647 sekunder (det maximala intervallet som är kompatibelt med [RFC-1035),](https://www.ietf.org/rfc/rfc1035.txt)så att du kan välja det värde som bäst balanserar behoven i ditt program.

## <a name="faqs"></a>Vanliga frågor och svar

* [Vilken IP-adress använder Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Vilka typer av trafik kan dirigeras med Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Stöder Traffic Manager "klibbiga" sessioner?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Varför visas ett HTTP-fel när jag använder Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Vad har prestandapåverkan med Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Vilka programprotokoll kan jag använda med Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Kan jag använda Traffic Manager med ett "naket" domännamn?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Tar Traffic Manager hänsyn till klientundernätsadressen när dns-frågor hanteras?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Vad är DNS TTL och hur påverkar det mina användare?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Hur högt eller lågt kan jag ställa in TTL för Traffic Manager-svar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Hur kan jag förstå mängden frågor som kommer till min profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunktsövervakning i](traffic-manager-monitoring.md)Traffic Manager och automatisk redundans .

Läs mer om [trafikstyrningsmetoder i](traffic-manager-routing-methods.md)Traffic Manager .

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

