---
title: Vad är Azure Load Balancer?
titlesuffix: Azure Load Balancer
description: Översikt över funktioner, arkitektur och implementering av Azure Load Balancer-funktioner. Lär dig hur Load Balancer fungerar och utnyttja den i molnet.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2018
ms.author: kumud
ms.openlocfilehash: 7b0a837bf23145c5ac5e5775d4324582fa3a6b20
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120843"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

Med Azure Load Balancer kan du skala dina program och skapa hög tillgänglighet för dina tjänster. Load Balancer har stöd för inkommande och utgående scenarier, du får korta svarstider och snabba dataflöden, och du kan skala upp till miljontals flöden för alla typer av TCP- och UDP-tillämpningar.  

Load Balancer fördelar nya inkommande flöden på lastbalanserarens poolinstanser från klientdel till serverdel, enligt regler och hälsoavsökningar. 

Dessutom kan en offentlig lastbalanserare ge utgående anslutningar för virtuella datorer genom att översätta deras privata IP-adresser till offentliga IP-adresser.

Azure Load Balancer finns tillgänglig i två SKU:er: Basic och Standard. Det finns skillnader i skala, funktioner och priser. Alla scenarier som är möjliga med en Basic Load Balancer kan även skapas med Standard Load Balancer, men metoderna kan skilja sig något. När du lär dig om Load Balancer är det viktigt att du bekantar dig med grunderna och SKU-specifika skillnader.

## <a name="why-use-load-balancer"></a>Varför använda Load Balancer? 

Du kan använda Azure Load Balancer till:

* Belastningsutjämning av inkommande Internettrafik till dina virtuella datorer. Den här konfigurationen kallas för [offentlig lastbalanserare](#publicloadbalancer).
* Belastningsutjämning av trafik på virtuella datorer i ett virtuellt nätverk. Du kan även nå en Load Balancer-klientdel från ett lokalt nätverk i ett hybridscenario. Båda scenarierna använder en konfiguration som kallas för [intern lastbalanserare](#internalloadbalancer).
* Vidarebefordra trafik till en viss port på specifika virtuella datorer med inkommande NAT-regler (Network Address Translation – nätverksadressöversättning).
* Ange [utgående anslutningsmöjlighet](load-balancer-outbound-connections.md) för virtuella datorer i ditt virtuella nätverket med hjälp av en offentlig lastbalanserare.


>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du är intresserad av TLS-avslut (Transport Layer Security) (”SSL-avlastning”) eller bearbetning på programnivå för enskilda HTTP/HTTPS-begäranden läser du avsnittet om [Application Gateway](../application-gateway/application-gateway-introduction.md). I [Traffic Manager](../traffic-manager/traffic-manager-overview.md) finns information om global DNS-belastningsutjämning. Du kan med fördel kombinera dessa lösningar efter behov för dina slutpunkts-till-slutpunkts-scenarier.

## <a name="what-are-load-balancer-resources"></a>Vad är lastbalanseringsresurser?

En lastbalanseringsresurs kan finnas i en offentlig lastbalanserare eller i en intern lastbalanserare. Lastbalanseringsresursens funktioner uttrycks som en klientdel, en regel, en hälsoavsökning och en serverdelspooldefinition. Du placerar de virtuella datorerna i serverdelspoolen genom att ange serverdelspoolen från den virtuella datorn.

Lastbalanseringsresurser är objekt där du kan uttrycka hur Azure ska programmera sin infrastruktur för flera klientorganisationer för att uppnå det scenario som du vill skapa. Det finns inget direkt förhållande mellan lastbalanseringsresurser och faktisk infrastruktur. När du skapar en lastbalanserare skapas ingen instans och det finns alltid tillgänglig kapacitet. 

## <a name="fundamental-load-balancer-features"></a>Grundläggande lastbalanseringsfunktioner

Load Balancer har följande grundläggande funktioner för TCP- och UDP-program:

* **Belastningsutjämning**

    Med Azure Load Balancer kan du skapa en belastningsutjämningsregel för att fördela trafik som kommer till server- och klientpoolinstanser. Load Balancer använder en hash-baserad algoritm för distribution av inkommande flöden och skriver om huvuden för flöden till serverdelsbaserade poolinstanser enligt detta. En server är tillgänglig att ta emot nya flöden när en hälsoavsökning indikerar en felfri serverdelsslutpunkt.
    
    Som standard använder Load Balancer en 5-tuppel-hash som består av käll-IP-adress, källport, mål-IP-adress, målport och IP-protokollnummer för att mappa flöden till tillgängliga servrar. Du kan välja att skapa tillhörighet till en specifik käll-IP-adress genom att välja en 2- eller 3-tuppel-hash för en viss regel. Alla paket i samma paketflöde kommer till samma instans bakom den belastningsutjämnade serverdelen. När klienten initierar ett nytt flöde från samma käll-IP-adress ändras källporten. Till följd av det kan 5-tuppeln göra så att trafiken går till en annan serverdelsslutpunkt.

    Mer information finns i [Distributionsläge för lastbalanserare](load-balancer-distribution-mode.md). Följande bild visar den hash-baserade distributionen:

    ![Hash-baserad distribution](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figur: Hash-baserad distribution*

* **Portvidarebefordran**

    Med Load Balancer kan du skapa en inkommande NAT-regel för att vidarebefordra trafik för port från en specifik port på en viss klientdels-IP-adress till en specifik port för en specifik serverdelinstans i det virtuella nätverket. Det här görs också med samma hash-baserade distribution som belastningsutjämning. Vanliga scenarier för den här funktionen är Remote Desktop Protocol- (RDP) eller Secure Shell-sessioner (SSH) för enskilda VM-instanser i det virtuella Azure-nätverket. Du kan mappa flera interna slutpunkter till olika porter på samma klientdels-IP-adress. Du kan använda klientdels-IP-adresserna till att fjärradministrera dina virtuella datorer via Internet utan att behöva en ytterligare jumpbox.

* **Programoberoende och transparent**

    Load Balancer interagerar inte direkt med TCP eller UDP eller programnivån, och alla TCP- eller UDP-programscenarier kan stödjas.  Load Balancer avslutar inte och ger inte upphov till flöden, interagerar inte med flödets nyttolast, ger ingen gatewayfunktion på programnivå, och protokollhandskakningar sker alltid direkt mellan klienten och serverdelspoolinstansen.  Ett svar till ett inkommande flöde är alltid ett svar från en virtuell dator.  När flödet kommer till den virtuella datorn bevaras också den ursprungliga käll-IP-adressen.  Några exempel som illustrerar transparensen ytterligare:
    - Varje slutpunkt besvaras bara av en virtuell dator.  Till exempel sker en TCP-handskakning alltid mellan klienten och den valda virtuella serverdelsdatorn.  Ett svar på en begäran till en klientdel är ett svar som genereras av den virtuella serverdelsdatorn. När du validerar anslutningen till en klientdel validerar du anslutningen slutpunkt till slutpunkt till minst en virtuell serverdelsdator.
    - Programnyttolaster är transparenta för Load Balancer och alla UDP- eller TCP-program kan stödjas. För arbetsbelastningar som kräver bearbetning per HTTP-begäran eller manipulation av nyttolaster på programnivå (t.ex. parsning av HTTP-URL:er) bör du använda en nivå 7-lastbalanserare som [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Eftersom Load Balancer är oberoende av TCP-nyttolasten och TLS-avlastning (”SSL”) inte tillhandahålls kan du skapa krypterade scenarier slutpunkt till slutpunkt med hjälp av Load Balancer och få stor utskalning för TLS-program genom att avsluta TLS-anslutningen på själva virtuella datorn.  Till exempel begränsas bara din TLS-sessions nyckelkapacitet av typen och antalet virtuella datorer som du har lagt till i serverdelspoolen.  Om du kräver ”SSL-avlastning”, behandling på programnivå eller vill delegera certifikathantering till Azure ska du istället använda Azures nivå 7-lastbalanserare [Application Gateway](https://azure.microsoft.com/services/application-gateway).
        

* **Automatisk omkonfiguration**

    Load Balancer konfigurerar direkt om sig själv när du skalar upp eller ned instanser. När du lägger till eller tar bort virtuella datorer från serverdelspoolen konfigureras lastbalanseraren om utan ytterligare åtgärder på lastbalanseringsresursen.

* **Hälsoavsökningar**

    Om du vill avgöra hälsotillståndet för instanser i serverdelspoolen använder Load Balancer hälsoavsökningar som du definierar. När en avsökning inte svarar slutar lastbalanseraren att skicka nya anslutningar till de ohälsosamma instanserna. Befintliga anslutningar påverkas inte och de fortsätter tills programmet avslutar flödet, en timeout för inaktivitet inträffar eller den virtuella datorn stängs av.
     
    Load Balancer har [olika hälsoavsökningstyper](load-balancer-custom-probe-overview.md#types) för TCP-, HTTP- och HTTPS-slutpunkter.

    När du använder klassiska molntjänster tillåts dessutom en ytterligare typ:  [Gästagent](load-balancer-custom-probe-overview.md#guestagent).  Det här ska ses som en hälsoavsökning som sista utväg och rekommenderas inte när andra alternativ är genomförbara.
    
* **Utgående anslutningar (SNAT)**

    Alla utgående flöden från privata IP-adresser i ditt virtuella nätverk till offentliga IP-adresser på Internet kan översättas till en klientdels-IP-adress för lastbalanseraren. När en offentlig klientdel är knuten till en virtuell serverdelsdator genom en belastningsutjämningsregel programmerar Azure utgående anslutningar så att de automatiskt översätts till den offentliga klientdels-IP-adressen.

    * Aktivera enkel uppgradering och haveriberedskap för tjänster, eftersom klientdelen kan mappas dynamiskt till en annan instans av tjänsten.
    * Enklare hantering av åtkomstkontrollistor (ACL). Åtkomstkontrollistor uttryckta som klientdels-IP-adresser ändras inte när tjänster skalas upp eller ned eller omdistribueras.  Översättning av utgående anslutningar till ett mindre antal IP-adresser än datorer kan minska belastningen på listan över tillåtna program.

    Mer information finns i [utgående anslutningar](load-balancer-outbound-connections.md).

Standard Load Balancer har ytterligare SKU-specifika funktioner utöver dessa grundläggande. Mer information finns nedan i den här artikeln.

## <a name="skus"></a> Load Balancer SKU:er – jämförelse

Load Balancer stöder både Basic- och Standard-SKU:er, med olika scenarioskalor, funktioner och priser. Alla scenarier som är möjliga med Basic Load Balancer kan även skapas med Standard Load Balancer. I själva verket liknar API:erna för båda SKU:er varandra och anropas via specifikationen av en SKU. API:et som stöder SKU:er för Load Balancer och offentlig IP-adress är tillgängligt från och med API:et 2017-08-01. Båda SKU:erna har samma allmänna API och struktur.

Men beroende på vilken SKU du väljer kan den kompletta scenariokonfigurationen skilja sig något. Load Balancer-dokumentationen anger när en artikel bara gäller för en viss SKU. Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns [Översikt över Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Nya designer bör införa Standard Load Balancer. 

Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. När du använder dem med offentliga IP-adresser måste både Load Balancer-SKU:n och SKU:n för den offentliga IP-adressen. SKU:er för Load Balancer och offentlig IP-adress är inte föränderliga.

_En rekommenderad metod är att ange SKU:erna uttryckligen, även om det ännu inte är obligatoriskt._  För tillfället hålls de ändringar som krävs på ett minimum. Om en SKU inte anges tolkas det som en avsikt att använda API-version 2017-08-01 av Basic-SKU:n.

>[!IMPORTANT]
>Standard Load Balancer är en ny Load Balancer-produkt och i stort sett en supermängd till Basic Load Balancer. Det finns viktiga och avsiktliga skillnader mellan de två produkterna. Alla scenarier slutpunkt till slutpunkt som är möjliga med Basic Load Balancer kan även skapas med Standard Load Balancer. Om du redan är van vid Basic Load Balancer bör du bekanta dig med Standard Load Balancer för att förstå de senaste ändringarna i beteende mellan Standard och Basic och inverkan. Granska det här avsnittet noggrant.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Mer information finns i [tjänstbegränsningarna för Load Balancer](https://aka.ms/lblimits). Information om Standard Load Balancer finns i [översikt](load-balancer-standard-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name="concepts"></a>Begrepp

### <a name = "publicloadbalancer"></a>Offentlig lastbalanserare

En offentlig lastbalanserare mappar den offentliga IP-adressen och portnumret för inkommande trafik till den privata IP-adressen och portnumret för den virtuella datorn och tvärtom för svarstrafiken från den virtuella datorn. Genom att tillämpa belastningsutjämningsregler kan du fördela specifika typer av trafik på flera virtuella datorer eller tjänster. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.

I följande bild visas en belastningsutjämnad slutpunkt för webbtrafik som delas mellan tre virtuella för den offentliga och TCP-port 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![Exempel på offentlig lastbalanserare](./media/load-balancer-overview/IC727496.png)

*Figur: Belastningsutjämning av webbtrafik med hjälp av en offentlig Load Balancer*

När Internetklienter skickar begäranden för webbsidor till den offentliga IP-adressen för en webbapp på TCP-port 80 distribuerar Azure Load Balancer begäranden mellan de tre virtuella datorer i den belastningsutjämnade uppsättningen. Mer information om Load Balancer-algoritmer finns i avsnittet [Load Balancer-funktioner](load-balancer-overview.md##fundamental-load-balancer-features) i den här artikeln.

Som standard distribuerar Azure Load Balancer nätverkstrafik jämnt mellan flera VM-instanser. Du kan även konfigurera sessionstillhörighet. Mer information finns i [Distributionsläge för lastbalanserare](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Intern lastbalanserare

En intern lastbalanserare dirigerar trafik bara till resurser som finns i ett virtuellt nätverk eller som använder en VPN-anslutning för åtkomst till Azure-infrastrukturen. I det här avseendet skiljer sig en intern lastbalanserare från en offentlig lastbalanserare. Azure-infrastrukturen begränsar åtkomsten till de belastningsutjämnade klientdels-IP-adresserna för ett virtuellt nätverk. Klientdels-IP-adresser och virtuella nätverk är aldrig direkt exponerade för en Internetslutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser.

En intern lastbalanserare möjliggör följande typer av belastningsutjämning:

* **Inom ett virtuellt nätverk**: Belastningsutjämning från virtuella datorer i det virtuella nätverket, till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För ett virtuellt nätverk mellan olika platser**: Belastningsutjämning från lokala datorer till en uppsättning virtuella datorer som finns i samma virtuella nätverk. 
* **För flernivåprogram**: Belastningsutjämning för flernivåprogram på Internet när serverdelsnivåerna inte är Internetanslutna. Serverdelsnivåerna kräver belastningsutjämning av trafik från Internetansluten nivå (se nästa bild).
* **För verksamhetsspecifika appar**: Belastningsutjämning för verksamhetsspecifika appar som finns i Azure utan ytterligare maskin- eller programvara för belastningsutjämning. Det här scenariot innehåller lokala servrar som finns i uppsättningen datorer vars trafik är belastningsutjämnad.

![Exempel på intern lastbalanserare](./media/load-balancer-overview/IC744147.png)

*Figur: Belastningsutjämning av flernivåprogram med hjälp av både en offentlig och en intern Load Balancer*

## <a name="pricing"></a>Prissättning
Standard Load Balancer-användning debiteras baserat på antalet konfigurerade belastningsutjämningsregler och mängden bearbetade inkommande och utgående data. Prisinformation om Standard Load Balancer finns på sidan med [Load Balancer-priser](https://azure.microsoft.com/pricing/details/load-balancer/).

Basic Load Balancer tillhandahålls kostnadsfritt.

## <a name="sla"></a>SLA

Information om Standard Load Balancer SLA finns på sidan med [Load Balancer-serviceavtal](https://aka.ms/lbsla). 

## <a name="limitations"></a>Begränsningar

- Load Balancer är en TCP- eller UDP-produkt för belastningsutjämning och portvidarebefordran för dessa specifika IP-protokoll.  Belastningsutjämningsregler och inkommande NAT-regler stöds för TCP och UDP och stöds inte för andra IP-protokoll, inklusive ICMP. Load Balancer avslutar inte, svarar inte på eller på annat sätt interagerar med nyttolasten för ett UDP- eller TCP-flöde. Det är inte en proxy. Lyckad validering av anslutningen till en klientdel måste äga rum in-band med samma protokoll som används i en belastningsutjämningsregel eller inkommande NAT-regel (TCP eller UDP) _och_ minst en av dina virtuella datorer måste generera ett svar för en klient för att se ett svar från en klientdel.  Ett uteblivet in-band-svar från lastbalanserarens klientdel anger att inga virtuella datorer kunde svara.  Det är inte möjligt att interagera med en lastbalanserares klientdel utan att en virtuell dator kan svara.  Det här gäller även för utgående anslutningar där [portmaskerings-SNAT](load-balancer-outbound-connections.md#snat) bara stöds för TCP och UDP. Alla andra IP-protokoll, inklusive ICMP, kommer att misslyckas.  Tilldela en offentlig IP-adress på instansnivå som åtgärd.
- Till skillnad från offentliga lastbalanserare som ger [utgående anslutningar](load-balancer-outbound-connections.md) vid övergång från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser så översätter inte interna lastbalanserare anslutningar med utgående ursprung till klientdelen för en intern lastbalanserare eftersom båda finns i det privata IP-adressutrymmet.  På så sätt undviker du risken för SNAT-portöverbelastning i ett unikt internt IP-adressutrymme där översättning inte krävs.  Bieffekten är att om ett utgående flöde från en virtuell dator i serverdelspoolen försöker med ett flöde till klientdelen för den interna lastbalanseraren där poolen finns _och_ mappas tillbaka till sig själv så matchar inte båda benen för flödet och flödet misslyckas.  Om flödet inte har mappats tillbaka till samma virtuella dator i serverdelspoolen som har skapat flödet till klientdelen kommer flödet att lyckas.   När flödet mappas tillbaka till sig själv verkar det utgående flödet komma från den virtuella datorn till klientdelen och det motsvarande inkommande flödet verkar komma från själva den virtuella datorn. Från gästoperativsystemets perspektiv matchar inte de inkommande och utgående delarna av samma flöde i den virtuella datorn. TCP-stacken känner inte igen dessa halvor av samma flöde som en del av samma flöde som källan eftersom källan och målet inte matchar.  När flödet mappas till en annan virtuell dator i serverdelspoolen matchar halvorna av flödet och den virtuella datorn kan svara på flödet.  Symtomet för det här scenariot är tillfälliga anslutningstimeouter när flödet returnerar samma serverdel som är ursprung till flödet. Det finns flera vanliga lösningar för att tillförlitligt uppnå det här scenariot (flöden från en serverdelspool till serverdelspoolerna respektive den interna lastbalanserarens klientdel) som innehåller infogande av en proxynivå bakom den interna lastbalanseraren eller [med hjälp av DSR-formatregler](load-balancer-multivip-overview.md).  Kunder kan kombinera en intern lastbalanserare med en tredjepartsproxy eller en ersättande intern [Application Gateway](../application-gateway/application-gateway-introduction.md) för proxyscenarier begränsade till HTTP/HTTPS. Du kan använda en offentlig lastbalanserare som åtgärd men det resulterande scenariot har en fallenhet för [SNAT-överbelastning](load-balancer-outbound-connections.md#snat) och bör undvikas om det inte hanteras noggrant.

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure Load Balancer. Du kan komma igång med att använda en lastbalanserare genom att skapa en, skapa virtuella datorer med en anpassat IIS-tillägg installerat och belastningsutjämna webbappen mellan de virtuella datorerna. Information om hur du gör det finns i snabbstarten [Skapa en Basic Load Balancer](quickstart-create-basic-load-balancer-portal.md).
