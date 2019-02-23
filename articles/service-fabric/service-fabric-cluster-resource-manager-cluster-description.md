---
title: Klusterbeskrivning för cluster Resource Manager | Microsoft Docs
description: Beskriva ett Service Fabric-kluster genom att ange Feldomäner, uppgradera domäner, nodegenskaper och nodkapaciteterna för Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7be10f03d65e53b51c3916849dc12feb4de9c919
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737662"
---
# <a name="describing-a-service-fabric-cluster"></a>Som beskriver ett service fabric-kluster
Service Fabric Cluster Resource Manager tillhandahåller flera mekanismer för att beskriva ett kluster. Under körning använder den här informationen i Cluster Resource Manager för att garantera hög tillgänglighet för de tjänster som körs i klustret. Samtidigt framtvinga dessa viktiga regler, försöker den också optimera resursförbrukning i klustret.

## <a name="key-concepts"></a>Viktiga begrepp
Cluster Resource Manager stöder flera funktioner som beskriver ett kluster:

* Feldomäner
* Upgrade Domains
* Nodegenskaper
* Nodkapaciteterna

## <a name="fault-domains"></a>Feldomäner
En Feldomän är alla delar av samordnad fel. En enda dator är en Feldomän (eftersom det kan misslyckas på en egen för olika anledningar, av strömavbrott tillgång till enhetsfel till felaktig NIC firmware). Datorer som är anslutna till samma Ethernet-växel finns i samma Feldomän, som är datorer som delar en enda källa ström eller på en enda plats. Eftersom det är naturligt för maskinvarufel att överlappa Feldomäner är sin natur hierarkisk och visas i form av URI: er i Service Fabric.

Det är viktigt att Feldomäner har ställts in korrekt eftersom Service Fabric använder denna information för att placera tjänster på ett säkert sätt. Service Fabric vill inte placera tjänster så att förlust av en Feldomän (orsakas av fel på vissa komponent) gör att en tjänst att gå nedåt. I Azure använder Service Fabric-miljön Feldomän informationen i miljön till korrekt konfigurera noderna i klustret för din räkning. För Service Fabric fristående definieras Feldomäner när klustret har ställts in 

> [!WARNING]
> Det är viktigt att Feldomän informationen till Service Fabric är korrekt. Anta exempelvis att att noderna i ditt Service Fabric-kluster körs inom 10 virtuella datorer som körs på fem fysiska värdar. I det här fallet, även om det finns 10 virtuella datorer, det finns endast 5 olika (toppnivå) feldomäner. Delar samma fysiska värd gör att virtuella datorer för att dela samma rot feldomänen, eftersom de virtuella datorerna uppleva samordnad fel om deras fysiska värd skulle sluta fungera.  
>
> Service Fabric förväntar sig en nod inte att ändra Feldomänen. Andra metoder för att säkerställa hög tillgänglighet för de virtuella datorerna som [hög tillgänglighet för virtuella datorer](https://technet.microsoft.com/library/cc967323.aspx) kan orsaka konflikter med Service Fabric, eftersom de använder transparent migrering av virtuella datorer från en värd till en annan. Dessa mekanismer inte konfigurera om eller meddela köra kod på den virtuella datorn. Därför måste de är **stöds inte** som miljöer för att köra Service Fabric-kluster. Service Fabric ska bara hög tillgänglighet-tekniken används. Mekanismer som Direktmigrering av Virtuella SAN-nätverk eller andra är inte nödvändigt. Om används tillsammans med Service Fabric kan dessa mekanismer _minska_ programmets tillgänglighet och tillförlitlighet eftersom medför de ytterligare komplexitet, lägga till centraliserade källor till felet och använda tillförlitlighet och strategier för tillgänglighet som står i konflikt med dessa i Service Fabric. 
>
>

Vi bakgrundsfärg alla entiteter som bidrar till Feldomäner och listar alla de olika Feldomäner som resulterar i bilden nedan. I det här exemplet har vi datacenter (”DC”), rack (”R”) och blad (”B”). Möjligen, om varje bladet innehåller mer än en virtuell dator, kan det vara en annan nivå i hierarkin Feldomän.

<center>
![Noder som är ordnade via Feldomäner][Image1]
</center>

Under körning, Service Fabric Cluster Resource Manager tar hänsyn till Feldomäner i klustret och planer layouter. Tillståndskänsliga repliker eller tillståndslösa instanser för en viss tjänst distribueras så att de är i separata Feldomäner. Distribuera tjänsten över feldomäner garanterar tillgängligheten för tjänsten inte komprometteras när en Feldomän misslyckas på vilken nivå i hierarkin.

Service Fabric Cluster Resource Manager hand inte hur många lager som finns i hierarkin Feldomän. Men försöker det se till att förlusten av en delar av hierarkin inte påverka tjänster som körs i den. 

Det är bäst om det är samma antal noder på varje nivå i detalj i hierarkin Feldomän. Om ”trädet” feldomäner är Obalanserat i ditt kluster, gör det svårare för Cluster Resource Manager för att ta reda på den rekommenderade allokeringen av tjänster. Imbalanced Feldomäner layouter innebär att förlust av vissa domäner inverkan tillgängligheten för tjänster som är mer än andra domäner. Klusterresurshanteraren är därför torn mellan två mål: Företaget vill använda datorerna i domänen ”tung” genom att placera tjänster på dem och företaget vill placera tjänster i andra domäner så att förlust av en domän inte orsakar problem. 

Hur ser imbalanced domäner ut? I diagrammet nedan visar vi två olika kluster layouterna. I det första exemplet jämnt noderna över Feldomänerna. I det andra exemplet har en Feldomän fler noder än de andra Feldomänerna. 

<center>
![Två olika kluster layouterna][Image2]
</center>

I Azure hanteras välja vilket Feldomän innehåller en nod åt dig. Men, beroende på antalet noder som du etablerar du kan fortfarande få Feldomäner med flera noder i dem än andra. Anta exempelvis att du har fem Feldomäner i klustret men etablera sju noder för en viss NodeType. I det här fallet få de första två Feldomänerna fler noder. Om du fortsätter att distribuera flera NodeTypes med bara ett par instanser hämtar problemet sämre. Av den anledningen rekommenderar vi som antalet noder i varje nod är typen en multipel av antalet Feldomäner.

## <a name="upgrade-domains"></a>Uppgradera domäner
Uppgraderingsdomäner är en annan funktion som hjälper till att Service Fabric Cluster Resource Manager förstå layouten för klustret. Uppgraderingsdomäner definiera uppsättningar med noder som uppgraderas samtidigt. Uppgraderingsdomäner hjälpa Cluster Resource Manager förstå och dirigera hanteringsåtgärder som uppgraderingar.

Uppgraderingsdomäner är mycket som Feldomäner, men med några viktiga skillnader. Först definiera delar av samordnad maskinvarufel Feldomäner. Uppgradera domäner, å andra sidan definieras av principen. Du får du bestämma hur många, i stället för det som anges i miljön. Du kan ha så många uppgradera domäner som du gör noder. En annan skillnaden mellan Feldomäner och uppgradera domäner är att uppgradera domäner inte är hierarkiska. De är däremot mer som en enkel tagg. 

I följande diagram visas tre uppgradera domäner stripe över tre Feldomäner. Den visar även en möjlig placering för tre olika repliker av en tillståndskänslig tjänst, där var och en identisk i olika fel- och uppgradera domäner. Den placeringen kan förlusten av en Feldomän i mitten av en uppgradering av tjänsten och fortfarande har en kopia av kod och data.  

<center>
![Placering med fel- och Uppgraderingsdomäner][Image3]
</center>

Det finns för- och nackdelar till att stora mängder uppgradera domäner. Fler uppgradera domäner innebär att varje steg i uppgraderingen är mer detaljerad och påverkar därför ett mindre antal noder eller tjänster. Därför färre tjänster har att flytta samtidigt, introduktion till mindre omsättning i systemet. Detta tenderar att förbättra tillförlitligheten, eftersom mindre tjänsten påverkas av eventuella problem som introducerats under uppgraderingen. Fler uppgradera domäner innebär också att du behöver mindre tillgängliga buffert på andra noder för att hantera effekten av uppgraderingen. Till exempel om du har fem uppgradera domäner, hanterar noderna i var och en ungefär 20% av din trafik. Om du vill ta bort Uppgraderingsdomänen för en uppgradering behövs vanligtvis att belastningen och sedan en plats. Eftersom du har fyra återstående uppgradera domäner, måste var och en har utrymme för cirka 5% av den totala trafiken. Fler uppgradera domäner innebär att du behöver mindre buffert på noderna i klustret. Anta exempelvis att om du har haft 10 uppgradera domäner i stället. Varje UD hantering i så fall bara cirka 10% av den totala trafiken. När en uppgradering i klustret, behöver bara varje domän har utrymme för ungefär 1.1% av den totala trafiken. Fler uppgradera domäner Allmänt kan du köra dina noder vid högre användning, eftersom du behöver mindre reserverad kapacitet. Detsamma gäller för Feldomäner.  

Nackdelen med att ha många uppgradera domäner är att uppgraderingar tenderar att ta längre tid. Service Fabric väntar en kort tidsperiod när en uppgradera domän har slutförts och utför kontroller innan du börjar uppgradera nästa. Dessa fördröjningar kan upptäcka problem som introducerades av uppgraderingen innan uppgraderingen fortsätter. Nackdelen är acceptabelt eftersom den förhindrar att felaktiga ändringar påverkar för mycket av tjänsten i taget.

För få uppgradera domäner har många negativt sidoeffekter – även om varje enskild Uppgraderingsdomänen är igång och håller på att uppgraderas en stor del av den totala kapaciteten är inte tillgänglig. Exempel: Om du bara har tre uppgradera domäner du vidtar av cirka 1/3 av din övergripande tjänst eller klusterkapacitet i taget. Med så mycket av din tjänst nedåt på en gång inte önskvärt eftersom du måste ha tillräckligt med kapacitet i resten av klustret för att hantera arbetsbelastningen. Underhålla det buffert innebär att under normal drift är dessa noder mindre lästs in än de skulle vara annars. Detta ökar kostnaden för att köra din tjänst.

Det finns ingen verklig gräns för totala antalet fel eller uppgradera domäner i en miljö eller begränsningar för hur de överlappar varandra. Men det finns flera vanliga mönster:

- Feldomäner och uppgradera domäner mappas 1:1
- En Uppgraderingsdomänen per nod (fysisk eller virtuell OS instans)
- En ”stripe” eller ”en” modell där Feldomäner och uppgradera domäner utgör en matris med datorer som vanligtvis körs ned de diagonala linjer

<center>
![Fel- och layouter för Uppgraderingsdomän][Image4]
</center>

Det finns inga bästa besvara vilken layout du vill välja, var och en har vissa- och nackdelar. Till exempel är 1FD:1UD modellen enkelt att konfigurera. 1 Uppgraderingsdomänen per nod modell är de flesta som vilka personer som används för att. Under uppgraderingar uppdateras varje nod oberoende av varandra. Detta liknar hur små uppsättningar datorer har uppgraderats manuellt i förflutna. 

Den vanligaste modellen är FD/UD matrisen där FD och ud utgör en tabell och noder placeras från och med diagonalt. Det här är den modell som används som standard i Service Fabric-kluster i Azure. Allt identisk ser ut som mönstret kompakta matris för kluster med många noder.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Fel- och Uppgraderingsdomänen begränsningar och resulterande beteende
### <a name="default-approach"></a>*Standard-metod*
Som standard sparas Klusterresurshanteraren tjänster balanserade över fel- och uppgradera domäner. Detta är utformat som en [begränsningen](service-fabric-cluster-resource-manager-management-integration.md). Fel- och Uppgraderingsdomänen begränsningen status: ”För en viss tjänst-partition det ska aldrig vara någon skillnad större än ett av antalet service-objekt (tillståndslös tjänstinstanser eller tillståndskänslig tjänst repliker) mellan två domäner på samma nivå i hierarkin”. Vi antar att det här villkoret erbjuder en garanti för ”högsta skillnaden”. Fel- och Uppgraderingsdomänen begränsningen förhindrar vissa flyttar eller arrangemang som bryter mot regeln som anges ovan. 

Nu ska vi titta på ett exempel. Anta att vi har ett kluster med sex noder som konfigurerats med fem Feldomäner och fem uppgradera domäner.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfiguration 1*

Nu anta att vi skapar en tjänst med en TargetReplicaSetSize (eller för en tillståndslös tjänst en InstanceCount) med fem medlemmar. Replikerna hamnar på N1 N5. I själva verket används N6 aldrig oavsett hur många tjänster så här skapar du. Men varför? Låt oss titta på skillnaden mellan den aktuella layouten och vad som skulle hända om N6 väljs.

Här visas layouten vi och det totala antalet repliker per fel- och uppgradera domän:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 1*


Den här layouten belastningsutjämnad när det gäller noder per Feldomän och Uppgraderingsdomänen. Det är också belastningsutjämnad vad gäller antalet repliker per fel- och Uppgraderingsdomänen. Varje domän har samma antal noder och samma antal repliker.

Nu ska vi titta på vad som skulle hända om vi hade använt N6 i stället för N2. Hur skulle medan replikerna distribueras sedan?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Layout 2*


Den här layouten strider mot vår definitionen för ”högsta skillnaden”-garantin för begränsningen Feldomän. FD0 har två repliker, medan FD1 är noll, göra skillnaden mellan FD0 och FD1 totalt två, vilket är större än den maximala skillnaden mellan en. Eftersom villkoret överskrids, tillåter inte den här ordningen i Cluster Resource Manager. På samma sätt om vi valt N2 och N6 (i stället för N1 och N2) skulle vi få:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 3*


Den här layouten belastningsutjämnad när det gäller Feldomäner. Men nu den bryter mot villkoret Uppgraderingsdomänen eftersom UD0 har noll repliker medan UD1 har två. Därför kan den här layouten också är ogiltigt och hämtas inte av Cluster Resource Manager.

Den här metoden rör distribution av tillståndskänsliga repliker eller tillståndslösa instanser ger bästa möjliga feltolerans. I en situation när en domän som stängs av, förloras minsta antal repliker/instanser. 

Den här metoden kan å andra sidan är för begränsade och inte att klustret ska kunna använda alla resurser. Vissa noder kan inte användas för vissa konfigurationer med klustret. Detta kan leda till Service Fabric inte placera dina tjänster, vilket resulterar i varningsmeddelanden. Vissa av klusternoderna kan inte vara i det föregående exemplet används (N6 i angivna exempel). Även om du vill lägga till noder i klustret (N7 – N10), skulle bara repliker/instanser placeras på N1 – N5 på grund av fel- och Uppgraderingsdomänen. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfiguration 2*


### <a name="alternative-approach"></a>*Annan metod*

Klusterresurshanteraren har stöd för en annan version av villkoret fel- och Uppgraderingsdomänen där placering vid fortfarande, vilket ger en lägsta nivå av säkerhet. Alternativa fel- och Uppgraderingsdomänen begränsningen kan anges på följande sätt: ”För en viss tjänst-partition repliken fördelningen mellan domäner bör se till att partitionen inte har drabbats en förlorar kvorum”. Vi antar att det här villkoret erbjuder en garanti för ”kvorum säker”. 

> [!NOTE]
>En tillståndskänslig tjänst definierar vi *förlorar kvorum* i en situation när en majoritet av partitionsrepliker är nere på samma gång. Om TargetReplicaSetSize är fem, representerar en uppsättning alla tre repliker kvorum. På samma sätt, om TargetReplicaSetSize 6, fyra repliker krävs för kvorum. I båda fallen kan högst två repliker vara otillgängliga samtidigt om partitionen vill fortsätta att fungera normalt. Det finns ingenting som heter för en tillståndslös tjänst *förlorar kvorum* tillståndslösa tjänster fortsätter att fungera normalt även om de flesta fall gå på samma gång. Därför ska vi fokusera på tillståndskänsliga tjänster i resten av texten.
>

Vi går tillbaka till föregående exempel. Med ”kvorum säker”-versionen av begränsningen är alla tre angivna layouter giltig. Det beror på att även om det skulle vara fel på FD0 i andra layout eller UD1 i den tredje layouten, partitionen fortfarande skulle ha kvorum (en majoritet av dess repliker blir fortfarande upp). Med den här begränsningen kan nästan alltid N6 användas.

”Kvorum säker”-metoden ger bättre flexibilitet än metoden som ”högsta skillnaden” eftersom det är lättare att hitta repliken distributioner som är giltiga i nästan alla klustertopologi. Men den här metoden kan inte garantera bästa felet tolerans egenskaper eftersom vissa fel är sämre resultat än andra. I värsta fall scenario kan en majoritet av repliker gå förlorade med fel på en domän och en ytterligare replik. Till exempel i stället för 3 fel måste förlorar kvorum med 5 repliker eller instanser, kan du nu gå en majoritet med bara två fel. 

### <a name="adaptive-approach"></a>*Anpassningsbar metod*
Eftersom både av metoder har styrkor och svagheter, har vi lanserat en anpassningsbar metod som kombinerar dessa två olika metoder.

> [!NOTE]
>Det här är standardbeteendet från och med Service Fabric Version 6.2. 
>
Anpassningsbar metoden använder ”högsta skillnaden” logiken som standard och växlar till ”kvorum säker” logiken vid behov. Klusterresurshanteraren räknat automatiskt ut vilken strategi krävs genom att titta på hur klustret och tjänster har konfigurerats. För en viss tjänst: *Om TargetReplicaSetSize är jämnt delbart med antalet Feldomäner och antalet uppgradera domäner **och** antalet noder som är mindre än eller lika med (antalet Feldomäner) * (antal uppgradera domäner), klustret Resource Manager ska använda ”kvorum baserat” logiken för tjänsten.* Ha i åtanke att Cluster Resource Manager kommer att använda den här metoden för både tillståndslösa och tillståndskänsliga tjänster, trots förlorar kvorum som inte är relevanta för tillståndslösa tjänster.

Vi går tillbaka till föregående exempel och antar att ett kluster nu har 8 noder (klustret fortfarande är konfigurerad med fem Feldomäner och fem uppgradera domäner och TargetReplicaSetSize av en tjänst som finns i de kluster förblir fem). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfiguration 3*

Eftersom alla nödvändiga villkor är uppfyllda, ska Cluster Resource Manager använda ”kvorum baserad” logiken i distribuera tjänsten. På så sätt kan användningen av N6 – N8. En möjliga service-distribution i det här fallet kan se ut:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Layout 4*

Om din tjänst TargetReplicaSetSize minskar till fyra (till exempel) Observera som ändrar Cluster Resource Manager och fortsätta att använda ”högsta skillnaden” logiken eftersom TargetReplicaSetSize inte är delbara med antalet FD och ud längre. Därför kan inträffar vissa repliken förflyttningar för att distribuera återstående fyra repliker på noderna N1 N5 så att ”högsta skillnaden”-versionen av domänlogiken Feldomän och uppgradering inte har överskridits. 

Ser tillbaka till den fjärde layouten och TargetReplicaSetSize med fem medlemmar. Antalet uppgradera domäner blir lika med fyra om N1 tas bort från klustret. Cluster Resource Manager startar igen, med ”högsta skillnaden”-logik som antalet ud inte jämnt dela upp tjänstens TargetReplicaSetSize längre. Replica R1, när du bygger igen och har därför hamnar på N4 så att fel- och uppgradera domänbegränsningar inte har överskridits.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |Gäller inte |Saknas |Saknas |Saknas |Saknas |Gäller inte |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurera fel- och uppgradera domäner
Definiera Feldomäner och uppgradera domäner görs automatiskt i Azure-värdbaserade Service Fabric-distributioner. Service Fabric hämtar och använder miljöinformation från Azure.

Om du skapar ett eget kluster (eller vill köra en viss topologi i utveckling), kan du ange information i Feldomän och Uppgraderingsdomänen själv. I det här exemplet definierar vi ett lokalt utvecklingskluster för nio noden som sträcker sig över tre ”datacenter” (var och en med tre rack). Det här klustret har också tre uppgradera domäner stripe över dessa tre datacenter. Det är ett exempel av konfigurationen nedan: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

via ClusterConfig.json för fristående distributioner

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> När du definierar kluster via Azure Resource Manager måste tilldelas Feldomäner och uppgradera domäner av Azure. Definitionen av din nodtyper och VM-Skalningsuppsättningar i Azure Resource Manager-mallen innehåller därför inte Feldomän eller uppgradera domäninformation.
>

## <a name="node-properties-and-placement-constraints"></a>Nodegenskaper och placeringsbegränsningar
Ibland (i fakta i de flesta fall) ska du vill vara säker på att vissa arbetsbelastningar endast för vissa typer av noder i klustret. Exempelvis kan vissa arbetsbelastning kräva GPU: er eller SSD-enheter och andra inte. Ett bra exempel på riktar in sig på maskinvara för specifika arbetsbelastningar är nästan alla arkitektur på n-nivå där. Vissa datorer fungerar som klientdel eller API som betjänar sida av programmet och är exponerade mot klienter eller internet. Olika datorer, ofta med olika maskinvaruresurser kan hantera verk som tillhör lagren för beräkning eller lagring. Det här handlar vanligtvis _inte_ direkt exponerade mot klienter eller internet. Service Fabric förväntar sig att det finns fall där specifika arbetsbelastningar måste köras på specifika maskinvarukonfigurationer. Exempel:

* ett befintligt program på n-nivå har ”lyfts och beräkningsarbete” i en miljö med Service Fabric
* en arbetsbelastning vill köras på maskinvara för prestanda, skalbarhet eller isolering av säkerhetsskäl
* En arbetsbelastning klusterservrar isoleras från andra arbetsbelastningar princip- eller resursen förbrukning skäl

För att stödja dessa typer av konfigurationer, har Service Fabric en första klassens begreppet taggar som kan tillämpas på noderna. Dessa taggar kallas **nodegenskaper**. **Placeringsbegränsningar** är de instruktioner som är kopplade till enskilda tjänster väljer för en eller flera egenskaper för noden. Placeringsbegränsningar definiera där tjänsterna ska köras. Uppsättningen med begränsningar kan utökas – alla nyckel/värde-par fungerar. 

<center>
![Olika arbetsbelastningar för Layout av kluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Inbyggda nodegenskaper
Service Fabric definierar några standardegenskaper för noden som kan användas automatiskt utan att användaren behöver definiera dem. Standardegenskaperna som definierats vid varje nod är den **NodeType** och **nodnamn**. Exempelvis så du kan skriva en placering begränsning som `"(NodeType == NodeType03)"`. Vi har Allmänt sett NodeType ska vara en av mest ofta de egenskaper som används. Det är användbart eftersom det motsvarar en typ av en dator 1:1. Varje typ av dator motsvarar en typ av arbetsbelastning i ett traditionellt n-nivåprogram.

<center>
![Placeringsbegränsningar och nodegenskaper][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Begränsningen för placering och noden egenskapssyntax 
Värdet som anges i egenskapen nod kan vara en sträng, bool, eller signerats länge. Instruktionen på tjänsten kallas en placering *begränsningen* eftersom den begränsar där tjänsten kan köras i klustret. Begränsningen kan vara valfri booleskt instruktionen som körs med egenskaperna annan nod i klustret. Giltigt väljare i dessa booleskt uttryck är:

1) villkorliga kontroller för att skapa specifika instruktioner

| Meddelande | Syntax |
| --- |:---:|
| ”lika med” | "==" |
| ”inte lika med” | "!=" |
| ”större än” | ">" |
| ”större än eller lika med” | ">=" |
| ”mindre än” | "<" |
| ”mindre än eller lika med” | "<=" |

2) Booleskt uttryck för gruppering och logiska åtgärder

| Meddelande | Syntax |
| --- |:---:|
| ”och” | "&&" |
| ”eller” | "&#124;&#124;" |
| ”not” | "!" |
| ”gruppen som enskild instruktion” | "()" |

Här följer några exempel på grundläggande begränsning instruktioner.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Endast noder där den övergripande placering begränsning instruktionen utvärderas till ”True” kan ha tjänsten placeras på den. Noder som inte har en definierad egenskap matchar inte någon placering begränsningen som innehåller egenskapen.

Anta att följande noden egenskaper har definierats för en viss nodtyp:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

värdbaserad kluster via ClusterConfig.json för distribution av fristående eller Template.json för Azure. 

> [!NOTE]
> Nodtyp är vanligtvis parametriserade i Azure Resource Manager-mallen. Det skulle se ut som ”[parameters('vmNodeType1Name')]” i stället för ”NodeType01”.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Du kan skapa tjänsteplacering *begränsningar* för en tjänst som på följande sätt:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Om alla noder i NodeType01 är giltiga, du kan också välja den nodtypen med begränsningen ”(NodeType == NodeType01)”.

En av de bästa sakerna placeringsbegränsningar för en tjänst är att de kan uppdateras dynamiskt vid körning. Så om du vill kan kan du runt en tjänst i klustret, lägga till och ta bort krav och så vidare. Service Fabric hand tar om att säkerställa att tjänsten är igång och tillgängliga även om dessa typer av ändringar görs.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Placeringsbegränsningar har angetts för varje olika namngiven tjänst-instans. Uppdateringar alltid ersätta (Skriv över) vad som tidigare har angetts.

Klusterdefinitionen definierar egenskaper för på en nod. Ändra egenskaper för en nod kräver en uppgradering för konfiguration av klustret. Uppgradera en nodegenskaper kräver alla berörda noder startas om för att rapportera dess nya egenskaper. Dessa löpande uppgraderingar hanteras av Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Som beskriver och hantera klusterresurser
En av de viktigaste jobben eventuella orchestrator är för att hantera resurser som används i klustret. Hantera klusterresurser kan innebära ett antal olika saker. Först, det är att säkerställa att datorer inte är överbelastad. Det innebär att se till att datorer inte kör fler tjänster än de kan hantera. Det finns andra belastningsutjämning och optimering som är viktigt att köra tjänster effektivt. Cost effective eller prestanda känsliga service-erbjudanden kan inte tillåter att vissa noder är medan andra är kalla. Frekvent noder leda till resurskonkurrens och dålig prestanda och kalla noder representerar oanvänt resurser och ökade kostnader. 

Service Fabric representerar resurser som `Metrics`. Mått är en logisk eller fysisk resurs som du vill beskriva till Service Fabric. Exempel på mått är till exempel ”WorkQueueDepth” eller ”MemoryInMb”. Information om de fysiska resurserna som Service Fabric kan styra på noderna finns i [resursstyrning](service-fabric-resource-governance.md). Information om hur du konfigurerar anpassade mått och hur används finns i [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)

Mått skiljer sig från placeringar begränsningarna och nodegenskaper. Nodegenskaper är statiska beskrivningar av noderna. Mått beskrivs resurser som noder har och att tjänsterna ska använda när de körs på en nod. En nodegenskap kan vara ”HasSSD” och kan anges till true eller false. Mängden tillgängligt utrymme på den SSD och hur mycket som används av tjänster är ett mått som ”DriveSpaceInMb”. 

Det är viktigt att Observera att precis som för placeringsbegränsningar och nodegenskaper kan Service Fabric Cluster Resource Manager inte förstår namnen på mått som innebär. Tjänstmåttets namn är bara strängar. Det är en bra idé att deklarera enheter som en del av tjänstmåttets namn som du skapar när det kan vara tvetydiga.

## <a name="capacity"></a>Kapacitet
Om du har inaktiverat alla *belastningsutjämning*, Service Fabric Cluster Resource Manager skulle fortfarande se till att ingen nod avslutades över sin kapacitet. Det är möjligt att hantera kapacitet överskridanden såvida inte klustret är full eller arbetsbelastningen är större än en nod. Kapaciteten är en annan *begränsningen* att Cluster Resource Manager använder för att förstå hur mycket av en resurs för en nod har. Återstående kapacitet spåras också för klustret som helhet. Både den aktuella kapaciteten och förbrukningen på tjänstnivå uttrycks i mått. Till exempel måttet kan vara ”ClientConnections” och en viss nod kan ha en kapacitet för ”ClientConnections” på 32768. Andra noder kan ha andra begränsningar för vissa-tjänsten körs på den noden kan säga att det för närvarande förbrukar 32256 för mått ”ClientConnections”.

Under körning spårar Klusterresurshanteraren återstående kapacitet i klustret och på noder. För att kunna spåra kapacitet subtraherar Cluster Resource Manager varje tjänst användning från nodens kapacitet där tjänsten körs. Service Fabric Cluster Resource Manager kan ta reda på var du vill placera eller flytta repliker så att noderna inte går över kapacitet med den här informationen.

<center>
![Klusternoder och kapacitet][Image7]
</center>

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Du kan se de kapaciteter som definierats i klustermanifestet:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

värdbaserad kluster via ClusterConfig.json för distribution av fristående eller Template.json för Azure. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Vanligtvis en tjänst att läsa in ändringar dynamiskt. Säg att en replik belastningen på ”ClientConnections” ändras från 1024 till 2048, men noden den kördes på sedan bara behövde 512 kapacitet kvar för att mått. Nu den repliken eller instansens placering är ogiltig, eftersom det inte finns tillräckligt med utrymme på noden. Klusterresurshanteraren har att sätta och få noden under kapacitet. Det minskar belastningen på den nod som är överbelastade genom att flytta en eller flera av repliker eller instanser från noden till andra noder. När du flyttar repliker försöker Cluster Resource Manager minimera kostnaderna för dessa förflyttningar. Kostnader för tjänsteflytt diskuteras i [i den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md) och mer om Cluster Resource Manager ombalansering strategier och regler beskrivs [här](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Klusterkapacitet
Hur håller övergripande klustret inte är full av Service Fabric Cluster Resource Manager? Med dynamisk belastning som det inte finns mycket du kan göra. Tjänster kan ha sina belastningen topp oberoende av åtgärder som vidtas av Cluster Resource Manager. Ditt kluster med gott om plats i dag kan därför vara underpowered när du blir berömda imorgon. Men det finns vissa kontroller som är inbyggd i att förhindra problem. Det första vi kan göra är att förhindra skapandet av nya arbetsbelastningar som innebär att klustret ska bli fullständig.

Anta att du skapar en tillståndslös tjänst och den har vissa belastning som är kopplade till den. Anta att tjänsten värnar om måttet ”DiskSpaceInMb”. Även anta att den kommer att använda fem enheter av ”DiskSpaceInMb” för varje instans av tjänsten. Du vill skapa tre instanser av tjänsten. Bra! Så att innebär det att vi behöver 15 enheter av ”DiskSpaceInMb” måste finnas i klustret för att vi kan även kunna skapa dessa instanser av tjänsten. Cluster Resource Manager beräknar kontinuerligt kapacitet och förbrukning av varje mått, så den kan fastställa återstående kapacitet i klustret. Om det inte finns tillräckligt med utrymme, avvisar Cluster Resource Manager skapa tjänst-anrop.

Eftersom kravet är endast att det inte finnas 15 enheter, kan det här utrymmet tilldelas många olika sätt. Exempelvis kan uppstå det en enhet för återstående kapacitet på 15 olika noder eller tre återstående enheter med kapacitet på fem olika noder. Om Cluster Resource Manager kan ändra saker så att det finns fem enheter på tre noder, placerar tjänsten. Om du ordnar om klustret är vanligtvis möjligt om inte klustret är nästan full eller befintliga tjänster går inte att konsolidera av någon anledning.

## <a name="buffered-capacity"></a>Buffrade kapacitet
Buffrade kapacitet är en annan funktion för Cluster Resource Manager. Det gör reservation av en del av den totala kapaciteten för noden. Denna kapacitet buffert används endast för att placera tjänster under uppgraderingar och nodfel. Buffrade kapacitet anges globalt per mått för alla noder. Värdet som du väljer för reserverad kapacitet är en funktion av antalet fel och uppgradera domäner som du har i klustret. Flera fel- och uppgradera domäner innebär att du kan välja ett lägre värde för din buffrade kapacitet. Om du har flera domäner, kan du förvänta sig mindre mängder klustret för att vara otillgänglig under uppgraderingar och fel. Ange buffras kapacitet bara bra om du har angett nodens kapacitet för ett mått.

Här är ett exempel på hur du anger buffrade kapacitet:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Det går inte att skapa nya tjänster när klustret är buffrade kapacitet för ett mått. Det gick inte att skapa nya tjänster att bevara bufferten säkerställer att uppgraderingar och fel inte orsakar noder gå igenom kapacitet. Buffrade kapacitet är valfritt men rekommenderas i alla kluster som definierar en kapacitet för ett mått.

Klusterresurshanteraren visar den här informationen för belastningen. För varje mått innehåller den här informationen: 
  - buffrade kapacitetsinställningarna
  - total kapacitet
  - aktuella förbrukningen
  - om varje mått anses belastningsutjämnade eller inte
  - statistik om standardavvikelsen
  - noder som har mest och minst belastning  
  
Nedan kan vi se ett exempel på dessa utdata:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Nästa steg
* Kolla in information om arkitektur- och informationsflödet i Cluster Resource Manager [den här artikeln ](service-fabric-cluster-resource-manager-architecture.md)
* Definiera defragmentering mått är ett sätt att konsolidera belastningen på noder i stället för sprida. Läs hur du konfigurerar defragmentering [i den här artikeln](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret kan du läsa artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
