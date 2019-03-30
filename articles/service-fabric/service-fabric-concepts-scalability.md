---
title: Skalbarhet för Service Fabric-tjänster | Microsoft Docs
description: Beskriver hur du skalar Service Fabric-tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 14a7389fe562b5f3206b81411d2224257051c636
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666655"
---
# <a name="scaling-in-service-fabric"></a>Skalning i Service Fabric
Azure Service Fabric gör det enkelt att bygga skalbara program genom att hantera tjänster, partitionerna och replikerna på noderna i ett kluster. Många arbetsbelastningar på samma maskinvara ger maximala användning, men ger även flexibilitet när det gäller hur du väljer att skala dina arbetsbelastningar. Den här videon på Channel 9 beskrivs hur du kan skapa skalbara mikrotjänstprogram:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalning i Service Fabric utförs flera olika sätt:

1. Skalning genom att skapa eller ta bort instanser för tillståndslös tjänst
2. Skalning genom att skapa eller ta bort ny med namnet tjänster
3. Skalning genom att skapa eller ta bort nya namngivna instanser av programmet
4. Skalning genom att använda partitionerade tjänster
5. Skalning genom att lägga till och ta bort noder från klustret 
6. Skalning med Cluster Resource Manager-mått

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalning genom att skapa eller ta bort instanser för tillståndslös tjänst
Ett av de enklaste sätten att skala i Service Fabric fungerar med tillståndslösa tjänster. När du skapar en tillståndslös tjänst kan du prova att definiera en `InstanceCount`. `InstanceCount` definierar hur många exemplar av tjänstens kod som körs skapas när tjänsten startas. Anta exempelvis att det finns 100 noder i klustret. Även anta att en tjänst har skapats med en `InstanceCount` av 10. Under körning, dessa 10 körs kopior av koden kan bli alla upptagen (eller kan inte vara tillräckligt upptagen). Ett sätt att skala arbetsbelastningen är att ändra antalet instanser. Vissa typer av övervakning eller hantering av kod kan till exempel ändra befintliga antalet instanser till mellan 50 och 5, beroende på om arbetsbelastningen behöver skala in eller ut utifrån belastningen. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Med hjälp av dynamisk Instansantalet
Mer specifikt för tillståndslösa tjänster erbjuder Service Fabric ett automatiskt sätt att ändra instansantalet. På så sätt kan tjänsten att skala dynamiskt med antalet noder som är tillgängliga. Sätt att välja det här beteendet är att ange instansantalet = -1. InstanceCount = -1 är en instruktion till Service Fabric med texten ”kör den tillståndslösa tjänsten på varje nod”. Om antalet noder ändras, ändras Service Fabric automatiskt instansantalet att matcha att säkerställa att tjänsten körs på alla giltiga noder. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalning genom att skapa eller ta bort ny med namnet tjänster
En namngiven tjänst-instans är en specifik instans av en typ av tjänst (se [Service Fabric programmets hela livscykel](service-fabric-application-lifecycle.md)) inom vissa namngivna programinstans i klustret. 

Den nya tjänstinstanser kan skapas (eller tas bort) som blir mer eller mindre upptagen-tjänster. På så sätt kan begäranden sprids över flera instanser av tjänsten, vanligtvis så att belastningen på befintliga tjänster att minska. När du skapar tjänster, placerar tjänsterna i klustret på ett sätt som är distribuerade i Service Fabric Cluster Resource Manager. De exakta beslut regleras av den [mått](service-fabric-cluster-resource-manager-metrics.md) i klustret och andra placeringsregler. Tjänster kan skapa flera olika sätt, men de vanligaste är antingen via administrativa åtgärder som att någon anropa [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), eller genom att anropa kod [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` kan även vara anropas från andra tjänster som körs i klustret.

Skapa tjänster dynamiskt kan användas i alla typer av scenarier och är ett vanligt mönster. Anta exempelvis att en tillståndskänslig tjänst som representerar ett visst arbetsflöde. Anrop som representerar arbete ska visas på tjänsten och den här tjänsten kommer att köra stegen för att den arbetsflödet och registrera pågår. 

Hur skulle du skapa skalans viss tjänst? Tjänsten kan vara flera innehavare i någon form och acceptera anrop och sätta igång stegen för många olika instanser av samma arbetsflöde på samma gång. Dock som kan se koden mer komplexa, eftersom nu den har bekymra dig om många olika instanser av samma arbetsflöde, allt på olika faser och från olika kunder. Dessutom problemet hantering av flera olika arbetsflöden på samma gång inte skala. Det beror på att någon gång den här tjänsten kommer att använda för många resurser för att få plats på en viss dator. Många tjänster som inte har skapats för det här mönstret uppstå i första hand också problem på grund av vissa inbyggda flaskhals eller minskningen i sin kod. Dessa typer av problem kan tjänsten inte fungerar när antalet samtidiga arbetsflöden som den för att spåra hämtar större.  

En lösning är att skapa en instans av den här tjänsten för alla olika instanser av arbetsflödet som du vill spåra. Detta är ett bra mönster och fungerar om tjänsten är tillståndslösa eller tillståndskänsliga. Det här mönstret ska fungera, har vanligtvis en annan tjänst som fungerar som en ”arbetsbelastning Manager-tjänst”. Jobb för den här tjänsten är att ta emot begäranden och för att vidarebefordra dessa begäranden till andra tjänster. Chefen kan skapa en instans av tjänsten arbetsbelastning dynamiskt när den får meddelandet och sedan vidarebefordra begäranden till dessa tjänster. Manager-tjänsten kan också få återanrop när dess jobbet har slutförts med en viss arbetsflödestjänst. När manager tar emot dessa återanrop kan det ta bort instansen av arbetsflödestjänsten eller lämna den om fler anrop förväntades. 

Avancerade versioner av den här typen av manager kan även skapa pooler med de tjänster som den hanterar. Poolen säkerställer att när en ny begäran kommer den inte behöver vänta på att tjänsten ska starta. I stället kan chefen bara välja en arbetsflödestjänst som inte är för närvarande upptagen av poolen eller dirigera slumpmässigt. Att hålla en pool av tillgängliga tjänster gör nya begäranden om hantering av snabbare, eftersom det är mindre troligt att begäran måste vänta tills en ny tjänst för att vara kunde. Det är snabb, men inte kostnadsfri eller omedelbara att skapa nya tjänster. Poolen kan minimera den tid som begäran måste vänta innan servad. Ofta visas den här manager och poolen mönstret när svarstider viktigast. På begäran och att tjänsten i bakgrunden och _sedan_ förmedlar är också ett populärt manager-mönster som skapar och tar bort tjänster baserat på vissa spårning av mängden arbete tjänsten för närvarande har väntande . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalning genom att skapa eller ta bort nya namngivna instanser av programmet
Skapa och ta bort hela programinstanser liknar mönstret för att skapa och ta bort tjänster. Det finns vissa manager-tjänsten som gör beslutet baserat på de förfrågningar som den ser och informationen som den tar emot från andra tjänster i klustret för det här mönstret. 

När ska skapa en ny namngiven programinstans användas istället för att skapa en ny tjänsten-instanser i ett befintligt program? Det finns några fall:

  * Instansen för nya programmet är för en kund vars kod måste köras under en viss identitet eller säkerhetsinställningar.
    * Service Fabric kan du definiera olika kodpaket ska köras under specifika identiteter. För att starta samma kodpaketet under olika identiteter, måste aktiveringarna kan förekomma i olika programinstanser. Tänk dig ett fall där du har en befintlig kunds arbetsbelastningar som distribuerats. Dessa kan köras under en viss identitet så att du kan övervaka och styr åtkomsten till andra resurser, till exempel remote databaser eller andra system. I det här fallet när en ny kund registrerar sig, vill du förmodligen inte aktivera sin kod i samma kontext (processområdet). När du gick gör det det svårare för koden för tjänsten att fungera inom ramen för en viss identitet. Du måste vanligtvis ha mer säkerhet, isolering och identity management-kod. Istället för att använda olika instanser av tjänsten inom samma programinstansen som heter och kan därför samma process utrymme kan du använda olika namngivna instanser i Service Fabric-program. Detta gör det enklare att definiera olika identitet sammanhang.
  * Instansen för nya programmet fungerar också som en konfiguration
    * Som standard körs alla förekomster av en viss tjänst-typ i en programinstans namngiven tjänst i samma process på en viss nod. Det innebär att även om du kan konfigurera varje tjänstinstans på olika sätt, detta så är komplicerad. Tjänsterna måste ha vissa token som de använder för att leta upp sina config inom ett konfigurationspaket. Detta är vanligtvis bara tjänstens namn. Detta fungerar bra, men det kombinerar konfigurationen att namnen på enskilda namngivna instanser av tjänsten inom den programinstansen. Detta kan vara förvirrande och svårhanterligt eftersom konfigurationen är normalt en design tid artefakt med specifika värden för program-instans. Att skapa fler tjänster alltid innebär mer programuppgraderingar att ändra informationen i config-paket eller för att distribuera nya så att de nya tjänsterna kan slå upp sina specifik information. Det är ofta lättare att skapa en helt ny namngiven programinstans. Du kan sedan använda applikationsparametrarna att ställa in konfigurationen som krävs för tjänsterna. Det här sättet som alla tjänster som är äldre än som heter programinstans kan ärva inställningar för aktuell konfiguration. Till exempel i stället för att en enda konfigurationsfil med de inställningar och anpassningar för varje kund, till exempel hemligheter eller per kund-resursgränser måste du i stället en annat program-instans för varje kund med de här inställningarna åsidosätts. 
  * Det nya programmet fungerar som en gräns för uppgradering
    * I Service Fabric fungerar olika namngivna programinstanser som gränser för uppgradering. En uppgradering av en namngiven programinstans påverkar inte den kod som kör en annan namngiven programinstans. Olika program ska få kör olika versioner av samma kod på samma noder. Det kan vara en faktor när du behöver göra en skalning beslut eftersom du kan välja om den nya koden bör följa samma uppgraderingar som en annan tjänst eller inte. Anta exempelvis att ett anrop når manager-tjänsten som ansvarar för att skala en viss kund arbetsbelastningar genom att skapa och ta bort tjänster dynamiskt. I det här fallet men anropet är för en arbetsbelastning som är associerade med en _nya_ kund. De flesta kunder som är isolerade från varandra inte bara på grund av säkerhet och konfiguration som angavs tidigare, men eftersom det ger mer flexibilitet i fråga kör specifika versioner av programvaran och välja när de uppgraderas. Du kan också skapa en ny instans av programmet och skapa tjänsten det bara att partitionera mängden tjänster som alla en uppgradering kommer touch. Separat programinstanser ger större precision när du gör programuppgraderingar och även aktivera A / B-testning och blå/grön distributioner. 
  * Instansen för befintliga programmet är full
    * I Service Fabric [programkapacitet](service-fabric-cluster-resource-manager-application-groups.md) är ett begrepp som du kan använda för att styra mängden resurser som är tillgängliga för viss programinstanser. Du kan till exempel bestämma att en viss tjänst måste ha en annan instans som skapats för att skala. Den här programinstans är dock kapacitet för ett visst mått. Om den här specifik kund eller en arbetsbelastning bör fortfarande att få fler resurser, kan sedan antingen öka befintliga kapaciteten för programmet eller skapa ett nytt program. 

## <a name="scaling-at-the-partition-level"></a>Skala på nivån partition
Service Fabric har stöd för partitionering. Partitionering delar upp en tjänst i flera logiska och fysiska avsnitt som fungerar oberoende av varandra. Detta är användbart med tillståndskänsliga tjänster, eftersom ingen har angetts av repliker har att hantera alla anrop och ändra alla tillståndet på en gång. Den [partitionering översikt](service-fabric-concepts-partitioning.md) innehåller information om vilka typer av partitioneringsscheman som stöds. Repliker för varje partition är fördelade på noderna i ett kluster, distribuera tjänstens belastning och se till att varken tjänsten som helhet eller en partition har en enskild felpunkt. 

Överväg att en tjänst som använder ett ranged partitioneringsschema med en låg nyckel 0, en hög nyckel 99 och ett partitionsantal på 4. I ett kluster med tre noder, kan tjänsten placeras med fyra repliker som delar resurser på varje nod som visas här:

<center>

![Partitionslayout med tre noder](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Om du ökar antalet noder, flyttar Service Fabric några av de befintliga replikerna med det. Exempelvis kan hämta vi anta att antalet noder ökar till fyra och replikerna om. Nu har tjänsten nu tre replikerna som körs på varje nod, var och en som hör till olika partitioner. Detta ger bättre resursutnyttjande eftersom den nya noden inte kalla. Vanligtvis förbättrar det också prestanda eftersom varje tjänst har fler resurser som är tillgängliga för den.

<center>

![Partitionslayout med fyra noder](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalning med hjälp av Service Fabric Cluster Resource Manager och mått
[Mått](service-fabric-cluster-resource-manager-metrics.md) är hur tjänster express deras användning av databasresurser till Service Fabric. Med hjälp av mätvärden får Klusterresurshanteraren möjlighet att ordna om och optimera layouten för klustret. Exempelvis kan det finnas tillräckligt med resurser i klustret, men de kan inte allokeras till de tjänster som för närvarande utför arbete. Med hjälp av mätvärden kan Cluster Resource Manager att ordna om klustret för att säkerställa att tjänster har åtkomst till de tillgängliga resurserna. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalning genom att lägga till och ta bort noder från klustret 
Ett annat alternativ för skalning med Service Fabric är att ändra storlek på klustret. Ändra storlek på klustret innebär att lägga till eller ta bort noder för en eller flera nodtyperna i klustret. Tänk dig ett fall där alla noder i klustret är frekvent. Det innebär att klustrets resurser är nästan alla förbrukas. I det här fallet, lägga till fler noder i klustret är det bästa sättet att skala. När de nya noderna ansluta till klustret flyttar Service Fabric Cluster Resource Manager tjänster, vilket resulterar i mindre totala belastningen på de befintliga noderna. För tillståndslösa tjänster med instansantal =-1, mer service instanser skapas automatiskt. På så sätt kan vissa anrop att flytta från de befintliga noderna till de nya noderna. 

Mer information finns i [skalning av klustret](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Färdigställa allt
Låt oss ta alla idéer som vi nämnt här och kommunicera via ett exempel. Överväg följande tjänst: du försöker skapa en tjänst som fungerar som en adressbok, håller att namn och kontaktinformation. 

Höger har direkt du en massa frågor som rör skala: Hur många användare ska du ha? Hur många kontakter lagrar varje användare? När du är att ställa upp tjänsten för första gången är svårt att försöka ta detta allt ut. Vi antar att du tänker gå med en enda statisk tjänst med en specifik partition-uppräkning. Konsekvenserna av att välja antalet fel partitioner kan orsaka att du har problem med skala senare. På samma sätt, även om du väljer rätt antal inte kanske du har all information du behöver. Exempel: du måste också bestämma storleken på klustret direkt, både vad gäller antalet noder och deras storlek. Det är ofta svårt att förutse hur många resurser som en tjänst ska använda under livslängden. Det kan också vara svårt att veta förväg trafikmönstret som faktiskt ser tjänsten. Exempelvis kanske personer lägga till och ta bort deras kontakter endast direkt på morgonen, eller kanske det är jämnt under loppet av dagen. Baserat på detta kan du behöva skala ut och in dynamiskt. Kanske du kan lära dig att förutsäga när du kommer att behöva skalas ut och in, men oavsett hur du förmodligen kommer att behöva att reagera på förändrade resursförbrukning av din tjänst. Det kan innebära att ändra storlek på klustret för att tillhandahålla mer resurser när organisera om användning av befintliga resurser inte är tillräckligt. 

Men varför även prova att välja ett enda partitionsschema ut för alla användare? Varför begränsa dig till en tjänst och en statisk kluster? Den verkliga situationen är vanligtvis mer dynamiskt. 

När du skapar för skalning, Överväg följande dynamiska mönster. Du kan behöva anpassa det till din situation:

1. Skapa en ”manager-tjänst” istället för att försöka att välja ett partitioneringsschema för alla direkt.
2. Jobb för manager-tjänsten är att titta på kundinformation när de registrerar sig för din tjänst. Beroende på den här informationen manager-tjänsten för att skapa en instans av din _faktiska_ kontakta lagringstjänsten _bara för den aktuella kunden_. Om de kräver specifik konfiguration, isolering eller uppgraderingar, kan du också välja att sätta upp en programinstans för kunden. 

Den här skapas dynamiskt mönstret många fördelar:

  - Du försöker inte gissa rätt partitioner för alla användare direkt eller skapa en enskild tjänst som är oändligt skalbar allt på egen hand. 
  - Olika användare inte har samma partitioner, replikantal, placeringsbegränsningar, mått, standard belastning, tjänstnamn, dns-inställningarna, eller någon av de andra egenskaperna som anges på tjänst- eller nivå. 
  - Du får ytterligare data segmentering. Varje kund har sin egen kopia av tjänsten
    - Varje kundtjänst kan konfigureras på olika sätt och beviljas fler eller färre resurser med fler eller färre partitioner eller repliker efter behov baserat på deras förväntade skala.
      - Till exempel anta att kunden betald ”guld” nivå - kunde vara flera repliker eller antalet större partitioner och potentiellt resurser dedikerade till sina tjänster via mått- och kapaciteter.
      - Eller säga de angivna information som anger hur många kontakter som de behövs var ”liten” - de får endast några partitioner eller kan även lägga till en delad tjänst-pool med andra kunder.
  - Du kör inte en massa en tjänstinstans eller repliker under tiden du väntar för kunder att visas
  - Om en kund någonsin lämnar är sedan ta bort sin information från din tjänst lika enkelt som att ha manager ta bort den tjänst eller program som det skapats.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

* [Tillgängligheten för Service Fabric-tjänster](service-fabric-availability-services.md)
* [Partitionera Service Fabric-tjänster](service-fabric-concepts-partitioning.md)
