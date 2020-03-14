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
ms.openlocfilehash: aab6a4de7be57df1f691861533a4528a0bcae571
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241684"
---
# <a name="load-balancer-components-and-limitations"></a>Load Balancer komponenter och begränsningar
Azure Load Balancer innehåller flera viktiga komponenter för den här åtgärden.  Dessa komponenter kan konfigureras i din prenumeration via Azure Portal, Azure CLI eller Azure PowerShell.  

## <a name="load-balancer-components"></a>Load Balancer-komponenter

* **IP-konfigurationer för klient**del: belastnings UTJÄMNINGens IP-adress. Det är kontakt punkten för-klienter. Dessa adresser kan vara antingen: 

    - **[Offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privat IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Backend-pool**: gruppen med virtuella datorer eller instanser i den virtuella datorns skalnings uppsättning som ska betjäna inkommande begäran. Om du vill skala kostnads effektivt för att möta stora volymer inkommande trafik rekommenderar vi att du lägger till fler instanser i Server dels poolen. Load Balancer omkonfigureras omedelbart via automatisk omkonfiguration när du skalar upp eller ned instanser. Om du lägger till eller tar bort virtuella datorer från backend-poolen omkonfigureras Load Balancer utan ytterligare åtgärder. Omfånget för backend-poolen är en virtuell dator i det virtuella nätverket. En backend-pool kan ha upp till 1000 Server dels instanser eller IP-konfigurationer.
Grundläggande belastningsutjämnare har begränsad omfattning (tillgänglighets uppsättning) kan bara skala upp till 300 IP-konfigurationer. Mer information om gränser finns [Load Balancer gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). När du överväger att utforma en backend-pool kan du utforma för det minsta antalet enskilda resurser för Server dels poolen för att ytterligare optimera varaktigheten för hanterings åtgärder. Det finns ingen skillnad i prestanda eller skalning för data planet.
* **Hälso avsökningar**: en **[hälso avsökning](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** används för att fastställa hälso tillståndet för instanserna i backend-poolen. Du kan definiera tröskelvärdet för hälso avsökningar. När en avsökning inte svarar slutar lastbalanseraren att skicka nya anslutningar till de ohälsosamma instanserna. Ett avsöknings haveri påverkar inte befintliga anslutningar. 
    
    Anslutningen fortsätter tills programmet: 
    - Avslutar flödet
    - Tids gräns för inaktivitet inträffar
    - Den virtuella datorn stängs av

    Load Balancer innehåller olika typer av hälso avsökningar för slut punkter:
    - TCP
    - HTTP
    - HTTPS (HTTP-avsökning med Transport Layer Security (TLS))
     
     Basic Load Balancer stöder inte HTTPS-avsökningar. Dessutom kommer grundläggande Load Balancer att avsluta alla TCP-anslutningar (inklusive etablerade anslutningar). 
    Mer information finns i [avsöknings typer](load-balancer-custom-probe-overview.md#types).

* **Belastnings Utjämnings regler**: regler för belastnings utjämning är de som talar om för Load Balancer vad som måste göras när. 
* **Inkommande NAT-regler**: en inkommande NAT-regel vidarebefordrar trafik från en speciell port för en speciell klient dels-IP-adress till en angiven port för en speciell Server dels instans i det virtuella nätverket. **[Vidarebefordran av portar](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** utförs av samma hash-baserade distribution som belastnings utjämning. Vanliga scenarier för den här funktionen är Remote Desktop Protocol (RDP) eller Secure Shell-sessioner (SSH) till enskilda VM-instanser i en Azure-Virtual Network. Du kan mappa flera interna slut punkter till portar på samma IP-adress för klient delen. Du kan använda klient dels-IP-adresserna för att fjärradministrera dina virtuella datorer utan någon ytterligare hopp ruta.
* **Utgående regler**: en **[utgående regel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av backend-poolen för standard Load Balancer som ska översättas till klient delen.
Basic Load Balancer stöder inte utgående regler.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name = "load-balancer-concepts"></a>Load Balancer begrepp

Load Balancer har följande grundläggande funktioner för TCP- och UDP-program:

* **Algoritm för belastnings utjämning**: med Azure Load Balancer kan du skapa en regel för belastnings utjämning för att distribuera trafik som anländer till klient delen till Server dels bassängs instanser. Load Balancer använder en hash-algoritm för distribution av inkommande flöden (inte byte) och skriver om meddelandehuvuden för flöden till instanser av Server dels pooler. En server är tillgänglig för att ta emot nya flöden när en hälso avsökning indikerar en felfri slut punkt för backend.
Som standard använder Load Balancer en hash med 5 tupler. 

   Hashen innehåller: 

   - **Käll-IP-adress**
   - **Källport**
   - **Mål-IP-adress**
   - **Målport**
   - **IP-protokoll nummer för att mappa flöden till tillgängliga servrar** 

Du kan skapa tillhörighet till en käll-IP-adress genom att använda en hash med två eller tre tupleer för en specifik regel. Alla paket i samma paketflöde kommer till samma instans bakom den belastningsutjämnade serverdelen. När klienten startar ett nytt flöde från samma käll-IP-adress ändras käll porten. Därför kan en hash på 5 tupler leda till att trafiken går till en annan backend-slutpunkt.
Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](./load-balancer-distribution-mode.md). 

Följande bild visar den hash-baserade distributionen:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Hash-baserad distribution">
</p>

  *Bild: Hash-baserad distribution*

* **Program oberoende och genomskinlighet**: Load Balancer interagerar inte direkt med TCP eller UDP eller program skiktet. Det kan finnas stöd för alla TCP-eller UDP-programscenarier. Load Balancer inte avslutar eller härstammar från flöden, interagerar med nytto lasten för flödet eller tillhandahåller en program nivå för gateway-funktion. Protokoll hand skakningar sker alltid direkt mellan klienten och backend-instansen. Ett svar till ett inkommande flöde är alltid ett svar från en virtuell dator. När flödet kommer till den virtuella datorn bevaras också den ursprungliga käll-IP-adressen.
  * Varje slutpunkt besvaras bara av en virtuell dator. En TCP-handskakning sker till exempel alltid mellan klienten och den valda backend-VM: en. Ett svar på en begäran till en klient del är ett svar som genereras av en backend-VM. När du har verifierat anslutningen till en klient del, verifierar du slut punkt till slut punkt på minst en virtuell server dels dator.
  * Program nytto laster är transparenta för Load Balancer. Alla UDP-eller TCP-program kan stödjas.
  * Eftersom Load Balancer inte samverkar med TCP-nyttolasten och tillhandahålla TLS-avlastning, kan du skapa krypterade scenarier från slut punkt till slut punkt. Genom att använda Load Balancer får du stor skalbarhet för TLS-program genom att avsluta TLS-anslutningen på den virtuella datorn. Din nyckel kapacitet för TLS-sessioner begränsas till exempel bara av typen och antalet virtuella datorer som du lägger till i backend-poolen.

* **Utgående anslutningar**: alla utgående flöden från privata IP-adresser i ditt virtuella nätverk till offentliga IP-adresser på Internet kan översättas till en IP-adress för klient delen för Load Balancer. När en offentlig klient del är kopplad till en VM-baserad virtuell dator via en belastnings Utjämnings regel översätter Azure utgående anslutningar till den offentliga IP-adressen för klient delen. Den här konfigurationen har följande fördelar:
  * Enkel uppgradering och haveri beredskap för tjänster, eftersom klient delen kan mappas dynamiskt till en annan instans av tjänsten.
  * Enklare hantering av åtkomst kontrol lista (ACL). ACL: er uttryckt som klient-IP-adresser ändras inte när tjänster skalas upp eller ned eller omdistribueras. Översättning av utgående anslutningar till ett mindre antal IP-adresser än datorer minskar belastningen på att implementera listor över betrodda mottagare.

  Standard Load Balancer använder sig av en [robust, skalbar och förutsägbar SNAT-algoritm](load-balancer-outbound-connections.md#snat). Det här är de viktiga Tenets som du behöver tänka på när du arbetar med Standard Load Balancer:

    - regler för belastnings utjämning härleder hur SNAT programmeras. Belastnings Utjämnings regler är protokoll information. SNAT är protokoll information och konfigurationen bör avspegla detta i stället för att skapa en sido effekt.

    - **Flera klient** delar När flera klient delar är tillgängliga används alla-frontend och varje klient del multiplicerar antalet tillgängliga SNAT-portar. Om du vill ha fler SNAT-portar eftersom du förväntar dig eller redan har en hög efter frågan för utgående anslutningar, kan du också lägga till en incrementy-port inventering genom att konfigurera ytterligare klient portar, regler och backend-pooler till samma virtuella dator resurser.

    - **Styr vilken klient del som används för utgående** Du kan välja och kontrol lera om du inte vill att en viss klient del ska användas för utgående anslutningar. Om du vill begränsa utgående anslutningar till endast härstammar från en särskild IP-adress för klient delen kan du välja att inaktivera utgående SNAT för regeln som uttrycker utgående mappning.

    - Kontroll av utgående scenarier för **utgående anslutningar** är explicit och utgående anslutning finns inte förrän det har angetts. Standard Load Balancer finns inom kontexten för det virtuella nätverket.  Ett virtuellt nätverk är ett isolerat privat nätverk.  Om det inte finns någon Association med en offentlig IP-adress, tillåts inte offentlig anslutning.  Du kan komma åt [VNet-tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) eftersom de finns inuti och lokalt i det virtuella nätverket.  Om du vill upprätta utgående anslutning till ett mål utanför det virtuella nätverket har du två alternativ:
        - tilldela en offentlig IP-adress för standard-SKU som en offentlig IP-adress på instans nivå till den virtuella dator resursen eller
        - Placera den virtuella dator resursen i backend-poolen för en offentlig Standard Load Balancer.

        Båda kommer att tillåta utgående anslutningar från det virtuella nätverket till utanför det virtuella nätverket. 

        Om du _bara_ har ett internt standard Load Balancer associerat med den backend-pool där din virtuella dator resurs finns, kan den virtuella datorn bara komma åt virtuella nätverks resurser och virtuella nätverks [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md).  Du kan följa stegen som beskrivs i föregående stycke för att skapa utgående anslutning.

        Den utgående anslutningen för en virtuell dator resurs som inte är associerad med standard-SKU: er finns kvar som tidigare.

        Granska [detaljerad diskussion om utgående anslutningar](load-balancer-outbound-connections.md).

* **Tillgänglighetszoner**: standard Load Balancer har stöd för ytterligare möjligheter i regioner där Tillgänglighetszoner är tillgängliga. Dessa funktioner är stegvisa för alla Standard Load Balancer tillhandahåller.  Tillgänglighetszoner konfigurationer är tillgängliga för både typer, offentliga och interna Standard Load Balancer.
 En zon-redundant klient del överleva zon haveri och betjänas av dedikerad infrastruktur i alla zoner samtidigt. 
Dessutom kan du garantera en klient del till en speciell zon. En zonindelade-frontend-resurs avbildar med respektive zon och hanteras endast av dedikerad infrastruktur i en enda zon.
Belastnings utjämning mellan zoner är tillgänglig för backend-poolen och alla virtuella dator resurser i ett virtuellt nätverk kan ingå i en backend-pool.
Basic Load Balancer stöder inte zoner.
Mer information hittar du [i detaljerad beskrivning av Tillgänglighetszoner relaterade](load-balancer-standard-availability-zones.md) funktioner och [Tillgänglighetszoner översikt](../availability-zones/az-overview.md) .

* **Ha-portar**: du kan konfigurera regler för belastnings utjämning för att göra programmets skalning och vara mycket tillförlitlig. När du använder en belastnings Utjämnings regel för belastnings utjämning ger Standard Load Balancer per flödes belastnings utjämning för varje tillfällig port i en intern Standard Load Balancers IP-adress för klient delen.  Funktionen är användbar för andra scenarier där det är opraktiskt eller olämpligt att ange enskilda portar. Med belastnings Utjämnings regeln för en belastnings utjämning kan du skapa aktiva, passiva eller aktiva-aktiva n + 1-scenarier för virtuella nätverks enheter och program, vilket kräver stora intervall av inkommande portar.  En hälso avsökning kan användas för att avgöra vilka Server delar som ska ta emot nya flöden.  Du kan använda en nätverks säkerhets grupp för att emulera ett scenario för port intervall. Basic Load Balancer stöder inte HA-portar.
Granska [detaljerad diskussion om ha-portar](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Om du planerar att använda en virtuell nätverks installation kontaktar du leverantören för att få vägledning om huruvida produkten har testats med HA-portar och följer de rikt linjer som gäller för implementeringen. 

* **Flera klient**delar: Load Balancer stöder flera regler med flera klient delar.  Standard Load Balancer expanderar detta till utgående scenarier.  Utgående scenarier är i stort sett inversen till en inkommande regel för belastnings utjämning.  Den inkommande belastnings Utjämnings regeln skapar även en koppling för utgående anslutningar. Standard Load Balancer använder alla frontend-klienter som är kopplade till en virtuell dator resurs via en belastnings Utjämnings regel.  Dessutom kan du använda en parameter i belastnings Utjämnings regeln och låta dig utelämna en belastnings Utjämnings regel för utgående anslutningar, vilket gör att du kan välja vissa klient delar, inklusive ingen.

För jämförelsen väljer Basic Load Balancer en enskild klient del slumpmässigt och det finns ingen möjlighet att styra vilken som har valts.
## <a name="load-balancer-types"></a>Load Balancer typer

### <a name = "publicloadbalancer"></a>Offentlig lastbalanserare

En offentlig Load Balancer mappar den offentliga IP-adressen och porten för inkommande trafik till den privata IP-adressen och porten för den virtuella datorn. Load Balancer mappar trafik på det andra sättet kring svars trafiken från den virtuella datorn. Du kan distribuera vissa typer av trafik över flera virtuella datorer eller tjänster genom att använda regler för belastnings utjämning. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.

>[!NOTE]
>Du kan endast implementera en offentlig Load Balancer och en intern Load Balancer per tillgänglighets uppsättning.

Följande bild visar en belastningsutjämnad slut punkt för webb trafik som delas mellan tre virtuella datorer för den offentliga och TCP-port 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Offentlig belastningsutjämnare">
</p>

*Bild: utjämna webb trafik med hjälp av en offentlig belastningsutjämnare*

Internet klienter skickar webb sidor begär anden till den offentliga IP-adressen för en webbapp på TCP-port 80. Azure Load Balancer distribuerar begär Anden mellan de tre virtuella datorerna i den belastningsutjämnade uppsättningen. Mer information om Load Balancer algoritmer finns i [Load Balancer begrepp](concepts-limitations.md#load-balancer-concepts).

Azure Load Balancer distribuerar nätverks trafiken jämnt mellan flera virtuella dator instanser som standard. Du kan även konfigurera sessionstillhörighet. Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Intern lastbalanserare

En intern belastningsutjämnare dirigerar enbart trafik till resurser som finns i ett virtuellt nätverk eller som använder en VPN för att få åtkomst till Azure-infrastrukturen, i motsats till en offentlig belastningsutjämnare. Azure-infrastrukturen begränsar åtkomsten till de belastningsutjämnade frontend-IP-adresserna för ett virtuellt nätverk. IP-adresser och virtuella nätverk på klient sidan exponeras aldrig direkt till en Internet-slutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser.

En intern lastbalanserare möjliggör följande typer av belastningsutjämning:

* **I ett virtuellt nätverk**: belastnings utjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För ett virtuellt nätverk mellan olika platser**: belastnings utjämning från lokala datorer till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För program på flera nivåer**: belastnings utjämning för Internet riktade program på flera nivåer där backend-nivåerna inte är Internet-riktade. Backend-nivåerna kräver trafik belastnings utjämning från den Internetbaserade nivån. Se nästa figur.
* **För verksamhetsspecifika appar**: Lastbalansering för verksamhetsspecifika appar som finns i Azure utan ytterligare maskin- eller programvara för lastbalanserare. Det här scenariot omfattar lokala servrar som finns i den uppsättning datorer vars trafik är belastningsutjämnad.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Offentlig belastningsutjämnare">
</p>

*Bild: balansera program på flera nivåer med både offentliga och interna Load Balancer*

## <a name="skus"></a> Load Balancer SKU:er – jämförelse

Load Balancer stöder både Basic-och standard-SKU: er. Dessa SKU: er skiljer sig åt i scenario skala, funktioner och priser. Ett scenario som är möjligt med grundläggande Load Balancer kan skapas med Standard Load Balancer. API: erna för båda SKU: erna är liknande och anropas via specifikationen av en SKU. API för stöd för SKU: er för belastningsutjämnare och den offentliga IP-adressen är tillgängliga från och med `2017-08-01` API. Båda SKU: erna delar samma allmänna API och struktur.

Den fullständiga scenario konfigurationen kan skilja sig något beroende på SKU. Dokumentation om belastningsutjämnare anropas när en artikel endast gäller en speciell SKU. Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns i [Översikt över Azure standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft rekommenderar Standard Load Balancer.
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Load Balancer och den offentliga IP-adressens SKU måste matcha när du använder dem med offentliga IP-adresser. Load Balancer och offentliga IP-SKU: er är inte föränderligt.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Mer information finns i [gränser för belastnings utjämning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Information om Standard Load Balancer finns i [översikt](load-balancer-standard-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name = "limitations"></a>Hos

- SKU: er är inte föränderligt. Du kan inte ändra SKU: n för en befintlig resurs.
- En fristående virtuell dator resurs, tillgänglighets uppsättnings resurs eller en resurs för skalnings uppsättning för virtuell dator kan referera till en SKU, aldrig båda.
- En Load Balancer regel kan inte omfatta två virtuella nätverk.  Frontend-enheter och deras relaterade Server dels instanser måste finnas i samma virtuella nätverk.  
- [Flytt av prenumerations åtgärder](../azure-resource-manager/management/move-resource-group-and-subscription.md) stöds inte för standard-lb-och offentliga IP-resurser.
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås från instanser enbart bakom en intern Standard Load Balancer. Du måste inte förlita dig på detta eftersom själva själva tjänsten eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa [utgående anslutningar](load-balancer-outbound-connections.md) om du vill när du bara använder en intern standard Load Balancer.

- Load Balancer tillhandahåller belastnings utjämning och port vidarebefordring för vissa TCP-eller UDP-protokoll. Regler för belastnings utjämning och inkommande NAT-regler stöder TCP och UDP, men inte andra IP-protokoll, inklusive ICMP.

  Load Balancer avslutas inte, svarar eller på annat sätt interagerar med nytto lasten för ett UDP-eller TCP-flöde. Det är inte en proxy. Verifieringen av anslutningen till en klient del måste ske i band med samma protokoll som används i en belastnings utjämning eller en inkommande NAT-regel. Minst en av dina virtuella datorer måste generera ett svar för en klient för att kunna se ett svar från klient delen.

  Att inte ta emot ett in-band-svar från Load Balancer klient delen anger att inga virtuella datorer kan svara. Ingenting kan samverka med en Load Balancer klient del utan att en virtuell dator kan svara. Den här principen gäller även för utgående anslutningar där port maskerade SNAT endast stöds för TCP och UDP. Andra IP-protokoll, inklusive ICMP, fungerar inte. Tilldela en offentlig IP-adress på instans nivå för att undvika det här problemet. Mer information finns i [förstå SNAT och Pat](load-balancer-outbound-connections.md#snat).

- Interna belastningsutjämnare översätter inte utgående, ursprungliga anslutningar till klient delen av en intern Load Balancer eftersom båda finns i privata IP-adressutrymme. Publika belastningsutjämnare tillhandahåller [utgående anslutningar](load-balancer-outbound-connections.md) från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser. För interna belastningsutjämnare gör den här metoden att du undviker möjliga SNAT-portar i ett unikt internt IP-adressutrymme, där översättning inte krävs.

  En sido effekt är att om ett utgående flöde från en virtuell dator i backend-poolen försöker använda ett flöde till klient delen av den interna Load Balancer i poolen _och_ som mappas tillbaka till sig själv, matchar inte de två benen i flödet. Eftersom de inte matchar, Miss lyckas flödet. Flödet lyckas om flödet inte mappades tillbaka till samma virtuella dator i backend-poolen som skapade flödet till klient delen.

  När flödet mappar tillbaka till sig själv, visas det utgående flödet som härstammar från den virtuella datorn till klient delen och motsvarande inkommande flöde visas som härstammar från den virtuella datorn till sig själv. Från gäst operativ systemets visnings punkt matchar de inkommande och utgående delarna av samma flöde inte i den virtuella datorn. TCP-stacken känner inte igen de hälften av samma flöde som en del av samma flöde. Källan och målet matchar inte. När flödet mappar till en annan virtuell dator i backend-poolen matchar hälften av flödet och den virtuella datorn kan svara på flödet.

  Symptomet för det här scenariot är tillfälligt anslutnings-timeout när flödet återgår till samma server del som initierade flödet. Vanliga lösningar är infogning av ett proxy-lager bakom den interna Load Balancer och användnings regler för direkt Server retur (DSR). Mer information finns i [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

  Du kan kombinera ett internt Load Balancer med valfri tredjeparts-proxy eller använda interna [Application Gateway](../application-gateway/application-gateway-introduction.md) för proxy-scenarier med http/https. Även om du kan använda en offentlig Load Balancer för att undvika det här problemet, är det här scenariot känsligt för [SNAT-belastning](load-balancer-outbound-connections.md#snat). Undvik den andra metoden om du inte noggrant hanterar den.

- I allmänhet stöds inte vidarebefordrande IP-fragment i regler för belastnings utjämning. IP-fragmentering av UDP-och TCP-paket stöds inte i regler för belastnings utjämning. Belastnings Utjämnings regler för portar med hög tillgänglighet kan användas för att vidarebefordra befintliga IP-fragment. Mer information finns i [Översikt över portar med hög tillgänglighet](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer: skapa en, skapa virtuella datorer med ett anpassat IIS-tillägg installerat och belastningsutjämna webbappen mellan de virtuella datorerna.
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Lär dig mer om hur du använder [Load Balancer för utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md).
- Läs mer om [TCP-återställning vid inaktivitet](load-balancer-tcp-reset.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Lär dig mer om att använda [Load Balancer med flera klient](load-balancer-multivip-overview.md)delar.
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
