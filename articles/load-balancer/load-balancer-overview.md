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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 4f46e796ff1ab85c0061c70ff9a725a6945a4f5d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-load-balancer-overview"></a>Översikt över belastningsutjämnaren i Azure

Azure belastningsutjämnare kan du skala ditt program och skapa hög tillgänglighet för dina tjänster. Belastningsutjämnaren stöder inkommande samt utgående scenarier och ger hög genomströmning, låg svarstid och skalas upp till miljontals flöden för alla TCP och UDP-program.   

Belastningsutjämnaren ska distribuera nya inkommande flöden som ankommer till belastningsutjämnarens klientdel till backend-pool instanser enligt regler och hälsoavsökningar.  

En offentlig belastningsutjämnare kan dessutom också ge utgående anslutningar för virtuella datorer i ditt virtuella nätverk genom att översätta sina privata IP-adresser till offentliga IP-adresser.

Azure belastningsutjämnare finns i två olika SKU: er: Basic och Standard.  Det finns skillnader i skala, funktioner och priser.  Scenarier med grundläggande belastningsutjämnare kan även skapas med Standard belastningsutjämnare, även om metoden kan skilja sig något.  Som du lär dig mer om belastningsutjämning, är det viktigt att bekanta dig med grundläggande och SKU-specifik skillnader.

## <a name="why-use-load-balancer"></a>Varför använda belastningsutjämnaren? 

Azure belastningsutjämnare kan användas för att:

* Läsa in Utjämna inkommande Internet-trafiken till virtuella datorer. Den här konfigurationen kallas en [offentliga belastningsutjämnare](#publicloadbalancer).
* Läs in Utjämna trafiken mellan virtuella datorer i ett virtuellt nätverk. Du kan också nå en belastningsutjämnare klientdel från ett lokalt nätverk i ett hybridscenario. Båda dessa scenarier använder en konfiguration som kallas en [interna belastningsutjämnare](#internalloadbalancer).
* Port vidarebefordra trafik till en viss port på den specifika virtuella datorer med ingående NAT-regler.
* Ange [utgående anslutning](load-balancer-outbound-connections.md) för virtuella datorer i ditt virtuella nätverk med hjälp av en offentlig belastningsutjämnare.


>[!NOTE]
> Azure tillhandahåller en uppsättning helt hanterad lösningar för dina scenarier för belastningsutjämning.  Om du letar efter TLS uppsägning (”SSL avlastning”) eller HTTP/HTTPS application layer bearbetning, granska [Programgateway](../application-gateway/application-gateway-introduction.md).  Om du behöver för globala DNS belastningsutjämning, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Slutpunkt till slutpunkt-scenarier kan dra nytta av att kombinera dessa lösningar efter behov.

## <a name="what-is-load-balancer"></a>Vad är belastningsutjämnare?

En belastningsutjämnare resurs kan finnas som en offentlig belastningsutjämnare eller en intern belastningsutjämnare. Resursen belastningsutjämnaren funktioner uttrycks som en klientdel, en regel, en hälsoavsökningen och en definition för backend-adresspool.  Virtuella datorer placeras i serverdelspoolen genom att ange serverdelspoolen från den virtuella datorn.

Läs in belastningsutjämnaren resurser är objekt inom vilken express hur Azure ska programmet sin infrastruktur för flera innehavare för att få det scenario som du vill skapa.  Det finns ingen direkt relation mellan belastningsutjämnaren resurser och faktiska infrastruktur. Skapa en belastningsutjämnare inte skapa en instans och kapacitet är alltid tillgängligt. 

## <a name="fundamental-load-balancer-features"></a>Grundläggande funktioner för belastningsutjämning

Belastningsutjämnare ger följande grundläggande funktioner för TCP och UDP-program:

* **Belastningsutjämning**

    Azure belastningsutjämnare kan du skapa en regel om du vill distribuera trafik anländer till en klientdel till backend-pool instanser för belastningsutjämning.  Den använder en hash-baserad algoritm för distribution av inkommande flöden och skrivs därefter huvuden flödar till backend-pool instanser. En server är tillgänglig för att ta emot nya flöden när hälsoavsökningen indikerar en felfri backend-slutpunkt.
    
    Som standard används en 5-tuppel-hash som består av källans IP-adress, källport, mål-IP-adress, målport och IP-protokollnumret för att mappa flöden till tillgängliga servrar.  Du kan välja att skapa tillhörighet för en viss käll-IP-adress genom att välja i en 2 - eller 3-tuppel-hash för en viss regel.  Alla paket i samma paketflöde tas emot på samma instans bakom Utjämning av nätverksbelastning-klientdel.  När klienten startar en ny flödet, från samma käll-IP, de port ändringarna av datakällan. Resulterande 5-tuppel kan orsaka trafik att gå till en annan backend-slutpunkt som ett resultat.

    Mer information finns i [belastningsdistributionsläget nätverksbelastningsutjämning](load-balancer-distribution-mode.md). Följande bild visar den hash-baserad distributionen:

    ![Hash-baserad distribution](./media/load-balancer-overview/load-balancer-distribution.png)

    *Bild - Hash-baserad distribution*

* **Vidarebefordrade portar**

    Azure belastningsutjämnare kan du skapa en inkommande NAT-regel till port vidarebefordra trafik från en specifik port för en specifik klientdelens IP-adress till en specifik port för en specifik backend-instans i det virtuella nätverket. Detta är också göra samma hash-baserad distribution som belastningsutjämning.  Vanliga scenarier för den här möjligheten är Remote Desktop Protocol (RDP) eller SSH (Secure Shell) sessioner till enskilda virtuella instanser i det virtuella nätverket.  Du kan mappa flera interna slutpunkter för olika portar på samma IP-adress för klientdel. Du kan använda dessa för att fjärradministrera dina virtuella datorer via Internet utan att behöva en ytterligare hopp ruta.

* **Programmet storleksoberoende och transparent**

    Belastningsutjämnaren interagerar inte direkt med TCP eller UDP eller programlager och alla TCP eller UDP-baserade program scenario kan stödjas.  Exempelvis medan belastningsutjämnaren inte avslutar TLS själva kan du skapa och skala ut TLS-program med hjälp av belastningsutjämnare och avsluta TLS-anslutning på den virtuella datorn sig själv. Belastningsutjämnaren Avsluta inte ett flöde och protokollet handskakningar på alltid direkt mellan klienten och valda hash backend pool-instans. En TCP-handskakning är exempelvis alltid mellan klienten och valda backend virtuella datorn.  Ett svar på en begäran om att en klientdel är ett svar som skapas från den virtuella datorn för serverdelen.  Belastningsutjämnarens utgående nätverkets prestanda är endast begränsas av den virtuella datorn SKU som du väljer och flöden ska vara aktiv under långa perioder om tidsgränsen för inaktivitet nåddes.

* **Automatisk omkonfiguration**

    Azure belastningsutjämnare konfigurerar omedelbart om sig själv när du skalar delar upp eller ned. Lägga till eller ta bort virtuella datorer från serverdelspoolen konfigurerar belastningsutjämnaren utan ytterligare åtgärder på resursen belastningsutjämnare.

* **Hälsoavsökningar**

    Azure belastningsutjämnare använder hälsoavsökningar som du definierar för att fastställa hälsotillståndet för instanserna i serverdelspoolen. När en avsökning inte svarar, stannar belastningsutjämnaren skickar nya anslutningar till feltillstånd instanser. Befintliga anslutningar påverkas inte och fortsätter tills programmet avslutar flödet, en timeout för inaktivitet inträffar, eller den virtuella datorn är avstängd.

    Tre typer av avsökningar stöds:

    - **Anpassade HTTP-avsökningen:** du kan använda för att skapa egna anpassade logik för att fastställa hälsotillståndet för en pool backend-instans. Belastningsutjämnaren ska regelbundet söka slutpunkten (var 15 sekunder, som standard). Instansen anses vara felfri om svarar den med en HTTP-200 inom tidsgränsen (standard 31 sekunder). Status än HTTP 200 gör den här avsökningen misslyckas.  Detta är också användbart för att implementera din egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan till exempel konfigurera instans för att returnera en icke-200 status om instansen är över 90% CPU.   Den här avsökningen åsidosätter standard gäst agent avsökning.

    - **Anpassade TCP-avsökning:** den här avsökningen förlitar sig om lyckade TCP-session till en definierad avsökningsport.  Så länge som den angivna lyssnaren på den virtuella datorn finns den här avsökningen kommer att lyckas. Avsökningen misslyckas om anslutningen inte godkänns. Den här avsökningen åsidosätter standard gäst agent avsökning.

    - **Gästen agent avsökningen (på plattform som en tjänst endast virtuella datorer):** belastningsutjämnaren kan också använda gästagenten på den virtuella datorn. Gästagenten lyssnar och svarar med ett HTTP-200 OK svar endast när instansen är i tillståndet redo. Om agenten inte svarar med en HTTP-200 OK, markerar instansen som inte svarar belastningsutjämnaren och stoppar skickar trafik till den instansen. Belastningsutjämnaren fortsätter att försöka att nå instansen. Om gästagenten svarar med en HTTP-200, skickar belastningsutjämnaren trafik till instansen igen.  Gästen agent avsökningar är en sista utväg och ska inte användas när HTTP- eller TCP anpassad avsökningsåtgärd konfigurationer är möjliga. 
    
* **Utgående anslutningar (källa NAT)**

    Alla utgående flöden från privata IP-adresser i det virtuella nätverket till den offentliga IP-adresser på Internet kan översättas till ett klientdelens IP-adressen för belastningsutjämnaren. När en offentlig klientdels är knuten till en backend virtuell dator med en regel för belastningsutjämning, Azure-program utgående anslutningar som automatiskt ska översättas till offentlig klientdels-IP-adress. Detta kallas också källa NAT (SNAT). SNAT ger viktiga fördelar:

    + Enkel uppgradering och katastrofåterställning återställning för tjänster, kan eftersom klientdelen dynamiskt kan mappas till en annan instans av tjänsten.
    + Det underlättar åtkomstkontrollistan (ACL) för åtkomstkontrollhantering. ACL: er som uttrycks i klientdelens IP-adresser inte ändras när tjänsterna skala upp, ned eller hämta omdistribueras.

    Referera till [utgående anslutningar](load-balancer-outbound-connections.md) artikel för en detaljerad beskrivning av den här möjligheten.

Standard belastningsutjämnaren har ytterligare SKU-specifik utökade funktioner utöver dessa grunderna.  Granska resten av den här artikeln för information.

## <a name="skus"></a> Läsa in belastningsutjämning SKU jämförelse

Azure belastningsutjämnare stöder två olika SKU: er: Basic och Standard.  Det finns skillnader i scenariot skala, funktioner och priser.  Scenarier med grundläggande belastningsutjämnare kan skapas med Standard belastningsutjämnare samt.  API: er för båda SKU: er finns i själva verket liknande och anropade via specificering av en SKU.  API för att stödja SKU: er för belastningsutjämnaren och offentliga IP-Adressen är tillgängliga från och med 2017-08-01-API.  Båda SKU: er har samma allmänna API och struktur.

Beroende på vilket SKU är valt, kan konfigurationsdetaljer för hela scenariot dock variera. I dokumentationen till belastningsutjämnaren anropar när det gäller en artikel till en specifik SKU. Granska följande tabellen nedan för att jämföra och förstå skillnaderna.  Granska [Standard översikt över belastningsutjämnare belastningen](load-balancer-standard-overview.md) för mer information.

>[!NOTE]
> Nya designer ska använda Standard belastningsutjämnare. 

Fristående virtuella datorer, tillgänglighetsuppsättningar och skalningsuppsättningar i virtuella datorer kan bara vara ansluten till en SKU aldrig båda. När det används med offentliga IP-adresser, både belastningsutjämnare och offentliga IP-adressen SKU måste överensstämma. Belastningsutjämnare och offentliga IP-SKU: er är inte föränderliga.

_Det är bäst att ange de SKU: er explicit, även om det inte är ännu obligatoriskt._  För tillfället hålls nödvändiga ändringar till ett minimum. Om en SKU inte anges, tolkas det som avser att använda grundläggande SKU i 2017-08-01 API-versionen.

>[!IMPORTANT]
>Standard belastningsutjämnare är en ny produkt för belastningsutjämnaren och i stort sett inbegriper grundläggande belastningsutjämnaren.  Det finns viktiga och avsiktlig skillnader mellan båda produkterna.  Slutpunkt till slutpunkt scenarier kan användas med grundläggande belastningsutjämnare kan skapas med Standard belastningsutjämnare.  Om du redan är för grundläggande belastningsutjämnaren bör du bekanta dig med Standard belastningsutjämnaren att förstå viktiga förändringar i beteende mellan Standard och Basic och deras effekt. Granska det här avsnittet noggrant.

| | [Standard-SKU](load-balancer-standard-overview.md) | Grundläggande SKU |
| --- | --- | --- |
| Storlek för backend-pool | upp till 1 000 instanser | upp till 100 instanser|
| Backend-pool-slutpunkter | En virtuell dator i ett enda virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar virtuella datorn anger. | virtuella datorer i en enda tillgänglighet eller en virtuell dator skala |
| Tillgänglighetszoner | Zonredundant och zonal frontends för inkommande och utgående, utgående flöden mappningar klara zonen fel, cross-zon belastningsutjämning | / |
| Diagnostik | Övervakare för Azure flerdimensionella mått, t.ex. byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutningshälsa (SNAT lyckade och misslyckade flöden), aktiva data plan mått | Azure logganalys för offentliga belastningsutjämnare, SNAT uttömning avisering, backend poolen hälsa antal |
| Hög tillgänglighet portar | Interna belastningsutjämnare | / |
| Som standard | offentliga IP- och belastningsutjämnare slutpunkter alltid som standard stängs, säkerhetsgrupp för nätverk som används för att uttryckligen godkända | Standardvärden öppnas nätverkssäkerhetsgruppen som är valfritt |
| Utgående anslutningar | Flera frontends per regel CEIP. Associering av en virtuell dator med en utgående adress _måste_ skapas explicit.  Detta omfattar att anslutningen till andra Azure-PaaS-tjänster eller [VNet Tjänsteslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) måste användas. Utgående anslutningar via standard SNAT är inte tillgängliga när bara en intern belastningsutjämnare används av en virtuell dator. | Enskild klientdel. Standard SNAT används när endast interna belastningsutjämnare används av en virtuell dator |
| Flera frontends | Inkommande och utgående | Endast inkommande |
| Åtgärder | De flesta åtgärder < 30 sekunder | 60-90 sekunder vanliga |
| SLA | 99,99% för datasökväg med två felfri virtuella datorer | Implicit i VM SLA | 
| Prissättning | Debiteras baserat på antalet regler bearbetade data inkommande eller utgående som hör till resursen  | Utan kostnad |

Granska [tjänsten gränser för belastningsutjämnaren](https://aka.ms/lblimits).  Standard belastningsutjämnaren också granska för en mer detaljerad [översikt](load-balancer-standard-overview.md), [priser](https://aka.ms/lbpricing), och [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Begrepp

### <a name = "publicloadbalancer"></a>Offentlig belastningsutjämnare

Offentliga belastningsutjämnare mappar den offentliga IP-adressen och portnumret för inkommande trafik till den privata IP-adressen och portnummer för den virtuella datorn och vice versa för svarstrafik från den virtuella datorn. Belastningsutjämningsregler kan du distribuera specifika typer av trafik mellan flera virtuella datorer eller tjänster. Sprida belastningen på begäran Internet-trafik över flera webbservrar.

Följande bild visar en belastningsutjämnad slutpunkt för webbtrafik som delas mellan tre virtuella datorer för den offentliga och privata TCP-porten 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![exempel på offentliga belastningsutjämnare](./media/load-balancer-overview/IC727496.png)

**Bild 1: Internet-trafik med hjälp av en offentlig belastningsutjämnare för belastningsutjämning**

När Internet-klienter skickar förfrågningar för webbsidan till offentliga IP-adressen för ett webbprogram på TCP-port 80, distribuerar Azure belastningsutjämnare begäranden mellan tre virtuella datorer i den belastningsutjämnade uppsättningen. Mer information om belastningen belastningsutjämnaren algoritmer finns i [belastningen belastningsutjämnaren översiktssidan](load-balancer-overview.md#load-balancer-features).

Som standard distribuerar Azure belastningsutjämnare nätverkstrafik jämnt mellan flera virtuella datorer. Du kan också konfigurera tillhörighet för sessionen. Mer information finns i [belastningsdistributionsläget nätverksbelastningsutjämning](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Interna belastningsutjämnare

Interna belastningsutjämnare dirigerar endast trafik till resurser som inte ingår i ett virtuellt nätverk eller som använder en VPN-anslutning för att få åtkomst till Azure-infrastrukturen. I detta avseende interna belastningsutjämnare skiljer sig från en offentlig belastningsutjämnare. Azure-infrastrukturen begränsar åtkomsten till Utjämning av nätverksbelastning klientdelens IP-adresser i ett virtuellt nätverk. Frontend-IP-adresser och virtuella nätverk exponeras aldrig direkt till en internet-slutpunkt. Interna line-of-business-program körs i Azure och kan nås från Azure eller från lokala resurser.

Interna belastningsutjämnare kan följande typer av nätverksbelastning:

* Ett virtuellt nätverk: belastningsutjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer i samma virtuella nätverk.
* För ett virtuellt nätverk mellan platser: belastningsutjämning från lokala datorer till en uppsättning virtuella datorer i samma virtuella nätverk. 
* För program på flera nivåer: belastningsutjämning för internet-riktade flera nivåer program där backend-nivåer inte är mot internet. Backend-nivåer kräver trafik belastningsutjämning från internet-riktade tjänstnivån (se figur 2).
* För line-of-business-program: belastningsutjämning för line-of-business-program som finns i Azure utan ytterligare belastningen belastningsutjämnaren maskinvara eller programvara. Det här scenariot innehåller lokala servrar som finns i en uppsättning datorer vars trafik Utjämning av nätverksbelastning.

![exempel på interna belastningsutjämnare](./media/load-balancer-overview/IC744147.png)

**Bild 2 - program på flera nivåer med hjälp av både offentliga och interna belastningsutjämnare för belastningsutjämning**

## <a name="pricing"></a>Prissättning
Standard belastningsutjämnare är en produkt som debiteras baserat på antalet belastningsutjämningsregler konfigurerad och alla inkommande och utgående data bearbetas. Standard belastningsutjämnaren information om priser finns i [belastningen belastningsutjämnaren priser](https://azure.microsoft.com/pricing/details/load-balancer/) sidan.

Grundläggande belastningsutjämnaren erbjuds utan kostnad.

## <a name="sla"></a>SLA

Information om Standard Load Balancer SLA finns i [belastningen belastningsutjämnaren SLA](https://aka.ms/lbsla) sidan. 

## <a name="next-steps"></a>Nästa steg

- Granska [Standard belastningsutjämnare i detalj](load-balancer-standard-overview.md)
- Lär dig mer om hur du använder [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md)
- Lär dig mer om hur du använder [belastningsutjämnaren för utgående anslutningar](load-balancer-outbound-connections.md)
- Lär dig mer om [läsa in belastningsutjämning portar för hög tillgänglighet](load-balancer-ha-ports-overview.md)
- Lär dig mer om hur du använder [belastningsutjämnaren med flera Frontends](load-balancer-multivip-overview.md)
- Lär dig mer om [VNet-tjänstens slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)
- Lär dig hur du skapar en [grundläggande offentlig belastningsutjämnare](load-balancer-get-started-internet-portal.md)

