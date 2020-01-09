---
title: Vad är Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Översikt över funktioner, arkitektur och implementering av Azure Load Balancer-funktioner. Lär dig hur Load Balancer fungerar och hur du utnyttjar det i molnet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2019
ms.author: allensu
ms.openlocfilehash: 4f6a0a2631c3f7458548a2ff435c3e565e744f41
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660228"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

*Belastnings utjämning* syftar på en effektiv distribution av belastningen eller inkommande nätverks trafik i en grupp av Server dels resurser eller servrar. Azure erbjuder en [mängd olika alternativ för belastnings utjämning](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) som du kan välja bland utifrån dina behov. Det här dokumentet täcker Azure Load Balancer.

Azure Load Balancer arbetar i lager fyra av OSI-modellen (Open Systems Interconnection). Det är den enda kontakt punkten för-klienter. Load Balancer distribuerar nya inkommande flöden som anländer till Load Balancer klient delen till backend-instanser, enligt angivna regler för belastnings utjämning och hälso avsökningar. Backend-poolens instanser kan vara Azure Virtual Machines eller instanser i en skalnings uppsättning för virtuella datorer. 

Med Azure Load Balancer kan du skala dina program och skapa tjänster med hög tillgänglighet. Load Balancer stöder både inkommande och utgående scenarier, ger låg latens och högt data flöde och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

En **[offentlig Load Balancer](#publicloadbalancer)** kan tillhandahålla utgående anslutningar för virtuella datorer i det virtuella nätverket genom att översätta sina privata IP-adresser till offentliga IP-adresser. Offentliga belastnings utjämning används för att belastningsutjämna Internet trafik till dina virtuella datorer.

Ett **[internt (eller privat) Load Balancer](#internalloadbalancer)** kan användas för scenarier där endast privata IP-adresser behövs på klient delen. Interna belastnings utjämning används för att belastningsutjämna trafik i ett virtuellt nätverk. Du kan också komma åt en Load Balancer-frontend från ett lokalt nätverk i ett hybrid scenario.

## <a name="load-balancer-components"></a>Load Balancer-komponenter
* **IP-konfigurationer för klient**del: IP-adressen för Load Balancer. Det är kontakt punkten för-klienter. Dessa kan vara antingen offentliga eller privata IP-adresser, vilket skapar antingen offentliga eller interna belastningsutjämnare.
* **Backend-pool**: grupp Virtual Machines eller instanser i den virtuella datorns skalnings uppsättning som ska betjäna inkommande begäran. Om du vill skala kostnads effektivt för att möta stora mängder inkommande trafik beräknings metoder rekommenderar vi vanligt vis att lägga till fler instanser i backend-poolen. Load Balancer omkonfigureras omedelbart via automatisk omkonfiguration när du skalar upp eller ned instanser. När du lägger till eller tar bort virtuella datorer från serverdelspoolen konfigureras lastbalanseraren om utan ytterligare åtgärder på lastbalanseringsresursen.
* **Hälso avsökningar**: en hälso avsökning används för att fastställa hälso tillståndet för instanserna i backend-poolen. Du kan definiera tröskelvärdet för hälso avsökningar. När en avsökning inte svarar slutar lastbalanseraren att skicka nya anslutningar till de ohälsosamma instanserna. Ett avsöknings haveri påverkar inte befintliga anslutningar. Anslutningen fortsätter tills programmet avslutar flödet, tids gränsen för inaktivitet inträffar eller den virtuella datorn stängs av. Load Balancer tillhandahåller olika typer av hälso avsökningar för TCP-, HTTP-och HTTPS-slutpunkter. Mer information finns i [avsöknings typer](load-balancer-custom-probe-overview.md#types).
* **Belastnings Utjämnings regler**: belastnings Utjämnings regler är de som talar om för Load Balancer vad som måste göras när. 
* **Inkommande NAT-regler**: en inkommande NAT-regel vidarebefordrar trafik från en speciell port för en speciell klient dels-IP-adress till en angiven port för en speciell Server dels instans i det virtuella nätverket. Vidarebefordran av portar utförs av samma hash-baserade distribution som belastnings utjämning. Vanliga scenarier för den här funktionen är Remote Desktop Protocol (RDP) eller Secure Shell-sessioner (SSH) till enskilda VM-instanser i en Azure-Virtual Network. Du kan mappa flera interna slut punkter till portar på samma IP-adress för klient delen. Du kan använda klient dels-IP-adresserna för att fjärradministrera dina virtuella datorer utan någon ytterligare hopp ruta.
* **Utgående regler**: en utgående regel konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av backend-poolen som ska översättas till klient delen.

## <a name="load-balancer-concepts"></a>Load Balancer begrepp

Load Balancer har följande grundläggande funktioner för TCP- och UDP-program:

* **Algoritm för belastnings utjämning**: med Azure Load Balancer kan du skapa en regel för belastnings utjämning för att distribuera trafik som kommer till klient delen till Server dels bassängs instanser. Load Balancer använder en hash-algoritm för distribution av inkommande flöden och skriver om meddelandehuvuden för flöden till instanser i backend-poolen. En server är tillgänglig för att ta emot nya flöden när en hälso avsökning indikerar en felfri slut punkt för backend.
Som standard använder Load Balancer en hash med 5 tupler. Hashen omfattar Källans IP-adress, källport, mål-IP-adress, målport och IP-protokollnummer för att mappa flöden till tillgängliga servrar. Du kan skapa tillhörighet till en käll-IP-adress genom att använda en hash med två eller tre tupleer för en specifik regel. Alla paket i samma paketflöde kommer till samma instans bakom den belastningsutjämnade serverdelen. När klienten initierar ett nytt flöde från samma käll-IP, ändras käll porten. Därför kan en hash på 5 tupler leda till att trafiken går till en annan backend-slutpunkt.
Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](load-balancer-distribution-mode.md). Följande bild visar den hash-baserade distributionen:

  ![Hash-baserad distribution](./media/load-balancer-overview/load-balancer-distribution.png)

  *Bild: Hash-baserad distribution*

* **Program oberoende och genomskinlighet**: Load Balancer interagerar inte direkt med TCP eller UDP eller program skiktet. Det kan finnas stöd för alla TCP-eller UDP-programscenarier. Load Balancer inte avslutar eller härstammar från flöden, interagerar med nytto lasten för flödet eller tillhandahåller en program nivå för gateway-funktion. Protokoll hand skakningar sker alltid direkt mellan klienten och backend-instansen. Ett svar till ett inkommande flöde är alltid ett svar från en virtuell dator. När flödet kommer till den virtuella datorn bevaras också den ursprungliga käll-IP-adressen.
  * Varje slutpunkt besvaras bara av en virtuell dator. En TCP-handskakning sker till exempel alltid mellan klienten och den valda backend-VM: en. Ett svar på en begäran till en klient del är ett svar som genereras av en backend-VM. När du har verifierat anslutningen till en klient del, verifierar du slut punkt till slut punkt på minst en virtuell server dels dator.
  * Program nytto laster är transparenta för Load Balancer. Alla UDP-eller TCP-program kan stödjas.
  * Eftersom Load Balancer inte samverkar med TCP-nyttolasten och tillhandahålla TLS-avlastning, kan du skapa krypterade scenarier från slut punkt till slut punkt. Att använda Load Balancer får stor skalbarhet för TLS-program genom att avbryta TLS-anslutningen på den virtuella datorn. Din nyckel kapacitet för TLS-sessioner begränsas till exempel bara av typen och antalet virtuella datorer som du lägger till i backend-poolen.

* **Utgående anslutningar (SNAT)** : alla utgående flöden från privata IP-adresser i ditt virtuella nätverk till offentliga IP-adresser på Internet kan översättas till en klient-IP-adress för Load Balancer. När en offentlig klient del är knuten till en VM-baserad VM med en belastnings Utjämnings regel översätter Azure utgående anslutningar till den offentliga klient delens IP-adress. Den här konfigurationen har följande fördelar:
  * Enkel uppgradering och haveri beredskap för tjänster, eftersom klient delen kan mappas dynamiskt till en annan instans av tjänsten.
  * Enklare hantering av åtkomst kontrol lista (ACL). ACL: er uttryckt som klient-IP-adresser ändras inte när tjänster skalas upp eller ned eller omdistribueras. Översättning av utgående anslutningar till ett mindre antal IP-adresser än datorer minskar belastningen på att implementera listor över betrodda mottagare.
Mer information finns i [utgående anslutningar i Azure](load-balancer-outbound-connections.md).
Standard Load Balancer har ytterligare SKU-/regionsspecifika funktioner utöver de här grunderna, enligt beskrivningen nedan.

## <a name="skus"></a> Load Balancer SKU:er – jämförelse

Load Balancer stöder både Basic-och standard-SKU: er. Dessa SKU: er skiljer sig åt i scenario skala, funktioner och priser. Ett scenario som är möjligt med grundläggande Load Balancer kan skapas med Standard Load Balancer. API: erna för båda SKU: erna är liknande och anropas via specifikationen av en SKU. API: et för stöd för SKU: er för Load Balancer och den offentliga IP-adressen är tillgänglig från och med `2017-08-01` API. Båda SKU: erna delar samma allmänna API och struktur.

Den fullständiga scenario konfigurationen kan skilja sig något beroende på SKU. Load Balancer-dokumentationen anger när en artikel bara gäller för en viss SKU. Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns i [Översikt över Azure standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft rekommenderar Standard Load Balancer.
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Load Balancer och den offentliga IP-adressens SKU måste matcha när du använder dem med offentliga IP-adresser. Load Balancer och offentliga IP-SKU: er är inte föränderligt.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Mer information finns i [gränser för belastnings utjämning](https://aka.ms/lblimits). Information om Standard Load Balancer finns i [översikt](load-balancer-standard-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name="load-balancer-types"></a>Load Balancer typer

### <a name = "publicloadbalancer"></a>Offentlig lastbalanserare

En offentlig Load Balancer mappar den offentliga IP-adressen och porten för inkommande trafik till den privata IP-adressen och porten för den virtuella datorn. Load Balancer mappar trafik på det andra sättet kring svars trafiken från den virtuella datorn. Du kan distribuera vissa typer av trafik över flera virtuella datorer eller tjänster genom att använda regler för belastnings utjämning. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.

>[!NOTE]
>Du kan endast implementera en offentlig Load Balancer och en intern Load Balancer per tillgänglighets uppsättning.

Följande bild visar en belastningsutjämnad slut punkt för webb trafik som delas mellan tre virtuella datorer för den offentliga och TCP-port 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![Exempel på offentlig lastbalanserare](./media/load-balancer-overview/IC727496.png)

*Bild: balansera webb trafik med hjälp av en offentlig Load Balancer*

Internet klienter skickar webb sidor begär anden till den offentliga IP-adressen för en webbapp på TCP-port 80. Azure Load Balancer distribuerar begär Anden mellan de tre virtuella datorerna i den belastningsutjämnade uppsättningen. Mer information om Load Balancer algoritmer finns i [Load Balancer begrepp](load-balancer-overview.md##load-balancer-concepts).

Azure Load Balancer distribuerar nätverks trafiken jämnt mellan flera virtuella dator instanser som standard. Du kan även konfigurera sessionstillhörighet. Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Intern lastbalanserare

En intern Load Balancer dirigerar enbart trafik till resurser som finns i ett virtuellt nätverk eller som använder en VPN för att få åtkomst till Azure-infrastrukturen, i motsats till en offentlig Load Balancer. Azure-infrastrukturen begränsar åtkomsten till de belastningsutjämnade frontend-IP-adresserna för ett virtuellt nätverk. IP-adresser och virtuella nätverk på klient sidan exponeras aldrig direkt till en Internet-slutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser.

En intern lastbalanserare möjliggör följande typer av belastningsutjämning:

* **I ett virtuellt nätverk**: belastnings utjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För ett virtuellt nätverk mellan olika platser**: belastnings utjämning från lokala datorer till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För program på flera nivåer**: belastnings utjämning för Internet riktade program på flera nivåer där backend-nivåerna inte är Internet-riktade. Backend-nivåerna kräver trafik belastnings utjämning från den Internetbaserade nivån. Se nästa figur.
* **För verksamhetsspecifika appar**: Lastbalansering för verksamhetsspecifika appar som finns i Azure utan ytterligare maskin- eller programvara för lastbalanserare. Det här scenariot omfattar lokala servrar som finns i den uppsättning datorer vars trafik är belastningsutjämnad.

![Exempel på intern lastbalanserare](./media/load-balancer-overview/IC744147.png)

*Bild: balansera program på flera nivåer med både offentliga och interna Load Balancer*

## <a name="pricing"></a>Prissättning

Standard Load Balancer-användning debiteras.

* Antal konfigurerade regler för belastnings utjämning och utgående trafik. Inkommande NAT-regler räknas inte i det totala antalet regler.
* Mängden data som bearbetas inkommande och utgående oberoende av regler.

Standard Load Balancer pris information finns i [Load Balancer prissättning](https://azure.microsoft.com/pricing/details/load-balancer/).

Basic Load Balancer tillhandahålls kostnadsfritt.

## <a name="sla"></a>SLA

Information om Standard Load Balancer SLA finns i [SLA för Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Begränsningar

* Load Balancer tillhandahåller belastnings utjämning och port vidarebefordring för vissa TCP-eller UDP-protokoll. Regler för belastnings utjämning och inkommande NAT-regler stöder TCP och UDP, men inte andra IP-protokoll, inklusive ICMP.

  Load Balancer avslutas inte, svarar eller på annat sätt interagerar med nytto lasten för ett UDP-eller TCP-flöde. Det är inte en proxy. Verifieringen av anslutningen till en klient del måste ske i band med samma protokoll som används i en belastnings utjämning eller en inkommande NAT-regel. Minst en av dina virtuella datorer måste generera ett svar för en klient för att kunna se ett svar från klient delen.

  Att inte ta emot ett in-band-svar från Load Balancer klient delen anger att inga virtuella datorer kan svara. Ingenting kan samverka med en Load Balancer klient del utan att en virtuell dator kan svara. Den här principen gäller även för utgående anslutningar där port maskerade SNAT endast stöds för TCP och UDP. Andra IP-protokoll, inklusive ICMP, fungerar inte. Tilldela en offentlig IP-adress på instans nivå för att undvika det här problemet. Mer information finns i [förstå SNAT och Pat](load-balancer-outbound-connections.md#snat).

* Interna belastningsutjämnare översätter inte utgående, ursprungliga anslutningar till klient delen av en intern Load Balancer eftersom båda finns i privata IP-adressutrymme. Publika belastningsutjämnare tillhandahåller [utgående anslutningar](load-balancer-outbound-connections.md) från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser. För interna belastningsutjämnare gör den här metoden att du undviker möjliga SNAT-portar i ett unikt internt IP-adressutrymme, där översättning inte krävs.

  En sido effekt är att om ett utgående flöde från en virtuell dator i backend-poolen försöker använda ett flöde till klient delen av den interna Load Balancer i poolen _och_ som mappas tillbaka till sig själv, matchar inte de två benen i flödet. Eftersom de inte matchar, Miss lyckas flödet. Flödet lyckas om flödet inte mappades tillbaka till samma virtuella dator i backend-poolen som skapade flödet till klient delen.

  När flödet mappar tillbaka till sig själv, visas det utgående flödet som härstammar från den virtuella datorn till klient delen och motsvarande inkommande flöde visas som härstammar från den virtuella datorn till sig själv. Från gäst operativ systemets visnings punkt matchar de inkommande och utgående delarna av samma flöde inte i den virtuella datorn. TCP-stacken känner inte igen de hälften av samma flöde som en del av samma flöde. Källan och målet matchar inte. När flödet mappar till en annan virtuell dator i backend-poolen matchar hälften av flödet och den virtuella datorn kan svara på flödet.

  Symptomet för det här scenariot är tillfälligt anslutnings-timeout när flödet återgår till samma server del som initierade flödet. Vanliga lösningar är infogning av ett proxy-lager bakom den interna Load Balancer och användnings regler för direkt Server retur (DSR). Mer information finns i [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

  Du kan kombinera ett internt Load Balancer med valfri tredjeparts-proxy eller använda interna [Application Gateway](../application-gateway/application-gateway-introduction.md) för proxy-scenarier med http/https. Även om du kan använda en offentlig Load Balancer för att undvika det här problemet, är det här scenariot känsligt för [SNAT-belastning](load-balancer-outbound-connections.md#snat). Undvik den andra metoden om du inte noggrant hanterar den.

* I allmänhet stöds inte vidarebefordrande IP-fragment i regler för belastnings utjämning. IP-fragmentering av UDP-och TCP-paket stöds inte i regler för belastnings utjämning. Belastnings Utjämnings regler för portar med hög tillgänglighet kan användas för att vidarebefordra befintliga IP-fragment. Mer information finns i [Översikt över portar med hög tillgänglighet](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Nästa steg

Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer: skapa en, skapa virtuella datorer med ett anpassat IIS-tillägg installerat och belastningsutjämna webbappen mellan de virtuella datorerna.
