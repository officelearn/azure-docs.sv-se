---
title: "Designa din nätverksinfrastruktur för katastrofåterställning | Microsoft Docs"
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
ms.date: 07/20/2017
ms.author: pratshar
ms.openlocfilehash: 90ffd3dd1cf5068359afa1b60892cdee43ec0658
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="designing-your-network-for-disaster-recovery"></a>Utforma nätverket för katastrofåterställning

Den här artikeln omdirigeras till IT-tekniker som ansvarar för att bygga, implementera, och stöd för affärskontinuitet och disaster recovery (BCDR)-infrastruktur och som vill utnyttja Microsoft Azure Site Systemåterställning (ASR) för att stödja och förbättra deras BCDR-tjänster. Den här artikeln beskrivs praktiska överväganden i strukturera nätverk på återställningsplatsen för katastrofåterställning, är det Azure eller en annan lokal plats. 

## <a name="overview"></a>Översikt
[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) är en Microsoft Azure-tjänst som samordnar skydd och återställning av din virtualiserade program för affärsändamål kontinuitet disaster recovery (BCDR). Det här dokumentet är avsett att hjälpa läsaren genom processen med att skapa nätverk fokusera på att bygga om IP-adressintervall och undernät på webbplatsen disaster recovery vid replikering av virtuella datorer (VM) med Site Recovery.

Dessutom visar den här artikeln hur Site Recovery kan utforma och implementera en multisite virtuella datacenter för att stödja BCDR tjänster när test och katastrofåterställning.

I en värld där alla förväntar sig 24/7 anslutning, är det mer viktig än någonsin kan klara din infrastruktur och dina program och körs. Syftet med Business Continuity and Disaster Recovery BCDR-är att återställa misslyckade komponenter så att organisationen kan snabbt återuppta normal drift. Utveckla strategi för katastrofåterställning hantera osannolikt, förödande händelser är väldigt svårt. Detta beror på inbyggd svårigheten att förutsäga framtida, särskilt när det gäller osannolika händelser och höga kostnader för att tillhandahålla lämpliga åtgärder för skydd mot omfattande catastrophes.

Avgörande för BCDR planering, återställning tid mål för Återställningstid och återställning punkt mål (RPO) måste definieras som en del av en plan för katastrofåterställning. När en katastrof inträffar kundens datacenter med hjälp av Azure Site Recovery kunder kan snabbt (låga RTO) Anslut de replikerade virtuella datorer som finns i den sekundära Datacenter eller i Microsoft Azure med minimal dataförlust (låg RPO).

Redundans möjliggörs med ASR som från början kopierar avsedda virtuella datorer från primära Datacenter till ett sekundärt Datacenter eller till Azure (beroende på scenario) och regelbundet uppdaterar replikerna. Under infrastrukturplanering, bör nätverksdesign betraktas som potentiell flaskhals som kan förhindra att du från möte företag RTO och Återställningspunktmål mål.  

När administratörer planerar att distribuera en lösning för katastrofåterställning, är en av de viktiga frågorna i minne hur den virtuella datorn skulle kunna nås när redundansväxlingen är klar. ASR kan administratören välja nätverket som en virtuell dator skulle anslutas till efter redundansväxlingen. Om den primära platsen är Azure eller också är en lokal plats som hanteras av en VMM-server och sedan detta uppnås med hjälp av nätverksmappning. Lär dig mer om [nätverksmappning i Azure till Azure DR](site-recovery-network-mapping-azure-to-azure.md) och [nätverksmappning från VMM](site-recovery-network-mapping.md)


När du utformar nätverket för återställningsplatsen har administratören två alternativ:

* Använd en annan IP-adressintervall för nätverk på återställningsplatsen. I det här fallet den virtuella datorn efter redundans får en ny IP-adress och administratören måste göra en DNS-uppdatering. 
* Använd samma IP-adressintervall för nätverk på återställningsplatsen. I vissa fall administratörer som vill behålla IP-adresser som de har på den primära platsen även efter växling vid fel. I ett vanligt scenario måste en administratör uppdatera vägar för att ange en ny plats för IP-adresser. Men i scenario där ett sträckta undernät distribueras mellan den primära servern och recovery platser behålla IP-adresser för virtuella datorer blir ett bra alternativ. Det går inte att sträcka ut ett undernät från ett lokalt nätverk till ett Azure-nätverk eller mellan två Azure-nätverk.  

När administratörer planerar att distribuera en lösning för katastrofåterställning, är en av de viktiga frågorna i sig hur program som kan nås när redundansväxlingen är klar. Moderna program är nästan alltid beroende nätverk till viss mån så fysiskt flytta en tjänst från en plats till en annan representerar en nätverk utmaning. Det finns två huvudsakliga sätt som det här problemet behandlas i lösningar för katastrofåterställning. Det första tillvägagångssättet är att hantera den fasta IP-adresser. Trots de tjänster som flyttar och värdservrar som på olika fysiska platser, ta program IP-adresskonfiguration med dem till den nya platsen. Det andra sättet är helt ändra IP-adress under övergången till den återställda platsen. Varje metod har flera varianter av implementering som sammanfattas nedan.

När du utformar nätverket för återställningsplatsen har administratören två alternativ:

## <a name="option-1-retain-ip-addresses"></a>Alternativ 1: Behåll IP-adresser
Med fasta IP-adresser verkar vara det enklaste sättet att implementera ur disaster recovery process, men den har ett antal möjliga utmaningar som i praktiken gör det minst populär metod. Azure Site Recovery tillhandahåller möjligheten att behålla IP-adresser i alla scenarier. Innan en beslutar att behålla IP lämpliga bör du tänka på begränsningarna inför på funktioner för redundans. Låt oss titta på de faktorer som kan hjälpa dig att fatta ett beslut att behålla IP-adresser, eller inte. Detta kan ske på två sätt med hjälp av utsträckta undernät eller genom att göra en fullständig undernät växling vid fel.

### <a name="stretched-subnet"></a>Ambitiöst undernät
Här görs undernätet tillgängliga samtidigt i både primär och DR-platser. Detta innebär i enkla villkor kan du flytta en server och dess konfiguration för IP (nivå 3) till den andra platsen och nätverket ska vidarebefordra trafiken till den nya platsen automatiskt. Detta är enkelt att hantera ur server men den har ett antal utmaningar:

* Det kräver nätverksutrustning som kan hantera ett ambitiöst VLAN ur Lager2 (data-länk lager), men det har blivit mindre problem som den är allmänt tillgänglig. Andra och svårare problemet är att genom att dra ut VLAN potentiella feldomänen utökas till båda platserna i stort sett blir en enskild felpunkt. Även om detta är ett ovanligt inträffat att broadcast storm startats men det gick inte att isolerade. Vi har hittat blandat åsikter om problemet senaste och har sett många lyckade implementeringar samt ”vi kommer aldrig att implementera den här tekniken här”.
* Det går inte att sträckta undernät om du använder Microsoft Azure som DR-plats.

### <a name="subnet-failover"></a>Undernät växling vid fel
Det är möjligt att implementera undernät redundansen för att få fördelarna med sträckta undernät lösningen ovan utan att sträcka ut undernätet på flera platser. Här skulle några angivet undernät finnas på plats 1 eller 2 för platsen, men aldrig på båda platser samtidigt. För att upprätthålla IP-adressutrymme vid en växling vid fel är det möjligt att ordna programmässigt router-infrastruktur att flytta undernäten från en plats till en annan. Skyddade virtuella datorer i ett failover-scenario undernäten flyttas med den associerade. Den huvudsakliga Nackdelen med denna metod är om du behöver flytta hela undernät, vilket kan vara OK men kan det påverka redundans granularitet överväganden.

Låt oss nu undersöka hur ett fiktivt företag med namnet Contoso kan replikera dess virtuella datorer till en återställningsplats när misslyckas över hela undernätet. Först beskrivs hur Contoso kan hantera sina undernät medan replikera virtuella datorer mellan två lokala platser och sedan diskuteras hur undernät redundans fungerar när [Azure används som webbplatsen disaster recovery](#failover-to-azure).

#### <a name="failover-from-on-premises-to-azure"></a>Växling från lokal till Azure 
Azure Site Recovery (ASR) kan Azure som ska användas som en återställningsplats för katastrofåterställning för dina virtuella datorer.  

Låt oss nu undersöka ett scenario där fiktiva företaget Woodgrove Bank har lokal infrastruktur som är värd för sina verksamhetsspecifika program och de är värd för sin mobila program på Azure. Anslutningen mellan Woodgrove Bank virtuella datorer i Azure och lokala servrar som en plats-till-plats (S2S) virtuella privata nätverk (VPN) eller ExpressRoute. S2S VPN kan Woodgrove Bank virtuellt nätverk i Azure för att ses som ett tillägg för Woodgrove Bank lokalt nätverk. Den här kommunikationen aktiveras av S2S VPN mellan Woodgrove Bank kant och virtuella Azure-nätverket. Nu vill Woodgrove använda ASR för att replikera dess arbetsbelastningar som körs primära Azure-region till en annan Azure-region. Det här alternativet uppfyller behoven hos Woodgrove som vill ha ett ekonomiska DR-alternativ och kan lagra data i offentliga molnmiljöer. Woodgrove har att hantera program och konfigurationer som är beroende av hårdkodade IP-adresser, så de har ett krav att behålla IP-adresser för sina program efter ett misslyckande till en annan region i Azure.

Woodgrove beslutat att tilldela IP-adresser från IP-adressintervall (172.16.1.0/24, 172.16.2.0/24) till dess resurser som körs i Azure.

Ett Azure Virtual Network måste skapas för Woodgrove för att kunna replikera en virtuell dator till Azure samtidigt som IP-adresser. Det bör vara ett tillägg till det lokala nätverket så att program kan växling från den lokala platsen till Azure sömlöst. Azure kan du lägga till plats-till-plats samt punkt-till-plats VPN-anslutning i virtuella nätverk som skapats i Azure. När du konfigurerar din plats-till-plats-anslutning, kan Azure-nätverk du dirigera trafik till den lokala platsen (Azure anropar den lokala nätverk) bara om IP-adressintervallet skiljer sig från det lokala IP-adressintervallet eftersom Azure inte stöder sträcka ut undernät.  Det innebär att om du har ett undernät 192.168.1.0/24 lokala, det går inte att lägga till 192.168.1.0/24 ett lokalt nätverk i Azure-nätverk. Detta är förväntat eftersom Azure inte vet att det inte finns några aktiva virtuella datorer i undernät och undernätet skapas endast för Katastrofåterställning. För att kunna dirigera nätverkstrafik utanför ett Azure-nätverk inte vara i konflikt undernät i nätverket och det lokala nätverket korrekt.

![Innan du undernät redundans](./media/site-recovery-network-design/network-design7.png)

Innan du redundans

För att uppfylla affärskrav Woodgrove, behöver vi implementera följande arbetsflöden:

* Skapa ytterligare ett nätverk, låt oss anropa Recovery nätverk, där de virtuella datorerna misslyckades över skulle skapas.
* För att säkerställa att IP-Adressen för en virtuell dator finns kvar efter en växling vid fel, gå till fliken Konfigurera under VM egenskaper ange samma IP-Adressen som den virtuella datorn har en lokal och klicka sedan på Spara. När den virtuella datorn har redundansväxlats tilldelar Azure Site Recovery den angivna IP-Adressen till den virtuella datorn.

![Egenskaper för nätverk](./media/site-recovery-network-design/network-design8.png)

När växling vid fel utlöses och virtuella datorer skapas i nätverkets återställning med önskad IP-anslutning till det här nätverket kan upprättas med hjälp av en [Vnet Vnet-anslutningen till](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Om det krävs för den här åtgärden kan skriptas.  Enligt beskrivningen i föregående avsnitt om undernätet redundans även vid redundans till Azure skulle vägar behöva ändras på lämpligt sätt för att återspegla den 192.168.1.0/24 har nu flyttats till Azure.

![Efter undernät redundans](./media/site-recovery-network-design/network-design9.png)

Efter växling vid fel

Om du inte har ett Azure nätverk som visas i bilden ovan. Du kan skapa en plats-till-plats-vpn-anslutning mellan 'Primära platsen' och 'Recovery nätverket' efter växling vid fel.  


#### <a name="failover-to-a-secondary-on-premises-site"></a>Växling till en sekundär lokal plats
Låt oss se ett scenario där vi vill behålla IP-Adressen för var och en av de virtuella datorerna och failover fullständig undernätet tillsammans. Den primära platsen har program som körs i undernätet 192.168.1.0/24. När redundansväxlingen sker alla virtuella datorer som ingår i det här undernätet kommer att flyttas över till återställningsplatsen och behålla sina IP-adresser. Vägar måste ändras på lämpligt sätt om du vill visa alla virtuella datorer som hör till 192.168.1.0/24 undernät har nu flyttats till återställningsplatsen.

I följande illustration behöver vägar mellan primära platsen och återställningsplatsen, tredje plats och primär plats och tredje plats och återställningsplatsen ändras på lämpligt sätt.

Följande bilder visar undernät innan växling vid fel. Undernät 192.168.0.1/24 är aktiv på den primära platsen före växlingen vid fel och aktiveras av återställningsplatsen efter växling vid fel

![Innan du redundans](./media/site-recovery-network-design/network-design2.png)

Innan du redundans

Bilden nedan visar nätverk och undernät efter växling vid fel.

![Efter växling vid fel](./media/site-recovery-network-design/network-design3.png)

Efter växling vid fel

På den sekundära platsen är lokalt och du använder en VMM-server för att hantera den sedan när du aktiverar skyddet för en specifik virtuell dator allokeras ASR nätverksresurser enligt följande arbetsflöde:

* ASR tilldelar en IP-adress för varje nätverksgränssnitt på den virtuella datorn från den statiska IP-adresspool som definierats i relevanta nätverket för varje System Center VMM-instans.
* Om administratören definierar samma IP-adresspool för nätverket på återställningsplatsen med IP-adresspool för nätverk på den primära platsen, när tilldelning av IP-adress till den virtuella replikdatorn ASR skulle tilldela samma IP adress som den den primära virtuella datorn.  IP-Adressen är reserverad i VMM men har angetts inte som redundans IP på Hyper-v-värden. Failover IP på en Hyper-v-värden anges innan växling vid fel.


Om samma IP-Adressen inte är tillgänglig, skulle ASR allokera vissa andra tillgänglig IP-adress från den angivna IP-adresspoolen.

När den virtuella datorn har aktiverats för skydd som du kan använda följande exempelskript för att verifiera IP-adress som har allokerats till den virtuella datorn. Samma IP-Adressen skulle ange Failover IP och tilldelas den virtuella datorn vid tidpunkten för växling vid fel:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> I scenariot där virtuella datorer använda DHCP, är hantering av IP-adresser helt utanför kontroll av ASR. En administratör måste se till att du kan hantera DHCP-servern hanterar IP-adresser på återställningsplatsen från samma intervall som den primära platsen.
>
>



## <a name="option-2-changing-ip-addresses"></a>Alternativ 2: Ändra IP-adresser
Den här metoden verkar vara den vanligaste baserat på vad vi sett. Det tar formuläret för att ändra IP-adressen för varje virtuell dator som ingår i växling vid fel. En nackdel med den här metoden kräver inkommande nätverket ' Läs ' att programmet som skedde vid IPx är nu vid IPy. Även om IPx och IPy är logiska namn, DNS-poster har vanligtvis ändras eller rensade i hela nätverket, och cachelagrade poster i nätverket tabeller behöver uppdateras eller rensade, därför ett driftstopp kan ses beroende på hur DNS-infrastrukturen har installationsprogrammet. De här problemen kan begränsas med hjälp av låg TTL-värden för intranätsprogram och använder [Azure Traffic Manager med ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) för internet-baserade program

### <a name="changing-the-ip-addresses---illustration"></a>Ändra IP-adresser - bild
Låt oss se ett scenario där du planerar att använda olika IP-adresser mellan den primära servern och återställningsplatser. I följande exempel vi också ha en tredje plats från där programmen finns på primära eller återställning är webbplatsen tillgänglig.

![Olika IP - före redundans](./media/site-recovery-network-design/network-design10.png)


I bilden ovan är vissa program som finns i undernät 192.168.1.0/24 undernät på den primära platsen och de har konfigurerats för att komma på återställningsplatsen i undernätet 172.16.1.0/24 efter en redundansväxling. VPN-anslutningar/nätverksvägar har konfigurerats på rätt sätt så att alla tre platser som har åtkomst till varandra.

De kommer att återställas i recovery undernät för som på bilden nedan visas efter ett eller flera program inte körs. I det här fallet är vi inte begränsade till redundans på hela subnätet på samma gång. Inga ändringar krävs för att konfigurera om VPN- eller nätverksvägar. En växling vid fel och DNS-uppdateringar ska kontrollera att program fortfarande är tillgänglig. Om DNS-servern är konfigurerad för att tillåta dynamiska uppdateringar kan sedan de virtuella datorerna registrera sig själva med hjälp av den nya IP när de startar efter en redundansväxling.

![Olika IP - efter växling vid fel](./media/site-recovery-network-design/network-design11.png)


Efter över misslyckas kanske den virtuella replikdatorn en IP-adress som inte är samma som IP-adressen för den primära virtuella datorn. Virtuella datorer kommer att uppdatera DNS-servern som de använder när de startar. DNS-poster har vanligtvis ändras eller rensade i hela nätverket och cachelagrade poster i nätverket tabeller behöver uppdateras eller rensade, så det inte är ovanligt att drabbas av driftstopp medan de här ändringarna äga rum. Det här problemet kan begränsas med:

* Med hjälp av låg TTL-värden för intranätsprogram.
* Med ASR Azure Traffic Manager för internet-baserade program.
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

### <a name="changing-the-ip-addresses--dr-to-azure"></a>Ändra IP-adresser – DR till Azure
Den [nätverk Infrastrukturinställningar för Microsoft Azure som en plats för Disaster Recovery](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) blogginlägget beskriver hur du installationen krävs Azure nätverksinfrastrukturen när behåller IP-adresser är inte ett krav. Den börjar med som beskriver programmet och titta på hur du ställer in nätverk lokalt och på Azure och sedan sluta med hur du gör ett redundanstest och en planerad redundans.

## <a name="next-steps"></a>Nästa steg
[Läs](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) hur Site Recovery mappar käll- och nätverk när en VMM-server används för att hantera den primära platsen.
