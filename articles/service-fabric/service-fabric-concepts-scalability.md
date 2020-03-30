---
title: Skalbarhet för Service Fabric-tjänster
description: Lär dig mer om skalning i Azure Service Fabric och de olika tekniker som används för att skala program.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: 17827342b67d37d9fbeb56654824e004367823ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282567"
---
# <a name="scaling-in-service-fabric"></a>Skalning i serviceinfrastruktur
Azure Service Fabric gör det enkelt att skapa skalbara program genom att hantera tjänster, partitioner och repliker på noderna i ett kluster. Om du kör många arbetsbelastningar på samma maskinvara kan maximal resursutnyttjande vara flexibelt, men du kan också minska resursanvändningen. I den här channel 9-videon beskrivs hur du kan skapa skalbara mikrotjänstprogram:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalning i Service Fabric sker på flera olika sätt:

1. Skala genom att skapa eller ta bort tillståndslösa tjänstinstanser
2. Skala genom att skapa eller ta bort nya namngivna tjänster
3. Skala genom att skapa eller ta bort nya namngivna programinstanser
4. Skala med partitionerade tjänster
5. Skala genom att lägga till och ta bort noder från klustret 
6. Skala med hjälp av mätvärden för Klusterresurshanteraren

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skala genom att skapa eller ta bort tillståndslösa tjänstinstanser
Ett av de enklaste sätten att skala inom Service Fabric fungerar med tillståndslösa tjänster. När du skapar en tillståndslös tjänst får `InstanceCount`du en chans att definiera en . `InstanceCount`definierar hur många kopior av tjänstens kod som körs när tjänsten startas. Anta till exempel att det finns 100 noder i klustret. Låt oss också säga att en `InstanceCount` tjänst skapas med en av 10. Under körningen kan de 10 som kör kopior av koden alla bli för upptagna (eller kan inte vara tillräckligt upptagen). Ett sätt att skala den arbetsbelastningen är att ändra antalet instanser. En del övervaknings- eller hanteringskod kan till exempel ändra det befintliga antalet instanser till 50 eller till 5, beroende på om arbetsbelastningen behöver skalas in eller ut baserat på belastningen. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Använda antal dynamiska instanser
Service Fabric är särskilt för tillståndslösa tjänster och erbjuder ett automatiskt sätt att ändra instansantalet. Detta gör att tjänsten kan skalas dynamiskt med antalet noder som är tillgängliga. Sättet att välja detta beteende är att ställa in instansantalet = -1. InstanceCount = -1 är en instruktion till Service Fabric med det där det står "Kör den här tillståndslösa tjänsten på varje nod". Om antalet noder ändras ändrar Service Fabric automatiskt instansantalet så att det matchar, vilket säkerställer att tjänsten körs på alla giltiga noder. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skala genom att skapa eller ta bort nya namngivna tjänster
En namngiven tjänstinstans är en specifik instans av en tjänsttyp (se [Livscykeln för Tjänst fabric-program](service-fabric-application-lifecycle.md)) inom någon namngiven programinstans i klustret. 

Nya namngivna tjänstinstanser kan skapas (eller tas bort) när tjänster blir mer eller mindre upptagna. Detta gör att begäranden kan spridas över fler tjänstinstanser, vilket vanligtvis gör att belastningen på befintliga tjänster minskar. När du skapar tjänster placerar Service Fabric Cluster Resource Manager tjänsterna i klustret på ett distribuerat sätt. De exakta besluten styrs av måtten i klustret och andra [placeringsregler.](service-fabric-cluster-resource-manager-metrics.md) Tjänster kan skapas på flera olika sätt, men de [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)vanligaste är antingen [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)genom administrativa åtgärder som någon som ringer eller genom kodsamtal . `CreateServiceAsync`kan även anropas inifrån andra tjänster som körs i klustret.

Skapa tjänster dynamiskt kan användas i alla möjliga scenarier, och är ett vanligt mönster. Tänk dig till exempel en tillståndskänslig tjänst som representerar ett visst arbetsflöde. Anrop som representerar arbete kommer att visas till den här tjänsten, och den här tjänsten kommer att köra stegen till arbetsflödet och registrera förloppet. 

Hur skulle du göra just den här serviceskalan? Tjänsten kan vara flera innehavare i någon form, och acceptera samtal och starta steg för många olika instanser av samma arbetsflöde på en gång. Men det kan göra koden mer komplex, eftersom det nu måste oroa sig för många olika instanser av samma arbetsflöde, allt i olika skeden och från olika kunder. Att hantera flera arbetsflöden samtidigt löser inte heller skalningsproblemet. Detta beror på att den här tjänsten någon gång kommer att förbruka för många resurser för att få plats på en viss dator. Många tjänster som inte är byggda för detta mönster i första hand upplever också svårigheter på grund av någon inneboende flaskhals eller avmattning i sin kod. Dessa typer av problem gör att tjänsten inte fungerar lika bra när antalet samtidiga arbetsflöden som spåras blir större.  

En lösning är att skapa en instans av den här tjänsten för varje enskild instans av arbetsflödet som du vill spåra. Detta är ett bra mönster och fungerar oavsett om tjänsten är statslös eller tillståndskänslig. För att det här mönstret ska fungera finns det vanligtvis en annan tjänst som fungerar som en "Workload Manager Service". Tjänstens uppgift är att ta emot förfrågningar och dirigera dessa begäranden till andra tjänster. Chefen kan dynamiskt skapa en instans av arbetsbelastningstjänsten när den tar emot meddelandet och sedan vidarebefordra begäranden till dessa tjänster. Chefstjänsten kan också ta emot motringningar när en viss arbetsflödestjänst slutför sitt jobb. När chefen tar emot dessa motringningar kan den ta bort den instansen av arbetsflödestjänsten eller lämna den om fler anrop förväntas. 

Avancerade versioner av den här typen av chef kan till och med skapa pooler av de tjänster som hanteras. Poolen hjälper till att säkerställa att när en ny begäran kommer in behöver den inte vänta på att tjänsten ska snurra upp. I stället kan chefen bara välja en arbetsflödestjänst som för närvarande inte är upptagen från poolen eller dirigera slumpmässigt. Att hålla en pool av tjänster tillgängliga gör hanteringen av nya förfrågningar snabbare, eftersom det är mindre troligt att begäran måste vänta på att en ny tjänst ska snurras upp. Det går snabbt att skapa nya tjänster, men inte gratis eller omedelbart. Poolen hjälper till att minimera den tid som begäran måste vänta innan den servas. Du ser ofta den här chefen och poolmönstret när svarstiderna betyder mest. Att köa begäran och skapa tjänsten i bakgrunden och _sedan_ vidarebefordra den är också ett populärt managermönster, liksom att skapa och ta bort tjänster baserat på viss spårning av mängden arbete som tjänsten för närvarande har väntande. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skala genom att skapa eller ta bort nya namngivna programinstanser
Att skapa och ta bort hela programinstanser liknar mönstret för att skapa och ta bort tjänster. För det här mönstret finns det någon chefstjänst som fattar beslutet baserat på de begäranden som den ser och den information den tar emot från andra tjänster i klustret. 

När ska skapa en ny namngiven programinstans användas i stället för att skapa en ny namngiven tjänstinstans i vissa redan befintliga program? Det finns några fall:

  * Den nya programinstansen är för en kund vars kod måste köras under vissa specifika identitets- eller säkerhetsinställningar.
    * Service Fabric gör det möjligt att definiera olika kodpaket som ska köras under särskilda identiteter. För att kunna starta samma kodpaket under olika identiteter måste aktiveringarna ske i olika programinstanser. Överväg ett fall där du har distribuerat en befintlig kunds arbetsbelastningar. Dessa kan köras under en viss identitet så att du kan övervaka och kontrollera deras åtkomst till andra resurser, till exempel fjärrdatabaser eller andra system. I det här fallet, när en ny kund registrerar sig, vill du förmodligen inte aktivera deras kod i samma sammanhang (processutrymme). Även om du kan, detta gör det svårare för din tjänstkod att agera inom ramen för en viss identitet. Du måste vanligtvis ha mer säkerhets-, isolerings- och identitetshanteringskod. I stället för att använda olika namngivna tjänstinstanser i samma programinstans och därmed samma processutrymme kan du använda olika namngivna tjänst fabric-programinstanser. Detta gör det enklare att definiera olika identitetskontexter.
  * Den nya tillämpningsinstansen fungerar också som ett sätt att
    * Som standard körs alla namngivna tjänstinstanser av en viss tjänsttyp i en programinstans i samma process på en viss nod. Vad detta innebär är att även om du kan konfigurera varje tjänstinstans på olika sätt, är det komplicerat att göra det. Tjänster måste ha någon token som de använder för att slå upp sin konfigurationspaket. Vanligtvis är detta bara tjänstens namn. Detta fungerar bra, men det kopplar konfigurationen till namnen på de enskilda namngivna tjänstinstanserna i den programinstansen. Detta kan vara förvirrande och svårt att hantera eftersom konfigurationen normalt är en designtidsartefakt med programinstansspecifika värden. Att skapa fler tjänster innebär alltid fler programuppgraderingar för att ändra informationen i konfigurationspaketen eller distribuera nya så att de nya tjänsterna kan söka efter specifik information. Det är ofta enklare att skapa en helt ny namngiven programinstans. Sedan kan du använda programparametrarna för att ställa in den konfiguration som krävs för tjänsterna. På så sätt kan alla tjänster som skapas inom den namngivna programinstansen ärva vissa konfigurationsinställningar. I stället för att till exempel ha en enda konfigurationsfil med inställningar och anpassningar för varje kund, till exempel hemligheter eller per kundresursgränser, skulle du i stället ha en annan programinstans för varje kund med dessa inställningar Åsidosätts. 
  * Det nya programmet fungerar som en uppgraderingsgräns
    * Inom Service Fabric fungerar olika namngivna programinstanser som gränser för uppgradering. En uppgradering av en namngiven programinstans påverkar inte koden som en annan namngiven programinstans körs. De olika programmen kommer att sluta köra olika versioner av samma kod på samma noder. Detta kan vara en faktor när du behöver göra ett skalningsbeslut eftersom du kan välja om den nya koden ska följa samma uppgraderingar som en annan tjänst eller inte. Anta till exempel att ett samtal kommer till chefstjänsten som ansvarar för att skala en viss kunds arbetsbelastningar genom att skapa och ta bort tjänster dynamiskt. I det här fallet är dock samtalet för en arbetsbelastning som är associerad med en _ny_ kund. De flesta kunder gillar att vara isolerade från varandra inte bara för säkerhets- och konfigurationsskäl som anges tidigare, utan för att det ger mer flexibilitet när det gäller att köra specifika versioner av programvaran och välja när de uppgraderas. Du kan också skapa en ny programinstans och skapa tjänsten där helt enkelt för att ytterligare partitionera mängden av dina tjänster som en uppgradering kommer att röra. Separata programinstanser ger större granularitet när du gör programuppgraderingar och aktiverar även A/B-testning och blå/grön distributioner. 
  * Den befintliga programinstansen är full
    * I Service Fabric är [programkapacitet](service-fabric-cluster-resource-manager-application-groups.md) ett begrepp som du kan använda för att styra mängden resurser som är tillgängliga för vissa programinstanser. Du kan till exempel bestämma att en viss tjänst måste ha en annan instans skapad för att kunna skalas. Den här programinstansen har dock inte kapacitet för ett visst mått. Om den här kunden eller arbetsbelastningen fortfarande bör beviljas mer resurser kan du antingen öka den befintliga kapaciteten för det programmet eller skapa ett nytt program. 

## <a name="scaling-at-the-partition-level"></a>Skala på partitionsnivå
Service Fabric stöder partitionering. Partitionering delar en tjänst i flera logiska och fysiska avsnitt, som var och en fungerar oberoende av varandra. Detta är användbart med tillståndskänsliga tjänster, eftersom ingen uppsättning repliker har att hantera alla samtal och manipulera alla tillstånd på en gång. [Partitioneringsöversikten](service-fabric-concepts-partitioning.md) innehåller information om vilka typer av partitioneringsscheman som stöds. Replikerna för varje partition är spridda över noderna i ett kluster, distribuerar tjänstens belastning och ser till att varken tjänsten som helhet eller någon partition har en enda felpunkt. 

Överväg en tjänst som använder ett intervallpartitioneringsschema med en låg nyckel på 0, en hög nyckel på 99 och ett partitionsantal på 4. I ett kluster med tre noder kan tjänsten läggas ut med fyra repliker som delar resurserna på varje nod som visas här:

<center>

![Partitionslayout med tre noder](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Om du ökar antalet noder flyttar Service Fabric några av de befintliga replikerna dit. Anta till exempel att antalet noder ökar till fyra och att replikerna omfördelas. Nu har tjänsten nu tre repliker som körs på varje nod, var och en som tillhör olika partitioner. Detta möjliggör bättre resursutnyttjande eftersom den nya noden inte är kall. Vanligtvis förbättrar det också prestanda eftersom varje tjänst har mer resurser tillgängliga för den.

<center>

![Partitionslayout med fyra noder](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skala med hjälp av Service Fabric Cluster Resource Manager och mått
Mått är hur tjänster [uttrycker](service-fabric-cluster-resource-manager-metrics.md) sin resursförbrukning till Service Fabric. Med hjälp av mått ger Cluster Resource Manager en möjlighet att omorganisera och optimera klustrets layout. Det kan till exempel finnas gott om resurser i klustret, men de kanske inte allokeras till de tjänster som för närvarande utför arbete. Med hjälp av mått kan Klusterresurshanteraren ordna om klustret för att säkerställa att tjänsterna har åtkomst till tillgängliga resurser. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skala genom att lägga till och ta bort noder från klustret 
Ett annat alternativ för skalning med Service Fabric är att ändra storleken på klustret. Att ändra storleken på klustret innebär att lägga till eller ta bort noder för en eller flera nodtyper i klustret. Tänk dig till exempel ett fall där alla noder i klustret är heta. Det innebär att klustrets resurser nästan alla förbrukas. I det här fallet är det bästa sättet att skala att lägga till fler noder i klustret. När de nya noderna ansluter till klustret flyttar Service Fabric Cluster Resource Manager tjänster till dem, vilket resulterar i mindre total belastning på befintliga noder. För tillståndslösa tjänster med instansantal = -1 skapas fler tjänstinstanser automatiskt. På så sätt kan vissa anrop flyttas från befintliga noder till de nya noderna. 

Mer information finns i [klusterskalning](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Välja en plattform

På grund av implementeringsskillnader mellan operativsystem kan det vara viktigt att välja Service Fabric med Windows eller Linux. En potentiell barriär är hur iscensatt loggning utförs. Service Fabric i Windows använder en kernel-drivrutin för en logg för en per dator som delas mellan tillståndskänsliga tjänstrepliker. Denna logg väger in på ca 8 GB. Linux, å andra sidan, använder en 256 MB mellanlagringslogg för varje replik, vilket gör den mindre idealisk för program som vill maximera antalet lättviktstjänstrepliker som körs på en viss nod. Dessa skillnader i tillfälliga lagringskrav kan potentiellt informera den önskade plattformen för distribution av Service Fabric-kluster.

## <a name="putting-it-all-together"></a>Färdigställa allt
Låt oss ta alla de idéer som vi har diskuterat här och prata igenom ett exempel. Tänk på följande tjänst: du försöker skapa en tjänst som fungerar som en adressbok och håller fast vid namn och kontaktinformation. 

Rätt på framsidan har du en massa frågor relaterade till skala: Hur många användare kommer du att ha? Hur många kontakter lagrar varje användare? Att försöka lista ut allt detta när du står upp din tjänst för första gången är svårt. Låt oss säga att du skulle gå med en enda statisk tjänst med en specifik partition räknas. Konsekvenserna av att välja fel partitionsantal kan leda till att du har skalproblem senare. På samma sätt, även om du väljer rätt räkna du kanske inte har all information du behöver. Du måste till exempel också bestämma storleken på klustret på framsidan, både när det gäller antalet noder och deras storlekar. Det är oftast svårt att förutsäga hur många resurser en tjänst kommer att förbruka under sin livstid. Det kan också vara svårt att veta i förväg trafikmönstret som tjänsten faktiskt ser. Till exempel, kanske folk lägga till och ta bort sina kontakter bara första sak på morgonen, eller kanske det är jämnt fördelat under loppet av dagen. Baserat på detta kan du behöva skala ut och i dynamiskt. Kanske kan du lära dig att förutsäga när du kommer att behöva skala ut och in, men hur som helst du förmodligen kommer att behöva reagera på att ändra resursförbrukning av din tjänst. Det kan handla om att ändra storleken på klustret för att ge mer resurser när det inte räcker att omorganisera användningen av befintliga resurser. 

Men varför ens försöka välja en enda partition system ut för alla användare? Varför begränsa dig till en tjänst och ett statiskt kluster? Den verkliga situationen är oftast mer dynamisk. 

När du skapar för skala bör du tänka på följande dynamiska mönster. Du kan behöva anpassa den till din situation:

1. Istället för att försöka välja ett partitioneringsschema för alla på framsidan, skapa en "manager service".
2. Chefstjänstens uppgift är att titta på kundinformation när de registrerar sig för din tjänst. Beroende på den informationen skapar chefstjänsten en instans av din _faktiska_ kontaktlagringstjänst _bara för den kunden._ Om de kräver särskild konfiguration, isolering eller uppgraderingar kan du också välja att starta en programinstans för den här kunden. 

Detta dynamiska skapande mönster många fördelar:

  - Du försöker inte gissa rätt partitionsantal för alla användare på framsidan eller bygga en enda tjänst som är oändligt skalbar på egen hand. 
  - Olika användare behöver inte ha samma partitionsantal, replikantal, placeringsbegränsningar, mått, standardbelastningar, tjänstnamn, DNS-inställningar eller någon av de andra egenskaper som anges på tjänst- eller programnivå. 
  - Du får ytterligare datasegmentering. Varje kund har sin egen kopia av tjänsten
    - Varje kundtjänst kan konfigureras på olika sätt och beviljas mer eller färre resurser, med fler eller färre partitioner eller repliker efter behov baserat på deras förväntade skala.
      - Anta till exempel att kunden betalade för "Gold"-nivån – de kan få fler repliker eller större partitionsantal och eventuellt resurser som är avsedda för deras tjänster via mått och programkapacitet.
      - Eller säga att de lämnat information som anger hur många kontakter de behövde var "Liten" - de skulle få bara ett fåtal partitioner, eller ens kan sättas i en delad tjänst pool med andra kunder.
  - Du kör inte en massa tjänstinstanser eller repliker medan du väntar på att kunderna ska dyka upp
  - Om en kund någonsin lämnar, sedan ta bort sin information från din tjänst är så enkelt som att låta chefen ta bort den tjänst eller program som den skapade.

## <a name="next-steps"></a>Nästa steg
Mer information om service fabric-begrepp finns i följande artiklar:

* [Tillgång till Service Fabric-tjänster](service-fabric-availability-services.md)
* [Tjänster för partitionering av tjänst fabric](service-fabric-concepts-partitioning.md)
