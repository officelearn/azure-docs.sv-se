---
title: Beskriv ett kluster med Cluster Resource Manager | Microsoft Docs
description: Beskriv ett Service Fabric-kluster genom att ange feldomäner, uppgraderingsdomäner, nodegenskaper och nodkapaciteterna för Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271640"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Beskriv ett Service Fabric-kluster med Cluster Resource Manager
Cluster Resource Manager-funktion i Azure Service Fabric tillhandahåller flera mekanismer för att beskriva ett kluster:

* Feldomäner
* Uppgradera domäner
* Nodegenskaper
* Nodkapaciteterna

Under körning använder den här informationen i Cluster Resource Manager för att garantera hög tillgänglighet för de tjänster som körs i klustret. Samtidigt framtvinga dessa viktiga regler, försöker den också optimera användning av databasresurser i klustret.

## <a name="fault-domains"></a>Feldomäner
En feldomän är alla delar av samordnad fel. En enda dator är en feldomän. Det kan misslyckas på en egen av olika skäl, från power tillgångs-fel för att enhetsfel till felaktig NIC firmware. 

Datorer som är anslutna till samma Ethernet-växel finns i samma feldomän. Det finns datorer som delar en enda källa ström eller på en enda plats. 

Eftersom det är naturligt för maskinvarufel att överlappa är feldomäner sin natur hierarkiska. De är representeras som URI: er i Service Fabric.

Det är viktigt att feldomäner har ställts in korrekt eftersom Service Fabric använder den här informationen för att placera tjänster på ett säkert sätt. Service Fabric vill inte placera tjänster så att förlust av en feldomän (orsakas av fel på vissa komponent) gör att en tjänst att gå nedåt. 

I Azure-miljön använder Service Fabric felinformationen för domänen som tillhandahålls av miljön för att konfigurera korrekt noderna i klustret för din räkning. För fristående instanser av Service Fabric definieras feldomäner när klustret har ställts in. 

> [!WARNING]
> Det är viktigt att domäninformationen fel till Service Fabric är korrekt. Anta exempelvis att att noderna i ditt Service Fabric-kluster körs inom 10 virtuella datorer som körs på 5 fysiska värdar. I det här fallet, även om det finns 10 virtuella datorer, det finns endast 5 olika (toppnivå) feldomäner. Delar samma fysiska värd gör att virtuella datorer för att dela samma rot feldomänen, eftersom de virtuella datorerna uppleva samordnad fel om deras fysiska värd skulle sluta fungera.  
>
> Service Fabric förväntar sig en nod inte att ändra feldomänen. Andra metoder för att säkerställa hög tillgänglighet för de virtuella datorerna, till exempel [hög tillgänglighet för virtuella datorer](https://technet.microsoft.com/library/cc967323.aspx), kan orsaka konflikter med Service Fabric. Dessa mekanismer använda transparent migrering av virtuella datorer från en värd till en annan. De inte konfigurera om eller meddela köra kod på den virtuella datorn. Därför måste de är *stöds inte* som miljöer för att köra Service Fabric-kluster. 
>
> Service Fabric ska bara hög tillgänglighet-tekniken används. Mekanismer som live VM-migrering och SAN-nätverk krävs inte. Om dessa mekanismer används tillsammans med Service Fabric kan de _minska_ programmets tillgänglighet och tillförlitlighet. Det beror på att de introducera ytterligare komplexitet, lägga till centraliserade källor till felet och använda tillförlitlighet och tillgänglighet strategier som står i konflikt med dessa i Service Fabric. 
>
>

I följande bild ser bakgrundsfärg vi för alla entiteter som bidrar till feldomäner och listar alla olika feldomäner som uppstår. I det här exemplet har vi datacenter (”DC”), rack (”R”) och blad (”B”). Om varje bladet innehåller mer än en virtuell dator, kan det finnas en annan nivå i hierarkin som fel.

<center>

![Noder som är ordnade via feldomäner][Image1]
</center>

Under körning, Service Fabric Cluster Resource Manager tar hänsyn till feldomäner i klustret och planer layouter. Tillståndskänsliga repliker eller tillståndslösa instanser för en tjänst distribueras så att de är i separata feldomäner. Distribuera tjänsten över feldomäner säkerställer att tillgängligheten för tjänsten inte är äventyras när en feldomän misslyckas på vilken nivå i hierarkin.

Cluster Resource Manager hand inte hur många lager som finns i hierarkin som fel. Försök att säkerställa att förlusten av en delar av hierarkin inte påverka tjänster som körs i den. 

Det är bäst om samma antal noder finns på varje nivå i detalj i hierarkin som fel. Om ”trädet” feldomäner obalanserade i klustret, är det svårare för Cluster Resource Manager för att ta reda på den rekommenderade allokeringen av tjänster. Imbalanced fel domän layouter innebär att förlust av vissa domäner påverkar tillgängligheten för tjänster som är mer än andra domäner. Cluster Resource Manager är därför torn mellan två mål: 

* Företaget vill använda datorerna i domänen ”tung” genom att placera tjänster på dem. 
* Företaget vill placera tjänster i andra domäner så att förlust av en domän inte orsakar problem. 

Hur ser imbalanced domäner ut? I följande diagram visas två olika kluster layouterna. I det första exemplet jämnt noderna över feldomänerna. I det andra exemplet har en feldomän fler noder än de andra feldomänerna. 

<center>

![Två olika kluster layouterna][Image2]
</center>

I Azure hanteras val av vilka fel domän innehåller en nod åt dig. Men beroende på antalet noder som du etablerar, du kan fortfarande få feldomäner som har fler noder i dem än andra. 

Anta exempelvis att du har fem feldomäner i klustret men etablerat sju noder för en nodtyp (**NodeType**). I det här fallet få de första två feldomänerna fler noder. Om du fortsätter att distribuera flera **NodeType** instanser med bara ett par instanser, problemet hämtar sämre. Därför rekommenderar vi att antalet noder i varje nodtyp är en multipel av antalet feldomäner.

## <a name="upgrade-domains"></a>Uppgradera domäner
Uppgraderingsdomäner är en annan funktion som hjälper dig att förstå layouten för klustret Service Fabric Cluster Resource Manager. Uppgraderingsdomäner definiera uppsättningar med noder som uppgraderas samtidigt. Uppgraderingsdomäner hjälpa Cluster Resource Manager förstå och dirigera hanteringsåtgärder som uppgraderingar.

Uppgraderingsdomäner är mycket som feldomäner, men med några viktiga skillnader. Först definiera delar av samordnad maskinvarufel feldomäner. Uppgradera domäner, å andra sidan definieras av principen. Du får du bestämma hur många, i stället för att låta miljön som bestämmer hur många. Du kan ha så många uppgraderingsdomäner som du gör noder. En annan skillnad mellan feldomäner och uppgraderingsdomäner är att uppgraderingsdomäner inte är hierarkiska. Det är däremot mer som en enkel tagg. 

I följande diagram visas tre uppgraderingsdomäner stripe över tre feldomäner. Den visar även en möjlig placering för tre olika repliker av en tillståndskänslig tjänst, där var och en identisk i olika fel- och uppgraderingsdomäner. Den placeringen kan förlusten av en feldomän i mitten av en uppgradering av tjänsten och fortfarande har en kopia av kod och data.  

<center>

![Placering med fel- och uppgraderingsdomäner][Image3]
</center>

Det finns för- och nackdelar att ha stort antal uppgraderingsdomäner. Fler uppgraderingsdomäner betyder att varje steg i uppgraderingen är mer detaljerad och påverkar ett mindre antal noder eller tjänster. Färre tjänster har att flytta samtidigt, introduktion till mindre omsättning i systemet. Detta tenderar att förbättra tillförlitligheten, eftersom mindre tjänsten påverkas av eventuella problem som introducerats under uppgraderingen. Fler uppgraderingsdomäner innebär också att du behöver mindre tillgängliga buffert på andra noder för att hantera effekten av uppgraderingen. 

Till exempel om du har fem uppgraderingsdomäner hanterar noderna i var och en ungefär 20 procent av din trafik. Om du vill ta bort den uppgraderingsdomänen för en uppgradering behövs vanligtvis att belastningen och sedan en plats. Eftersom du har fyra återstående uppgraderingsdomäner, ha var och en utrymme för ungefär 5 procent av den totala trafiken. Fler uppgraderingsdomäner innebär att du behöver mindre buffert på noderna i klustret. 

Överväg om du har haft 10 uppgraderingsdomäner i stället. Varje uppgraderingsdomän skulle i så fall hanterar bara cirka 10 procent av den totala trafiken. När en uppgradering i klustret, behöver varje domän har utrymme för normalt bara 1.1 procent av den totala trafiken. Fler uppgraderingsdomäner Allmänt kan du köra dina noder vid högre användning, eftersom du behöver mindre reserverad kapacitet. Detsamma gäller för feldomäner.  

Nackdelen med att ha många uppgraderingsdomäner är att uppgraderingar tenderar att ta längre tid. Service Fabric väntar en kort tidsperiod när en uppgraderingsdomän har slutförts och utför kontroller innan du börjar uppgradera nästa. Dessa fördröjningar kan upptäcka problem som introducerades av uppgraderingen innan uppgraderingen fortsätter. Nackdelen är acceptabelt eftersom den förhindrar att felaktiga ändringar påverkar för mycket av tjänsten i taget.

Finns för få uppgraderingsdomäner har många negativa sidoeffekter. När varje uppgraderingsdomän nedåt och håller på att uppgraderas, en stor del av den totala kapaciteten är inte tillgänglig. Till exempel om du har bara tre uppgraderingsdomäner du tar upp ned ungefär en tredjedel av din övergripande tjänst eller klusterkapacitet i taget. Med så mycket av din tjänst ned på samma gång är inte önskvärt eftersom du måste ha tillräcklig kapacitet i resten av klustret för att hantera arbetsbelastningen. Underhålla det buffert innebär att under normal drift, är dessa noder mindre lästs in än de skulle vara annars. Detta ökar kostnaden för att köra din tjänst.

Det finns ingen verklig gräns för antal fel eller uppgradera domäner i en miljö eller begränsningar i hur de överlappar varandra. Men det finns vanliga mönster:

- Feldomäner och uppgraderingsdomäner mappas 1:1
- En uppgraderingsdomän per nod (fysisk eller virtuell OS instans)
- En ”stripe” eller ”en” modell där feldomäner och uppgraderingsdomäner utgör en matris med datorer som vanligtvis körs ned de diagonala linjer

<center>

![Skisser på fel- och uppgraderingsdomäner][Image4]
</center>

Det finns inga bästa svaret för vilken layout du vill välja. Var och en har- och nackdelar. Till exempel är 1FD:1UD modellen enkelt att konfigurera. Modell för en domän per nod modell är mest som vilka andra används för att. Under uppgraderingar uppdateras varje nod oberoende av varandra. Detta liknar hur små uppsättningar datorer har uppgraderats manuellt i förflutna.

Den vanligaste modellen är FD/UD matrisen där feldomäner och uppgraderingsdomäner utgör en tabell och noder placeras från och med diagonalt. Det här är den modell som används som standard i Service Fabric-kluster i Azure. För kluster med många noder identisk allt ser ut som ett kompakt matris-mönster.

> [!NOTE]
> Service Fabric-kluster i Azure stöder inte ändra standardstrategi. Endast fristående kluster erbjuder den anpassningen.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Fel- och begränsningar för domänen och resulterande beteende
### <a name="default-approach"></a>Standard-metod
Som standard behåller Cluster Resource Manager tjänster balanserade över fel- och uppgraderingsdomäner. Detta är utformat som en [begränsningen](service-fabric-cluster-resource-manager-management-integration.md). Villkoret för fel- och domäner tillstånd: ”För en viss tjänst-partition det ska aldrig vara någon skillnad större än ett av antalet service-objekt (tillståndslös tjänstinstanser eller tillståndskänslig tjänst repliker) mellan två domäner på samma nivå i hierarkin”.

Anta att den här begränsningen ger en garanti för ”högsta skillnaden”. Villkoret för fel- och uppgraderingsdomäner förhindrar vissa flyttar eller arrangemang som bryter mot regeln.

Anta exempelvis att vi har ett kluster med sex noder som konfigurerats med fem feldomäner och fem uppgraderingsdomäner.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Nu anta att vi skapar en tjänst med en **TargetReplicaSetSize** (eller för en tillståndslös tjänst **InstanceCount**) värdet för fem. Replikerna hamnar på N1 N5. I själva verket används N6 aldrig oavsett hur många tjänster så här skapar du. Men varför? Låt oss titta på skillnaden mellan den aktuella layouten och vad som skulle hända om N6 väljs.

Här visas layouten vi och det totala antalet repliker per fel- och domän:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Den här layouten belastningsutjämnad när det gäller noder per feldomän och uppgraderingsdomän. Det är också belastningsutjämnad vad gäller antalet repliker per fel- och domän. Varje domän har samma antal noder och samma antal repliker.

Nu ska vi titta på vad som skulle hända om vi hade använt N6 i stället för N2. Hur skulle medan replikerna distribueras sedan?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Den här layouten strider mot vår definitionen för ”högsta skillnaden”-garantin för begränsningen fel domän. FD0 har två repliker, medan FD1 har noll. Skillnaden mellan FD0 och FD1 är totalt två, vilket är större än den maximala skillnaden mellan en. Eftersom villkoret överskrids, tillåter inte den här ordningen i Cluster Resource Manager. På samma sätt, om vi valt N2 och N6 (i stället för N1 och N2), skulle vi få:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Den här layouten belastningsutjämnad när det gäller feldomäner. Men nu den bryter mot villkoret uppgraderingsdomän eftersom UD0 har noll repliker och UD1 har två. Den här layouten också är ogiltigt och hämtas inte av Cluster Resource Manager.

Den här metoden rör distribution av tillståndskänsliga repliker eller tillståndslösa instanser ger bästa möjliga feltolerans. Minsta antal repliker/instanser går förlorad om en domän slutar att fungera. 

Den här metoden kan å andra sidan är för begränsade och inte att klustret ska kunna använda alla resurser. Vissa noder kan inte användas för vissa konfigurationer med klustret. Detta kan orsaka Service Fabric för att inte placera dina tjänster, vilket resulterar i varningsmeddelanden. Vissa av noderna i klustret kan inte vara i exemplet ovan används (N6 i det här exemplet). Även om du har lagt till noder till klustret (N7 N10) placeras repliker/instanser endast på N1 – N5 på grund av begränsningar i fel- och uppgraderingsdomäner. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Annan metod

Cluster Resource Manager stöder en annan version av begränsningen för fel- och uppgraderingsdomäner. Det gör att placering vid fortfarande, vilket ger en lägsta nivå av säkerhet. Den alternativa begränsningen kan anges på följande sätt: ”För en viss tjänst-partition repliken fördelningen mellan domäner bör se till att partitionen inte har drabbats en förlorar kvorum”. Anta att den här begränsningen ger garanterad ”kvorum säker”. 

> [!NOTE]
> En tillståndskänslig tjänst definierar vi *förlorar kvorum* i en situation när en majoritet av partitionsrepliker är nere på samma gång. Till exempel om **TargetReplicaSetSize** är fem, en uppsättning alla tre repliker representerar kvorum. På samma sätt, om **TargetReplicaSetSize** är sex, fyra repliker är nödvändiga för att kvorum. I båda fallen kan kan högst två repliker vara otillgängliga samtidigt om partitionen vill fortsätta att fungera normalt. 
>
> Det finns ingenting som heter för en tillståndslös tjänst *förlorar kvorum*. Tillståndslösa tjänster fortsätter att fungera normalt även om de flesta fall gå på samma gång. Därför ska vi fokusera på tillståndskänsliga tjänster i resten av den här artikeln.
>

Vi går tillbaka till föregående exempel. Med ”kvorum säker”-versionen av begränsningen är alla tre layouter giltig. Även om FD0 misslyckades i den andra layouten eller UD1 misslyckades i den tredje layouten, skulle fortfarande partitionen har något kvorum. (En majoritet av replikerna blir fortfarande in.) Med den här versionen av begränsningen kan nästan alltid N6 användas.

Metoden ”kvorum säker” ger bättre flexibilitet än ”högsta skillnaden”-metoden. Det beror på att det är lättare att hitta repliken distributioner som är giltiga i nästan alla klustertopologi. Men den här metoden kan inte garantera bästa felet tolerans egenskaper eftersom vissa fel är sämre resultat än andra. 

I värsta fall scenario kan en majoritet av repliker gå förlorade med fel på en domän och en ytterligare replik. Till exempel i stället för tre fel måste förlorar kvorum med fem repliker eller instanser, kan du nu förlorar en majoritet med bara två fel. 

### <a name="adaptive-approach"></a>Anpassningsbar metod
Eftersom båda metoderna har styrkor och svagheter, har vi lanserat en anpassningsbar metod som kombinerar dessa två olika metoder.

> [!NOTE]
> Detta är standardbeteendet från och med Service Fabric version 6.2. 
> 
> Anpassningsbar metoden använder ”högsta skillnaden” logiken som standard och växlar till ”kvorum säker” logiken vid behov. Klusterresurshanteraren räknat automatiskt ut vilken strategi krävs genom att titta på hur klustret och tjänster har konfigurerats.
> 
> Cluster Resource Manager använder ”kvorum baserat”-logik för en tjänst båda av dessa villkor är uppfyllda:
>
> * **TargetReplicaSetSize** för tjänsten är jämnt delbart med antalet feldomäner och antalet uppgraderingsdomäner.
> * Antalet noder som är mindre än eller lika med antalet feldomäner multiplicerat med antalet uppgraderingsdomäner.
>
> Ha i åtanke att Cluster Resource Manager kommer att använda den här metoden för både tillståndslösa och tillståndskänsliga tjänster, även om förlorar kvorum inte är relevanta för tillståndslösa tjänster.

Vi går tillbaka till föregående exempel och antar att ett kluster nu har åtta noder. Klustret fortfarande är konfigurerad med fem feldomäner och fem uppgraderingsdomäner och **TargetReplicaSetSize** värdet för en tjänst som finns i klustret förblir fem. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Eftersom alla nödvändiga villkor är uppfyllda, använder Cluster Resource Manager ”kvorum baserad” logiken i distribuera tjänsten. På så sätt kan användningen av N6 N8. En möjliga service-distribution i det här fallet kan se ut så här:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Om din tjänst **TargetReplicaSetSize** värdet minskas till fyra (till exempel), Cluster Resource Manager kommer att se ändringen. Det kommer att återupptas med ”högsta skillnaden”-logik eftersom **TargetReplicaSetSize** inte är delbara med antalet feldomäner och uppgraderingsdomäner längre. Därför kan inträffar vissa repliken förflyttningar för att distribuera de återstående fyra replikerna på noderna N1 N5. På så sätt kan har ”högsta skillnaden”-versionen av fel domän och uppgraderingen domänlogiken inte överskridits. 

I den föregående layouten, om den **TargetReplicaSetSize** värdet är fem och N1 tas bort från klustret, antalet uppgraderingsdomäner är lika med fyra. Cluster Resource Manager startas igen, med hjälp ”högsta skillnaden” logic eftersom antalet uppgraderingsdomäner jämnt inte dela upp tjänstens **TargetReplicaSetSize** värde längre. Replica R1, när du bygger igen och har därför hamnar på N4 så att villkoret för fel- och domänen inte överskrids.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |Gäller inte |Saknas |Saknas |Saknas |Saknas |Gäller inte |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurera fel- och uppgraderingsdomäner
Azure-baserat Service Fabric-distributioner definieras feldomäner och uppgraderingsdomäner i automatiskt. Service Fabric hämtar och använder miljöinformation från Azure.

Om du skapar ett eget kluster (eller vill köra en viss topologi i utveckling), kan du ange feldomänen och uppgradera domäninformation själv. I det här exemplet definierar vi ett lokalt utvecklingskluster med nio noder som sträcker sig över tre datacenter (var och en med tre rack). Det här klustret har också tre uppgraderingsdomäner stripe över dessa tre datacenter. Här är ett exempel på konfigurationen i ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
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

Det här exemplet används ClusterConfig.json för fristående distributioner:

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
> När du definierar kluster via Azure Resource Manager, Azure tilldelar feldomäner och uppgraderingsdomäner. Så definitionen av din nodtyper och VM-skalningsuppsättning som anger i innehåller Azure Resource Manager-mallen inte information om feldomän och uppgraderingsdomän.
>

## <a name="node-properties-and-placement-constraints"></a>Nodegenskaper och placeringsbegränsningar
Ibland (i fakta i de flesta fall) kommer du att se till att vissa arbetsbelastningar endast för vissa typer av noder i klustret. Exempelvis kan vissa arbetsbelastningar kan kräva GPU: er eller SSD-enheter och andra kanske inte. 

Ett bra exempel på mål maskinvara till specifika arbetsbelastningar är nästan alla n-tier-arkitektur. Vissa datorer fungerar som klientdel eller API-hämtning på klientsidan för programmet och är exponerade mot klienter eller internet. Olika datorer, ofta med olika maskinvaruresurser kan hantera verk som tillhör lagren för beräkning eller lagring. Det här handlar vanligtvis _inte_ direkt exponerade mot klienter eller internet. 

Service Fabric förväntar sig att i vissa fall kan specifika arbetsbelastningar kan behöva köra på specifika maskinvarukonfigurationer. Exempel:

* Ett befintligt program på n-nivå har ”lyfts och beräkningsarbete” i en miljö med Service Fabric.
* En arbetsbelastning måste köras på maskinvara för prestanda, skalbarhet eller säkerhet isolering skäl.
* En arbetsbelastning ska vara isolerade från andra arbetsbelastningar princip- eller resursen förbrukning skäl.

För att stödja dessa typer av konfigurationer, innehåller Service Fabric-taggar som du kan använda till noder. Dessa taggar kallas *nodegenskaper*. *Placeringsbegränsningar* är de instruktioner som är kopplade till enskilda tjänster som du väljer för en eller flera egenskaper för noden. Placeringsbegränsningar definiera där tjänsterna ska köras. Uppsättningen med begränsningar kan utökas. Alla nyckel/värde-par fungerar. 

<center>

![Olika arbetsbelastningar för en kluster-layout][Image5]
</center>

### <a name="built-in-node-properties"></a>Inbyggda nodegenskaper
Service Fabric definierar några standardegenskaper för noden som kan användas för automatiskt så att du inte behöver definiera dem. Standardegenskaperna som definierats vid varje nod är **NodeType** och **nodnamn**. 

Du kan till exempel skriva en placering begränsning som `"(NodeType == NodeType03)"`. **NodeType** är en egenskap som används ofta. Det är användbart eftersom det motsvarar en typ av en dator 1:1. Varje typ av dator motsvarar en typ av arbetsbelastning i ett traditionellt n-nivåprogram.

<center>

![Placeringsbegränsningar och nodegenskaper][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Placeringsbegränsningar och noden egenskapssyntax 
Värdet som anges i egenskapen nod kan vara en sträng, boolesk, eller signerats länge. Instruktionen på tjänsten kallas en placering *begränsningen* eftersom den begränsar där tjänsten kan köras i klustret. Begränsningen kan vara valfri booleskt instruktionen som körs med egenskaper för nod i klustret. Giltigt väljare i dessa booleskt uttryck är:

* Villkorlig kontroller för att skapa specifika instruktioner:

  | Instruktionen | Syntax |
  | --- |:---:|
  | ”lika med” | "==" |
  | ”inte lika med” | "!=" |
  | ”större än” | ">" |
  | ”större än eller lika med” | ">=" |
  | ”mindre än” | "<" |
  | ”mindre än eller lika med” | "<=" |

* Booleskt uttryck för gruppering och logiska åtgärder:

  | Instruktionen | Syntax |
  | --- |:---:|
  | ”och” | "&&" |
  | ”eller” | "&#124;&#124;" |
  | ”not” | "!" |
  | ”gruppen som enskild instruktion” | "()" |

Här följer några exempel på grundläggande begränsning-uttryck:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Endast noder där den övergripande placering begränsning instruktionen utvärderas till ”True” kan ha tjänsten placeras på den. Noder som inte har en definierad egenskap stämmer inte överens med de begränsningar som placering som innehåller egenskapen.

Anta att följande noden egenskaper har definierats för en nodtyp i ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

I följande exempel visas nodegenskaper definieras via ClusterConfig.json för distribution av fristående eller Template.json för Azure-baserade kluster. 

> [!NOTE]
> Nodtyp är vanligtvis parametriserade i din Azure Resource Manager-mall. Det skulle se ut som `"[parameters('vmNodeType1Name')]"` i stället för NodeType01.
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

Du kan skapa tjänsteplacering *begränsningar* för en tjänst på följande sätt:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Om alla noder i NodeType01 är giltiga, du kan också välja den nodtypen med begränsningen `"(NodeType == NodeType01)"`.

Placeringsbegränsningar för en tjänst kan uppdateras dynamiskt vid körning. Om du vill kan du flytta en tjänst i klustret, lägga till och ta bort krav och så vidare. Service Fabric ser till att tjänsten är igång och tillgängliga även om dessa typer av ändringar görs.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Placeringsbegränsningar har angetts för varje instans av tjänsten. Uppdateringar alltid ersätta (Skriv över) vad som tidigare har angetts.

Klusterdefinitionen definierar egenskaper för på en nod. Ändra egenskaper för en nod kräver en uppgradering av klusterkonfigurationen. Uppgradera en nodegenskaper kräver alla berörda noder startas om för att rapportera dess nya egenskaper. Service Fabric hanterar dessa löpande uppgraderingar.

## <a name="describing-and-managing-cluster-resources"></a>Som beskriver och hantera klusterresurser
En av de viktigaste jobben eventuella orchestrator är för att hantera resurser som används i klustret. Hantera klusterresurser kan innebära ett antal olika saker. 

Först, det är att säkerställa att datorer inte är överbelastad. Det innebär att se till att datorer inte kör fler tjänster än de kan hantera. 

Dessutom finns belastningsutjämning och optimering, som är viktiga för att köra tjänster effektivt. Kostnadseffektiv eller resultatdrivna erbjudanden kan inte tillåter att vissa noder är medan andra är kalla. Frekvent noder leda till resurskonkurrens och sämre prestanda. Kalla noder representerar oanvänt resurser och ökade kostnader. 

Service Fabric representerar resurser som *mått*. Mått är en logisk eller fysisk resurs som du vill beskriva till Service Fabric. Exempel på mått är ”WorkQueueDepth” eller ”MemoryInMb”. Information om de fysiska resurserna som Service Fabric kan styra på noderna finns i [resursstyrning](service-fabric-resource-governance.md). Information om hur du konfigurerar anpassade mått och hur används finns i [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md).

Mått skiljer sig från placeringsbegränsningar och nodegenskaper. Nodegenskaper är statiska beskrivningar av noderna. Mått beskrivs resurser som noder har och att tjänsterna ska använda när de körs på en nod. En nodegenskap kanske **HasSSD** och kan anges till true eller false. Mängden tillgängligt utrymme på den SSD och hur mycket som används av tjänster är ett mått som ”DriveSpaceInMb”. 

Precis som för placeringsbegränsningar och nodegenskaper kan förstå Service Fabric Cluster Resource Manager inte vilka namnen på mått medelvärdet. Tjänstmåttets namn är bara strängar. Det är en bra idé att deklarera enheter som en del av tjänstmåttets namn som du skapar när de är tvetydig.

## <a name="capacity"></a>Kapacitet
Om du har inaktiverat alla *belastningsutjämning*, Service Fabric Cluster Resource Manager skulle fortfarande se till att ingen nod går via sin kapacitet. Det är möjligt att hantera kapacitet överskridanden såvida inte klustret är full eller arbetsbelastningen är större än en nod. Kapaciteten är en annan *begränsningen* att Cluster Resource Manager använder för att förstå hur mycket av en resurs för en nod har. Återstående kapacitet spåras också för klustret som helhet. 

Både den aktuella kapaciteten och förbrukningen på tjänstnivå uttrycks i mått. Till exempel måttet kan vara ”ClientConnections” och en nod kan ha en kapacitet för ”ClientConnections” av 32 768. Andra noder kan ha andra begränsningar. En tjänst som körs på noden kan du säga det för närvarande använder 32 256 för mått ”ClientConnections”.

Under körning spårar Cluster Resource Manager återstående kapacitet i klustret och på noder. Om du vill spåra kapacitet subtraherar Cluster Resource Manager varje tjänst användning från en nod kapacitet där tjänsten körs. Med den här informationen kan Cluster Resource Manager ta reda på var du vill placera eller flytta repliker så att noderna inte går över kapacitet.

<center>

![Klusternoder och kapacitet][Image7]
</center>

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

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Du kan se de kapaciteter som definierats i klustermanifestet. Här är ett exempel på ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Här är ett exempel på de kapaciteter som definieras via ClusterConfig.json för distribution av fristående eller Template.json för Azure-baserade kluster: 

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

En tjänst läsa in ändras ofta dynamiskt. Anta att en replik belastningen på ”ClientConnections” ändrats från 1 024 till 2 048. Den nod som den kördes på sedan hade en enda 512 återstående för det måttet-kapacitet. Nu den repliken eller instansens placering är ogiltig, eftersom det inte finns tillräckligt med utrymme på noden. Cluster Resource Manager måste få noden under kapacitet. Det minskar belastningen på den nod som är överbelastade genom att flytta en eller flera av repliker eller instanser från noden till andra noder. 

Cluster Resource Manager försöker minimera kostnaderna för att flytta repliker. Du kan läsa mer om [förflyttningskostnad](service-fabric-cluster-resource-manager-movement-cost.md) och [ombalansering strategier och regler](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Klusterkapacitet
Hur håller övergripande klustret inte är full av Service Fabric Cluster Resource Manager? Med dynamisk belastning finns det inte mycket du kan göra. Tjänster kan ha sina belastningen topp oberoende av åtgärder som tar Cluster Resource Manager. Ditt kluster med gott om plats i dag kan därför vara underpowered om det uppstår en topp i morgon. 

Kontroller i Cluster Resource Manager kan förhindra problem. Det första som du kan göra är att förhindra skapandet av nya arbetsbelastningar som innebär att klustret ska bli fullständig.

Anta att du skapar en tillståndslös tjänst och den har vissa belastning som är kopplade till den. Tjänsten värnar om måttet ”DiskSpaceInMb”. Tjänsten kommer att använda fem enheter av ”DiskSpaceInMb” för varje instans av tjänsten. Du vill skapa tre instanser av tjänsten. Det innebär att du behöver 15 enheter av ”DiskSpaceInMb” måste finnas i klustret som du kan även skapa dessa instanser av tjänsten.

Cluster Resource Manager beräknar kontinuerligt kapacitet och förbrukning av varje mått, så den kan fastställa återstående kapacitet i klustret. Om det inte finns tillräckligt med utrymme, avvisar Cluster Resource Manager samtalet för att skapa en tjänst.

Eftersom kravet är bara att 15 enheter är tillgängliga, kan du allokera tomrummet på många olika sätt. Till exempel kan det finnas en enhet för återstående kapacitet på 15 olika noder eller tre återstående enheter med kapacitet på fem olika noder. Om Cluster Resource Manager kan ändra saker så att det finns fem enheter på tre noder, placerar tjänsten. Om du ordnar om klustret är vanligtvis möjligt om inte klustret är nästan full eller befintliga tjänster går inte att konsolidera av någon anledning.

## <a name="buffered-capacity"></a>Buffrade kapacitet
Buffrade kapacitet är en annan funktion för Cluster Resource Manager. Det gör reservation av en del av den totala kapaciteten för noden. Denna kapacitet buffert används endast för att placera tjänster under uppgraderingar och nodfel. 

Buffrade kapacitet anges globalt per mått för alla noder. Det värde som du väljer för reserverad kapacitet är en funktion av antalet fel- och uppgraderingsdomäner som du har i klustret. Flera fel- och uppgraderingsdomäner innebär att du kan välja ett lägre värde för din buffrade kapacitet. Om du har flera domäner, kan du förvänta sig mindre mängder klustret för att vara otillgänglig under uppgraderingar och fel. Ange buffrade kapacitet är meningsfullt endast om du har angett nodens kapacitet för ett mått.

Här är ett exempel på hur du anger buffrade kapacitet i ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Här är ett exempel på hur du anger buffrade kapacitet via ClusterConfig.json för distribution av fristående eller Template.json för Azure-baserade kluster:

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

Cluster Resource Manager visar den här informationen för belastningen. För varje mått innehåller den här informationen: 
- Buffrade kapacitetsinställningarna.
- Den totala kapaciteten.
- Den aktuella förbrukningen.
- Om varje mått anses vara belastningsutjämnad eller inte.
- Statistik om standardavvikelsen.
- De noder som har mest och minst belastning.  
  
Följande kod visar ett exempel på dessa utdata:

```PowerShell
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
* Information om arkitektur- och informationsflödet i Cluster Resource Manager finns i [översikt över Cluster Resource Manager-arkitekturen](service-fabric-cluster-resource-manager-architecture.md).
* Definiera defragmentering mått är ett sätt att konsolidera belastningen på noder i stället för sprida. Läs hur du konfigurerar defragmentering i [defragmentering av mått och belastningen i Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Läs hur Cluster Resource Manager hanterar och balanserar belastningen i klustret i [belastningsutjämning Service Fabric-klustret](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
