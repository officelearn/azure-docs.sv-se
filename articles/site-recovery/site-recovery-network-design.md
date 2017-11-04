---
title: "Utforma din nätverksinfrastruktur för katastrofåterställning med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskrivs nätverksdesign för Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: jwhit
editor: 
ms.assetid: ce787731-d930-4f00-a309-e2fbc2e1f53b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: pratshar
ms.openlocfilehash: 5b6fb7bac852b29663866e99758241bd5a7ab59e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="designing-your-network-for-disaster-recovery"></a>Utforma nätverket för katastrofåterställning

Den här artikeln beskrivs Nätverksöverväganden när du använder [Azure Site Recovery](site-recovery-overview.md) för katastrofåterställning återställning från lokal till Azure eller till en sekundär lokal plats. Den fokuserar på hur du definierar IP-adressintervall och undernät efter växling till en sekundär plats.

## <a name="overview"></a>Översikt

Site Recovery är en Microsoft Azure-tjänst som samordnar skydd och återställning av virtualiserade program för katastrofåterställning för verksamhetskontinuitet (BCDR).

I en värld av 24/7 anslutning är det viktigt att hålla dina infrastrukturer och program och körs. Syftet med BCDR är att återställa misslyckade komponenter, så att din organisation kan snabbt återupptas normal drift. Utveckla strategi för katastrofåterställning hantera osannolikt händelser är väldigt svårt. Detta beror på inbyggd svårigheten att förutsäga framtida, särskilt för osannolika händelser. Och på grund av hög kostnaden för att upprätthålla lämpliga åtgärder för skydd mot omfattande catastrophes.

Avgörande för planering av BCDR, en återställning tid mål för Återställningstid och återställning punkt mål (RPO) måste definieras i din BCDR-plan. När en katastrof inträffar ett datacenter, kan du snabbt (låga RTO) Anslut de replikerade virtuella datorer som finns i den sekundära Datacenter eller i Microsoft Azure med minimal dataförlust (låg RPO).

Redundans möjliggörs med Site Recovery som från början kopierar avsedda virtuella datorer från primära Datacenter till ett sekundärt Datacenter eller till Azure (beroende på scenario) och regelbundet uppdaterar replikerna. Under infrastrukturplanering, bör nätverksdesign betraktas som potentiell flaskhals som kan förhindra att du från möte företag RTO och Återställningspunktmål mål.  

När administratörer planerar att distribuera en lösning för katastrofåterställning, är en av de viktiga frågorna i minne hur den virtuella datorn skulle kunna nås när redundansväxlingen är klar. Site Recovery kan administratören välja nätverket som en virtuell dator skulle anslutas till efter redundansväxlingen. Om den primära platsen är Azure eller om det är en lokal plats som hanteras av en VMM-server, är detta uppnås genom att använda nätverksmappning. Lär dig mer om [nätverksmappning i Azure till Azure DR](site-recovery-network-mapping-azure-to-azure.md) och [nätverksmappning från VMM](site-recovery-network-mapping.md)


När du utformar nätverket för återställningsplatsen har administratören två alternativ:

* Använd en annan IP-adressintervall för nätverk på återställningsplatsen. I det här fallet den virtuella datorn efter redundans får en ny IP-adress och administratören måste göra en DNS-uppdatering. 
* Använd samma IP-adressintervall för nätverk på återställningsplatsen. I vissa fall administratörer som vill behålla IP-adresser som de har på den primära platsen även efter växling vid fel. I ett vanligt scenario måste en administratör uppdatera vägar för att ange en ny plats för IP-adresser. Men i scenario där ett sträckta undernät distribueras mellan den primära servern och recovery platser behålla IP-adresser för virtuella datorer blir ett bra alternativ. Det går inte att sträcka ut ett undernät från ett lokalt nätverk till ett Azure-nätverk eller mellan två Azure-nätverk.  

När administratörer planerar att distribuera en lösning för katastrofåterställning, är en av de viktiga frågorna i sig hur program som kan nås när redundansväxlingen är klar. Moderna program är nästan alltid beroende nätverk till viss mån så fysiskt flytta en tjänst från en plats till en annan representerar en nätverk utmaning. Det finns två huvudsakliga sätt som det här problemet behandlas i lösningar för katastrofåterställning. Det första tillvägagångssättet är att hantera den fasta IP-adresser. Trots de tjänster som flyttar och värdservrar som på olika fysiska platser, ta program IP-adresskonfiguration med dem till den nya platsen. Det andra sättet är helt ändra IP-adress under övergången till den återställda platsen. Varje metod har flera varianter av implementering som sammanfattas nedan.

När du utformar nätverket för återställningsplatsen har administratören två alternativ:

## <a name="option-1-retain-ip-addresses"></a>Alternativ 1: Behåll IP-adresser
Med fasta IP-adresser verkar vara det enklaste sättet att implementera ur disaster recovery process, men den har ett antal möjliga utmaningar som i praktiken är det minst populär metod. Azure Site Recovery tillhandahåller möjligheten att behålla IP-adresser i alla scenarier. Innan en beslutar att behålla IP lämpliga bör du tänka på begränsningarna inför på funktioner för redundans. Låt oss titta på de faktorer som kan hjälpa dig att fatta ett beslut att behålla IP-adresser, eller inte. Detta kan ske på två sätt med hjälp av utsträckta undernät eller genom att göra en fullständig undernät växling vid fel.

### <a name="stretched-subnet"></a>Ambitiöst undernät
Här görs undernätet tillgängliga samtidigt i både primär och DR-platser. Detta innebär i enkla villkor kan du flytta en server och dess konfiguration för IP (nivå 3) till den andra platsen och nätverket ska vidarebefordra trafiken till den nya platsen automatiskt. Detta är enkelt att hantera ur server men den har ett antal utmaningar:

* Det kräver nätverksutrustning som kan hantera ett ambitiöst VLAN ur Lager2 (data-länk lager), men det har blivit mindre problem som den är allmänt tillgänglig. Andra och svårare problemet är att genom att dra ut VLAN potentiella feldomänen utökas till båda platserna i stort sett blir en enskild felpunkt. Även om detta är ett ovanligt inträffat att broadcast storm startats men det gick inte att isolerade. Vi har hittat blandat åsikter om problemet senaste och har sett många lyckade implementeringar samt ”vi inte implementera den här tekniken här”.
* Det går inte att sträckta undernät om du använder Microsoft Azure som DR-plats.

### <a name="subnet-failover"></a>Undernät växling vid fel
Det är möjligt att implementera undernät redundansen för att få fördelarna med sträckta undernät lösningen ovan utan att sträcka ut undernätet på flera platser. Här skulle några angivet undernät finnas på plats 1 eller 2 för platsen, men aldrig på båda platser samtidigt. För att upprätthålla IP-adressutrymme vid en växling vid fel är det möjligt att ordna programmässigt router-infrastruktur att flytta undernäten från en plats till en annan. Skyddade virtuella datorer i ett failover-scenario undernäten flyttas med den associerade. Den huvudsakliga Nackdelen med denna metod är om du behöver flytta hela undernätet. Detta kan bero på OK, men det kan påverka granularitet för växling vid fel.

Låt oss nu undersöka hur ett fiktivt företag med namnet Contoso kan replikera dess virtuella datorer till en återställningsplats när misslyckas över hela undernätet. Nu ska vi se hur Contoso kan hantera sina undernät vid replikering av virtuella datorer mellan två lokala platser och sedan diskuterar vi hur undernät redundans fungerar när [Azure används som webbplatsen disaster recovery](#failover-to-azure).

#### <a name="fail-over-from-on-premises-to-azure"></a>Växla över från lokal till Azure 
Azure Site Recovery kan Azure som ska användas som en återställningsplats för katastrofåterställning för dina virtuella datorer.  

Låt oss nu undersöka ett scenario där fiktiva företaget Woodgrove Bank har lokal infrastruktur som är värd för sina line-of-business-program och de är värd för sin mobila program på Azure. Anslutningen mellan Woodgrove Bank virtuella datorer i Azure och lokala servrar som en plats-till-plats (S2S) virtuella privata nätverk (VPN) eller ExpressRoute. Plats-till-plats-VPN kan Woodgrove Bank virtuellt nätverk i Azure för att ses som ett tillägg för Woodgrove Bank lokalt nätverk. Den här kommunikationen aktiveras av plats-till-plats VPN mellan Woodgrove Bank kant och virtuella Azure-nätverket. Nu vill Woodgrove använda Site Recovery replikera dess arbetsbelastningar som körs primära Azure-region till en annan Azure-region. Det här alternativet uppfyller behoven hos Woodgrove som vill ha ett ekonomiska DR-alternativ och kan lagra data i offentliga molnmiljöer. Woodgrove har att hantera program och konfiguration, som beror på hårdkodade IP-adresser, och därför har ett krav att behålla IP-adresser för sina program efter ett misslyckande till en annan region i Azure.

Woodgrove beslutat att tilldela IP-adresser från IP-adressintervall (172.16.1.0/24, 172.16.2.0/24) till dess resurser som körs i Azure.

Ett Azure Virtual Network måste skapas för Woodgrove för att kunna replikera en virtuell dator till Azure samtidigt som IP-adresser. Det bör vara ett tillägg till det lokala nätverket så att program kan växla över från den lokala platsen till Azure sömlöst. Azure kan du lägga till plats-till-plats samt punkt-till-plats VPN-anslutning i virtuella nätverk som skapats i Azure. När du konfigurerar din plats-till-plats-anslutning, kan Azure-nätverk du dirigera trafik till den lokala platsen (Azure anropar den lokala nätverk) bara om IP-adressintervallet skiljer sig från det lokala IP-adressintervallet eftersom Azure inte stöder sträcka ut undernät.  Det innebär att om du har ett undernät 192.168.1.0/24 lokala, det går inte att lägga till 192.168.1.0/24 ett lokalt nätverk i Azure-nätverk. Detta är förväntat eftersom Azure inte vet att det inte finns några aktiva virtuella datorer i undernät och undernätet skapas endast för Katastrofåterställning. För att kunna dirigera nätverkstrafik utanför ett Azure-nätverk inte vara i konflikt undernät i nätverket och det lokala nätverket korrekt.

![Innan du undernät redundans](./media/site-recovery-network-design/network-design7.png)

Innan du redundans

För att uppfylla affärskrav Woodgrove, behöver vi implementera följande arbetsflöden:

* Skapa ytterligare ett nätverk, låt oss anropa Recovery nätverk, där de virtuella datorerna misslyckades över skulle skapas.
* För att säkerställa att IP-Adressen för en virtuell dator finns kvar efter en växling vid fel, gå till fliken Konfigurera under VM egenskaper ange samma IP-Adressen som den virtuella datorn har en lokal och klicka sedan på Spara. När den virtuella datorn har redundansväxlats tilldelar Azure Site Recovery den angivna IP-Adressen till den virtuella datorn.

![Egenskaper för nätverk](./media/site-recovery-network-design/network-design8.png)

När växling vid fel utlöses och virtuella datorer skapas i nätverkets återställning med önskad IP-anslutning till det här nätverket kan upprättas med hjälp av en [Vnet Vnet-anslutningen till](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Om det krävs för den här åtgärden kan skriptas.  Enligt beskrivningen i föregående avsnitt om undernätet redundans även vid redundans till Azure skulle vägar behöva ändras på lämpligt sätt för att återspegla den 192.168.1.0/24 har nu flyttats till Azure.

![Efter undernät redundans](./media/site-recovery-network-design/network-design9.png)

Efter växling vid fel

Om du inte har ett Azure nätverk som visas i bilden ovan. Du kan skapa en plats-till-plats VPN-anslutning mellan 'Primära platsen' och 'Recovery nätverket' efter växling vid fel.  


#### <a name="fail-over-to-a-secondary-on-premises-site"></a>Växla över till en sekundär lokal plats
Låt oss se ett scenario där vi vill behålla IP-Adressen för var och en av de virtuella datorerna och failover fullständig undernätet tillsammans. Den primära platsen har program som körs i undernätet 192.168.1.0/24. När redundansväxlingen sker alla virtuella datorer som ingår i det här undernätet kommer att flyttas över till återställningsplatsen och behålla sina IP-adresser. Vägar måste ändras på lämpligt sätt om du vill visa alla virtuella datorer som hör till 192.168.1.0/24 undernät har nu flyttats till återställningsplatsen.

I följande illustration behöver vägar mellan primära platsen och återställningsplatsen, tredje plats och primär plats och tredje plats och återställningsplatsen ändras på lämpligt sätt.

Följande bilder visar undernät innan växling vid fel. Undernät 192.168.0.1/24 är aktiv på den primära platsen före växlingen vid fel och aktiveras av återställningsplatsen efter växling vid fel

![Innan du redundans](./media/site-recovery-network-design/network-design2.png)

Innan du redundans

Bilden nedan visar nätverk och undernät efter växling vid fel.

![Efter växling vid fel](./media/site-recovery-network-design/network-design3.png)

Efter växling vid fel

Om din sekundära plats är lokalt, och du använder en VMM-server för att hantera den, sedan när du aktiverar skyddet för en specifik virtuell dator, allokeras Site Recovery nätverksresurser enligt följande arbetsflöde:

* Site Recovery tilldelar en IP-adress för varje nätverksgränssnitt på den virtuella datorn från den statiska IP-adresspool som definierats i relevanta nätverket för varje System Center VMM-instans.
* Om administratören definierar samma IP-adresspool för nätverket på återställningsplatsen med IP-adresspool för nätverk på den primära platsen när IP-adress till den replikerade virtuella datorn, skulle Site Recovery tilldela samma IP-adressen som som den primära virtuella datorn.  IP-adressen är reserverad i VMM, men anges inte som IP-adress för redundans i Hyper-v-värden. IP-adress för redundans i Hyper-v-värd anges innan växling vid fel.


Om samma IP-adress inte är tillgänglig allokerar Site Recovery vissa andra tillgänglig IP-adress från den angivna IP-adresspoolen.

När den virtuella datorn har aktiverats för skydd som du kan använda följande exempelskript för att verifiera IP-adress som har allokerats till den virtuella datorn. Samma IP-Adressen skulle ange Failover IP och tilldelas den virtuella datorn vid tidpunkten för växling vid fel:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> I scenariot där virtuella datorer använda DHCP, är hantering av IP-adresser helt utanför kontroll av Site Recovery. En administratör måste se till att du kan hantera DHCP-servern hanterar IP-adresser på återställningsplatsen från samma intervall som den primära platsen.
>
>



## <a name="option-2-changing-ip-addresses"></a>Alternativ 2: Ändra IP-adresser
Den här metoden verkar vara den vanligaste baserat på vad vi sett. Det tar formuläret för att ändra IP-adressen för varje virtuell dator som ingår i växling vid fel. En nackdel med den här metoden kräver inkommande nätverket ' Läs ' att programmet som skedde vid IPx är nu vid IPy. Även om IPx och IPy är logiska namn, DNS-poster har vanligtvis ändras eller rensade i hela nätverket, och cachelagrade poster i nätverket tabeller behöver uppdateras eller rensade, därför ett driftstopp kan ses beroende på hur DNS-infrastrukturen har Ställ in. De här problemen kan begränsas med hjälp av låg TTL-värden för intranätsprogram och använder [Azure Traffic Manager med Site Recovery](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/), för internet-baserade program

### <a name="changing-the-ip-addresses---illustration"></a>Ändra IP-adresser - bild
Låt oss se ett scenario där du planerar att använda olika IP-adresser mellan den primära servern och återställningsplatser. I följande exempel vi också ha en tredje plats från där programmen finns på primära eller återställning är webbplatsen tillgänglig.

![Olika IP - före redundans](./media/site-recovery-network-design/network-design10.png)


I bilden ovan är vissa program som finns i undernät 192.168.1.0/24 undernät på den primära platsen och de har konfigurerats för att komma på återställningsplatsen i undernätet 172.16.1.0/24 efter en redundansväxling. VPN-anslutningar/nätverksvägar har konfigurerats på rätt sätt så att alla tre platser som har åtkomst till varandra.

De kommer att återställas i recovery undernät för som på bilden nedan visas efter ett eller flera program inte körs. I det här fallet är vi inte begränsade till växla över hela undernätet samtidigt. Inga ändringar krävs för att konfigurera om VPN- eller nätverksvägar. En växling vid fel och DNS-uppdateringar ska kontrollera att program fortfarande är tillgänglig. Om DNS-servern är konfigurerad för att tillåta dynamiska uppdateringar kan sedan de virtuella datorerna registrera sig själva med hjälp av den nya IP när de startar efter en redundansväxling.

![Olika IP - efter växling vid fel](./media/site-recovery-network-design/network-design11.png)


Efter över misslyckas kanske den virtuella replikdatorn en IP-adress som inte är samma som IP-adressen för den primära virtuella datorn. Virtuella datorer kommer att uppdatera DNS-servern som de använder när de startar. DNS-poster har vanligtvis ändras eller rensade i hela nätverket och cachelagrade poster i nätverket tabeller behöver uppdateras eller rensade, så det inte är ovanligt att drabbas av driftstopp medan de här ändringarna äga rum. Det här problemet kan begränsas med:

* Med hjälp av låg TTL-värden för intranätsprogram.
* Med Azure Traffic Manager med Site Recovery för internet-baserade program.
* Uppdatera DNS-servern för att säkerställa en rimlig uppdatering (skriptet inte är nödvändigt om dynamisk DNS-registrering har konfigurerats) med följande skript i din återställningsplan

        param(
        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

### <a name="changing-the-ip-addresses--disaster-recovery-to-azure"></a>Ändra IP-adresser – katastrofåterställning till Azure
Den [nätverk Infrastrukturinställningar för Microsoft Azure som en plats för Disaster Recovery](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) blogginlägget beskriver hur du ställer in den nödvändiga Azure nätverksinfrastrukturen när behåller IP-adresser är inte ett krav. Den börjar med som beskriver programmet och titta på hur du ställer in nätverk lokalt och på Azure och sedan sluta med hur du gör ett redundanstest och en planerad redundans.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [nätverksmappning](site-recovery-network-mapping.md).
