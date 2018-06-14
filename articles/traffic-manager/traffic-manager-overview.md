---
title: Vad är Traffic Manager | Microsoft Docs
description: Den här artikeln hjälper dig att förstå vad Traffic Manager är och oavsett om det är rätt trafik routning valet för ditt program
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877057"
---
# <a name="overview-of-traffic-manager"></a>Översikt över Traffic Manager

Microsoft Azure Traffic Manager kan du styra distributionen av användartrafik för slutpunkter i olika datacenter. Service-slutpunkter som stöds av Traffic Manager omfattar virtuella Azure-datorer Web Apps och molntjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte tillhör Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en metod för routning av nätverkstrafik och hälsotillståndet för slutpunkter. Traffic Manager erbjuder en uppsättning [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) och [endpoint övervakningsalternativ](traffic-manager-monitoring.md) efter programbehov för olika och automatisk redundans modeller. Traffic Manager är känsligt för fel, inklusive fel på en hel Azure-region.

## <a name="traffic-manager-benefits"></a>Fördelar med Traffic Manager

Traffic Manager kan hjälpa dig:

* **Förbättra tillgänglighet för viktiga program**

    Traffic Manager ger hög tillgänglighet för dina program genom att övervaka dina slutpunkter och ger automatisk redundans när en slutpunkt som kraschar.

* **Förbättra svarstiden för program med höga prestanda**

    Azure låter dig köra molntjänster eller webbplatser i datacenter finns runtom i världen. Traffic Manager förbättrar programtillgängligheten genom att dirigera trafik till slutpunkten med den lägsta Nätverksfördröjningen för klienten.

* **Utföra underhåll utan avbrott**

    Du kan utföra åtgärder för planerat underhåll på dina program utan driftavbrott. Traffic Manager dirigerar trafik till alternativa slutpunkter medan underhåll pågår.

* **Kombinera lokala och molnbaserade program**

    Traffic Manager stöder extern, Azure-slutpunkter att användas med hybrid molnet och lokala distributioner, däribland ”burst-till-molnet”, ”migrera till moln”, och ”redundans till moln” scenarier.

* **Distribuera trafiken för stora, komplexa distributioner**

    Med hjälp av [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md), routning av nätverkstrafik metoderna kombineras för att skapa avancerade och flexibla regler för att stödja större och mer komplexa distributioner behov.

## <a name="how-traffic-manager-works"></a>Så här fungerar Traffic Manager

Azure Traffic Manager kan du styra distributionen av trafik över dina slutpunkter för programmet. En slutpunkt är alla mot Internet-tjänst som finns i eller utanför Azure.

Traffic Manager har två viktiga fördelar:

1. Fördelning av trafik enligt ett av flera [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md)
2. [Kontinuerlig övervakning av hälsotillstånd för slutpunkten](traffic-manager-monitoring.md) och automatisk redundans när slutpunkter misslyckas

När en klient försöker ansluta till en tjänst, måste det först matcha DNS-namn för tjänsten till en IP-adress. Klienten ansluter sedan till att IP-adress för att komma åt tjänsten.

**Det mest viktiga att förstå är att Traffic Manager fungerar på DNS-nivå.**  Traffic Manager använder DNS för att dirigera klienterna till specifika slutpunkter baserat på regler för routning av nätverkstrafik-metoden. Klienterna ansluter till den valda slutpunkten **direkt**. Traffic Manager är inte en proxy eller en gateway. Traffic Manager kan inte se trafik som skickas mellan klienten och tjänsten.

### <a name="traffic-manager-example"></a>Traffic Manager-exempel

Contoso Corp har utvecklat en ny partner-portalen. URL för den här portalen är https://partners.contoso.com/login.aspx. Programmet finns i tre områden i Azure. För att förbättra tillgängligheten och maximera globala prestanda, använder de Traffic Manager för att distribuera klienttrafik till närmaste tillgängliga slutpunkten.

För att uppnå den här konfigurationen, kan de utföra följande steg:

1. Distribuera tre instanser av tjänsten. DNS-namnen på dessa distributioner är ”contoso us.cloudapp .net', 'contoso eu.cloudapp .net' och 'contoso asia.cloudapp .net'.
2. Skapa en Traffic Manager-profil med namnet contoso.trafficmanager.net, och konfigurera den att använda metoden 'Prestanda' routning av nätverkstrafik för de tre slutpunkterna.
* Konfigurera deras alternativa domännamn 'partners.contoso.com', peka på contoso.trafficmanager.net, med hjälp av en DNS CNAME-post.

![Traffic Manager DNS-konfiguration][1]

> [!NOTE]
> När du använder en alternativa domän med Azure Traffic Manager, måste du använda en CNAME-post så att den pekar ditt alternativa domännamn till domännamnet för Traffic Manager. DNS-standarden tillåter inte att skapa en CNAME-post på 'apex' (eller rot) i en domän. Därmed kan du skapa en CNAME-post för ”contoso.com” (kallas ibland för en 'asbestgaller' domän). Du kan bara skapa en CNAME-post för en domän under ”contoso.com”, till exempel ”www.contoso.com”. Du kan undvika den här begränsningen bör du använda en enkel HTTP-omdirigering till direkta begäranden för ”contoso.com” till ett alternativt namn, till exempel ”www.contoso.com”.

### <a name="how-clients-connect-using-traffic-manager"></a>Hur klienter ska anslutas med Traffic Manager

Klienten utför fortsätter från föregående exempel, när en klient begär sidan https://partners.contoso.com/login.aspx, följande steg för att matcha DNS-namnet och upprätta en anslutning:

![Anslutningsupprättande med Traffic Manager][2]

1. Klienten skickar en DNS-fråga till dess konfigurerade rekursiv DNS-tjänsten för att matcha namnet partners.contoso.com. En rekursiv DNS-tjänsten som ibland kallas en ”lokala DNS-tjänsten inte är värd för DNS-domäner direkt. I stället off-loads klienten arbetet med att kontakta olika auktoritära DNS-tjänster på Internet som behövs för att matcha DNS-namn.
2. Om du vill matcha DNS-namnet hittar rekursiv DNS-tjänsten på namnservrar för domänen ”contoso.com”. Den kontaktar sedan servrarna namn om du vill begära 'partners.contoso.com' DNS-post. DNS-servrar contoso.com returnera CNAME-post som pekar på contoso.trafficmanager.net.
3. Därefter hittar rekursiv DNS-tjänsten namnservrarna för domän 'trafficmanager.net', som tillhandahålls av Azure Traffic Manager-tjänsten. En begäran om DNS-posten 'contoso.trafficmanager.net' skickar sedan till dessa DNS-servrar.
4. Traffic Manager-namnservrar tar emot förfrågan. De välja en slutpunkt som baseras på:

    - Konfigurerade tillståndet för varje slutpunkt (inaktiverad slutpunkter returneras inte)
    - Det aktuella hälsotillståndet för varje slutpunkt som bestäms av hälsotillståndet för Traffic Manager kontrollerar. Mer information finns i [Traffic Manager-slutpunkten övervakning](traffic-manager-monitoring.md).
    - Den valda metoden routning av nätverkstrafik. Mer information finns i [Traffic Manager routning metoder](traffic-manager-routing-methods.md).

5. Den valda slutpunkten returneras som en annan DNS-CNAME-post. I det här fallet Låt oss anta att contoso-us.cloudapp.net returneras.
6. Därefter hittar rekursiv DNS-tjänsten namnservrarna för domänen 'cloudapp.net'. Den kontaktar servrarna namn om du vill begära contoso us.cloudapp .net DNS-post. En DNS-”A”-post som innehåller IP-adressen för tjänstslutpunkten USA-baserade returneras.
7. Rekursiva DNS-tjänsten konsoliderar resultaten och returnerar ett enda DNS-svar till klienten.
8. Klienten tar emot DNS-resultat och ansluter till den angivna IP-adressen. Klienten ansluter till tjänstslutpunkten programmet direkt, inte via Trafikhanterarprofilen. Eftersom det är en HTTPS-slutpunkt klienten utför nödvändiga SSL/TLS-handskakning och gör sedan en HTTP GET-begäran för den ' / login.aspx-sidan.

Rekursiva DNS-tjänsten cachelagrar DNS-svar tas emot. DNS-matchning på klientenheten cachelagrar också resultatet. Cachelagring kan efterföljande DNS-frågor som ska besvaras snabbare genom att använda data från cacheminnet i stället för att fråga andra namnservrar. Varaktighet för cachen bestäms av egenskapen 'time-to-live ”(TTL) för varje DNS-post. Kortare värden ger snabbare cache upphör att gälla och därmed mer turer till namnservrar Traffic Manager. Längre värden innebär att det kan ta längre tid att trafiken från en misslyckad slutpunkt. Traffic Manager kan du konfigurera TTL-värde som används i Traffic Manager DNS-svar för att vara så lågt som 0 sekunder och lika hög som 2 147 483 647 sekunder (största intervall som är kompatibla med [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), så att du kan välja värdet som bäst balanserar behoven i ditt program.

## <a name="pricing"></a>Prissättning

Information om priser finns [Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Vanliga frågor om Traffic Manager finns [Traffic Manager vanliga frågor och svar](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Traffic Manager [endpoint övervaknings- och automatisk redundans](traffic-manager-monitoring.md).

Lär dig mer om Traffic Manager [trafikroutningsmetoder](traffic-manager-routing-methods.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

