---
title: Hur fungerar Azure Traffic Manager | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Traffic Manager dirigerar trafik för hög prestanda och tillgänglighet för dina webbprogram
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2018
ms.author: kumud
ms.openlocfilehash: 7edd6acc090e4a41939811497a21886d91631fdf
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229259"
---
# <a name="how-traffic-manager-works"></a>Så här fungerar Traffic Manager

Med Azure Traffic Manager kan du styra distributionen av trafiken mellan slutpunkterna för din. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager har två viktiga fördelar:

- Fördelning av trafik enligt ett av flera [trafikroutningsmetoder](traffic-manager-routing-methods.md)
- [Kontinuerlig övervakning av slutpunktshälsa](traffic-manager-monitoring.md) och automatisk redundans när slutpunkter misslyckas

När en klient försöker ansluta till en tjänst, måste den först matcha DNS-namnet på tjänsten till en IP-adress. Klienten ansluter sedan till IP-adressen åtkomst till tjänsten.

**Det mest viktiga att förstå är att Traffic Manager fungerar på DNS-nivå.**  Traffic Manager använder DNS för att dirigera klienterna till specifika tjänstslutpunkter utifrån reglerna för routning av nätverkstrafik-metoden. Klienterna ansluter till den valda slutpunkten **direkt**. Traffic Manager är inte en proxy eller en gateway. Traffic Manager kan inte se den trafik som passerar mellan klienten och tjänsten.

## <a name="traffic-manager-example"></a>Traffic Manager-exempel

Contoso Corp har utvecklat en ny partner-portalen. URL: en för den här portalen är https://partners.contoso.com/login.aspx. Programmet finns i tre regioner för Azure. För att förbättra tillgängligheten och maximera globala prestanda, använda de Traffic Manager för att distribuera klienttrafik till närmaste tillgängliga slutpunkten.

För att uppnå den här konfigurationen kan utföra de följande steg:

1. Distribuera tre instanser av tjänsten. DNS-namn för dessa distributioner är ”contoso-us.cloudapp .net”, ”contoso-eu.cloudapp .net” och ”contoso-asia.cloudapp .net”.
2. Skapa en Traffic Manager-profil med namnet contoso.trafficmanager.net, och konfigurera den att använda metoden ”Performance” routning av nätverkstrafik i tre slutpunkter.
* Konfigurera sina anpassad domännamn, ”partners.contoso.com” så att den pekar till contoso.trafficmanager.net, med hjälp av en DNS CNAME-post.

![Traffic Manager DNS-konfiguration][1]

> [!NOTE]
> När du använder en anpassad domän med Azure Traffic Manager, måste du använda en CNAME-post så att den pekar ditt domännamn för anpassad till ditt Traffic Manager-domännamn. DNS-standarden tillåter inte att du kan skapa en CNAME-post på 'apex ”(eller rot) i en domän. Därför skapa du inte en CNAME-post för ”contoso.com” (kallas ibland för en ”utan www-domän). Du kan bara skapa en CNAME-post för en domän under ”contoso.com”, till exempel ”www.contoso.com”. För att undvika denna begränsning, bör du använda en enkel HTTP-omdirigering till direkta begäranden för ”contoso.com” till ett alternativt namn, till exempel ”www.contoso.com”.

### <a name="how-clients-connect-using-traffic-manager"></a>Hur klienter ansluter via Traffic Manager

Fortsätter från föregående exempel, när en klient begär sidan https://partners.contoso.com/login.aspx, utför följande steg för att lösa DNS-namn och upprätta en anslutning för klienten:

![Anslutningen upprättas med Traffic Manager][2]

1. Klienten skickar en DNS-fråga till dess konfigurerade rekursiv DNS-tjänst för att matcha namnet ”partners.contoso.com”. DNS-domäner är inte värd direkt i en rekursiv DNS-tjänst, vilket ibland kallas en ”lokal DNS-tjänst. I stället off-loads klienten arbetet med att kontakta de olika auktoritativa DNS-tjänsterna via Internet som behövs för att lösa ett DNS-namn.
2. Om du vill matcha DNS-namnet, hittar rekursiv DNS-tjänst namnservrarna för domänen ”contoso.com”. Den kontaktar sedan dessa namnservrar för att begära DNS-posten ”partners.contoso.com”. Contoso.com DNS-servrar returnera CNAME-post som pekar på contoso.trafficmanager.net.
3. Därefter hittar rekursiv DNS-tjänst namnservrarna för domänen ”trafficmanager.net”, som tillhandahålls av Azure Traffic Manager-tjänsten. Sedan skickar den en begäran om DNS-posten 'contoso.trafficmanager.net' till dessa DNS-servrar.
4. Traffic Managers namnservrar tar emot förfrågan. De välja en slutpunkt som baseras på:

    - Det angivna tillståndet för varje slutpunkt (inaktiverad slutpunkter returneras inte)
    - Det aktuella hälsotillståndet för varje slutpunkt, enligt systemets hälsokontroller Traffic Manager. Mer information finns i [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md).
    - Den valda trafikdirigeringsmetoden. Mer information finns i [Traffic Manager Routing Methods](traffic-manager-routing-methods.md).

5. Den valda slutpunkten returneras som en annan DNS CNAME-post. I det här fallet kan vi anta att contoso-us.cloudapp.net returneras.
6. Därefter hittar rekursiv DNS-tjänst namnservrarna för domänen ”cloudapp.net”. Den kontaktar dessa namnservrar för att begära contoso us.cloudapp .net DNS-post. En DNS-”A”-post som innehåller IP-adressen för den amerikanska tjänstslutpunkten returneras.
7. Rekursiv DNS-tjänst konsoliderar resultaten och returnerar ett enda DNS-svar till klienten.
8. Klienten tar emot DNS-resultat och ansluter till den angivna IP-adressen. Klienten ansluter till tjänstslutpunkten programmet direkt, inte via Traffic-Manager. Eftersom det är en HTTPS-slutpunkt klienten utför nödvändiga SSL/TLS-handskakning och gör sedan en HTTP GET-begäran för den ' / login.aspx-sidan.

Rekursiv DNS-tjänst cachelagrar DNS-svar tas emot. DNS-matchare på klientenheten cachelagrar också resultatet. Cachelagring kan efterföljande DNS-frågor som ska besvaras snabbare genom att använda data från cachen i stället för att fråga andra namnservrar. Varaktigheten för cachen bestäms av egenskapen ”time-to-live” (TTL) för varje DNS-post. Kortare värden resultera i snabbare cache förfallodatum och därmed flera turer till Traffic Managers namnservrar. Längre värden innebär att det kan ta längre tid att dirigera trafik från en misslyckad slutpunkt. Traffic Manager låter dig konfigurera TTL-värde som används i Traffic Manager DNS-svar för att vara så lågt som 0 sekunder och så mycket som 2 147 483 647 sekunder (maximalt intervall som är kompatibla med [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), så att du kan välja värdet som på bästa sätt balanserar programmets behov.


## <a name="next-steps"></a>Nästa steg

Läs mer om Traffic Manager [slutpunkt för övervakning och automatisk redundans](traffic-manager-monitoring.md).

Läs mer om Traffic Manager [trafikroutningsmetoder](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

