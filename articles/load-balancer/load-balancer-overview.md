---
title: Översikt över Azure belastningsutjämnare | Microsoft Docs
description: Översikt över Azure belastningsutjämnare funktioner, arkitektur och implementering. Lär dig hur belastningsutjämnaren fungerar och utnyttja i molnet.
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
ms.date: 05/03/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8a3eedb5a3d96eedd1a64d85afdb58f8961df272
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure belastningsutjämnare?

Du kan skala ditt program och skapa hög tillgänglighet för dina tjänster med Azure belastningsutjämnare. Belastningsutjämnaren inkommande och utgående scenarier stöds, ger låg latens och hög genomströmning och skalas upp till miljontals flöden för alla TCP och UDP-program.  

Belastningsutjämnare distribuerar nya inkommande flöden som inkommer till belastningsutjämnarens frontend till backend-adresspool instanser enligt regler och hälsoavsökningar. 

En offentlig belastningsutjämnare kan dessutom ge utgående anslutningar för virtuella datorer (VM) i det virtuella nätverket genom att översätta sina privata IP-adresser till offentliga IP-adresser.

Azure belastningsutjämning är tillgänglig i två SKU: er: Basic och Standard. Det finns skillnader i skala, funktioner och priser. Alla scenario som är möjligt med grundläggande belastningsutjämnare kan även skapas med Standard belastningsutjämnare, även om metoderna som kan skilja sig något. Som du lär dig mer om belastningsutjämning, är det viktigt att bekanta dig med grundläggande och SKU-specifik skillnader.

## <a name="why-use-load-balancer"></a>Varför använda belastningsutjämnaren? 

Du kan använda Azure belastningsutjämnare:

* Belastningsutjämna inkommande internet-trafik till din virtuella dator. Den här konfigurationen kallas en [offentlig belastningsutjämnare](#publicloadbalancer).
* Belastningsutjämna trafik över virtuella datorer i ett virtuellt nätverk. Du kan också nå load balancer klientdelen från ett lokalt nätverk i ett hybridscenario. Båda scenarierna använder en konfiguration som kallas en [intern belastningsutjämnare](#internalloadbalancer).
* Port vidarebefordra trafik till en viss port på specifika virtuella datorer med regler för inkommande network adress translation (NAT).
* Ange [utgående anslutning](load-balancer-outbound-connections.md) för virtuella datorer i ditt virtuella nätverk med hjälp av en offentlig belastningsutjämnare.


>[!NOTE]
> Azure tillhandahåller en uppsättning fullständigt hanterade lösningar för belastningsutjämning för dina scenarier. Om du letar efter Transport Layer Security (TLS) protokollet avslutning (”SSL avlastning”) eller per-HTTP/HTTPS-begäran, programnivå bearbetning, granska [Programgateway](../application-gateway/application-gateway-introduction.md). Om du behöver för globala DNS belastningsutjämning, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Slutpunkt till slutpunkt-scenarier kan ha nytta av att kombinera dessa lösningar efter behov.

## <a name="what-are-load-balancer-resources"></a>Vad är load balancer resurser?

En belastningsutjämningsresurs kan finnas som en offentlig belastningsutjämnare eller en intern belastningsutjämnare. Den belastningsutjämningsresurs funktioner uttrycks som en klientdel, en regel, en hälsoavsökningen och en definition av backend-adresspool. Du kan placera virtuella datorer i backend-poolen genom att ange backend-poolen från den virtuella datorn.

Läs in belastningsutjämnaren resurser är objekt inom vilken express hur Azure ska programmet sin infrastruktur för flera innehavare för att få det scenario som du vill skapa. Det finns ingen direkt relation mellan belastningen belastningsutjämnaren resurser och faktiska infrastruktur. Skapa en belastningsutjämnare inte skapa en instans och kapacitet är alltid tillgängligt. 

## <a name="fundamental-load-balancer-features"></a>Grundläggande funktioner för belastningsutjämning

Belastningsutjämnare ger följande grundläggande funktioner för TCP och UDP-program:

* **Belastningsutjämning**

    Du kan skapa en regel för belastningsutjämning om du vill distribuera trafik som når frontend till backend-adresspool instanser med Azure belastningsutjämnare. Belastningsutjämnare använder en hash-baserad algoritm för distribution av inkommande flöden och skrivs därefter huvuden flödar till backend-adresspool instanser. En server är tillgänglig för att ta emot nya flöden när en hälsoavsökningen indikerar en felfri backend-slutpunkt.
    
    Som standard använder en 5-tuppel-hash som består av källans IP-adress, källport, mål-IP-adress, målport och IP-protokollnumret i belastningsutjämnaren för att mappa flöden till tillgängliga servrar. Du kan välja att skapa tillhörighet för en viss käll-IP-adress genom att välja i en 2 - eller 3-tuppel-hash för en viss regel. Alla paket i samma paketflöde tas emot på samma instans bakom Utjämning av nätverksbelastning klientdelen. När klienten startar en ny flödet, från samma käll-IP, de port ändringarna av datakällan. 5-tuppel kan därför orsaka trafik att gå till en annan backend-slutpunkt.

    Mer information finns i [belastningsdistributionsläget nätverksbelastningsutjämning](load-balancer-distribution-mode.md). Följande bild visar den hash-baserad distributionen:

    ![Hash-baserad distribution](./media/load-balancer-overview/load-balancer-distribution.png)

    *Bild: Hash-baserad distribution*

* **Vidarebefordrade portar**

    Med belastningsutjämnare, kan du skapa en inkommande NAT-regel att port vidarebefordra trafik från en viss port för en specifik frontend IP-adress till en specifik port för en specifik backend-instans i det virtuella nätverket. Detta är också göra samma hash-baserad distribution som belastningsutjämning. Vanliga scenarier för den här funktionen är Remote Desktop Protocol (RDP) eller SSH (Secure Shell) sessioner till enskilda VM-instanser i Azure Virtual Network. Du kan mappa flera interna slutpunkter för olika portar på samma frontend IP-adress. Du kan använda dem för att fjärradministrera din virtuella dator via internet utan att behöva en ytterligare hopp ruta.

* **Programmet storleksoberoende och transparent**

    Belastningsutjämnaren interagerar inte direkt med TCP eller UDP eller programlager och alla TCP eller UDP-programmet scenario kan stödjas.  Belastningsutjämnaren matchar inte avsluta eller kommer flöden, interagera med nyttolasten för flödet, innehåller inga program layer gateway-funktionen och protokollet handskakningar på alltid ske direkt mellan klienten och backend-pool-instans.  Ett svar på ett inkommande flöde är alltid ett svar från en virtuell dator.  När flödet anländer på den virtuella datorn, bevaras också ursprungliga källans IP-adress.  Några exempel för att illustrera ytterligare genomskinlighet:
    - Varje slutpunkt har bara besvarats av en virtuell dator.  Till exempel sker en TCP-handskakning alltid mellan klienten och valda backend-VM.  Ett svar på en begäran om att en klientdel är ett svar som genererats av backend-VM. När du har Validera anslutningen till en frontend validerar du slutpunkt till slutpunkt-anslutning till minst en backend-virtuella datorn.
    - Programmet nyttolaster är transparent för belastningsutjämnaren och eventuella UDP eller TCP-programmet kan användas. För arbetsbelastningar som kräver per bearbetning av HTTP-begäranden eller modifiering av application layer nyttolaster (till exempel tolkning av HTTP-URL: er), bör du använda en lager 7 belastningsutjämnare som [Programgateway](https://azure.microsoft.com/en-us/services/application-gateway).
    - Eftersom belastningsutjämnare är oberoende av TCP-nyttolasten och TLS-avlastning (”SSL”) har inte angetts, kan du skapa slutpunkt till slutpunkt krypterade scenarier med hjälp av belastningsutjämnare och få stor skala ut för TLS-program genom att avsluta TLS-anslutning på Virtuellt datorn.  Sessionen TLS transparens kapacitet begränsas bara efter typ och antal virtuella datorer som du lägger till backend-poolen.  Om du behöver ”SSL genom att avlasta” application layer behandling eller vill delegera certifikathantering till Azure, bör du använda Azures layer 7 belastningsutjämnaren [Programgateway](https://azure.microsoft.com/en-us/services/application-gateway) i stället.
        

* **Automatisk omkonfiguration**

    Belastningsutjämnaren konfigurerar omedelbart om sig själv när du skalar delar upp eller ned. Konfigurerar belastningsutjämnaren utan ytterligare åtgärder på belastningsutjämningsresurs att lägga till eller ta bort virtuella datorer från backend-adresspool.

* **Hälsoavsökningar**

     Om du vill fastställa hälsotillståndet för instanserna i backend-poolen används belastningsutjämnaren hälsoavsökningar som du definierar. När en avsökning inte svarar, stannar belastningsutjämnaren skickar nya anslutningar till feltillstånd instanser. Befintliga anslutningar påverkas inte och de fortsätter tills programmet avslutar flödet, en timeout för inaktivitet inträffar, eller den virtuella datorn är avstängd.

    Tre typer av avsökningar stöds:

    - **Anpassade HTTP-avsökningen**: du kan använda den här avsökningen för att skapa egna anpassade logik för att fastställa hälsotillståndet för en instans av backend-adresspool. Belastningsutjämnaren avsökningar regelbundet slutpunkten (var 15 sekunder, som standard). Instansen anses vara felfri om svarar den med en HTTP-200 inom tidsgränsen (standard 31 sekunder). Status än HTTP 200 gör den här avsökningen misslyckas. Den här avsökningen är också användbart för att implementera din egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan till exempel konfigurera instans för att returnera en icke-200 status om instansen är större än 90 procent CPU.  Den här avsökningen åsidosätter standard gäst agent avsökning.

    - **Anpassade TCP-avsökning**: den här avsökningen förlitar sig på att upprätta en lyckad TCP-session till en definierad avsökningsport. Den här avsökningen lyckas så länge som den angivna lyssnaren på den virtuella datorn finns. Avsökningen misslyckas om anslutningen nekas. Den här avsökningen åsidosätter standard gäst agent avsökning.

    - **Gästen agent avsökningen (på plattform som en tjänst [PaaS] virtuella datorer bara)**: belastningsutjämnaren kan också använda gästagenten inuti den virtuella datorn. Gästagenten lyssnar och svarar med ett HTTP-200 OK svar endast när instansen är i tillståndet redo. Om agenten inte svarar med en HTTP-200 OK, markerar instansen som inte svarar belastningsutjämnaren och stoppar skickar trafik till den instansen. Belastningsutjämnaren fortsätter att försöka att nå instansen. Om gästagenten svarar med en HTTP-200, skickar belastningsutjämnaren trafik till instansen igen. Gästen agent avsökningar är en sista utväg och bör inte när HTTP- eller TCP anpassad avsökningsåtgärd konfigurationer är möjliga. 
    
* **Utgående anslutningar (SNAT)**

    Alla utgående flöden från privata IP-adresser i det virtuella nätverket till den offentliga IP-adresser på internet kan översättas till ett frontend IP-adressen för belastningsutjämnaren. När en offentlig klientdel är knuten till en backend-VM via en belastningsutjämningsregel Azure-program utgående anslutningar till översättas automatiskt till offentliga frontend IP-adressen.

    * Aktivera enkel uppgradering och katastrofåterställning för tjänster, eftersom klientdelen dynamiskt kan mappas till en annan instans av tjänsten.
    * Enklare åtkomstkontrollistan (ACL) åtkomstkontrollhantering till. ACL: er som uttrycks i frontend IP-adresser ändras inte services skala upp eller ned eller hämta omdistribueras.  Översätter utgående anslutningar till ett mindre antal IP-adresser än datorer kan minska belastningen på vitlistning.

    Mer information finns i [utgående anslutningar](load-balancer-outbound-connections.md).

Standard belastningsutjämnaren har ytterligare SKU-specifikt funktioner utöver dessa grunderna. Granska resten av den här artikeln för information.

## <a name="skus"></a> Läsa in belastningsutjämning SKU jämförelse

Belastningsutjämnaren stöder både Basic och Standard-SKU: er, varje skiljer sig i scenariot skala, funktioner, och prissättning. Alla scenario som är möjligt med grundläggande belastningsutjämnare kan skapas med Standard belastningsutjämnare samt. API: er för båda SKU: er finns i själva verket liknande och anropade via specificering av en SKU. API för att stödja SKU: er för belastningsutjämnaren och offentliga IP-Adressen är tillgängliga från och med 2017-08-01-API. Båda SKU: er har samma allmänna API och struktur.

Men, beroende på vilka SKU som du väljer konfigurationen av hela scenariot kan skilja sig något. Läsa in belastningsutjämning dokumentationen anropar när en artikel gäller endast för en specifik SKU. Om du vill jämföra och förstå skillnaderna, finns i följande tabell. Mer information finns i [Standard belastningsutjämnaren översikt](load-balancer-standard-overview.md).

>[!NOTE]
> Överväg att använda Standard belastningsutjämnaren om du använder ett nyare design-scenario. 

Fristående virtuella datorer, tillgänglighetsuppsättningar och skalningsuppsättningar i virtuella datorer kan anslutas till endast en SKU aldrig båda. När du använder dem med offentliga IP-adresser, både belastningsutjämnaren och den offentliga IP-adressen SKU måste överensstämma. Belastningsutjämnare och offentliga IP-SKU: er är inte föränderliga.

_Det är bäst att ange de SKU: er explicit, även om det inte är ännu obligatoriskt._  För tillfället hålls nödvändiga ändringar till ett minimum. Om en SKU inte anges, tolkas det som en avsikt att använda 2017-08-01 API-versionen av grundläggande SKU: N.

>[!IMPORTANT]
>Standard belastningsutjämnare är en ny produkt för belastningsutjämnaren och i stort sett inbegriper grundläggande belastningsutjämnaren. Det finns viktiga och avsiktlig skillnaderna mellan de här två produkterna. Alla slutpunkt till slutpunkt-scenario som är möjligt med grundläggande belastningsutjämnare kan även skapas med Standard belastningsutjämnare. Om du redan är för grundläggande belastningsutjämnaren bör du bekanta dig med Standard belastningsutjämnare för att förstå de senaste ändringarna i beteende mellan Standard och Basic och deras effekt. Granska det här avsnittet noggrant.

| | [Standard-SKU](load-balancer-standard-overview.md) | Grundläggande SKU |
| --- | --- | --- |
| Storlek på backend-pool | Upp till 1 000 instanser. | Upp till 100 instanser. |
| Backend-adresspool slutpunkter | Någon virtuell dator i ett enda virtuellt nätverk, inklusive en blandning av virtuella datorer, tillgänglighetsuppsättningar och skalningsuppsättningar i virtuella datorer. | Virtuella datorer i en enda tillgänglighet eller virtuella datorn. |
| Tillgänglighetszoner i Azure | Zonredundant och zonal frontwebbservrarna för inkommande och utgående, utgående flödet mappningar klara zonen fel mellan zon belastningsutjämning. | / |
| Diagnostik | Övervakare för Azure flerdimensionella mått, t.ex. byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutningshälsa (SNAT lyckade och misslyckade flöden), aktiva data plan mått. | Azure Log Analytics för offentliga ladda bara belastningsutjämnare, SNAT uttömning avisering, backend-adresspool hälsa antal. |
| Hög tillgänglighet portar | Intern belastningsutjämnare. | / |
| Som standard | Stängda för offentliga IP-adress och läsa in belastningsutjämning slutpunkter som standard. För trafik, måste du använda en nätverkssäkerhetsgrupp explicit godkända enheter. | Standard Öppna nätverkssäkerhetsgruppen som är valfria. |
| Utgående anslutningar | Flera framför slutar med CEIP per regel. Ett scenario för utgående _måste_ skapas explicit för den virtuella datorn för att kunna använda utgående anslutning. [Virtuellt nätverk Tjänsteslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) kan nås utan utgående anslutning och inte räknas in data bearbetas. Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som inte är tillgängliga som slutpunkter för virtuella nätverk måste uppnås via utgående anslutning och räknas in data bearbetas. När en intern belastningsutjämnare används av en virtuell dator, är utgående anslutningar via standard SNAT inte tillgängliga. Utgående SNAT programmering är transportprotokollet specifika, baserat på protokollet för inkommande regel för belastningsutjämning. | Enskild klientdelen slumpmässigt valda när det finns flera frontwebbservrarna. När en intern belastningsutjämnare används av en virtuell dator, används standardvärdet SNAT. |
| Flera frontwebbservrarna | Inkommande och utgående. | Endast inkommande. |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder. | 60-90 sekunder vanliga. |
| SLA | 99,99 procent för en datasökväg med två felfri virtuella datorer. | Implicit i VM SLA. | 
| Prissättning | Avgifterna baseras på antalet regler och data som behandlas inkommande eller utgående som associeras med resursen.  | Utan kostnad. |

Mer information finns i [tjänsten gränser för belastningsutjämnaren](https://aka.ms/lblimits). Standard belastningsutjämnaren information, se [översikt](load-balancer-standard-overview.md), [priser](https://aka.ms/lbpricing), och [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Begrepp

### <a name = "publicloadbalancer"></a>Offentlig belastningsutjämnare

En offentlig belastningsutjämnare mappar offentliga IP-adress och port antalet inkommande trafik till privata IP-adress och port numret på den virtuella datorn och vice versa för svarstrafik från den virtuella datorn. Du kan distribuera specifika typer av trafik över flera virtuella datorer och tjänster genom att använda regler för belastningsutjämning. Sprida belastningen på begäran Internet-trafik över flera webbservrar.

Följande bild visar en belastningsutjämnad slutpunkt för webbtrafik som delas mellan tre virtuella datorer för offentliga och privata TCP-port 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![exempel på offentliga belastningsutjämnare](./media/load-balancer-overview/IC727496.png)

*Bild: Läsa in belastningsutjämning webbtrafik med hjälp av en offentlig belastningsutjämnare*

När internet-klienter skickar webbsidesbegäranden till offentliga IP-adressen för ett webbprogram på TCP-port 80, distribuerar Azure belastningsutjämnare begäranden mellan tre virtuella datorer i den belastningsutjämnade uppsättningen. Mer information om belastningen belastningsutjämnaren algoritmer finns i [belastningen belastningsutjämnaren översiktssidan](load-balancer-overview.md#load-balancer-features).

Som standard distribuerar Azure belastningsutjämnare nätverkstrafik balanseras mellan flera VM-instanser. Du kan också konfigurera tillhörighet för sessionen. Mer information finns i [belastningsdistributionsläget nätverksbelastningsutjämning](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Interna belastningsutjämnare

En intern belastningsutjämnare dirigerar trafik endast till resurser som inte ingår i ett virtuellt nätverk eller som använder en VPN-anslutning för att få åtkomst till Azure-infrastrukturen. I detta avseende en intern belastningsutjämnare skiljer sig från en offentlig belastningsutjämnare. Azure-infrastrukturen begränsar åtkomsten till de belastningsutjämnade frontend IP-adresserna i ett virtuellt nätverk. Frontend IP-adresser och virtuella nätverk exponeras aldrig direkt till en internet-slutpunkt. Interna line-of-business-program körs i Azure och kan nås från Azure eller från lokala resurser.

En intern belastningsutjämnare kan följande typer av nätverksbelastning:

* **Ett virtuellt nätverk**: belastningsutjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer i samma virtuella nätverk.
* **För ett virtuellt nätverk mellan lokala**: belastningsutjämning från lokala datorer till en uppsättning virtuella datorer i samma virtuella nätverk. 
* **För program på flera nivåer**: belastningsutjämning för internet-riktade flera nivåer program där backend-nivåer inte är mot internet. Backend-nivåer kräver trafik belastningsutjämning från internet-riktade tjänstnivån (se nästa figur).
* **För av branschspecifika program**: belastningsutjämning för line-of-business-program som finns i Azure utan ytterligare belastningen belastningsutjämnaren maskinvara eller programvara. Det här scenariot innehåller lokala servrar som finns i en uppsättning datorer vars trafik Utjämning av nätverksbelastning.

![exempel på interna belastningsutjämnare](./media/load-balancer-overview/IC744147.png)

*Bild: Belastningsutjämning program på flera nivåer med hjälp av både offentliga och interna belastningsutjämnare*

## <a name="pricing"></a>Prissättning
Standard belastningsutjämnare användning debiteras baserat på antalet konfigurerade reglerna för belastningsutjämning och mängden bearbetade inkommande och utgående data. För Standard belastningsutjämnaren prisinformation, gå till den [belastningsutjämnaren priser](https://azure.microsoft.com/pricing/details/load-balancer/) sidan.

Grundläggande belastningsutjämnaren erbjuds utan kostnad.

## <a name="sla"></a>SLA

Information om Standard Load Balancer SLA går du till den [belastningen belastningsutjämnaren SLA](https://aka.ms/lbsla) sidan. 

## <a name="limitations"></a>Begränsningar

- Belastningsutjämning är en TCP- eller UDP-produkt för belastningsutjämning och vidarebefordrade portar för dessa specifika IP-protokoll.  Regler för belastningsutjämning och inkommande NAT-regler som stöds för TCP och UDP- och stöds inte för andra IP-protokoll inklusive ICMP. Belastningsutjämnaren inte avslutas, svara eller annars interagera med nyttolasten för ett UDP eller TCP-flöde. Det är inte en proxy. Lyckad validering av anslutning till en frontend måste vidta plats i band med samma protokoll som används i en belastningsutjämning eller ingående NAT-regel (TCP eller UDP) _och_ minst en av dina virtuella datorer måste generera ett svar för en klient för att se ett svar från en frontend.  Inte får en in-band-svar från belastningsutjämnaren frontend anger inga virtuella datorer kan svara.  Det går inte att interagera med en belastningsutjämnare frontend utan en virtuell dator kan svara.  Detta gäller även för utgående anslutningar där [port maskerade SNAT](load-balancer-outbound-connections.md#snat) är stöds endast för TCP och UDP, några andra inklusive ICMP IP-protokoll fungerar inte.  Tilldela en instansnivå offentliga IP-adress för att minimera.
- Till skillnad från offentliga belastningsutjämnare som innehåller [utgående anslutningar](load-balancer-outbound-connections.md) vid övergång från privata IP-adresser i det virtuella nätverket till den offentliga IP-adresser, interna belastningsutjämnare inte översätta utgående ursprung anslutningar till klientdelen av en intern belastningsutjämnare som båda finns i privata IP-adressutrymme.  På så sätt undviker du risken för SNAT uttömning i unika interna IP-adressutrymmet som där översättning inte krävs.  Bieffekten är att om ett utgående flöde från en virtuell dator i backend-poolen försöker ett flöde till frontend för intern belastningsutjämnare i vilka pool finns _och_ mappas till sig själv, både ben i flöde matchar inte och flödet misslyckas .  Om flödet inte mappade till samma virtuella dator i backend-poolen som flödar till frontend har skapat, lyckas flödet.   När flödet mappar till sig själv utgående flödet verkar kommer från den virtuella datorn till frontend och motsvarande inkommande flödet visas som kommer från den virtuella datorn till sig själv. Ur gäst-OS matchar inte de inkommande och utgående delarna i samma flöde inuti den virtuella datorn. TCP-stacken känner inte igen dessa halvorna i samma flöde som en del av samma flöde som källa och mål inte matchar.  När flödet mappar till till andra Virtuella i backend-poolen, hälften av flödet matchar och den virtuella datorn har kan svara på flödet.  Symtom för det här scenariot är tillfälligt timeout. Det finns flera vanliga lösningar för på ett tillförlitligt sätt att uppnå det här scenariot (ursprung flödar från en backend-pool till backend-adresspooler respektive interna belastningsutjämnare frontend) som innehåller antingen infogning av en tredjeparts-proxy bakom interna belastningen Belastningsutjämnare eller [med DSR formatmallsregler](load-balancer-multivip-overview.md).  När en offentlig belastningsutjämnare kan användas för att minska, det resulterande scenariot är enkelt att [SNAT resursuttömning](load-balancer-outbound-connections.md#snat) och bör inte användas om inte hanteras noggrant.

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure belastningsutjämnare. Skapa en för att komma igång med att använda en belastningsutjämnare, skapa virtuella datorer med en anpassad IIS-tillägg som är installerade och belastningsutjämning webbprogram mellan de virtuella datorerna. Mer information finns i [skapa en grundläggande belastningsutjämnare](quickstart-create-basic-load-balancer-portal.md) Snabbstart.
