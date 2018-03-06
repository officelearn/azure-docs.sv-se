---
title: "Skalbarheten för Service Fabric-tjänster | Microsoft Docs"
description: "Beskriver hur du skalar Service Fabric-tjänster"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dc89bda31af35e4c7eb0f2255db301b39ac05eb
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="scaling-in-service-fabric"></a>Skalning i Service Fabric
Azure Service Fabric gör det enkelt att skapa skalbara program genom att hantera tjänster, partitioner och repliker på noderna i klustret. Många arbetsbelastningar som körs på samma maskinvara kan maximalt resursutnyttjande, men ger även flexibilitet i hur du väljer att skala din arbetsbelastning. 

Skalningen i Service Fabric ska ske på flera olika sätt:

1. Skalning genom att skapa eller ta bort instanser av tillståndslösa tjänsten
2. Skalning genom att skapa eller ta bort nya heter tjänster
3. Skalning genom att skapa eller ta bort nya namngivna instanser av programmet
4. Skalning med partitionerade tjänster
5. Skalning genom att lägga till och ta bort noder från klustret 
6. Skalning med hjälp av hanteraren för filserverresurser mått

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalning genom att skapa eller ta bort instanser av tillståndslösa tjänsten
Ett av de enklaste sätten att skala inom Service Fabric fungerar med tillståndslösa tjänster. När du skapar en tillståndslös tjänst kan du prova att definiera en `InstanceCount`. `InstanceCount`definierar hur många kopior av den tjänsten kod som körs skapas när tjänsten startas. Anta exempelvis att det finns 100 noder i klustret. Även anta att en tjänst har skapats med en `InstanceCount` 10. Under körning kan de 10 körs kopiorna av koden kan bli alla upptagen (eller kan inte vara tillräckligt upptagen). Ett sätt att skala att arbetsbelastningar är att ändra antalet instanser. Vissa kodavsnitt övervakning eller hantering kan till exempel ändra befintliga antalet instanser 50 eller 5, beroende på om arbetsbelastningen behöver skala in eller ut utifrån belastningen. 

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
### <a name="using-dynamic-instance-count"></a>Med dynamisk instanser
Mer specifikt för tillståndslösa tjänster erbjuder Service Fabric en automatiskt sätt att ändra instansantal. Detta gör tjänsten för att skala dynamiskt med antalet noder som är tillgängliga. Sätt att delta i det här problemet är att ange instansantal = -1. InstanceCount = -1 är en instruktion till Service Fabric som säger ”kör tillståndslösa tjänsten på varje nod”. Om antalet noder ändras, ändras Service Fabric automatiskt instansantalet ska matchas, se till att tjänsten körs på alla noder som giltig. 

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

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalning genom att skapa eller ta bort nya heter tjänster
En namngiven tjänstinstans är en specifik instans av en typ (se [livscykel för Service Fabric-programmet](service-fabric-application-lifecycle.md)) inom vissa namngivna programinstansen i klustret. 

Nya instanser av tjänsten kan skapas (eller tas bort) som tjänster blir mer eller mindre upptagen. Detta innebär att begäranden att sprida över flera instanser av tjänsten, vanligtvis så att belastningen på befintliga tjänster att minska. När du skapar tjänster, placerar klustret Resource Manager för Service Fabric tjänsterna i klustret på ett sätt som är distribuerade. De exakta beslut styrs av den [mått](service-fabric-cluster-resource-manager-metrics.md) i klustret och andra regler för placering. Tjänster kan skapas på flera olika sätt, men de vanligaste är antingen genom att utföra administrativa åtgärder som någon anropar [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), eller genom att koden anropar [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync`kan även anropas från inom andra tjänster som körs i klustret.

Skapa tjänster dynamiskt kan användas i alla typer av scenarier och är ett vanligt mönster. Anta till exempel att en tillståndskänslig tjänst som representerar ett visst arbetsflöde. Den här tjänsten kommer att köras så att arbetsflödet och registrera framsteg och samtal som representerar resurser ska visas för den här tjänsten. 

Hur vill du göra viss tjänst skalans? Tjänsten kan vara flera innehavare i någon form och acceptera anrop och startar stegen för många olika instanser av samma arbetsflöde på en gång. Dock som kan se koden mer komplexa, eftersom nu den har bekymra dig om många olika instanser av samma arbetsflöde alla vid olika faser och från olika kunder. Dessutom problemet hantering av flera olika arbetsflöden samtidigt inte skala. Det beror på att någon gång den här tjänsten kommer att använda för många resurser för en viss dator. Många tjänster inte byggda för det här mönstret uppstå i första hand även problem på grund av en inbyggd flaskhals eller långsammare i koden. Dessa typer av frågor orsaka tjänsten inte att fungera även om antalet samtidiga arbetsflöden som den för att spåra hämtar större.  

En lösning är att skapa en instans av den här tjänsten för alla olika instanser av arbetsflödet som du vill spåra. Detta är ett bra mönster och fungerar om tjänsten är tillståndslösa och tillståndskänsliga. För det här mönstret fungerar, finns vanligtvis en annan tjänst som fungerar som en ”Workload Manager-tjänst”. Jobb för den här tjänsten är att ta emot begäranden och för att vidarebefordra dessa begäranden till andra tjänster. Hanteraren för kan skapa en instans av tjänsten arbetsbelastning dynamiskt när den tar emot meddelandet och sedan skicka för förfrågningar till dessa tjänster. Manager-tjänsten kan också få återanrop när en tjänst för angivet arbetsflödesinstans har slutförts sitt jobb. När hanteraren tar emot dessa återanrop kan det ta bort instansen av arbetsflödestjänsten i eller lämna den om fler anrop förväntades. 

Avancerade versioner av den här typen av manager kan även skapa pooler för de tjänster som den hanterar. Poolen säkerställer att när en ny begäran kommer det inte behöver vänta på att tjänsten för att få upp hastigheten. I stället kan hanteraren bara välja ett arbetsflödestjänst som inte är för närvarande upptagen från poolen eller vidarebefordra slumpmässigt. Att hålla en pool med tillgängliga tjänster gör hantering nya begäranden snabbare, eftersom det är mindre troligt att begäran måste vänta på en ny tjänst till att de. Skapa nya tjänster är snabb, men inte ledigt eller omedelbar. Poolen kan minimera den tid som begäran har ska vänta innan genomgår underhåll. Ofta visas den här manager och pool mönster när svarstider är mest. Begäran och att tjänsten i bakgrunden och _sedan_ skicka vidare är också ett populärt manager mönster som skapar och tar bort tjänster baserat på vissa spårningstjänst för mängden arbete som för närvarande har väntande. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalning genom att skapa eller ta bort nya namngivna instanser av programmet
Skapa och ta bort hela programinstanser liknar mönstret för att skapa och ta bort tjänster. Det finns vissa manager-tjänsten som du beslutar baserat på information och de begäranden som den ser den tar emot från andra tjänster i klustret för det här mönstret. 

När ska skapa en ny programinstans av namngivna användas istället för att skapa en ny namngiven tjänstinstanser i ett redan existerande program? Det finns några fall:

  * Den nya instansen för programmet är för en kund vars kod måste köras under en viss identitet eller säkerhetsinställningar.
    * Service Fabric kan du definiera olika kod paket ska köras under specifika identiteter. För att kunna starta samma kodpaketet under olika identiteter, måste aktiveringarna sker i olika programinstanser. Överväg att fall där du har en befintlig kunds arbetsbelastningar som har distribuerats. Dessa kan köras under en viss identitet så att du kan övervaka och kontrollera åtkomsten till vissa resurser, till exempel remote databaser eller andra system. I det här fallet när en ny kund loggar, vill du förmodligen inte aktivera koden i samma kontext (processen diskutrymme). När du gick gör det det svårare för koden för tjänsten att fungera inom ramen för en viss identitet. Du måste vanligtvis ha mer säkerhet, isolering och identity management-kod. Istället för att använda olika instanser av tjänsten inom samma programinstans som heter och därför samma process utrymme kan du använda olika namngivna instanser i Fabric-tjänstprogrammet. På så sätt blir det lättare att definiera olika identitet sammanhang.
  * Nya programinstansen fungerar också som ett sätt att konfiguration
    * Som standard kommer alla tjänstinstanser av en viss typ i en instans av programmet körs i samma process på en viss nod. Det innebär att när du kan konfigurera varje tjänstinstans på olika sätt, göra så är komplicerad. Tjänster måste ha en token som de använder för att söka efter deras config inom ett konfigurationspaket. Detta är vanligtvis bara tjänstens namn. Detta fungerar bra, men det kombinerar konfigurationen till namn av enskilda namngivna tjänstinstanser inom den programinstansen. Det kan vara förvirrande och svåra att hantera eftersom konfigurationen är normalt en design tid artefakt med programmet instans specifika värden. När du skapar fler tjänster alltid innebär mer programuppgraderingar att ändra informationen i config-paket eller för att distribuera nya så att nya tjänster kan slå upp sina specifik information. Det är ofta enklare att skapa en helt ny namngiven programinstansen. Du kan sedan använda applikationsparametrarna för att ange oavsett konfiguration krävs för tjänsterna. Det här sättet alla tjänster som är äldre än som heter programinstansen kan ärva inställningar för konfiguration. Till exempel i stället för att en enda konfigurationsfil med de inställningar och anpassningar för varje kund, till exempel hemligheter eller per gränserna för kunden, skulle du i stället har ett annat program-instans för varje kund med de här inställningarna åsidosätts. 
  * Det nya programmet fungerar som en gräns för uppgradering
    * Olika namngivna programinstanser fungera som gränser för uppgradering i Service Fabric. En uppgradering av en namngiven programinstansen påverkar inte den kod som en annan programinstans av namngivna körs. Olika program kommer att hamna kör olika versioner av samma kod på samma noder. Detta kan vara en faktor när du behöver fatta ett beslut för skalning eftersom du kan välja om den nya koden bör följa samma uppgraderingar som en annan tjänst eller inte. Anta till exempel att ett anrop når manager-tjänsten som ansvarar för att skala en viss kund arbetsbelastningar genom att skapa och ta bort tjänster dynamiskt. I det här fallet emellertid anropet är för en arbetsbelastning som är associerade med en _nya_ kunden. De flesta kunder som som de isoleras från varandra inte bara på grund av säkerhet och konfiguration som angavs tidigare, men eftersom det ger mer flexibilitet när det gäller kör vissa versioner av programvaran och välja när de uppgraderas. Du kan också skapa en ny instans av programmet och skapa tjänsten det bara att partitionera mängden dina tjänster som berör en uppgraderar. Separata programinstanser ger större precision när du utför programuppgraderingar och även aktivera A / B-testning och blå/grön distributioner. 
  * Den befintliga instansen av programmet är full
    * I Service Fabric [programmet kapacitet](service-fabric-cluster-resource-manager-application-groups.md) är ett begrepp som du kan använda för att styra hur mycket resurser som är tillgängliga för viss programinstanser. Du kan till exempel bestämma att en viss tjänst måste ha en annan instans skapas för att skala. Den här programinstansen är dock utanför kapacitet för ett viss mått. Om den här viss kund eller arbetsbelastning bör fortfarande beviljas mer resurser, kan sedan du antingen öka befintliga kapaciteten för programmet eller skapa ett nytt program. 

## <a name="scaling-at-the-partition-level"></a>Skalning på nivån partition
Service Fabric stöder partitionering. Partitionering delar en tjänst i flera logiska och fysiska avsnitt, där varje fungerar oberoende av varandra. Detta är användbart med tillståndskänsliga tjänster, eftersom ingen har angetts av repliker har att hantera alla anrop och ändra alla tillståndet på en gång. Den [partitionering översikt](service-fabric-concepts-partitioning.md) innehåller information om vilka typer av partitionering scheman som stöds. Repliker för varje partition sprids ut bland noder i ett kluster, distribuerar tjänstens belastning och se till att varken tjänsten hela eller en partition har en enskild felpunkt. 

Överväg att en tjänst som använder en ranged partitioneringsschema med en låg nyckel 0, en hög nyckel 99 och ett partitionsantal på 4. I ett kluster med tre noder, kan tjänsten anges med fyra repliker som delar resurser på varje nod som visas här:

<center>
![Partitionslayout med tre noder](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Om du ökar antalet noder, ska Service Fabric flytta några av de befintliga replikerna dit. Till exempel hämta Låt oss säga antalet noder ökar och fyra replikerna omdistribueras. Nu har tjänsten tre repliker som körs på varje nod, var och en som hör till olika partitioner. Detta gör att bättre resursutnyttjande eftersom den nya noden inte kalla. Normalt även förbättras prestanda eftersom varje tjänst har fler resurser tillgängliga för den.

<center>
![Partitionslayout med fyra noder](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalning med hjälp av Service Fabric-kluster Resource Manager och mått
[Mått](service-fabric-cluster-resource-manager-metrics.md) är hur tjänster express sina resursförbrukning till Service Fabric. Med hjälp av mätvärden får klustret Resource Manager möjlighet att ordna om och optimera layouten för klustret. T.ex, det kan finnas tillräckligt med resurser i klustret, men de kan inte allokeras till de tjänster som för närvarande gör arbete. Med hjälp av mätvärden kan klustret resurshanteraren att ordna om klustret för att säkerställa att tjänster har åtkomst till de tillgängliga resurserna. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalning genom att lägga till och ta bort noder från klustret 
Ett annat alternativ för skalning med Service Fabric är att ändra storlek på klustret. Ändra storlek på klustret innebär att lägga till eller ta bort noder för en eller flera av nodtyperna som i klustret. Tänk dig ett fall där alla noder i klustret är aktiva. Det innebär att den klusterresurserna är nästan alla används. I det här fallet lägger till fler noder i klustret är det bästa sättet att skala. När nya noder ansluta till klustret flyttar Service Fabric klustret Resource Manager services, vilket resulterar i mindre totala belastningen på de befintliga noderna. För tillståndslösa tjänster med instansantal =-1, mer service instanser skapas automatiskt. Detta gör att vissa samtal att flytta från de befintliga noderna till de nya noderna. 

Lägga till och ta bort noder i klustret kan utföras via Service Fabric Azure Resource Manager PowerShell-modulen.

```posh
Add-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName  -NumberOfNodesToAdd 5 
Remove-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName -NumberOfNodesToRemove 5
```

## <a name="putting-it-all-together"></a>Alltihop
Låt oss ta idéer som vi nämnt här och prata via ett exempel. Överväg följande tjänst: du försöker skapa en tjänst som fungerar som en adressbok, håller att namn- och kontaktinformation. 

Höger direkt du har några frågor som rör skala: hur många användare ska du ha? Hur många kontakter lagrar varje användare? När du Ständiga installera tjänsten för första gången är svårt att försöka detta alla ut. Anta att du kommer att gå med en enda statisk tjänst med en specifik partition count. Konsekvenserna av plocka antalet fel partitioner kan innebära att du har problem med skala senare. På liknande sätt, även om du väljer rätt antal inte kanske du har all information du behöver. T.ex, måste du också bestämma storleken på klustret direkt, både vad gäller antalet noder och deras storlek. Det är vanligtvis svårt att förutse hur många resurser som en tjänst ska använda under sin livslängd. Det kan också vara svårt att i förväg veta trafikmönstret som faktiskt ser tjänsten. Till exempel personer som kanske lägga till och ta bort deras kontakter endast direkt på morgonen, eller kanske det jämnt under loppet av dagen. Baserat på det du kan behöva skalas ut och in dynamiskt. Kanske kan du lära dig att förutsäga när du kommer att behöva skalas ut och in, men oavsett hur du förmodligen kommer att behöva att reagera på Ändra resursförbrukning med din tjänst. Detta kan innebära att ändra storlek på klustret för att ge mer resurser när organisera om användning av befintliga resurser inte är tillräckligt. 

Men varför även försöka att välja en enskild partitionsschema ut för alla användare? Varför begränsa dig till en tjänst och en statisk kluster? Den verkliga situationen är vanligtvis mer dynamiskt. 

När du skapar för att skala, Överväg följande dynamiska mönster. Du kan behöva anpassa det till din situation:

1. Skapa en ”manager-tjänst” istället för att försöka hämta en partitioneringsschema för alla direkt.
2. Jobbet för manager-tjänsten är att titta på kundinformation när de registrerar sig för din tjänst. Beroende på den informationen manager-tjänsten för att skapa en instans av din _faktiska_ kontakta lagringstjänsten _för kunden_. Om de kräver specifik konfiguration, isolering eller uppgraderingar, kan du också välja att få igång en programinstans för kunden. 

Den här skapas dynamiskt mönstret många fördelar:

  - Du försöker inte att gissa rätt partitionsantal för alla användare direkt eller skapa en enskild tjänst som är oändligt skalbara på sin egen. 
  - Olika användare inte har samma antalet partitioner replikantalet, placeringen, mått, standard belastning, tjänstnamn, dns-inställningarna eller någon av de egenskaper som anges på tjänst- eller nivå. 
  - Du får ytterligare data segmentering. Varje kund har sina egna kopia av tjänsten
    - Varje kund-tjänsten kan konfigureras på olika sätt och beviljas fler eller färre resurser med fler eller färre partitioner eller repliker efter behov baserat på deras förväntade skala.
      - Till exempel stå kunden betald för skiktet ”guld” - kunde vara mer repliker eller större partitionsantal och potentiellt resurser dedikerade till sina tjänster via mått och programmet kapacitet.
      - Eller Säg de angivna information som anger antalet kontakter som de behövs var ”liten” - de kommer bara några partitioner eller kan även användas i en pool för delade tjänster med andra kunder.
  - Du kör inte flera instanser av tjänsten eller repliker medan du väntar för kunder att visas
  - Om en kund någonsin lämnar är sedan ta bort deras information från din tjänst lika enkelt som hanteraren för ta bort den tjänsten eller programmet som skapats.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

* [Tillgänglighet för Service Fabric-tjänster](service-fabric-availability-services.md)
* [Partitionering Service Fabric-tjänster](service-fabric-concepts-partitioning.md)
