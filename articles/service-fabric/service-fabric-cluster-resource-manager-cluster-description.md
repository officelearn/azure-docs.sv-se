---
title: Beskrivning av hanteraren för filserverresurser klustret | Microsoft Docs
description: Som beskriver ett Service Fabric-kluster genom att ange Feldomäner, uppgradera domäner, nodegenskaper och kapacitet för noden för klustret Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 13ee238580d645f3e727090bc0e0275b36bdb225
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="describing-a-service-fabric-cluster"></a>Som beskriver ett service fabric-kluster
Service Fabric klustret Resource Manager tillhandahåller mekanismer för att beskriva ett kluster. Under körning använder informationen i klustret Resource Manager för att garantera hög tillgänglighet för de tjänster som körs i klustret. Medan reglerna viktigt, försöker den också Optimera resursanvändningen i klustret.

## <a name="key-concepts"></a>Viktiga begrepp
Klustret Resource Manager stöder flera funktioner som beskriver ett kluster:

* Feldomäner
* Uppgradera domäner
* Nodegenskaper
* Noden kapacitet

## <a name="fault-domains"></a>Feldomäner
En Feldomän är en del av samordnade fel. En enskild dator är en Feldomän (eftersom den kan misslyckas på en egen för olika skäl, av strömavbrott tillgång till enhetsfel till felaktig NIC firmware). Datorer som är anslutna till samma Ethernet-switch finns i samma Feldomän som är datorer som delar en enda källa ström eller på en enda plats. Eftersom det är naturligt för hårdvarufel överlappa Feldomäner är natur hierarkisk och visas i form av URI: er i Service Fabric.

Det är viktigt att Feldomäner har ställts in korrekt eftersom Service Fabric använder informationen för att på ett säkert sätt placera tjänster. Service Fabric vill inte att services så att förlust av en Feldomän (på grund av att vissa komponenter) gör att en tjänst gå nedåt. I Azure använder miljö Service Fabric Feldomän informationen i miljön för att konfigurera korrekt noderna i klustret för din räkning. För Service Fabric fristående definieras Feldomäner när klustret har konfigurerats 

> [!WARNING]
> Det är viktigt att Feldomän informationen till Service Fabric är korrekt. Anta exempelvis att att din Service Fabric klusternoder körs inom 10 virtuella datorer som körs på fem fysiska värdar. I det här fallet, även om det finns 10 virtuella datorer, det finns endast 5 olika (toppnivå) fault-domäner. Delar samma fysiska värd gör att virtuella datorer att dela samma fel rotdomänen, eftersom de virtuella datorerna får samordnade fel om inte deras fysiska värden.  
>
> Service Fabric förväntar Feldomänen för en nod inte att ändra. Andra mekanismer för att säkerställa hög tillgänglighet för de virtuella datorerna som [hög tillgänglighet för virtuella datorer](https://technet.microsoft.com/library/cc967323.aspx) kan orsaka konflikter med Service Fabric eftersom de använder transparent migrering av virtuella datorer från en värd till en annan. Dessa mekanismer inte konfigurera om eller meddela köra kod inuti den virtuella datorn. Därför är **stöds inte** som miljöer för att köra Service Fabric-kluster. Service Fabric ska bara hög tillgänglighet tekniken anställda. Mekanismer som VM Direktmigrering, SAN-nätverk eller andra behövs inte. Om används tillsammans med Service Fabric metoderna _minska_ programmets tillgänglighet och tillförlitlighet eftersom de införa ytterligare komplexitet, lägga till centraliserad källor till felet och använda tillförlitlighet och tillgänglighet strategier som står i konflikt med de i Service Fabric. 
>
>

Vi färg alla entiteter som bidrar till Feldomäner och visa en lista med alla olika Feldomäner som resulterar i bilden nedan. I det här exemplet har vi datacenter (”DC”), rack (”R”) och blad (”B”). Möjligen, om varje bladet innehåller mer än en virtuell dator, kan det finnas ett annat skikt i hierarkin Feldomän.

<center>
![Noder ordnade via Feldomäner][Image1]
</center>

Under körning, Service Fabric klustret Resource Manager anser Feldomäner i klustret och planer layouter. Tillståndskänsliga replikeringar eller tillståndslösa instanser för en viss tjänst distribueras så att de är i separata Feldomäner. Distribuera tjänsten på feldomäner garanterar tillgängligheten för tjänsten inte manipuleras när en feldomän på alla nivåer i hierarkin.

Resurshanteraren för Service Fabric-klustret hand inte hur många lager finns i hierarkin Feldomän. Dock försöker så att förlust av någon en del av hierarkin inte påverkar tjänster som körs i den. 

Det är bäst om samma antal noder på varje nivå på djupet i hierarkin Feldomän. Om Feldomäner ”trädet” Obalanserat i klustret, gör det svårare för klustret Resource Manager för att ta reda på bästa tilldelning av tjänster. Imbalanced Feldomäner layouter innebär att förlust av vissa domäner påverkan tillgängligheten för tjänster som är mer än andra domäner. Därför resurshanteraren kluster torn mellan två mål: ska användas på datorer i domänen ”tunga” genom att placera tjänster på dem och det vill placera tjänster i andra domäner så att förlust av en domän inte orsaka problem. 

Hur ser imbalanced domäner ut? I diagrammet nedan visar vi två olika kluster layouter. I det första exemplet jämnt noderna över domäner för fel. I det andra exemplet har en Feldomän fler noder än andra Feldomäner. 

<center>
![Två olika kluster layouter][Image2]
</center>

I Azure hanteras val av vilka Feldomän innehåller en nod. Men, beroende på antalet noder som du etablerar du kan fortfarande få Feldomäner med flera noder i dem än andra. Anta exempelvis att du har fem Feldomäner i klustret, men etablera sju noder för en given NodeType. I det här fallet få de två första Feldomäner fler noder. Om du fortsätter att distribuera flera nodetypes får med bara några instanser hämtar problemet försämras. Därför rekommenderar vi som antalet noder i varje nod är typen en multipel av antalet Feldomäner.

## <a name="upgrade-domains"></a>Uppgradera domäner
Uppgradera domäner är en annan funktion som hjälper Service Fabric klustret resurshanteraren förstå layouten för klustret. Uppgraderingsdomäner definiera uppsättningar av noder som uppgraderas samtidigt. Uppgraderingsdomäner med hjälp av hanteraren för filserverresurser klustret förstå och samordna hanteringsåtgärder som uppgraderingar.

Uppgradera domäner är mycket som Feldomäner, men med några viktiga skillnader. Först definierar delar av samordnade maskinvarufel Feldomäner. Uppgradera domäner definieras å andra sidan av en princip. Du får du bestämma hur många, i stället för den som anges i miljön. Du kan ha valfritt antal uppgradera domäner som du har noder. En annan skillnaden mellan Feldomäner och uppgradera domäner är att uppgradera domäner inte är hierarkiska. De är däremot mer som en enkel tagg. 

Följande diagram visar tre uppgradera domäner stripe över tre Feldomäner. Det visar också ett möjligt placering för tre olika repliker av tillståndskänsliga tjänster, där varje avslutas med olika fel- och uppgradera domäner. Den här placering kan förlust av en Feldomän i mitten av en uppgradering av tjänsten och fortfarande har en kopia av koden och data.  

<center>
![Placering med fel- och Uppgraderingsdomäner][Image3]
</center>

Det finns för- och nackdelar att ha många uppgradera domäner. Fler uppgradera domäner innebär varje steg i uppgraderingen är mer detaljerade och påverkar därför ett mindre antal noder eller tjänster. Detta innebär färre tjänsterna har flytta samtidigt, introduktion till mindre omsättning i systemet. Detta tenderar att förbättra tillförlitligheten, eftersom mindre tjänsten påverkas av eventuella problem som introduceras under uppgraderingen. Fler uppgradera domäner innebär också att du behöver mindre tillgängliga bufferten på andra noder att hantera effekten av uppgraderingen. Till exempel om du har fem uppgradera domäner noderna i varje hanterar ungefär 20 procent av trafiken. Om du behöver stänga ned uppgradera domänen för en uppgradering måste vanligtvis att belastningen gå någonstans. Eftersom du har fyra återstående uppgradera domäner måste ha plats för cirka 5% av den totala trafiken. Fler uppgradera domäner innebär att du behöver mindre buffert på noderna i klustret. Anta till exempel att om du har haft 10 uppgradera domäner i stället. Varje UD hanterar i så fall endast cirka 10% av den totala trafiken. När en uppgradering i klustret, behöver bara varje domän har plats för ca 1.1% av den totala trafiken. Vanligtvis kan fler uppgradera domäner du köra noderna med högre användning, eftersom du behöver mindre reserverad kapacitet. Detsamma gäller för Feldomäner.  

Nackdelen med att ha många uppgradera domäner är uppgraderingar brukar ta längre tid. Service Fabric väntar en kort tidsperiod när en uppgradering domän är klar och utför kontroller innan du börjar uppgradera nästa. Dessa fördröjningar kan identifiera problem som introducerades av uppgraderingen innan du fortsätter uppgraderingen. Förhållandet är acceptabelt eftersom den förhindrar att felaktiga ändringar påverkar för mycket av tjänsten i taget.

För få uppgradera domäner har många negativa sidoeffekter – när varje enskild uppgradera domän är nere och uppgraderas en stor del av den totala kapaciteten är inte tillgänglig. Till exempel om du bara har tre uppgradera domäner tar du ned ungefär 1/3 av din övergripande tjänst eller ett kluster kapacitet i taget. På samma gång med stor del av din tjänst ned inte önskvärt eftersom du måste ha tillräckligt med kapacitet i resten av klustret för att hantera arbetsbelastningen. Underhålla det buffert som innebär att under normal drift är de noderna mindre inlästa än de skulle vara annars. Detta ökar kostnaden för att köra din tjänst.

Det finns ingen verklig gräns för totala antalet fel eller uppgradera domäner i en miljö eller begränsningar för hur de överlappar varandra. Namn, det finns flera vanliga mönster:

- Feldomäner och uppgradera domäner mappas 1:1
- En uppgradering domän per nod (fysisk eller virtuell OS instans)
- En modell för ”stripe” eller ”matris” där Feldomäner och uppgradera domäner utgör en matris med datorer som körs vanligtvis ned de diagonala linjer

<center>
![Fel- och Uppgraderingsdomänen layouter][Image4]
</center>

Det finns inga bästa besvara vilken layout som du väljer, var och en har vissa- och nackdelar. Till exempel är 1FD:1UD modellen enkelt att ställa in. 1 uppgradera domänen per nod modell är de flesta som vilka personer som används för att. Under uppgraderingar uppdateras varje nod oberoende av varandra. Detta liknar hur mindre mängder datorer har uppgraderats manuellt i förflutna. 

Den vanligaste modellen är matrisen FD/UD där FDs och UDs bildar en tabell och noder placeras startar längs diagonalen. Det här är den modell som används som standard i Service Fabric-kluster i Azure. Allt slutar ser ut som ovan kompakta matris mönstret för kluster med flera noder.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Fel- och uppgradera domän begränsningar och resultatet
### <a name="default-approach"></a>*Standard-metoden*
Som standard sparas klustret Resource Manager services belastningsutjämnade över fel och uppgradera domäner. Det här modelleras som en [begränsningen](service-fabric-cluster-resource-manager-management-integration.md). Fel- och uppgradera domän begränsningen tillstånden: ”för en viss tjänst partition det ska aldrig vara skillnad större än ett i antal tjänstobjekt (tillståndslös tjänstinstanser eller tillståndskänslig service repliker) mellan två domäner på samma nivå hierarkin ”. Anta att den här begränsningen ger en garanti för ”högsta skillnaden”. Fel- och uppgradera domän begränsningen förhindrar vissa flyttar eller åtgärder som bryter mot regeln som anges ovan. 

Nu ska vi titta på ett exempel. Anta att vi har ett kluster med sex noder är konfigurerade med fem Feldomäner och fem uppgradera domäner.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfiguration 1*

Nu anta att vi skapa en tjänst med en TargetReplicaSetSize (eller för den tillståndslösa tjänsten en InstanceCount) fem. Repliker mark på N1 N5. I själva verket används N6 aldrig oavsett hur många tjänster så här du skapar. Men varför? Nu ska vi titta på skillnaden mellan den aktuella layouten och vad som skulle hända om N6 väljs.

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


Den här layouten balanseras vad gäller noder per fel och uppgradera domänen. Det är också balanserad vad gäller antal repliker per fel- och uppgradera domän. Varje domän har samma antal noder och samma antal repliker.

Nu ska vi titta på vad som skulle hända om vi hade använt N6 i stället för N2. Hur skulle replikerna att distribueras sedan?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Layout 2*


Den här layouten strider mot vår definitionen för ”högsta skillnaden” säkerheten för begränsningen Feldomän. FD0 har två repliker medan FD1 är noll, göra skillnaden mellan FD0 och FD1 totalt två, vilket är större än den största skillnaden i en. Eftersom villkoret överskrids tillåter inte den här ordningen i klustret Resource Manager. På liknande sätt om vi plockats N2 och N6 (i stället för N1 och N2) skulle vi få:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 3*


Den här layouten balanseras vad gäller Feldomäner. Men nu den bryter mot begränsningen uppgraderingen domän eftersom UD0 har noll repliker medan UD1 har två. Därför måste den här layouten också är ogiltigt och registreras inte av klustret Resource Manager.

Den här metoden för distribution av tillståndskänslig repliker eller tillståndslösa instanser ger bästa möjliga feltolerans. I en situation när en domän kraschar går minimalt antal repliker/instanser förlorad. 

Den här metoden kan å andra sidan är för begränsade och inte tillåter att klustret ska använda alla resurser. För vissa klusterkonfigurationer kan inte vissa noder användas. Detta kan leda till Service Fabric inte placera tjänsterna, vilket resulterar i varningsmeddelanden. Vissa av klusternoderna kan inte vara i det föregående exemplet används (N6 i angivna exempel). Även om du vill lägga till noder i klustret (N7 – N10), placeras repliker/instanser endast på N1 – N5 på grund av fel och uppgradera domän villkor. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfiguration 2*


### <a name="alternative-approach"></a>*Alternativ metod*

Klustret Resource Manager har stöd för en annan version av villkoret fel- och uppgradera domänen som gör att placering av samtidigt som fortfarande en lägsta nivå av säkerhet. Alternativa fel- och uppgradera domän begränsningen kan anges enligt följande: ”för en viss tjänst partition replik fördelningen mellan domäner bör se till att partitionen inte har drabbats en förlorar kvorum”. Anta att den här begränsningen innehåller ett ”kvorum säker” garanti. 

> [!NOTE]
>För en tillståndskänslig service vi definiera *förlorar kvorum* i en situation när en majoritet av replikerna partition är nere på samma gång. Om TargetReplicaSetSize är fem, representerar en uppsättning alla tre repliker kvorum. På liknande sätt, om TargetReplicaSetSize 6, fyra repliker krävs för kvorum. I båda fallen kan högst två repliker ligga nere samtidigt om partitionen vill fortsätta att fungera normalt. Det finns ingen sådan funktion som för en tillståndslös tjänst *förlorar kvorum* som tillståndslösa tjänster conitnue till functionate normalt även om en majoritet av instanser gå på samma gång. Därför kan fokuserar vi på tillståndskänsliga tjänster i resten av texten.
>

Vi går tillbaka till föregående exempel. Alla tre angivna layouter vara giltig med ”kvorum säker”-version av villkoret. Det beror på att även om det blir fel på FD0 i den andra layout eller UD1 i den tredje layouten, partitionen fortfarande skulle ha kvorum (en majoritet av dess repliker blir fortfarande in). Med denna version av villkoret kan N6 nästan alltid användas.

Metoden ”kvorum säker” ger bättre flexibilitet än metoden som ”högsta skillnaden” eftersom det är lättare att hitta repliken distributioner som är giltiga i nästan alla klustertopologi. Men den här metoden kan inte garantera bästa felet tolerans egenskaper eftersom några fel är värre än andra. En majoritet av replikerna kan gå förlorade med fel på en domän och ytterligare en replik i värsta fall scenario. Till exempel i stället för 3 fel måste förlorar kvorum med 5 repliker eller instanser, du kan nu gå förlorade en majoritet med två fel. 

### <a name="adaptive-approach"></a>*Anpassningsbar metod*
Vi har introducerat en anpassad metod som kombinerar dessa två strategier eftersom båda dessa metoder har styrkor och svagheter.

> [!NOTE]
>Detta är standardbeteendet från och med Service Fabric Version 6.2. 
>
Den anpassade metoden använder ”högsta skillnaden” logik som standard och växlar till ”kvorum säker” logiken bara när det behövs. Klustret Resource Manager räknat automatiskt ut vilka strategi krävs genom att titta på hur de kluster och tjänster som är konfigurerade. För en viss tjänst: *om TargetReplicaSetSize är jämnt delbart med antalet Feldomäner och antalet uppgradera domäner **och** antalet noder som är mindre än eller lika med (antalet Feldomäner) * (den Antal uppgradera domäner), resurshanteraren klustret ska använda ”kvorum baserat” logiken för tjänsten.* Tänk dessutom på att klustret Resource Manager kommer att använda den här metoden för både tillståndslösa och tillståndskänsliga tjänster, trots förlorar kvorum som inte är relevanta för tillståndslösa tjänster.

Vi går tillbaka till föregående exempel och förutsätter att 8 noder (klustret fortfarande är konfigurerad med fem Feldomäner och fem uppgradera domäner och TargetReplicaSetSize av en tjänst som finns på de klustret förblir fem) har nu ett kluster. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfiguration 3*

Eftersom alla nödvändiga villkor uppfylls, använder Hanteraren för filserverresurser ”kvorum baserad” logiken i distribuerar tjänsten. Detta gör att användning av N6 – N8. En distribution av möjliga service i det här fallet kan se ut så:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Layout 4*

Om din tjänst TargetReplicaSetSize minskar till fyra (till exempel), Lägg märke till att klustret Resource Manager och fortsätta att använda ”högsta skillnaden” logiken eftersom TargetReplicaSetSize delbar med antalet FDs och UDs inte längre. Därför inträffar vissa replik förflyttningar för att distribuera återstående fyra repliker på noderna N1 N5 så att ”högsta skillnaden”-versionen av logiken Feldomän och uppgradering domän inte har överskridits. 

Söker efter tillbaka den fjärde layouten och TargetReplicaSetSize fem. Antalet uppgradera domäner blir lika med fyra om N1 tas bort från klustret. Klustret Resource Manager startar igen med ”högsta skillnaden” logik som antalet UDs inte jämnt divideras med tjänstens TargetReplicaSetSize längre. Replik R1 när inbyggda igen och har därför att hamna på N4 så att fel- och domänbegränsningar uppgraderar inte överskrids.

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
Definiera Feldomäner och uppgradera domäner görs automatiskt i Azure värdbaserade Service Fabric-distributioner. Service Fabric hämtar och använder miljöinformation från Azure.

Du skapar ditt eget kluster (eller vill köra en viss topologi i utveckling) kan du ange fel och uppgradera domänen information om dig själv. I det här exemplet definierar vi nio lokal utveckling kluster med en nod som omfattar tre ”datacenter” (var och en med tre rack). Det här klustret har också tre uppgradera domäner stripe över de tre datacenter. Ett exempel på hur understiger: 

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
> När du definierar kluster via Azure Resource Manager tilldelas Feldomäner och uppgradera domäner av Azure. Definition av din nodtyper och virtuella datorer i Azure Resource Manager-mall innehåller därför inte Feldomän eller uppgradera domäninformation.
>

## <a name="node-properties-and-placement-constraints"></a>Egenskaper för en nod och placeringen
Ibland (i fakta i de flesta fall) ska du vill se till att vissa arbetsbelastningar körs bara på vissa typer av noder i klustret. Exempelvis kanske vissa arbetsbelastning kräver GPU-kort eller SSD och andra inte. Ett bra exempel på mål maskinvara för specifika arbetsbelastningar är i stort sett alla där n-nivå-arkitektur. Vissa datorer fungerar som klientdel eller API som betjänar sida av programmet och som exponeras av klienter eller internet. Olika datorer, ofta med olika maskinvaruresurser hantera arbete lager beräkning eller lagring. Dessa är ofta _inte_ exponeras direkt till klienter eller internet. Service Fabric förväntar att det finns fall där viss arbetsbelastning behöver köras på specifika maskinvarukonfigurationer. Exempel:

* har ”lyfts och flyttat” i ett befintligt program i flera nivåer i en miljö med Service Fabric
* en arbetsbelastning vill köra på maskinvara för prestanda, skala och isolering av säkerhetsskäl
* En arbetsbelastning isoleras från andra arbetsbelastningar för princip- eller förbrukning orsaker

För att stödja dessa typer av konfigurationer, har Service Fabric förstklassigt begreppet taggar som kan tillämpas på noder. Dessa taggar kallas **nodegenskaper**. **Placeringen** är instruktioner som är kopplade till enskilda tjänster väljer för en eller flera egenskaper i noden. Placeringen definiera där tjänsterna ska köras. Uppsättningen begränsningar kan utökas - alla nyckel/värde-par fungerar. 

<center>
![Klustret Layout olika arbetsbelastningar][Image5]
</center>

### <a name="built-in-node-properties"></a>Inbyggda nodegenskaper
Service Fabric definierar vissa standardegenskaper nod som kan användas automatiskt utan att användaren behöver ange dem. Standardegenskaper som definierats vid varje nod är den **NodeType** och **nodnamn**. Exempelvis så du kan skriva ett placering villkor som `"(NodeType == NodeType03)"`. Vi har vanligtvis hittat NodeType ska vara en av mest ofta egenskaper som används. Det är användbart eftersom det motsvarar en typ av en dator 1:1. Varje typ av dator motsvarar en typ av arbetsbelastning i ett program med traditionella n-nivå.

<center>
![Placeringen och nodegenskaper][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Begränsning för placering och noden egenskapssyntax 
Värdet som anges i egenskapen nod kan vara en sträng, bool, eller signerat länge. Instruktionen på tjänsten kallas en placering *begränsningen* eftersom den begränsar där tjänsten kan köras i klustret. Villkoret kan vara någon booleskt uttryck som körs på egenskaperna annan nod i klustret. Ogiltig väljare i dessa booleskt uttryck är:

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
| ”inte” | "!" |
| ”gruppen som enskild instruktion” | "()" |

Här följer några exempel på grundläggande begränsning instruktioner.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Endast noder där instruktionen begränsningen övergripande placering utvärderas till ”True” kan ha tjänsten placeras på den. Noder som inte har en definierad egenskap matchar inte någon placering begränsningen som innehåller egenskapen.

Anta att följande nodegenskaper har definierats för en viss nod:

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

värdbaserad kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure. 

> [!NOTE]
> Nodtypen är vanligtvis parametriserade i Azure Resource Manager-mall. Det skulle se ut som ”[parameters('vmNodeType1Name')]” i stället för ”NodeType01”.
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

Du kan skapa tjänsten placering *begränsningar* för en tjänst som på följande sätt:

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

Om alla noder i NodeType01 är giltiga, du kan också välja att nodtypen med begränsningen ”(NodeType == NodeType01)”.

En av de bästa sakerna placeringen för en tjänst är att de kan uppdateras dynamiskt vid körning. Så om du behöver kan du flytta en tjänst i klustret, lägga till och ta bort krav, osv. Service Fabric hand tar om att säkerställa att tjänsten är igång och tillgängliga även om dessa typer av ändringar görs.

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

Placeringen har angetts för varje olika namngivna service-instans. Uppdateringar alltid ersätta (Skriv över) det tidigare har angetts.

Kluster-definition definierar egenskaper på en nod. Ändra egenskaper för en nod kräver en uppgradering för konfiguration av klustret. Uppgradera en nodegenskaper kräver varje berörda nod startas om för att rapportera dess nya egenskaper. Dessa rullande uppgraderingar hanteras av Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Som beskriver och hantera klusterresurser
En av de viktigaste jobben i alla orchestrator är för att hantera resursförbrukning i klustret. Hantera klusterresurser kan innebära ett antal olika saker. Först, det är att säkerställa att datorer inte är överbelastad. Det innebär att se till att datorer inte kör fler tjänster än de kan hantera. Det finns andra, belastningsutjämning och optimering, vilket är mycket viktigt att köra tjänster effektivt. Kostnad gällande eller prestanda känsliga Tjänsterbjudanden tillåta inte vissa noder ska heta medan andra är Kall. Varm noder leda till resurskonflikter och sämre prestanda och kalla noder representerar oanvänt resurser och ökade kostnader. 

Service Fabric representerar resurser som `Metrics`. Mått är en logisk eller fysisk resurs som du vill att beskriva till Service Fabric. Exempel på mått är till exempel ”WorkQueueDepth” eller ”MemoryInMb”. Information om de fysiska resurserna som Service Fabric kan styra på noder finns [resurs styrning](service-fabric-resource-governance.md). Information om hur du konfigurerar anpassade mått och hur används finns i [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)

Mått skiljer sig från placeringar begränsningar och egenskaper för en nod. Nodegenskaper är statisk beskrivningar av själva noderna. Mått beskrivs resurser där noder och att tjänster ska använda när de körs på en nod. En nodegenskap kan vara ”HasSSD” och kan anges till true eller false. Mängden ledigt utrymme på den SSD och hur mycket som används av tjänster är ett mått som ”DriveSpaceInMb”. 

Det är viktigt att notera att precis som för placeringen och egenskaper för en nod, Service Fabric klustret Resource Manager inte förstår vad namnen på mätvärdena som betyder. Tjänstmåttets namn är bara strängar. Det är en bra idé att deklarera enheter som en del av de mått namn som du skapar när det kan vara tvetydig.

## <a name="capacity"></a>Kapacitet
Om du har inaktiverat alla *NLB*, Service Fabric-klustret Resource Manager skulle fortfarande se till att ingen nod avslutas över sin kapacitet. Det är möjligt att hantera kapacitet loggens överskrids om klustret är full eller arbetsbelastningen är större än en nod. Kapaciteten är en annan *begränsningen* att klustret Resource Manager använder för att förstå hur mycket av en resurs en nod har. Återstående kapacitet också spåras klustret som helhet. Både kapacitet och användning på tjänstnivå uttrycks i mått. Exempelvis måttet kan vara ”ClientConnections” och en viss nod kan ha en kapacitet för ”ClientConnections” på 32768. Andra noder kan har andra begränsningar vissa-tjänsten körs på den nod kan säga den för närvarande förbrukar 32256 av måttet ”ClientConnections”.

Under körning spårar återstående kapacitet i klustret och på noder i klustret Resource Manager. För att kunna spåra kapacitet subtraherar klustret Resource Manager varje tjänst användning från nodens kapacitet där tjänsten körs. Med den här informationen kan Service Fabric klustret Resource Manager ta reda på var du ska placera eller flytta repliker så att noderna inte gå igenom kapacitet.

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

värdbaserad kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure. 

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

Ofta en tjänst att läsa in ändras dynamiskt. Anta att en replik belastningen på ”ClientConnections” har ändrats från 1024 till 2048, men den nod som den kördes på sedan bara har 512 kapacitet kvar för att mått. Nu den repliken eller instansens placering är ogiltigt, eftersom det inte finns tillräckligt med utrymme på noden. Klustret Resource Manager har startar och få noden under kapacitet. Det minskar belastningen på den nod som är överbelastade genom att flytta en eller flera av repliker eller instanser från noden till andra noder. När du flyttar repliker försöker klustret Resource Manager minimerar kostnaden för dessa flyttningar. Förflyttningskostnad beskrivs i [i den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md) och mer om klustret Resource Manager ombalansering strategier och regler beskrivs [här](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kluster-kapacitet
Hur behålla övergripande klustret från att vara full av Service Fabric klustret Resource Manager? Dessutom med dynamiska belastning som det inte finns mycket du kan göra. Tjänster kan ha sina belastningen topp oberoende av åtgärder som vidtas av klustret Resource Manager. Klustret med tillräckligt med utrymme i dag kan därför vara underpowered när du blir berömda i morgon. Namn, det finns vissa kontroller som är inbyggd i för att förhindra problem. Det första vi kan göra är att förhindra att nya arbetsbelastningar som innebär att klustret ska bli fullständig.

Anta att du skapar en tillståndslös tjänst och den innehåller vissa som är kopplade till den. Anta att tjänsten mån om måttet ”DiskSpaceInMb”. Även anta att den kommer att använda fem enheter av ”DiskSpaceInMb” för varje instans av tjänsten. Du vill skapa tre instanser av tjänsten. Bra! Så att det innebär att vi behöver 15 enheter av ”DiskSpaceInMb” måste finnas i klustret för att vi kan även kunna skapa dessa instanser av tjänsten. Klustret Resource Manager beräknar kontinuerligt kapacitet och användning av varje mått så den kan fastställa återstående kapacitet i klustret. Om det inte finns tillräckligt med utrymme, avvisar klustret Resource Manager skapa service-anrop.

Eftersom kravet endast att det finnas 15 enheter, kan detta utrymme allokeras många olika sätt. Till exempel, kan det finnas en återstående enhet av kapacitet på 15 olika noder eller tre återstående enheter av kapacitet på fem olika noder. Om klustret Resource Manager kan ordna om saker så att det finns fem enheter på tre noder, placerar tjänsten. Ordna om klustret är vanligtvis möjligt om klustret är nästan full eller går inte att konsolidera befintliga tjänster av någon anledning.

## <a name="buffered-capacity"></a>Buffrat kapacitet
Buffrat kapaciteten är en annan funktion av klustret Resource Manager. Det gör att reservationen av en del av den totala kapaciteten för noden. Denna kapacitet buffert används bara för att placera tjänster under uppgraderingar och nodfel. Buffrat kapacitet anges globalt per mått för alla noder. Värdet som du väljer för reserverad kapacitet är en funktion av antalet fel och uppgradera domäner som du har i klustret. Flera fel och uppgradera domäner innebär att du kan välja ett lägre värde för din buffrat kapacitet. Om du har flera domäner, kan du förvänta sig mindre mängder klustret blir otillgänglig under uppgraderingar och fel. Ange buffras kapacitet endast meningsfullt om du har angett nod kapaciteten för ett mått.

Här är ett exempel på hur du anger buffrat kapacitet:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

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

Skapandet av nya tjänster misslyckas när klustret är utanför buffrat kapacitet för ett mått. Hindrar skapandet av nya tjänster att bevara bufferten garanterar inte uppgraderingar och fel orsakar noder för att gå igenom kapacitet. Buffrat kapacitet är valfritt men rekommenderas i ett kluster som definierar kapaciteten för ett mått.

Klustret Resource Manager visar informationen belastningen. Informationen omfattar för varje mått: 
  - buffrat kapacitetsinställningarna
  - den totala kapaciteten
  - aktuell förbrukning
  - Anger om varje mått anses belastningsutjämnade eller inte
  - statistik om standardavvikelsen
  - noder som har mest och minst belastning  
  
Nedan finns det ett exempel på dessa utdata:

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
* Information om arkitektur och information om flödet inom klustret Resource Manager kolla [den här artikeln ](service-fabric-cluster-resource-manager-architecture.md)
* Definiera defragmentering mått är ett sätt att konsolidera belastningen på noder i stället för att sprida. Om du vill veta hur du konfigurerar defragmentering avser [i den här artikeln](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Börja från början och [få en introduktion till Service Fabric klustret Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Om du vill veta mer om hur klustret Resource Manager hanterar och balanserar belastningen i klustret kan ta en titt i artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
