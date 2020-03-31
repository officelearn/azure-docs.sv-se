---
title: Komponenter och begränsningar
titleSuffix: Azure Load Balancer
description: Översikt över Azure Load Balancer-komponenter och begränsningar.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2020
ms.author: allensu
ms.openlocfilehash: a94b51e49951948974b8f42f6c89cd3c84f95d65
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064282"
---
# <a name="load-balancer-components-and-limitations"></a>Belastningsutjämnareskomponenter och begränsningar
Azure Load Balancer innehåller flera viktiga komponenter för dess funktion.  Dessa komponenter kan konfigureras i din prenumeration via Azure-portalen, Azure CLI eller Azure PowerShell.  

## <a name="load-balancer-components"></a>Belastningsutjämnarekomponenter

* **Frontend IP-konfigurationer**: IP-adressen för lastjämningsutjämnaren. Det är kontaktpunkten för kunderna. Dessa adresser kan vara antingen: 

    - **[Offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privat IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Servergrupp:** Den grupp av virtuella datorer eller instanser i skalauppsättningen för virtuella datorer som ska hantera den inkommande begäran. För att skala kostnadseffektivt för att uppfylla stora volymer inkommande trafik rekommenderar datorriktlinjer i allmänhet att du lägger till fler instanser i serverdapoolen. Belastningsutjämnaren konfigurerar om sig själv automatiskt via automatisk omkonfigurering när du skalar instanser uppåt eller nedåt. Om du lägger till eller tar bort virtuella datorer från serverda poolen konfigureras belastningsutjämnningen om utan ytterligare åtgärder. Omfattningen av serverda poolen är alla virtuella datorer i det virtuella nätverket. En serverdpool kan ha upp till 1000 serverdinstanser eller IP-konfigurationer.
Grundläggande belastningsutjämnare har begränsat scope (tillgänglighetsuppsättning) kan bara skala upp till 300 IP-konfigurationer. Mer information om begränsningar finns i [Belastningsutjämnadgränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). När du överväger hur du utformar backend-poolen kan du designa för minst antal enskilda backend poolresurser för att ytterligare optimera varaktigheten för hanteringsåtgärder. Det finns ingen skillnad i dataplansprestanda eller skala.
* **Hälsoavsökningar**: En **[hälsoavsökning](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** används för att fastställa hälsotillståndet för instanserna i serverdapoolen. Du kan definiera det felaktiga tröskelvärdet för hälsoavsökningar. När en avsökning inte svarar slutar lastbalanseraren att skicka nya anslutningar till de ohälsosamma instanserna. Ett avsökningsfel påverkar inte befintliga anslutningar. 
    
    Anslutningen fortsätter tills programmet: 
    - Avslutar flödet
    - Tidsgränsen för inaktiv inaktivitet inträffar
    - Den virtuella datorn stängs av

    Belastningsutjämnaren tillhandahåller olika typer av hälsoavsökningar för slutpunkter:
    - TCP
    - HTTP
    - HTTPS(HTTP-avsökning med TLS-omslag (Transport Layer Security)
     
     Grundläggande belastningsutjämnare stöder inte HTTPS-avsökningar. Dessutom kommer Basic Load Balancer att avsluta alla TCP-anslutningar (inklusive etablerade anslutningar). 
    Mer information finns i [Avsökningstyper](load-balancer-custom-probe-overview.md#types).

* **Belastningsutjämningsregler**: Belastningsutjämningsregler är de som talar om för belastningsutjämnaren vad som behöver göras när. 
* **Inkommande NAT-regler**: En inkommande NAT-regel vidarebefordrar trafik från en viss port med en specifik IP-adress för klientdel till en specifik port för en viss serverdelsinstans i det virtuella nätverket. **[Port vidarebefordran](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** görs av samma hash-baserad fördelning som belastningsutjämning. Vanliga scenarier för den här funktionen är RDP-sessioner (Remote Desktop Protocol) eller SSH-sessioner (Secure Shell) till enskilda VM-instanser i ett Virtuellt Azure-nätverk. Du kan mappa flera interna slutpunkter till portar på samma ip-adress för frontend. Du kan använda frontend-IP-adresserna för att fjärrstyra dina virtuella datorer utan en extra hoppruta.
* **Utgående regler**: En **[utgående regel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av serverdelspoolen på standardbelastningsutjämnaren som ska översättas till klientdelen.

  Grundläggande belastningsutjämnare stöder inte utgående regler.

  ![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)
* **Transportprotokoll**: Belastningsutjämnaren stöder inte ICMP; ICMP pingar till en publikt vänd belastningsutjämnare kommer time out. Om du vill pinga din offentliga belastningsutjämnare använder du TCP Ping

## <a name="load-balancer-concepts"></a><a name = "load-balancer-concepts"></a>Belastningsutjämnare begrepp

Load Balancer har följande grundläggande funktioner för TCP- och UDP-program:

* **Belastningsutjämningsalgoritm:** Med Azure Load Balancer kan du skapa en belastningsutjämningsregel för att distribuera trafik som anländer till klientdelen till serverdelspoolinstanser. Belastningsutjämnaren använder en hash-algoritm för distribution av inkommande flöden (inte byte) och skriver om rubrikerna för flöden till serverliggande poolinstanser. En server är tillgänglig för att ta emot nya flöden när en hälsoavsökning indikerar en felfri slutpunkt.
Som standard använder belastningsutjämnaren en 5-tuppelhh. 

   Hash inkluderar: 

   - **Källans IP-adress**
   - **Källa port**
   - **Mål-IP-adress**
   - **Destinationsport**
   - **IP-protokollnummer för att mappa flöden till tillgängliga servrar** 

Du kan skapa tillhörighet till en käll-IP-adress med hjälp av en 2- eller 3-tuppelhh för en viss regel. Alla paket i samma paketflöde kommer till samma instans bakom den belastningsutjämnade serverdelen. När klienten startar ett nytt flöde från samma käll-IP ändras källporten. Därför kan 5-tuppelhh orsaka att trafiken går till en annan slutpunkt för backend.
Mer information finns i [Konfigurera distributionsläge för Azure Load Balancer](./load-balancer-distribution-mode.md). 

Följande bild visar den hash-baserade distributionen:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Hash-baserad distribution">
</p>

  *Bild: Hash-baserad distribution*

* **Programmets oberoende och genomskinlighet**: Belastningsutjämnaren interagerar inte direkt med TCP eller UDP eller programlagret. Alla TCP- eller UDP-programscenarion kan stödjas. Belastningsutjämnaren avslutar inte eller påbörjar inte flöden, interagerar med flödets nyttolast eller tillhandahåller någon gateway-funktion för programlager. Protokollhandskakning sker alltid direkt mellan klienten och backend-poolinstansen. Ett svar till ett inkommande flöde är alltid ett svar från en virtuell dator. När flödet kommer till den virtuella datorn bevaras också den ursprungliga käll-IP-adressen.
  * Varje slutpunkt besvaras bara av en virtuell dator. Till exempel sker till exempel ett TCP-handslag alltid mellan klienten och den valda backend-VM. Ett svar på en begäran till en klientdel är ett svar som genereras av en sluten virtuell dator. När du har validerat anslutningen till en klientdel validerar du slutpunkt till slutpunkt-anslutning till minst en klientdator.
  * Programnyttolaster är transparenta för belastningsutjämnaren. Alla UDP- eller TCP-program kan stödjas.
  * Eftersom belastningsutjämnaren inte interagerar med TCP-nyttolasten och tillhandahåller TLS-avlastning kan du skapa krypterade scenarier från slutpunkt till slutpunkt. Om du använder Load Balancer får du storskalig utskalning för TLS-program genom att avsluta TLS-anslutningen på själva den virtuella datorn. TLS-sessionsnyckelkapaciteten begränsas till exempel endast av den typ och antalet virtuella datorer som du lägger till i backend-poolen.

* **Utgående anslutningar:** Alla utgående flöden från privata IP-adresser i ditt virtuella nätverk till offentliga IP-adresser på internet kan översättas till en frontend IP-adress för lastbalanseraren. När en offentlig klientdel är knuten till en fjärrsnårsd-VM via en belastningsutjämningsregel översätter Azure utgående anslutningar till den offentliga ip-adressen för klientdelen. Den här konfigurationen har följande fördelar:
  * Enkel uppgradering och haveriberedskap av tjänster, eftersom klientdelen kan mappas dynamiskt till en annan instans av tjänsten.
  * Enklare åtkomstkontrolllista (ACL) hantering. ACL: er uttryckt som front-end IPs ändras inte som tjänster skala upp eller ner eller få distribueras. Om du översätter utgående anslutningar till ett mindre antal IP-adresser än datorer minskar bördan av att implementera säkra mottagarlistor.

  Standardbelastningsutjämnaren använder en [robust, skalbar och förutsägbar SNAT-algoritm](load-balancer-outbound-connections.md#snat). Dessa är de viktigaste principerna att komma ihåg när du arbetar med Standard Load Balancer:

    - belastningsutjämningsregler drar slutsatsen hur SNAT programmeras. Belastningsutjämningsregler är protokollspecifika. SNAT är protokollspecifikt och konfigurationen bör återspegla detta i stället för att skapa en bieffekt.

    - **Flera frontends** När flera frontends är tillgängliga används alla klientdels och varje klientdel multiplicerar antalet tillgängliga SNAT-portar. Om du vill ha fler SNAT-portar eftersom du förväntar dig eller redan har en hög efterfrågan på utgående anslutningar, kan du också lägga till inkrementella SNAT-portar genom att konfigurera ytterligare frontends, regler och serverdelspooler till samma virtuella dator Resurser.

    - **Styr vilken frontsida som används för utgående** Du kan välja och styra om du inte vill att en viss klientdel ska användas för utgående anslutningar. Om du vill begränsa utgående anslutningar till endast kommer från en specifik ip-adress för klientdel kan du inaktivera utgående SNAT på regeln som uttrycker den utgående mappningen.

    - Utgående utgående scenarier för **ctrl-anslutning** är explicita och utgående anslutning finns inte förrän den har angetts. Standardbelastningsutjämnare finns inom ramen för det virtuella nätverket.  Ett virtuellt nätverk är ett isolerat, privat nätverk.  Om det inte finns en association med en offentlig IP-adress är offentlig anslutning inte tillåten.  Du kan nå [slutpunkter för virtual network service](../virtual-network/virtual-network-service-endpoints-overview.md) eftersom de finns inuti och är lokala i det virtuella nätverket.  Om du vill upprätta utgående anslutning till ett mål utanför det virtuella nätverket har du två alternativ:
        - tilldela en offentlig STANDARD SKU-IP-adress som en offentlig IP-adress på instansnivå till resursen för den virtuella datorn eller
        - placera resursen för den virtuella datorn i serverdapoolen för en offentlig standardbelastningsutjämningsapparat.

        Båda tillåter utgående anslutning från det virtuella nätverket till utanför det virtuella nätverket. 

        Om du _bara_ har en intern standardbelastningsutjämning som är associerad med serverdelspoolen där resursen för den virtuella datorn finns, kan den virtuella datorn bara nå virtuella nätverksresurser och [slutpunkter för virtual network service](../virtual-network/virtual-network-service-endpoints-overview.md).  Du kan följa stegen som beskrivs i föregående stycke för att skapa utgående anslutning.

        Utgående anslutning för en virtuell datorresurs som inte är associerad med standardSKU:er förblir som tidigare.

        Granska [detaljerad diskussion om utgående anslutningar](load-balancer-outbound-connections.md).

* **Tillgänglighetszoner:** Standardbelastningsutjämnaren stöder ytterligare förmågor i regioner där tillgänglighetszoner är tillgängliga. Dessa funktioner är inkrementella till alla standardbelastningsutjämnare ger.  Konfigurationer av tillgänglighetszoner är tillgängliga för båda typerna, offentliga och interna standardbelastningsutjämningsdon.
 En zonundant frontend överlever zonfel och betjänas av dedikerad infrastruktur i alla zoner samtidigt. 
Dessutom kan du garantera en frontend till en viss zon. En zonindelning frontend delar öde med respektive zon och betjänas endast av särskild infrastruktur i en enda zon.
Belastningsutjämning över flera zoner är tillgängligt för serverdelspoolen och alla virtuella datorresurser i ett virtuellt nätverk kan ingå i en serverdelspool.
Grundläggande belastningsutjämnare stöder inte zoner.
Granska [detaljerad diskussion om tillgänglighetszoner relaterade förmågor](load-balancer-standard-availability-zones.md) och [tillgänglighetszoner Översikt](../availability-zones/az-overview.md) för mer information.

* **HA-portar:** Du kan konfigurera belastningsutjämningsregler för att göra din programskala och vara mycket tillförlitlig. När du använder en HA Ports belastningsutjämningsregel tillhandahåller standardbelastningsutjämning per flödesbelastningsutjämning på varje efemär port i en intern standardbelastningsbalanserares IP-adress för frontend.  Funktionen är användbar för andra scenarier där det är opraktiskt eller oönskat att ange enskilda portar. Med en belastningsutjämningsregel för HA Ports kan du skapa aktiva eller aktiva n+1-scenarier för virtuella nätverksinstallationer och alla program, vilket kräver stora intervall av inkommande portar.  En hälsoavsökning kan användas för att avgöra vilka serverderingar som ska ta emot nya flöden.  Du kan använda en nätverkssäkerhetsgrupp för att emulera ett portintervallscenario. Basic Load Balancer stöder inte HA-portar.
Granska [detaljerad diskussion om HA-hamnar](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Om du planerar att använda en virtuell nätverksinstallation kontaktar du leverantören för att få vägledning om huruvida deras produkt har testats med HA-portar och följer deras specifika riktlinjer för implementering. 

* **Flera frontends**: Belastningsutjämnare stöder flera regler med flera frontends.  Standardbelastningsutjämnaren utökar detta till utgående scenarier.  Utgående scenarier är i huvudsak motsatsen till en inkommande belastningsutjämningsregel.  Regeln för inkommande belastningsutjämning skapar också en associerad för utgående anslutningar. Standardbelastningsutjämnaren använder alla frontends som är associerade med en virtuell datorresurs genom en belastningsutjämningsregel.  Dessutom en parameter på belastningsutjämningsregeln och du kan undertrycka en belastningsutjämningsregel för utgående anslutning, vilket gör det möjligt att välja specifika frontends inklusive ingen.

Som jämförelse väljer Basic Load Balancer en enda klientdel slumpmässigt och det finns ingen möjlighet att styra vilken som har valts.
## <a name="load-balancer-types"></a>Belastningsutjämnaretyper

### <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Offentlig lastbalanserare

En offentlig belastningsutjämnare mappar den offentliga IP-adressen och porten för inkommande trafik till den privata IP-adressen och porten för den virtuella datorn. Load Balancer mappar trafik åt andra hållet för svarstrafiken från den virtuella datorn. Du kan distribuera specifika typer av trafik mellan flera virtuella datorer eller tjänster genom att tillämpa belastningsutjämningsregler. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.

>[!NOTE]
>Du kan bara implementera en offentlig belastningsutjämnare och en intern belastningsutjämnare per tillgänglighetsuppsättning.

Följande bild visar en belastningsbalanserad slutpunkt för webbtrafik som delas mellan tre virtuella datorer för den offentliga och TCP-port 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Offentlig belastningsutjämnare">
</p>

*Bild: Balansera webbtrafik med hjälp av en offentlig belastningsutjämnare*

Internetklienter skickar webbplatsbegäranden till den offentliga IP-adressen för en webbapp i TCP-port 80. Azure Load Balancer distribuerar begäranden över de tre virtuella datorerna i den belastningsbalanserade uppsättningen. Mer information om belastningsutjämnarealgoritmer finns i [Belastningsutjämnare begrepp](concepts-limitations.md#load-balancer-concepts).

Azure Load Balancer distribuerar nätverkstrafik lika mellan flera VM-instanser som standard. Du kan även konfigurera sessionstillhörighet. Mer information finns i [Konfigurera distributionsläge för Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a> Intern lastbalanserare

En intern belastningsutjämnare dirigerar endast trafik till resurser som finns i ett virtuellt nätverk eller som använder ett VPN för att komma åt Azure-infrastruktur, i motsats till en offentlig belastningsutjämnare. Azure-infrastruktur begränsar åtkomsten till belastningsbalanserade ip-adresser för frontend i ett virtuellt nätverk. Front-end IP-adresser och virtuella nätverk är aldrig direkt exponerade för en internet slutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser.

En intern lastbalanserare möjliggör följande typer av belastningsutjämning:

* **Inom ett virtuellt nätverk:** Belastningsutjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För ett lokalt nätverk över flera lokala nätverk:** Belastningsutjämning från lokala datorer till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För program på flera nivåer:** Belastningsutjämning för internetinriktade program med flera nivåer där backend-nivåerna inte är internetinriktade. Backend-nivåerna kräver trafikbelastningsutjämning från internet-inför nivå. Se nästa figur.
* **För verksamhetsspecifika appar**: Lastbalansering för verksamhetsspecifika appar som finns i Azure utan ytterligare maskin- eller programvara för lastbalanserare. Det här scenariot omfattar lokala servrar som finns i uppsättningen datorer vars trafik är belastningsbalanserad.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Offentlig belastningsutjämnare">
</p>

*Bild: Balansera program på flera nivåer med hjälp av både offentliga och interna belastningsutjämnare*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Load Balancer SKU:er – jämförelse

Belastningsutjämnaren stöder både basic- och standardsku:er. Dessa SKU:er skiljer sig åt i scenarioskala, funktioner och priser. Alla scenarier som är möjliga med grundläggande belastningsutjämnare kan skapas med standardbelastningsutjämnare. API:erna för båda SKU:erna är likartade och anropas genom specifikationen för en SKU. API:et för att stödja SKU:er för `2017-08-01` belastningsutjämning och den offentliga IP-adressen är tillgängligt från och med API:et. Båda SKU:erna har samma allmänna API och struktur.

Den fullständiga scenariokonfigurationen kan skilja sig något beroende på SKU. Dokumentation av belastningsutjämnare ropar när en artikel endast gäller för en viss SKU. Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns i [översikt över Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft rekommenderar standardbelastningsutjämning.
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Belastningsutjämnare och den offentliga IP-adressen SKU måste matcha när du använder dem med offentliga IP-adresser. Belastningsutjämnare och offentliga IP-SKU:er kan inte stängas av.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Mer information finns i [Belastningsutjämnadgränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Information om Standard Load Balancer finns i [översikt](load-balancer-standard-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name="limitations"></a><a name = "limitations"></a>Begränsningar

- SKU:er kan inte stängas av. Du kanske inte ändrar SKU:n för en befintlig resurs.
- En fristående resurs för virtuella datorer, tillgänglighetsuppsättningsresurs eller skaluppsättningsresurs för virtuella datorer kan referera till en SKU, aldrig båda.
- En belastningsutjämnad regel kan inte sträcka sig över två virtuella nätverk.  Frontends och deras relaterade serverdelsinstanser måste finnas i samma virtuella nätverk.  
- [Flytta prenumerationsåtgärder](../azure-resource-manager/management/move-resource-group-and-subscription.md) stöds inte för Standard LB- och Public IP-resurser.
- Webbarbetareroller utan ett virtuella nätverk och andra Microsoft-plattformstjänster kan endast vara tillgängliga från instanser bakom en intern standardbelastningsutjämning. Du får inte förlita dig på detta eftersom respektive tjänst själv eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du måste skapa [utgående anslutning](load-balancer-outbound-connections.md) uttryckligen om så önskas när du endast använder en intern standardbelastningsutjämningsfaktor.

- Belastningsutjämnaren ger belastningsutjämning och port vidarebefordran för specifika TCP- eller UDP-protokoll. Belastningsutjämningsregler och inkommande NAT-regler stöder TCP och UDP, men inte andra IP-protokoll, inklusive ICMP.

  Belastningsutjämnaren avslutar, svarar eller på annat sätt interagerar med nyttolasten för ett UDP- eller TCP-flöde. Det är ingen proxy. En lyckad validering av anslutningen till en klientdel måste ske i bandet med samma protokoll som används i en belastningsutjämning eller inkommande NAT-regel. Minst en av dina virtuella datorer måste generera ett svar för att en klient ska kunna se ett svar från en klient.

  Får inte ett in-band-svar från belastningsutjämnarfronten anger att inga virtuella datorer kan svara. Ingenting kan interagera med en belastningsutjämnare front utan en virtuell dator som kan svara. Den här principen gäller även utgående anslutningar där portmaskerad SNAT endast stöds för TCP och UDP. Alla andra IP-protokoll, inklusive ICMP, misslyckas. Tilldela en offentlig IP-adress på instansnivå för att minska problemet. Mer information finns i [Förstå SNAT och PAT](load-balancer-outbound-connections.md#snat).

- Interna belastningsutjämnare översätter inte utgående anslutningar till klientdelen av en intern belastningsutjämnare eftersom båda finns i privat IP-adressutrymme. Offentliga belastningsutjämnare tillhandahåller [utgående anslutningar](load-balancer-outbound-connections.md) från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser. För interna belastningsutjämnare undviker den här metoden potentiell SNAT-portutmattning i ett unikt internt IP-adressutrymme, där översättning inte krävs.

  En bieffekt är att om ett utgående flöde från en virtuell dator i backend-poolen försöker ett flöde till främre delen av den interna belastningsutjämnaren i poolen _och_ mappas tillbaka till sig själv, matchar inte flödets två ben. Eftersom de inte matchar, misslyckas flödet. Flödet lyckas om flödet inte mappas tillbaka till samma virtuella dator i backend-poolen som skapade flödet till fronten.

  När flödet mappar tillbaka till sig själv, verkar det utgående flödet att komma från den virtuella datorn till klientdelen och motsvarande inkommande flöde verkar komma från den virtuella datorn till sig själv. Från gästoperativsystemets synvinkel matchar inte de inkommande och utgående delarna av samma flöde inuti den virtuella datorn. TCP-stacken känner inte igen dessa halvor av samma flöde som att vara en del av samma flöde. Källan och målet matchar inte. När flödet mappar till en annan virtuell dator i backend-poolen matchar halvorna av flödet och den virtuella datorn kan svara på flödet.

  Symptomet för det här scenariot är intermittenta anslutningstidsutgångar när flödet återgår till samma backend som har sitt ursprung i flödet. Vanliga lösningar är att infoga ett proxylager bakom den interna belastningsutjämnaren och använda DSR-formatregler (Direct Server Return). Mer information finns i [Flera frontändar för Azure Load Balancer](load-balancer-multivip-overview.md).

  Du kan kombinera en intern belastningsutjämnare med valfri proxy från tredje part eller använda intern [Programgateway](../application-gateway/application-gateway-introduction.md) för proxyscenarier med HTTP/HTTPS. Även om du kan använda en offentlig belastningsutjämnare för att minska problemet, är det resulterande scenariot benägna att [SNAT utmattning](load-balancer-outbound-connections.md#snat). Undvik denna andra metod om inte noggrant hanteras.

- I allmänhet stöds inte vidarebefordran av IP-fragment på belastningsutjämningsregler. IP-fragmentering av UDP- och TCP-paket stöds inte på belastningsutjämningsregler. Belastningsutjämningsregler med hög tillgänglighet kan användas för att vidarebefordra befintliga IP-fragment. Mer information finns i [Översikt över hög tillgänglighetsportar](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Nästa steg

- Se [Skapa en offentlig standardbelastningsutjämnare](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en belastningsutjämnare: skapa en, skapa virtuella datorer med ett anpassat IIS-tillägg installerat och belastningsbalans webbappen mellan de virtuella datorerna.
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Lär dig mer om hur du använder [standardbelastningsutjämning och tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Läs mer om [hälsoavsökningar](load-balancer-custom-probe-overview.md).
- Läs mer om [diagnostik för standardbelastningsutjämnardiagnostik](load-balancer-standard-diagnostics.md).
- Lär dig mer om hur du använder [belastningsutjämnaren för utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md).
- Läs mer om [TCP-återställning på inaktiv](load-balancer-tcp-reset.md).
- Lär dig mer om [standardbelastningsutjämnare med HA Ports belastningsutjämningsregler](load-balancer-ha-ports-overview.md).
- Lär dig mer om hur du använder [belastningsutjämnaren med flera frontends](load-balancer-multivip-overview.md).
- Läs mer om [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
