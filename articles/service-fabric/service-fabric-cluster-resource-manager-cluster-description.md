---
title: Beskriv ett kluster med hjälp av Cluster Resource Manager
description: Beskriv ett Service Fabric-kluster genom att ange feldomäner, uppgraderingsdomäner, nodegenskaper och nodkapaciteter för Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258777"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Beskriv ett service fabric-kluster med hjälp av Cluster Resource Manager
Funktionen Klusterresurshanteraren i Azure Service Fabric innehåller flera mekanismer för att beskriva ett kluster:

* Feldomäner
* Uppgradera domäner
* Nodegenskaper
* Nodkapacitet

Under körning använder Cluster Resource Manager den här informationen för att säkerställa hög tillgänglighet för de tjänster som körs i klustret. När du tillämpar dessa viktiga regler försöker den också optimera resursförbrukningen i klustret.

## <a name="fault-domains"></a>Feldomäner
En feldomän är ett område med koordinerat fel. En enda dator är en feldomän. Det kan misslyckas på egen hand av olika skäl, från strömförsörjningsfel för att driva fel till dålig NIC firmware. 

Datorer som är anslutna till samma Ethernet-switch finns i samma feldomän. Så är maskiner som delar en enda kraftkälla eller på en enda plats. 

Eftersom det är naturligt för maskinvarufel att överlappa varandra, är feldomäner i sig hierarkiska. De är representerade som URI:er i Service Fabric.

Det är viktigt att feldomäner ställs in korrekt eftersom Service Fabric använder den här informationen för att placera tjänster på ett säkert sätt. Service Fabric vill inte placera tjänster så att förlusten av en feldomän (orsakad av fel på någon komponent) gör att en tjänst går ner. 

I Azure-miljön använder Service Fabric feldomäninformationen som tillhandahålls av miljön för att korrekt konfigurera noderna i klustret för din räkning. För fristående instanser av Service Fabric definieras feldomäner när klustret ställs in. 

> [!WARNING]
> Det är viktigt att feldomäninformationen till Service Fabric är korrekt. Anta till exempel att service fabric-klustrets noder körs i 10 virtuella datorer och körs på fem fysiska värdar. I det här fallet, även om det finns 10 virtuella datorer, finns det bara 5 olika (toppnivå) feldomäner. Om du delar samma fysiska värd kan virtuella datorer dela samma rotfelsdomän, eftersom de virtuella datorerna upplever ett samordnat fel om deras fysiska värd inte fungerar.  
>
> Service Fabric förväntar sig att feldomänen för en nod inte ändras. Andra mekanismer för att säkerställa hög tillgänglighet för de virtuella datorerna, till exempel [HA-virtuella datorer,](https://technet.microsoft.com/library/cc967323.aspx)kan orsaka konflikter med Service Fabric. Dessa mekanismer använder transparent migrering av virtuella datorer från en värd till en annan. De konfigurerar inte om eller meddelar den kod som körs i den virtuella datorn. Därför stöds de *inte* som miljöer för att köra Service Fabric-kluster. 
>
> Service Fabric bör vara den enda teknik med hög tillgänglighet som används. Mekanismer som direkt vm-migrering och SN är inte nödvändiga. Om dessa mekanismer används tillsammans med Service Fabric _minskar_ de programmets tillgänglighet och tillförlitlighet. Anledningen är att de introducerar ytterligare komplexitet, lägger till centraliserade källor till fel och använder tillförlitlighets- och tillgänglighetsstrategier som står i konflikt med dem i Service Fabric. 
>
>

I följande bild färgar vi alla entiteter som bidrar till feldomäner och listar alla de olika feldomäner som resulterar. I det här exemplet har vi datacenter ("DC"), rack ("R" och blad ("B"). Om varje blad innehåller mer än en virtuell dator kan det finnas ett annat lager i feldomänhierarkin.

<center>

![Noder ordnade via feldomäner][Image1]
</center>

Under körning tar Service Fabric Cluster Resource Manager hänsyn till feldomänerna i klustret och planerar layouter. De tillståndskänsliga replikerna eller tillståndslösa instanserna för en tjänst distribueras så att de är i separata feldomäner. Distribuera tjänsten över feldomäner säkerställer att tjänstens tillgänglighet inte äventyras när en feldomän misslyckas på någon nivå i hierarkin.

Klusterresurshanteraren tar inte hand om hur många lager det finns i feldomänhierarkin. Den försöker se till att förlusten av en del av hierarkin inte påverkar tjänster som körs i den. 

Det är bäst om samma antal noder finns på varje djupnivå i feldomänhierarkin. Om "träd" av feldomäner är obalanserat i klustret är det svårare för Cluster Resource Manager att ta reda på den bästa fördelningen av tjänster. Obalanserade feldomänlayouter innebär att förlusten av vissa domäner påverkar tillgängligheten för tjänster mer än andra domäner. Som ett resultat slits Cluster Resource Manager mellan två mål: 

* Den vill använda maskinerna i den "tunga" domänen genom att placera tjänster på dem. 
* Den vill placera tjänster i andra domäner så att förlusten av en domän inte orsakar problem. 

Hur ser obalanserade domäner ut? I följande diagram visas två olika klusterlayouter. I det första exemplet fördelas noderna jämnt över feldomänerna. I det andra exemplet har en feldomän många fler noder än de andra feldomänerna. 

<center>

![Två olika klusterlayouter][Image2]
</center>

I Azure hanteras valet av vilken feldomän som innehåller en nod åt dig. Men beroende på antalet noder som du etablerar kan du fortfarande sluta med feldomäner som har fler noder i dem än i andra. 

Anta till exempel att du har fem feldomäner i klustret men etablerar sju noder för en**nodtyp ( NodeType**). I det här fallet får de två första feldomänerna fler noder. Om du fortsätter att distribuera fler **NodeType-instanser** med bara ett par instanser förvärras problemet. Därför rekommenderar vi att antalet noder i varje nodtyp är en multipel av antalet feldomäner.

## <a name="upgrade-domains"></a>Uppgradera domäner
Uppgraderingsdomäner är en annan funktion som hjälper Service Fabric Cluster Resource Manager att förstå klustrets layout. Uppgraderingsdomäner definierar uppsättningar noder som uppgraderas samtidigt. Uppgradera domäner hjälper Cluster Resource Manager att förstå och dirigera hanteringsåtgärder som uppgraderingar.

Uppgradera domäner är mycket som fel domäner, men med ett par viktiga skillnader. För det första definierar områden med samordnade maskinvarufel feldomäner. Uppgraderingsdomäner, å andra sidan, definieras av principen. Du får bestämma hur många du vill, istället för att låta miljön diktera numret. Du kan ha lika många uppgraderingsdomäner som du gör-noder. En annan skillnad mellan feldomäner och uppgraderingsdomäner är att uppgraderingsdomäner inte är hierarkiska. Istället är de mer som en enkel tagg. 

Följande diagram visar tre uppgraderingsdomäner som är randiga mellan tre feldomäner. Den visar också en möjlig placering för tre olika repliker av en tillståndskänslig tjänst, där var och en hamnar i olika fel- och uppgraderingsdomäner. Den här placeringen gör det möjligt att förlora en feldomän mitt i en tjänstuppgradering och fortfarande ha en kopia av koden och data.  

<center>

![Placering med fel- och uppgraderingsdomäner][Image3]
</center>

Det finns fördelar och nackdelar med att ha ett stort antal uppgraderingsdomäner. Fler uppgraderingsdomäner innebär att varje steg i uppgraderingen är mer granulärt och påverkar ett mindre antal noder eller tjänster. Färre tjänster måste röra sig åt gången, vilket medför mindre omsättning i systemet. Detta tenderar att förbättra tillförlitligheten, eftersom mindre av tjänsten påverkas av alla problem som introduceras under uppgraderingen. Fler uppgraderingsdomäner innebär också att du behöver mindre tillgänglig buffert på andra noder för att hantera effekten av uppgraderingen. 

Om du till exempel har fem uppgraderingsdomäner hanterar noderna i var och en ungefär 20 procent av trafiken. Om du behöver ta bort uppgraderingsdomänen för en uppgradering måste den belastningen vanligtvis gå någonstans. Eftersom du har fyra återstående uppgraderingsdomäner måste var och en ha plats för cirka 5 procent av den totala trafiken. Fler uppgraderingsdomäner innebär att du behöver mindre buffert på noderna i klustret. 

Överväg om du hade 10 uppgraderingsdomäner istället. I så fall skulle varje uppgraderingsdomän hantera endast cirka 10 procent av den totala trafiken. När en uppgradering går genom klustret måste varje domän ha plats för endast cirka 1,1 procent av den totala trafiken. Fler uppgraderingsdomäner gör det i allmänhet möjligt att köra noderna vid högre användning, eftersom du behöver mindre reserverad kapacitet. Detsamma gäller för feldomäner.  

Nackdelen med att ha många uppgraderingsdomäner är att uppgraderingar tenderar att ta längre tid. Service Fabric väntar en kort period efter att en uppgraderingsdomän har slutförts och utför kontroller innan du börjar uppgradera nästa. Dessa fördröjningar gör det möjligt att identifiera problem som introducerades av uppgraderingen innan uppgraderingen fortsätter. Avvägningen är acceptabel eftersom den förhindrar att dåliga ändringar påverkar för mycket av tjänsten åt gången.

Förekomsten av för få uppgraderingsdomäner har många negativa biverkningar. Varje uppgraderingsdomän är nere och uppgraderas, men en stor del av din totala kapacitet är inte tillgänglig. Om du till exempel bara har tre uppgraderingsdomäner tar du ner ungefär en tredjedel av din totala tjänst- eller klusterkapacitet åt gången. Att ha så mycket av din tjänst nere på en gång är inte önskvärt eftersom du behöver tillräckligt med kapacitet i resten av klustret för att hantera arbetsbelastningen. Om du underhåller bufferten innebär det att dessa noder läses in mindre än annars. Detta ökar kostnaden för att köra din tjänst.

Det finns ingen verklig gräns för det totala antalet fel- eller uppgraderingsdomäner i en miljö eller begränsningar för hur de överlappar varandra. Men det finns gemensamma mönster:

- Feldomäner och uppgraderingsdomäner mappade 1:1
- En uppgraderingsdomän per nod (fysisk eller virtuell OS-instans)
- En "randig" eller "matris"-modell där feldomäner och uppgraderingsdomäner bildar en matris med maskiner som vanligtvis kör längs diagonalerna

<center>

![Layouter för fel- och uppgraderingsdomäner][Image4]
</center>

Det finns inget bästa svar på vilken layout du ska välja. Var och en har för-och nackdelar. 1FD:1UD-modellen är till exempel enkel att ställa in. Modellen för en uppgraderingsdomän per nodmodell är mest lik vad människor är vana vid. Under uppgraderingar uppdateras varje nod oberoende av varandra. Detta liknar hur små uppsättningar av maskiner har uppgraderats manuellt tidigare.

Den vanligaste modellen är FD/UD-matrisen, där feldomäner och uppgraderingsdomäner bildar en tabell och noder placeras med början längs diagonalen. Det här är den modell som används som standard i Service Fabric-kluster i Azure. För kluster med många noder ser allt ut som ett tätt matrismönster.

> [!NOTE]
> Service Fabric-kluster som finns i Azure stöder inte att ändra standardstrategin. Endast fristående kluster erbjuder den anpassningen.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Fel- och uppgraderingsdomänbegränsningar och resulterande beteende
### <a name="default-approach"></a>Standardmetod
Som standard håller Cluster Resource Manager tjänsterna balanserade över fel- och uppgraderingsdomäner. Detta modelleras som ett [villkor](service-fabric-cluster-resource-manager-management-integration.md). Begränsningen för fel- och uppgraderingsdomäner lyder: "För en viss tjänstpartition bör det aldrig finnas en skillnad som är större än en i antalet tjänstobjekt (tillståndslösa tjänstinstanser eller tillståndskänsliga tjänstrepliker) mellan två domäner på samma hierarkinivå."

Låt oss säga att denna begränsning ger en "maximal skillnad" garanti. Begränsningen för fel- och uppgraderingsdomäner förhindrar vissa drag eller arrangemang som bryter mot regeln.

Anta till exempel att vi har ett kluster med sex noder, konfigurerat med fem feldomäner och fem uppgraderingsdomäner.

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |N1 (på andra) sätt | | | | |
| **UD1 (UD1)** |N6 (på andra) |N2 (på andra) sätt | | | |
| **Ud2** | | |N3 (på andra) | | |
| **Ud3** | | | |N4 (på andra) | |
| **UD4 (UD4)** | | | | |N5 (på andra) |

Anta nu att vi skapar en tjänst med ett **TargetReplicaSetSize** -värde (eller, för en tillståndslös tjänst, **InstanceCount)** på fem. Replikerna landar på N1-N5. I själva verket är N6 aldrig används oavsett hur många tjänster som denna du skapar. Men varför? Låt oss titta på skillnaden mellan den aktuella layouten och vad som skulle hända om N6 väljs.

Här är layouten vi fick och det totala antalet repliker per fel och uppgradering domän:

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 | UDTotal (UDTotal) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |R1 | | | | |1 |
| **UD1 (UD1)** | |R2 | | | |1 |
| **Ud2** | | |R3 (på andra) | | |1 |
| **Ud3** | | | |R4 (på andra sätt) | |1 |
| **UD4 (UD4)** | | | | |R5 (på andra) |1 |
| **FDTotal ()** |1 |1 |1 |1 |1 |- |

Den här layouten balanseras i termer av noder per feldomän och uppgraderingsdomän. Det är också balanserat när det gäller antalet repliker per fel och uppgraderingsdomän. Varje domän har samma antal noder och samma antal repliker.

Nu ska vi titta på vad som skulle hända om vi hade använt N6 istället för N2. Hur skulle replikerna distribueras då?

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 | UDTotal (UDTotal) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |R1 | | | | |1 |
| **UD1 (UD1)** |R5 (på andra) | | | | |1 |
| **Ud2** | | |R2 | | |1 |
| **Ud3** | | | |R3 (på andra) | |1 |
| **UD4 (UD4)** | | | | |R4 (på andra sätt) |1 |
| **FDTotal ()** |2 |0 |1 |1 |1 |- |

Den här layouten bryter mot vår definition av garantin för "maximal skillnad" för feldomänbegränsningen. FD0 har två repliker, medan FD1 har noll. Skillnaden mellan FD0 och FD1 är totalt två, vilket är större än den maximala skillnaden på en. Eftersom begränsningen har brutits tillåter inte Klusterresurshanteraren den här ordningen. På samma sätt, om vi valde N2 och N6 (i stället för N1 och N2), skulle vi få:

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 | UDTotal (UDTotal) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** | | | | | |0 |
| **UD1 (UD1)** |R5 (på andra) |R1 | | | |2 |
| **Ud2** | | |R2 | | |1 |
| **Ud3** | | | |R3 (på andra) | |1 |
| **UD4 (UD4)** | | | | |R4 (på andra sätt) |1 |
| **FDTotal ()** |1 |1 |1 |1 |1 |- |

Den här layouten är balanserad när det gäller feldomäner. Men nu är det bryter mot begränsningen uppgraderingsdomän, eftersom UD0 har noll repliker och UD1 har två. Den här layouten är också ogiltig och plockas inte av Cluster Resource Manager.

Den här metoden för distribution av tillståndskänsliga repliker eller tillståndslösa instanser ger bästa möjliga feltolerans. Om en domän går ner går det minimala antalet repliker/instanser förlorade. 

Å andra sidan kan den här metoden vara för strikt och inte tillåta klustret att använda alla resurser. För vissa klusterkonfigurationer kan vissa noder inte användas. Detta kan leda till att Service Fabric inte placerar dina tjänster, vilket resulterar i varningsmeddelanden. I föregående exempel kan vissa klusternoder inte användas (N6 i exemplet). Även om du har lagt till noder i klustret (N7-N10) placeras repliker/instanser endast på N1–N5 på grund av begränsningar för fel- och uppgraderingsdomäner. 

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |N1 (på andra) sätt | | | |N10 (på andra) |
| **UD1 (UD1)** |N6 (på andra) |N2 (på andra) sätt | | | |
| **Ud2** | |N7 (på andra) sätt |N3 (på andra) | | |
| **Ud3** | | |N8 (på andra) |N4 (på andra) | |
| **UD4 (UD4)** | | | |N9 |N5 (på andra) |



### <a name="alternative-approach"></a>Alternativt tillvägagångssätt

Cluster Resource Manager stöder en annan version av begränsningen för fel- och uppgraderingsdomäner. Det möjliggör placering samtidigt som man garanterar en lägsta säkerhetsnivå. Den alternativa begränsningen kan anges på följande sätt: "För en viss tjänstpartition bör replikdistribution över domäner se till att partitionen inte drabbas av kvorumförlust." Låt oss säga att denna begränsning ger en "kvorum säker" garanti. 

> [!NOTE]
> För en tillståndskänslig tjänst definierar vi *kvorumförlust* i en situation när en majoritet av partitionsrepliker är nere samtidigt. Om **TargetReplicaSetSize** till exempel är fem representerar en uppsättning med tre repliker kvorum. Om **TargetReplicaSetSize** är sex krävs fyra repliker för kvorum. I båda fallen kan inte fler än två repliker vara nere samtidigt om partitionen vill fortsätta fungera normalt. 
>
> För en statslös tjänst, det finns inget sådant som *kvorum förlust*. Statslösa tjänster fortsätter att fungera normalt även om en majoritet av fallen går ner samtidigt. Så vi kommer att fokusera på stateful tjänster i resten av den här artikeln.
>

Låt oss gå tillbaka till föregående exempel. Med den "kvorumsäkra" versionen av begränsningen skulle alla tre layouterna vara giltiga. Även om FD0 misslyckades i den andra layouten eller UD1 misslyckades i den tredje layouten, skulle partitionen fortfarande ha kvorum. (En majoritet av replikerna skulle fortfarande vara upp.) Med den här versionen av begränsningen kan N6 nästan alltid användas.

Den "kvorumsäkra" metoden ger mer flexibilitet än "maximal skillnad"-metoden. Anledningen är att det är lättare att hitta replikdistributioner som är giltiga i nästan alla klustertopologi. Detta tillvägagångssätt kan dock inte garantera de bästa feltolerans egenskaper eftersom vissa fel är värre än andra. 

I värsta fall kan en majoritet av replikerna gå förlorade med fel på en domän och ytterligare en replik. I stället för att tre fel krävs för att förlora kvorum med fem repliker eller instanser kan du nu förlora en majoritet med bara två fel. 

### <a name="adaptive-approach"></a>Adaptiv strategi
Eftersom båda metoderna har styrkor och svagheter har vi infört en adaptiv strategi som kombinerar dessa två strategier.

> [!NOTE]
> Detta är standardbeteendet som börjar med Service Fabric version 6.2. 
> 
> Den adaptiva metoden använder "maximal skillnad" logik som standard och växlar till "kvorum säker" logik endast när det behövs. Cluster Resource Manager räknar automatiskt ut vilken strategi som är nödvändig genom att titta på hur klustret och tjänsterna är konfigurerade.
> 
> Klusterresurshanteraren bör använda logiken "kvorumbaserad" för en tjänst som båda dessa villkor är sanna:
>
> * **TargetReplicaSetSize** för tjänsten är jämnt delbar av antalet feldomäner och antalet uppgraderingsdomäner.
> * Antalet noder är mindre än eller lika med antalet feldomäner multiplicerat med antalet uppgraderingsdomäner.
>
> Tänk på att Cluster Resource Manager använder den här metoden för både tillståndslösa och tillståndskänsliga tjänster, även om kvorumförlust inte är relevant för tillståndslösa tjänster.

Låt oss gå tillbaka till föregående exempel och anta att ett kluster nu har åtta noder. Klustret är fortfarande konfigurerat med fem feldomäner och fem uppgraderingsdomäner, och **värdet för TargetReplicaSetSize** för en tjänst som finns på klustret är fortfarande fem. 

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |N1 (på andra) sätt | | | | |
| **UD1 (UD1)** |N6 (på andra) |N2 (på andra) sätt | | | |
| **Ud2** | |N7 (på andra) sätt |N3 (på andra) | | |
| **Ud3** | | |N8 (på andra) |N4 (på andra) | |
| **UD4 (UD4)** | | | | |N5 (på andra) |

Eftersom alla nödvändiga villkor är uppfyllda använder Cluster Resource Manager logiken "kvorumbaserad" för att distribuera tjänsten. Detta möjliggör användning av N6-N8. En möjlig tjänstdistribution i det här fallet kan se ut så här:

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 | UDTotal (UDTotal) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |R1 | | | | |1 |
| **UD1 (UD1)** |R2 | | | | |1 |
| **Ud2** | |R3 (på andra) |R4 (på andra sätt) | | |2 |
| **Ud3** | | | | | |0 |
| **UD4 (UD4)** | | | | |R5 (på andra) |1 |
| **FDTotal ()** |2 |1 |1 |0 |1 |- |

Om tjänstens **TargetReplicaSetSize-värde** reduceras till fyra (till exempel) kommer Cluster Resource Manager att märka den ändringen. Den kommer att återupptas med hjälp av logiken "maximal skillnad" eftersom **TargetReplicaSetSize** inte längre kan divideras med antalet feldomäner och uppgraderingsdomäner. Som ett resultat kommer vissa replikförflyttningar att uppstå för att distribuera de återstående fyra replikerna på noderna N1-N5. På så sätt bryts inte den "maximala skillnaden" versionen av feldomänen och uppgraderingsdomänlogiken. 

Om värdet **TargetReplicaSetSize** är fem och N1 tas bort från klustret i föregående layout blir antalet uppgraderingsdomäner lika med fyra. Återigen börjar Cluster Resource Manager använda "maximal skillnad" logik eftersom antalet uppgraderingsdomäner inte jämnt dela upp tjänstens **TargetReplicaSetSize** värde längre. Därför måste replik R1, när den byggs igen, landa på N4 så att begränsningen för fel- och uppgraderingsdomänen inte bryts.

|  | FD0 (på andra) | FD1 | FD2 | FD3 | FD4 | UDTotal (UDTotal) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (på andra)** |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |
| **UD1 (UD1)** |R2 | | | | |1 |
| **Ud2** | |R3 (på andra) |R4 (på andra sätt) | | |2 |
| **Ud3** | | | |R1 | |1 |
| **UD4 (UD4)** | | | | |R5 (på andra) |1 |
| **FDTotal ()** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurera fel- och uppgraderingsdomäner
I Azure-värdförda Tjänst Fabric-distributioner definieras feldomäner och uppgraderingsdomäner automatiskt. Service Fabric hämtar och använder miljöinformation från Azure.

Om du skapar ett eget kluster (eller vill köra en viss topologi under utveckling) kan du själv ange feldomän- och uppgraderingsdomäninformationen. I det här exemplet definierar vi ett lokalt utvecklingskluster för nio noder som sträcker sig över tre datacenter (var och en med tre rack). Det här klustret har också tre uppgraderingsdomäner randiga över dessa tre datacenter. Här är ett exempel på konfigurationen i ClusterManifest.xml:

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

I det här exemplet används ClusterConfig.json för fristående distributioner:

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
> När du definierar kluster via Azure Resource Manager tilldelar Azure feldomäner och uppgraderingsdomäner. Så definitionen av nodtyper och skalningsuppsättningar för virtuella datorer i din Azure Resource Manager-mall innehåller inte information om feldomän eller uppgraderingsdomän.
>

## <a name="node-properties-and-placement-constraints"></a>Nodegenskaper och placeringsbegränsningar
Ibland (i själva verket, för det mesta) du vill se till att vissa arbetsbelastningar körs endast på vissa typer av noder i klustret. Vissa arbetsbelastningar kan till exempel kräva GPU:er eller SSD:er och andra kanske inte gör det. 

Ett bra exempel på att rikta maskinvara till särskilda arbetsbelastningar är nästan alla n-nivåarkitekturer. Vissa maskiner fungerar som den främre delen eller API-servering sidan av programmet och är utsatta för klienter eller Internet. Olika datorer, ofta med olika maskinvaruresurser, hanterar arbetet i beräknings- eller lagringslagren. Dessa är vanligtvis _inte_ direkt utsatta för kunder eller Internet. 

Service Fabric förväntar sig att vissa arbetsbelastningar i vissa fall kan behöva köras på vissa maskinvarukonfigurationer. Ett exempel:

* Ett befintligt n-nivåprogram har "lyfts och flyttats" till en Service Fabric-miljö.
* En arbetsbelastning måste köras på specifik maskinvara av prestanda-, skalnings- eller säkerhetsisoleringsskäl.
* En arbetsbelastning bör isoleras från andra arbetsbelastningar av princip- eller resursförbrukningsskäl.

Service Fabric innehåller taggar som du kan använda för noder för att stödja den här typen av konfigurationer. Dessa taggar kallas *nodegenskaper*. *Placeringsbegränsningar* är de uttryck som är kopplade till enskilda tjänster som du väljer för en eller flera nodegenskaper. Placeringsbegränsningar definierar var tjänster ska köras. Uppsättningen begränsningar kan utbyggas. Alla nyckel/värdepar kan fungera. 

<center>

![Olika arbetsbelastningar för en klusterlayout][Image5]
</center>

### <a name="built-in-node-properties"></a>Inbyggda nodegenskaper
Service Fabric definierar vissa standardnodegenskaper som kan användas automatiskt så att du inte behöver definiera dem. Standardegenskaperna som definieras vid varje nod är **NodeType** och **NodeName**. 

Du kan till exempel skriva `"(NodeType == NodeType03)"`ett placeringsvillkor som . **NodeType** är en vanlig egenskap. Det är användbart eftersom det motsvarar 1:1 med en typ av en maskin. Varje typ av dator motsvarar en typ av arbetsbelastning i ett traditionellt n-nivåprogram.

<center>

![Placeringsbegränsningar och nodegenskaper][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Syntax för placeringsbegränsningar och nodgenskapssyn 
Värdet som anges i nodegenskapen kan vara en sträng, boolesk eller signerad lång. Satsen på tjänsten kallas ett *placeringsvillkor* eftersom det begränsar var tjänsten kan köras i klustret. Begränsningen kan vara alla booleska uttryck som fungerar på nodegenskaperna i klustret. De giltiga selektorerna i dessa booleskutsatser är:

* Villkorliga kontroller för att skapa särskilda satser:

  | Instruktion | Syntax |
  | --- |:---:|
  | "lika med" | "==" |
  | "inte lika med" | "!=" |
  | "Större än" | ">" |
  | "större än eller lika med" | ">=" |
  | "Mindre än" | "<" |
  | "mindre än eller lika med" | "<=" |

* Booleska satser för gruppering och logiska operationer:

  | Instruktion | Syntax |
  | --- |:---:|
  | "och" | "&&" |
  | "eller" | "&#124;&#124;" |
  | "Inte" | "!" |
  | "grupp som enskilt uttalande" | "()" |

Här är några exempel på grundläggande begränsningssatser:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Endast noder där den övergripande placeringsbegränsningssatsen utvärderas till "True" kan ha tjänsten placerad på den. Noder som inte har en definierad egenskap matchar inte någon placeringsbegränsning som innehåller egenskapen.

Anta att följande nodegenskaper har definierats för en nodtyp i ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

I följande exempel visas nodegenskaper som definierats via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdbaserade kluster. 

> [!NOTE]
> I mallen Azure Resource Manager är nodtypen vanligtvis parameteriserad. Det skulle `"[parameters('vmNodeType1Name')]"` se ut som i stället för NodeType01.
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

Du kan skapa *begränsningar* för tjänstplacering för en tjänst enligt följande:

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

Om alla noder i NodeType01 är giltiga kan du också `"(NodeType == NodeType01)"`markera den nodtypen med villkoret .

En tjänsts placeringsbegränsningar kan uppdateras dynamiskt under körning. Om du behöver kan du flytta runt en tjänst i klustret, lägga till och ta bort krav och så vidare. Service Fabric säkerställer att tjänsten förblir upp och tillgänglig även när dessa typer av ändringar görs.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Placeringsbegränsningar anges för varje namngiven tjänstinstans. Uppdateringar sker alltid i stället för (skriva över) vad som tidigare angavs.

Klusterdefinitionen definierar egenskaperna för en nod. Om du ändrar egenskaperna för en nod krävs en uppgradering till klusterkonfigurationen. Om du uppgraderar egenskaperna för en nod måste varje berörd nod startas om för att rapportera sina nya egenskaper. Service Fabric hanterar dessa rullande uppgraderingar.

## <a name="describing-and-managing-cluster-resources"></a>Beskriva och hantera klusterresurser
Ett av de viktigaste jobben för alla orchestrator är att hantera resursförbrukning i klustret. Hantering av klusterresurser kan innebära ett par olika saker. 

Först finns det se till att maskiner inte överbelastas. Detta innebär att se till att datorer inte kör fler tjänster än de kan hantera. 

För det andra finns det balansering och optimering, som är avgörande för att köra tjänster effektivt. Kostnadseffektiva eller prestandakänsliga tjänsteerbjudanden kan inte tillåta att vissa noder är varma medan andra är kalla. Heta noder leder till resurskonkurrens och dåliga prestanda. Kalla noder representerar bortkastade resurser och ökade kostnader. 

Service Fabric representerar resurser som *mått*. Mått är alla logiska eller fysiska resurser som du vill beskriva för Service Fabric. Exempel på mått är "WorkQueueDepth" eller "MemoryInMb". Information om de fysiska resurser som Service Fabric kan styra på noder finns i [Resursstyrning](service-fabric-resource-governance.md). Information om standardmått som används av Cluster Resource Manager och hur du konfigurerar anpassade mått finns i den [här artikeln](service-fabric-cluster-resource-manager-metrics.md).

Mått skiljer sig från placeringsbegränsningar och nodegenskaper. Nodegenskaper är statiska beskrivningar av själva noderna. Mått beskriver resurser som noder har och som tjänster förbrukar när de körs på en nod. En nodegenskap kan vara **HasSSD** och kan vara inställd på sant eller falskt. Mängden utrymme som är tillgängligt på den SSD och hur mycket som förbrukas av tjänster skulle vara ett mått som "DriveSpaceInMb". 

Precis som för placeringsbegränsningar och nodegenskaper förstår Service Fabric Cluster Resource Manager inte vad namnen på måtten betyder. Måttnamn är bara strängar. Det är en bra idé att deklarera enheter som en del av de måttnamn som du skapar när de kan vara tvetydiga.

## <a name="capacity"></a>Kapacitet
Om du har inaktiverat all *resursbalansering*säkerställer Service Fabric Cluster Resource Manager fortfarande att ingen nod överskrider dess kapacitet. Det går inte att hantera kapacitetsöverskridningar om inte klustret är för fullt eller arbetsbelastningen är större än någon nod. Kapacitet är ett annat *villkor* som Cluster Resource Manager använder för att förstå hur mycket av en resurs en nod har. Återstående kapacitet spåras också för klustret som helhet. 

Både kapaciteten och förbrukningen på servicenivå uttrycks i mått. Måttet kan till exempel vara "ClientConnections" och en nod kan ha kapacitet för "ClientConnections" på 32 768. Andra noder kan ha andra gränser. En tjänst som körs på den noden kan säga att den för närvarande förbrukar 32 256 av måttet "ClientConnections".

Under körning spårar Cluster Resource Manager återstående kapacitet i klustret och på noder. Om du vill spåra kapacitet subtraherar Cluster Resource Manager varje tjänsts användning från en nods kapacitet där tjänsten körs. Med den här informationen kan Cluster Resource Manager ta reda på var du ska placera eller flytta repliker så att noder inte överskrider kapaciteten.

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

Du kan se kapacitet som definierats i klustermanifestet. Här är ett exempel för ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Här är ett exempel på kapaciteter som definierats via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värddamplar: 

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

En tjänsts belastning ändras ofta dynamiskt. Säg att en repliks belastning av "ClientConnections" har ändrats från 1 024 till 2 048. Noden som den kördes på hade då en kapacitet på endast 512 kvar för det måttet. Nu när repliken eller instansens placering är ogiltig, eftersom det inte finns tillräckligt med utrymme på noden. Klusterresurshanteraren måste få tillbaka noden under kapaciteten. Det minskar belastningen på noden som är över kapacitet genom att flytta en eller flera av replikerna eller instanserna från den noden till andra noder. 

Cluster Resource Manager försöker minimera kostnaden för att flytta repliker. Du kan läsa mer om [rörelsekostnader](service-fabric-cluster-resource-manager-movement-cost.md) och om [ombalansering strategier och regler](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Klusterkapacitet
Hur hindrar Service Fabric Cluster Resource Manager det övergripande klustret från att vara för fullt? Med dynamisk belastning, det finns inte mycket den kan göra. Tjänster kan ha sin belastningspik oberoende av åtgärder som Klusterresurshanteraren tar. Som ett resultat kan ditt kluster med gott om utrymme idag vara underpowered om det finns en spik i morgon. 

Kontroller i Cluster Resource Manager hjälper till att förhindra problem. Det första du kan göra är att förhindra att nya arbetsbelastningar skapas som skulle orsaka att klustret blir fullt.

Anta att du skapar en tillståndslös tjänst och att den har en viss belastning kopplad till den. Tjänsten bryr sig om måttet "DiskSpaceInMb". Tjänsten kommer att förbruka fem enheter av "DiskSpaceInMb" för varje instans av tjänsten. Du vill skapa tre instanser av tjänsten. Det innebär att du behöver 15 enheter av "DiskSpaceInMb" för att vara närvarande i klustret för att du ens ska kunna skapa dessa tjänstinstanser.

Cluster Resource Manager beräknar kontinuerligt kapaciteten och förbrukningen för varje mått så att den kan bestämma den återstående kapaciteten i klustret. Om det inte finns tillräckligt med utrymme avvisar Cluster Resource Manager anropet för att skapa en tjänst.

Eftersom kravet bara är att 15 enheter ska vara tillgängliga kan du allokera det här utrymmet på många olika sätt. Det kan till exempel finnas en återstående kapacitetsenhet på 15 olika noder eller tre återstående kapacitetsenheter på fem olika noder. Om Cluster Resource Manager kan ordna om saker så att det finns fem enheter tillgängliga på tre noder placeras tjänsten. Det är vanligtvis möjligt att ordna om klustret om inte klustret är nästan fullt eller om de befintliga tjänsterna inte kan konsolideras av någon anledning.

## <a name="buffered-capacity"></a>Buffrad kapacitet
Buffrad kapacitet är en annan funktion i Cluster Resource Manager. Det tillåter reservation av en del av den totala nodkapaciteten. Den här kapacitetsbufferten används endast för att placera tjänster under uppgraderingar och nodfel. 

Buffrad kapacitet anges globalt per mått för alla noder. Värdet som du väljer för den reserverade kapaciteten är en funktion av antalet fel- och uppgraderingsdomäner som du har i klustret. Fler fel- och uppgraderingsdomäner innebär att du kan välja ett lägre nummer för din buffrade kapacitet. Om du har fler domäner kan du förvänta dig att mindre mängder av klustret inte är tillgängligt under uppgraderingar och fel. Det är bara logiskt att ange buffrad kapacitet om du också har angett nodkapaciteten för ett mått.

Här är ett exempel på hur du anger buffrad kapacitet i ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Här är ett exempel på hur du anger buffrad kapacitet via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdbaserade kluster:

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

Skapandet av nya tjänster misslyckas när klustret har på buffrad kapacitet för ett mått. Förhindra skapandet av nya tjänster för att bevara bufferten säkerställer att uppgraderingar och fel inte orsakar noder att gå över kapacitet. Buffrad kapacitet är valfri, men vi rekommenderar den i alla kluster som definierar en kapacitet för ett mått.

Klusterresurshanteraren visar den här inläsningsinformationen. För varje mått innehåller den här informationen: 
- Inställningarna för buffrad kapacitet.
- Den totala kapaciteten.
- Den aktuella förbrukningen.
- Om varje mått anses vara balanserat eller inte.
- Statistik om standardavvikelsen.
- De noder som har mest och minst belastning.  
  
Följande kod visar ett exempel på utdata:

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
* Information om arkitektur och informationsflöde i Cluster Resource Manager finns i [arkitekturöversikt för Klusterresurshanteraren](service-fabric-cluster-resource-manager-architecture.md).
* Definiera defragmenteringsmått är ett sätt att konsolidera belastningen på noder i stället för att sprida ut den. Mer information om hur du konfigurerar defragmentering finns i [Defragmentering av mått och inläsning i Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Mer information om hur Cluster Resource Manager hanterar och balanserar belastning i klustret finns i [Balansera ditt Service Fabric-kluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
