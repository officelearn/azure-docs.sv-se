---
title: Översikt över Azure Load Balancer | Microsoft Docs
description: Översikt över Azure Load Balancer funktioner, arkitektur och implementering. Lär dig hur belastningsutjämnaren fungerar och utnyttja den i molnet.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7495ac8b1414412dba9d62d0fb5668c6db364997
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215058"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

Med Azure Load Balancer kan du skala dina program och skapa hög tillgänglighet för dina tjänster. Belastningsutjämnare har stöd för inkommande och utgående scenarier, ger mindre fördröjning och högt dataflöde och kan skalas upp till miljontals flöden för alla TCP och UDP-tillämpningar.  

Belastningsutjämnaren distribuerar nya inkommande flöden som inkommer till belastningsutjämnarens klientdel till backend-pool instanser, enligt regler och hälsokontroller av slutpunkter. 

En offentlig belastningsutjämnare kan dessutom ge utgående anslutningar för virtuella datorer (VM) i ditt virtuella nätverk genom att översätta sina privata IP-adresser till offentliga IP-adresser.

Azure Load Balancer är tillgängliga i två SKU: er: Basic och Standard. Det finns skillnader i skala, funktioner och priser. Alla scenarier som du kan göra med Basic Load Balancer kan även skapas med Standard Load Balancer, även om metoderna kan skilja sig något. När du lär dig mer om belastningsutjämnare, är det viktigt att bekanta dig med grunderna och SKU-specifika skillnader.

## <a name="why-use-load-balancer"></a>Varför använda belastningsutjämnare? 

Du kan använda Azure Load Balancer för att:

* Belastningsutjämna inkommande Internettrafik till dina virtuella datorer. Den här konfigurationen kallas en [offentlig belastningsutjämnare](#publicloadbalancer).
* Belastningsutjämna trafik mellan virtuella datorer i ett virtuellt nätverk. Du kan också nå en klientsidan belastningsutjämnare från ett lokalt nätverk i ett scenario med hybridanvändning. Båda scenarierna använder en konfiguration som kallas en [intern belastningsutjämnare](#internalloadbalancer).
* Port vidarebefordra trafik till en viss port på specifika virtuella datorer med regler för inkommande nätverksadressöversättning (NAT).
* Ange [utgående anslutning](load-balancer-outbound-connections.md) för virtuella datorer i ditt virtuella nätverk med hjälp av en offentlig belastningsutjämnare.


>[!NOTE]
> Azure tillhandahåller en uppsättning fullständigt hanterade lösningar för belastningsutjämning för dina scenarier. Om du letar efter Transport Layer Security (TLS) protokollet avslutning (”SSL-avlastning”) eller per-HTTP/HTTPS-begäran, programnivå bearbetning, granska [Application Gateway](../application-gateway/application-gateway-introduction.md). Om du behöver för globala DNS belastningsutjämning, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Slutpunkt till slutpunkt-scenarier kan dra nytta av att kombinera dessa lösningar efter behov.

## <a name="what-are-load-balancer-resources"></a>Vad är resurser för belastningsutjämning?

En belastningsutjämningsresurs kan finnas som en offentlig belastningsutjämnare eller en intern belastningsutjämnare. Funktioner för den belastningsutjämningsresursen uttrycks som en klientdel, en regel, en hälsoavsökning och en definition för backend-poolen. Du kan placera virtuella datorer i serverdelspoolen genom att ange serverdelspoolen från den virtuella datorn.

Resurser för belastningsutjämning är objekt som du kan uttrycka hur Azure bör programmet sin infrastruktur för flera klienter att uppnå det scenario som du vill skapa. Det finns ingen direkt relation mellan resurser för belastningsutjämning och faktiska infrastruktur. Skapa en belastningsutjämnare skapar inte en instans och kapacitet finns alltid. 

## <a name="fundamental-load-balancer-features"></a>Grundläggande funktioner för belastningsutjämnare

Belastningsutjämnaren innehåller följande grundläggande funktioner för TCP och UDP-program:

* **Belastningsutjämning**

    Du kan skapa en regel för belastningsutjämning för att distribuera trafik anländer till klientdel till backend-pool instanser med Azure Load Balancer. Belastningsutjämnaren använder en hash-baserad algoritm för distribution av inkommande flöden och skriver om huvuden för poolen serverdelsinstanser därefter. En server är tillgänglig för att ta emot nya flöden när en hälsoavsökning indikerar en felfri backend-slutpunkt.
    
    Som standard använder belastningsutjämnaren en 5-tuppel-hash som består av källans IP-adress, källport, mål-IP-adress, målport och IP-protokollnummer för att mappa flöden till tillgängliga servrar. Du kan välja att skapa tillhörigheten till en viss käll-IP-adress genom att när en 2 - eller 3-tuppel-hash för en viss regel. Alla paket som är av samma paketflödet tas emot på samma instans bakom Utjämning av nätverksbelastning klientdelen. När klienten skickar ett nytt flöde från samma käll-IP, port ändringarna källa. 5-tuppel kan därför orsaka trafik att gå till en annan backend-slutpunkt.

    Mer information finns i [Distributionsläge för Load balancer](load-balancer-distribution-mode.md). Följande bild visar den hash-baserade distributionen:

    ![Hash-baserade distribution](./media/load-balancer-overview/load-balancer-distribution.png)

    *Bild: Hash-baserade distribution*

* **Portvidarebefordran**

    Med Load Balancer, kan du skapa en inkommande NAT-regel att vidarebefordra trafik för port från en viss port på en specifik frontend IP-adress till en specifik port för en specifik backend-instans i virtuella nätverk. Detta är också göra samma hash-baserade distributionen som Utjämning av nätverksbelastning. Vanliga scenarier för den här funktionen är Remote Desktop Protocol (RDP) eller Secure Shell (SSH)-sessioner till enskilda VM-instanser i Azure Virtual Network. Du kan mappa flera interna slutpunkter till olika portar på samma IP-adress för klientdel. Du kan använda dem för att fjärradministrera dina virtuella datorer via internet utan att behöva en ytterligare jumpbox.

* **Programmet oberoende och transparent**

    Belastningsutjämnaren samverkar inte direkt med TCP eller UDP eller programnivån och alla TCP eller UDP programscenariot kan stödjas.  Belastningsutjämnaren inte avsluta eller kommer flöden, interagera med nyttolasten för flödet, innehåller inga program layer gateway-funktionen och protocol-handskakningar alltid ske direkt mellan klienten och serverdels-serverpoolinstans.  Ett svar till en inkommande flödet är alltid ett svar från en virtuell dator.  När flödet tas emot på den virtuella datorn, bevaras också ursprungliga källans IP-adress.  Ett par exempel som illustrerar ytterligare transparens:
    - Varje slutpunkt bara besvaras av en virtuell dator.  Till exempel inträffar en TCP-handskakning alltid mellan klienten och den valda virtuella datorn på serversidan.  Ett svar på en begäran till en klientdel är ett svar som genererats av den virtuella datorn på serversidan. När du validerar har anslutning till en klientdel, validerar du från slutpunkt till slutpunkt-anslutning till minst en virtuell dator för serverdelen.
    - Programmet nyttolasterna är transparent för belastningsutjämnaren och alla UDP eller TCP-program kan stödjas. För arbetsbelastningar som kräver per bearbetning av HTTP-begäran eller manipulation av application layer nyttolaster (till exempel tolkning av HTTP-URL: er), bör du använda layer 7 belastningsutjämning som [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Eftersom belastningsutjämnaren är oberoende av TCP-nyttolasten och TLS-avlastning (”SSL”) har inte angetts, kan du bygga heltäckande krypterade scenarier med hjälp av belastningsutjämnare och få stor skala ut för TLS-program genom att terminera TLS-anslutningen på Virtuellt datorn.  Din nyckelmaterial kapacitet för TLS-session begränsas till exempel bara efter typ och antal virtuella datorer som du lägger till i serverdelspoolen.  Om du behöver ”SSL-avlastning”, application layer behandling eller vill delegera certifikathantering till Azure, bör du använda Azures belastningsutjämnare för layer 7 [Application Gateway](https://azure.microsoft.com/services/application-gateway) i stället.
        

* **Automatisk omkonfigurering**

    Belastningsutjämnaren direkt konfigurerar om sig själv när du skalar instanser upp eller ned. Att lägga till eller ta bort virtuella datorer från serverdelspoolen konfigurerar du om belastningsutjämnaren utan ytterligare åtgärder på belastningsutjämningsresursen.

* **Hälsoavsökningar**

     Belastningsutjämnaren använder hälsoavsökningar som du definierar för att fastställa hälsotillståndet för instanser i serverdelspoolen. När en avsökning inte svarar slutar belastningsutjämnaren att skicka nya anslutningar till felaktiga instanser. Befintliga anslutningar påverkas inte och de fortsätter tills programmet avslutas flödet, en timeout för inaktivitet inträffar, eller Virtuellt datorn stängs av.

    Tre typer av avsökningar stöds:

    - **Anpassade HTTP-avsökning**: du kan använda den här avsökningen för att skapa en egen anpassad logik för att fastställa hälsotillståndet för en serverdels-serverpoolinstans. Belastningsutjämnaren kontrollerar regelbundet slutpunkten (var 15 sekunder, som standard). Instansen anses vara felfritt även om svarar den med en HTTP 200 inom tidsgränsen (standard 31 sekunder). Vilken status som helst än 200 HTTP gör den här avsökningen misslyckas. Den här avsökningen är också användbart för att implementera en egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan till exempel konfigurerar en instans för att returnera statusen icke-200 om-instansen är större än 90 procent CPU.  Den här avsökningen åsidosätter standard gäst agenten avsökningen.

    - **Anpassade TCP-avsökning**: den här avsökningen förlitar sig på att upprätta en lyckad TCP-session till en definierad avsökningsporten. Den här avsökningen lyckas så länge som den angivna lyssnaren på den virtuella datorn finns. Avsökningen misslyckas om anslutningen nekas. Den här avsökningen åsidosätter standard gäst agenten avsökningen.

    - **Gästen agenten avsökningen**: belastningsutjämnaren kan också använda gästagenten på den virtuella datorn. Gästagenten lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar. Om agenten inte svarar med en HTTP 200 OK, markerar instansen som inte svarar belastningsutjämnaren och stoppar skickar trafik till den instansen. Belastningsutjämnaren fortsätter att försöka att nå instansen. Om gästagenten svarar med ett 200 HTTP, skickar belastningsutjämnaren trafik till den instansen igen. Avsökningar för gäst-agenten är en _sista utväg och rekommenderas inte_ när HTTP eller TCP anpassad avsökningskonfigurationer är möjligt. 
    
* **Utgående anslutningar (SNAT)**

    Alla utgående flöden från privata IP-adresser i ditt virtuella nätverk till offentliga IP-adresser på internet kan översättas till en frontend-IP-adressen för belastningsutjämnaren. När en offentlig klientdel är knuten till en serverdel VM till en belastningsutjämningsregel program utgående anslutningar som automatiskt ska översättas till offentliga klientdelens IP-adress i Azure.

    * Aktivera enkel uppgradering och haveriberedskap för tjänster, eftersom klientdelen dynamiskt kan mappas till en annan instans av tjänsten.
    * Enklare åtkomstkontrollistan (ACL) åtkomstkontrollhantering till. ACL: er som är uttryckt i klientdelens IP-adresser ändras inte services skala upp eller ned eller hämta omdistribueras.  Översätter utgående anslutningar till ett mindre antal IP-adresser än datorer kan minska belastningen på listan över tillåtna program.

    Mer information finns i [utgående anslutningar](load-balancer-outbound-connections.md).

Standard Load Balancer har ytterligare SKU-specifika funktioner utöver dessa grunderna. Gå igenom resten av den här artikeln för information.

## <a name="skus"></a> Load Balancer SKU jämförelse

Load Balancer stöder både grundläggande och standardmässig, var och en skiljer sig i scenariot skala, funktioner och priser. Alla scenarier som du kan göra med Basic Load Balancer kan även skapas med Standard Load Balancer. API: er för båda SKU: er är i själva verket liknande och anropad via specifikation av en SKU. API för att stödja SKU: er för belastningsutjämnaren och den offentliga IP-Adressen är tillgängliga från och med 2017-08-01-API. Båda SKU: er har samma allmänna API och struktur.

Men, beroende på vilken SKU du väljer, konfigurationen av hela scenariot kan skilja sig något. Dokumentation om belastningsutjämnare som anropar när en artikel gäller enbart för en specifik SKU. Om du vill jämföra och information om skillnaderna finns i följande tabell. Mer information finns i [översikt över Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Ny utformning intar Standard Load Balancer. 

Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan anslutas till endast en SKU aldrig båda. När du använder dem med offentliga IP-adresser, både belastningsutjämnaren och den offentliga IP-adressen SKU måste överensstämma. Belastningsutjämnaren och offentliga IP-SKU: er är inte föränderliga.

_Det är bäst att ange SKU: erna explicit, även om det inte är ännu obligatoriskt._  För tillfället som nödvändiga ändringar hålls till ett minimum. Om en SKU inte anges, tolkas det som en avsikt att använda API-versionen 2017-08-01 av en grundläggande SKU.

>[!IMPORTANT]
>Standard Load Balancer är en ny produkt i belastningsutjämnaren och i stort sett en supermängd Basic Load Balancer. Det finns viktiga och avsiktlig skillnaderna mellan de här två produkterna. Alla slutpunkt till slutpunkt-scenario som är möjligt med Basic Load Balancer kan även skapas med Standard Load Balancer. Om du redan är van att Basic Load Balancer, bör du bekanta dig med Standard Load Balancer för att förstå de senaste ändringarna i beteendet mellan Standard och Basic och deras inverkan. Granska det här avsnittet noggrant.

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Backend-pool-storlek | upp till 1000 instanser | upp till 100 instanser |
| Slutpunkter för backend-pool | alla virtuella datorer i ett enda virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar, VM-skalningsuppsättningar. | virtuella datorer i en enda tillgänglighet eller en virtuell dator skala |
| Tillgänglighetszoner | zonredundant och zonindelade klienter för inkommande och utgående, utgående flöden mappningar överleva zon fel, belastningsutjämning mellan zoner | / |
| Diagnostik | Azure Monitor flerdimensionella mått, till exempel byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutning health (SNAT lyckade och misslyckade flöden), aktiva data plan mätning av faktisk användning | Azure Log Analytics för offentlig belastningsutjämnare, SNAT överbelastning avisering, backend-pool hälsotillstånd antal |
| Hög tillgänglighet portar | den interna belastningsutjämnaren | / |
| Säker som standard | standard stängd för offentliga IP-adress och belastningsutjämnare slutpunkter och en nätverkssäkerhetsgrupp måste användas för att uttryckligen lista över tillåtna för trafik kan flöda | standard öppen nätverkssäkerhetsgrupp valfritt |
| [Utgående anslutningar](load-balancer-outbound-connections.md) | Flera klienter med per avanmälan av regeln för belastningsutjämning. Ett scenario för utgående _måste_ uttryckligen skapas för den virtuella datorn för att kunna använda utgående anslutning.  [VNet-tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) kan nås utan utgående anslutning och räknas inte mot data som bearbetas.  Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som är inte tillgängligt som VNet-tjänstslutpunkter, måste uppnås via utgående anslutning och antal för data som bearbetas. Utgående anslutningar via standard SNAT är inte tillgängliga när bara en intern belastningsutjämnare fungerar som värd för en virtuell dator. Utgående SNAT-programmering är transportprotokollet specifika baserat på protokollet av inkommande regel för belastningsutjämning. | Enkel klientdel, slumpmässigt valda när det finns flera klienter.  När endast den interna belastningsutjämnaren fungerar som värd för en virtuell dator, används standardvärdet SNAT. |
| [Flera klienter](load-balancer-multivip-overview.md) | Inkommande och [utgående](load-balancer-outbound-connections.md) | Endast inkommande |
| [Hälsoavsökning ned beteende](load-balancer-custom-probe-overview.md) | TCP-anslutningarna förblir aktiva på instansen avsökning av __och__ på alla avsökningar av | TCP-anslutningarna förblir aktiva på instansen avsökningen ned. Alla TCP-anslutningar avslutas på alla avsökningar av |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60 – 90 sekunder vanliga |
| SLA | 99,99% för sökvägen med två felfria virtuella datorer | Implicit i VM-serviceavtal | 
| Prissättning | Debiteras baserat på antalet regler kan data som bearbetas för inkommande eller utgående som är associerad med resurs  | utan kostnad |

Mer information finns i [tjänstbegränsningar för belastningsutjämnaren](https://aka.ms/lblimits). Standard Load Balancer information finns i [översikt](load-balancer-standard-overview.md), [priser](https://aka.ms/lbpricing), och [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Begrepp

### <a name = "publicloadbalancer"></a>Offentlig belastningsutjämnare

En offentlig belastningsutjämnare mappar det offentliga IP-adress och port antalet inkommande trafik till privata IP-adressen och porten numret för den virtuella datorn och vice versa för svarstrafik från den virtuella datorn. Du kan distribuera specifika typer av trafik över flera virtuella datorer eller tjänster genom att tillämpa regler för belastningsutjämning. Exempelvis kan sprida du belastningen på begäran webbtrafik över flera webbservrar.

Följande bild visar en belastningsutjämnad slutpunkt för webbtrafik som delas mellan tre virtuella datorer för den offentliga och privata TCP-porten 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![Exempel på offentliga belastningsutjämnare](./media/load-balancer-overview/IC727496.png)

*Bild: Läsa in belastningsutjämning webbtrafik med hjälp av en offentlig belastningsutjämnare*

När internet-klienter skickar förfrågningar för webbsidan till offentliga IP-adressen för en webbapp på TCP-port 80, distribuerar Azure Load Balancer begäranden över tre virtuella datorer i den belastningsutjämnade uppsättningen. Läs mer om load balancer algoritmer, den [läsa in funktioner för belastningsutjämning](load-balancer-overview.md##fundamental-load-balancer-features) i den här artikeln.

Som standard distribuerar Azure Load Balancer nätverkstrafik balanseras mellan flera Virtuella datorinstanser. Du kan också konfigurera sessionstillhörighet. Mer information finns i [Distributionsläge för load balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Intern belastningsutjämnare

En intern belastningsutjämnare dirigerar trafik endast till resurser som är i ett virtuellt nätverk eller som använder en VPN-anslutning för att få åtkomst till Azure-infrastrukturen. I detta avseende en intern belastningsutjämnare skiljer sig från en offentlig belastningsutjämnare. Azure-infrastrukturen begränsar åtkomsten till Utjämning av nätverksbelastning frontend IP-adresserna för ett virtuellt nätverk. klientdelens IP-adresser och virtuella nätverk exponeras aldrig direkt till en internet-slutpunkt. Interna line-of-business-program körs i Azure och kan nås från Azure eller från lokala resurser.

En intern belastningsutjämnare kan följande typer av Utjämning av nätverksbelastning:

* **Inom ett virtuellt nätverk**: belastningsutjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För ett virtuellt nätverk mellan lokala**: belastningsutjämning från lokala datorer till en uppsättning virtuella datorer som finns i samma virtuella nätverk. 
* **För flernivåprogram**: belastningsutjämning för internet-riktade flerskiktade program där backend-nivåerna inte är internet-ansluten. Backend-nivåerna kräver trafik belastningsutjämning från internet-riktade nivå (se nästa bild).
* **För line-of-business-program**: belastningsutjämning för line-of-business-program som finns i Azure utan ytterligare load balancer maskinvara eller programvara. Det här scenariot innehåller lokala servrar som finns i uppsättningen med datorer vars trafik är Utjämning av nätverksbelastning.

![Exempel på intern belastningsutjämnare](./media/load-balancer-overview/IC744147.png)

*Bild: Load balancing flerskiktade program genom att använda både offentliga och interna belastningsutjämnare*

## <a name="pricing"></a>Prissättning
Standard Load Balancer-användning debiteras baserat på antalet konfigurerade regler för belastningsutjämning och mängden bearbetade inkommande och utgående data. För Standard Load Balancer prisinformation, gå till den [belastningsutjämnaren priser](https://azure.microsoft.com/pricing/details/load-balancer/) sidan.

Basic Load Balancer erbjuds utan kostnad.

## <a name="sla"></a>SLA

Information om Standard Load Balancer serviceavtal finns på den [Load Balancer SLA](https://aka.ms/lbsla) sidan. 

## <a name="limitations"></a>Begränsningar

- Belastningsutjämnare är en TCP eller UDP-produkt för belastningsutjämning och portvidarebefordran för dessa specifika IP-protokoll.  Regler för belastningsutjämning och inkommande NAT-regler stöds för TCP och UDP och stöds inte för andra IP-protokoll, inklusive ICMP. Belastningsutjämnaren inte avslutar, svara eller annars interagera med nyttolasten för ett flöde för UDP eller TCP. Det är inte en proxy. Lyckad validering av anslutning till en klientdel måste vidta plats i band med samma protokoll som används i en belastning belastningsutjämning eller inkommande NAT-regel (TCP eller UDP) _och_ minst en av dina virtuella datorer måste generera ett svar för en klient för se ett svar från en klientdel.  Inte får en in-band-svar från belastningsutjämnaren klientdelen anger att inga virtuella datorer kunde svara.  Det går inte att interagera med en klientdel för belastningsutjämnare utan en virtuell dator kan svara.  Detta gäller även för utgående anslutningar där [port maskerade SNAT](load-balancer-outbound-connections.md#snat) är stöds endast för TCP och UDP; alla andra IP-protokoll, inklusive ICMP också misslyckas.  Tilldela en offentlig IP-adress för på instansnivå att minska.
- Till skillnad från offentliga belastningsutjämnare som ger [utgående anslutningar](load-balancer-outbound-connections.md) när övergången från privata IP-adresser i virtuella nätverk till offentliga IP-adresser, interna belastningsutjämnare inte översätta utgående har sitt ursprung anslutningar till klientdelen av en intern belastningsutjämnare som båda är i privat IP-adressutrymme.  På så sätt undviker du risken för SNAT överbelastning i unika interna IP-adressutrymmet som där translation inte krävs.  Effekten på klientsidan är att om ett utgående flöde från en virtuell dator i serverdelspoolen försöker ett flöde till klientdel för den interna belastningsutjämnaren vilken pool finns _och_ mappas till sig själv, både ben av flödet inte matchar och flödet kommer att misslyckas.  Om flödet inte mappade till samma virtuella dator i serverdelspoolen som skapade flödet till klientdelen, lyckas flödet.   När flödet mappar tillbaka till själva utgående flödet verkar kommer från den virtuella datorn till klientdelen och motsvarande inkommande flödet visas som kommer från den virtuella datorn till sig själv. Från gäst-OS synsätt matchar inte de inkommande och utgående delarna av samma flöde inuti den virtuella datorn. TCP-stacken känner inte igen de här delarna av samma flöde som en del av samma flöde som källa och mål inte matchar.  När flödet mappar till andra virtuella datorer i serverdelspoolen, delarna av flödet kommer att matcha och den virtuella datorn har kan svara på flödet.  Symtom för det här scenariot är tillfälliga timeout. Det finns flera vanliga lösningar för att uppnå det här scenariot (med ursprung flöden från en serverdelspool till backend-pooler respektive interna belastningsutjämnare klientdelen) på ett tillförlitligt sätt bland annat antingen inmatningen av en tredjeparts-proxy bakom den interna belastningen Belastningsutjämnare eller [med DSR formatreglerna](load-balancer-multivip-overview.md).  Medan du kan använda en offentlig belastningsutjämnare för att minska, det resulterande scenariot är lätt att göra [SNAT resursuttömning](load-balancer-outbound-connections.md#snat) och bör inte användas om inte noggrant hanteras.

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure Load Balancer. Om du vill komma igång med hjälp av en belastningsutjämnare, skapar ett, skapa virtuella datorer med en anpassad IIS-tillägget installerat och belastningsutjämnar webbprogrammet mellan de virtuella datorerna. Läs finns i den [skapar en grundläggande belastningsutjämnare](quickstart-create-basic-load-balancer-portal.md) Snabbstart.
