---
title: Värdmodell för Azure Service Fabric
description: Beskriver relationen mellan repliker (eller instanser) för en distribuerad Service Fabric-tjänst och servicevärdprocessen.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 82bc5068be651b05eb24efa3b05e46c1e7c1e24d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115041"
---
# <a name="azure-service-fabric-hosting-model"></a>Värdmodell för Azure Service Fabric
Den här artikeln innehåller en översikt över programvärdmodeller som tillhandahålls av Azure Service Fabric och beskriver skillnaderna mellan modeller **för delad process** och exklusiv **process.** Den beskriver hur ett distribuerat program ser ut på en Service Fabric-nod och relationen mellan repliker (eller instanser) för tjänsten och tjänstvärdprocessen.

Innan du fortsätter vidare bör du se till att du förstår de olika begrepp och relationer som [förklaras][a1]i Modell ett program i Service Fabric . 

> [!NOTE]
> I den här artikeln, om inte uttryckligen nämns som betyder något annat:
>
> - *Replik* refererar till både en replik av en tillståndskänslig tjänst och en instans av en tillståndslös tjänst.
> - *CodePackage* behandlas som likvärdigt med en *ServiceHost-process* som registrerar en *ServiceType*och är värd för repliker av tjänster från *den ServiceType*.
>

För att förstå värdmodellen, låt oss gå igenom ett exempel. Låt oss säga att vi har en *ApplicationType* 'MyAppType', som har en *ServiceType* 'MyServiceType'. "MyServiceType" tillhandahålls av *ServicePackage* "MyServicePackage", som har ett *CodePackage* "MyCodePackage". "MyCodePackage" registrerar *ServiceType* 'MyServiceType' när den körs.

Låt oss säga att vi har ett trenodskluster och vi skapar ett *application* **programbeslag:/App1** av typen "MyAppType". Inuti den här **programstrukturen:/App1**skapar vi ett **servicetyg:/App1/ServiceEn** av typen "MyServiceType". Den här tjänsten har två partitioner (till exempel **P1** och **P2**) och tre repliker per partition. Följande diagram visar vyn för det här programmet när det hamnar distribuerat på en nod.


![Diagram över nodvy för distribuerat program][node-view-one]


Service Fabric aktiverat "MyServicePackage", som startade "MyCodePackage", som är värd repliker från båda partitionerna. Alla noder i klustret har samma vy, eftersom vi valde antalet repliker per partition som ska vara lika med antalet noder i klustret. Låt oss skapa en annan tjänst, **fabric:/App1/ServiceB**, i **programstrukturen:/App1**. Den här tjänsten har en partition (till exempel **P3**) och tre repliker per partition. I följande diagram visas den nya vyn på noden:


![Diagram över nodvy för distribuerat program][node-view-two]


Service Fabric placerade den nya repliken för partition **P3** av tjänst **fabric:/App1/ServiceB** i den befintliga aktiveringen av "MyServicePackage". Nwo. Låt oss skapa ett annat program **tyg:/App2** av typen "MyAppType". Inuti **tyg:/App2**, skapa en tjänst **tyg:/App2/ServiceA**. Den här tjänsten har två partitioner (**P4** och **P5**) och tre repliker per partition. I följande diagram visas den nya nodvyn:


![Diagram över nodvy för distribuerat program][node-view-three]


Service Fabric aktiverar en ny kopia av "MyServicePackage", som startar en ny kopia av "MyCodePackage". Repliker från båda partitionerna av tjänst **tyg:/App2/ServiceA** **(P4** och **P5**) placeras i denna nya kopia "MyCodePackage".

## <a name="shared-process-model"></a>Modell för delad process
I föregående avsnitt beskrivs standardvärdmodellen som tillhandahålls av Service Fabric, kallad modellen Delad process. I den här modellen aktiveras endast en kopia av ett visst *ServicePackage* på en nod (som startar alla *CodePackages* som finns i den). Alla repliker av alla tjänster från en viss *ServiceType* placeras i *CodePackage* som registrerar den *ServiceType*. Med andra ord delar alla repliker av alla tjänster på en nod i en viss *ServiceType* samma process.

## <a name="exclusive-process-model"></a>Exklusiv processmodell
Den andra värdmodellen som tillhandahålls av Service Fabric är den exklusiva processmodellen. I den här modellen, på en viss nod, lever varje replik i sin egen dedikerade process. Service Fabric aktiverar en ny kopia av *ServicePackage* (som startar alla *CodePackages* som finns i den). Repliker placeras i *CodePackage* som registrerade *ServiceType* för tjänsten som repliken tillhör. 

Om du använder Service Fabric version 5.6 eller senare kan du välja modellen Exclusive Process när du skapar en tjänst (med [PowerShell,][p1] [REST][r1]eller [FabricClient][c1]). Ange **ServicePackageActivationMode** som Exklusivprocess.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Om du har en standardtjänst i ditt programmanifest kan du välja modellen Exklusiv process genom att ange attributet **ServicePackageActivationMode:**

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nu ska vi skapa en annan tjänst, **tyg:/App1/ServiceC**, i **programvävnad:/App1**. Den här tjänsten har två partitioner (till exempel **P6** och **P7**) och tre repliker per partition. Du ställer in **ServicePackageActivationMode** till ExclusiveProcess. I följande diagram visas en ny vy på noden:


![Diagram över nodvy för distribuerat program][node-view-four]


Som ni kan se aktiverade Service Fabric två nya kopior av "MyServicePackage" (en för varje replik från **partitionen P6** och **P7**). Service Fabric placerade varje replik i sin dedikerade kopia av *CodePackage*. När du använder modellen Exclusive Process kan flera kopior av ett visst *ServicePackage* vara aktiva på en nod för ett visst program. I föregående exempel är tre kopior av "MyServicePackage" aktiva för **tyg:/App1**. Var och en av dessa aktiva kopior av "MyServicePackage" har en **ServicePackageActivationId** associerad med den. Det här ID:t identifierar kopieringen i **programinfrastrukturen:/App1**.

När du bara använder modellen Delad process för ett program finns det bara en aktiv kopia av *ServicePackage* på en nod. **ServicePackageActivationId** för den här aktiveringen av *ServicePackage* är en tom sträng. Detta är fallet, till exempel med **tyg:/App2**.

> [!NOTE]
>- Värdmodellen för delad process motsvarar **ServicePackageActivationMode** som är lika med **SharedProcess**. Detta är standardvärdmodellen och **ServicePackageActivationMode** behöver inte anges vid tidpunkten för skapandet av tjänsten.
>
>- Den exklusiva processvärda modellen motsvarar **ServicePackageActivationMode** är lika med **ExclusiveProcess**. Om du vill använda den här inställningen bör du uttryckligen ange den när tjänsten skapas. 
>
>- Om du vill visa värdmodellen för en tjänst frågar du [tjänstbeskrivningen][p2]och tittar på värdet för **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Arbeta med ett distribuerat tjänstpaket
En aktiv kopia av ett *ServicePackage* på en nod kallas ett [distribuerat tjänstpaket][p3]. När du använder modellen Exclusive Process för att skapa tjänster kan det finnas flera distribuerade tjänstpaket för samma *ServicePackage*. Om du utför åtgärder som är specifika för ett distribuerat tjänstpaket bör du tillhandahålla **ServicePackageActivationId** för att identifiera ett specifikt distribuerat tjänstpaket. Ange till exempel ID:t om du [rapporterar hälsotillståndet för ett distribuerat tjänstpaket][p4] eller [startar om kodpaketet för ett distribuerat tjänstpaket][p5].

Du kan ta reda på **ServicePackageActivationId** för ett distribuerat tjänstpaket genom att fråga listan över [distribuerade tjänstpaket][p3] på en nod. När du frågar efter de [distribuerade tjänsttyperna,][p6] [distribuerade repliker][p7]och [distribuerade kodpaket][p8] på en nod, innehåller frågeresultatet också **ServicePackageActivationId** för det överordnade distribuerade tjänstpaketet.

> [!NOTE]
>- Under den delade processens värdmodell aktiveras endast en kopia av ett *ServicePackage* på en viss nod för ett visst program. Den har en **ServicePackageActivationId** lika med *tom sträng*och behöver inte anges när åtgärder som är relaterade till det distribuerade tjänstpaketet utförs. 
>
> - Under den exklusiva processvärdmodellen kan en eller flera kopior av ett *ServicePackage* vara aktiva för en viss nod för ett visst program. Varje aktivering har en *icke-tom* **ServicePackageActivationId**, angiven när du utför åtgärder som är relaterade till det distribuerade tjänstpaketet. 
>
> - Om **ServicePackageActivationId** utelämnas är det standard *tom sträng*. Om det finns ett distribuerat tjänstpaket som har aktiverats under modellen Delad process utförs åtgärden på den. Annars misslyckas åtgärden.
>
> - Fråga inte en gång och cachelagra **ServicePackageActivationId**. ID:t genereras dynamiskt och kan ändras av olika anledningar. Innan du utför en åtgärd som behöver **ServicePackageActivationId**bör du först fråga listan över [distribuerade tjänstpaket][p3] på en nod. Använd sedan **ServicePackageActivationId** från frågeresultatet för att utföra den ursprungliga åtgärden.
>
>

## <a name="guest-executable-and-container-applications"></a>Körbara gäst- och behållarprogram
Service Fabric behandlar [gäst körbara][a2] program och [behållarprogram][a3] som tillståndslösa tjänster, som är fristående. Det finns ingen Service Fabric-körning i *ServiceHost* (en process eller behållare). Eftersom dessa tjänster är fristående är antalet repliker per *ServiceHost* inte tillämpligt för dessa tjänster. Den vanligaste konfigurationen som används med dessa tjänster är enpartition, med [InstanceCount][c2] lika med -1 (en kopia av servicekoden som körs på varje nod i klustret). 

**StandardservicepaketActivationMode** för dessa tjänster är **SharedProcess**, i vilket fall Service Fabric endast aktiverar en kopia av *ServicePackage* på en nod för ett visst program.  Det innebär att endast en kopia av servicekoden kör en nod. Om du vill att flera kopior av servicekoden ska köras på en nod anger du **ServicePackageActivationMode** som **ExclusiveProcess** när tjänsten skapas. Du kan till exempel göra detta när du skapar flera tjänster *(Service1* till *ServiceN)* av *ServiceType* (angivet i *ServiceManifest)* eller när tjänsten är flerpartitionerad. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Ändra värdmodellen för en befintlig tjänst
För närvarande kan du inte ändra värdmodellen för en befintlig tjänst från delad process till exklusiv process (eller vice versa).

## <a name="choose-between-the-hosting-models"></a>Välj mellan värdmodellerna
Du bör utvärdera vilken värdmodell som bäst passar dina behov. Modellen Delad process använder operativsystemets resurser bättre, eftersom färre processer skapas och flera repliker i samma process kan dela portar. Men om en av replikerna har ett fel där den behöver för att få ner tjänstvärden, påverkar det alla andra repliker i samma process.

 Modellen Exclusive Process ger bättre isolering, med varje replik i sin egen process. Om en av replikerna har ett fel påverkar det inte andra repliker. Den här modellen är användbar för fall där portdelning inte stöds av kommunikationsprotokollet. Det underlättar möjligheten att tillämpa resursstyrning på repliknivå. Den exklusiva processen förbrukar dock fler operativsystemresurser, eftersom den ger upphov till en process för varje replik på noden.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exklusiv processmodell och programmodellöverväganden
För de flesta program kan du modellera ditt program i Service Fabric genom att behålla en *ServiceType* per *ServicePackage*. 

I vissa fall tillåter Service Fabric också mer än en *ServiceType* per *ServicePackage* (och ett *CodePackage* kan registrera mer än en *ServiceType*). Följande är några av de scenarier där dessa konfigurationer kan vara användbara:

- Du vill optimera resursutnyttjandet genom att skapa färre processer och ha högre replikdensitet per process.
- Repliker från olika *ServiceTypes* måste dela några vanliga data som har en hög initiering eller minneskostnad.
- Du har ett kostnadsfritt tjänsteerbjudande och du vill sätta en gräns för resursutnyttjande genom att placera alla repliker av tjänsten i samma process.

Den exklusiva processvärdmodellen är inte förenligt med en programmodell som har flera *ServiceTypes* per *ServicePackage*. Detta beror på att flera *ServiceTypes* per *ServicePackage* är utformade för att uppnå högre resursdelning mellan repliker och möjliggör högre replikdensitet per process. Exclusive Process-modellen är utformad för att uppnå olika resultat.

Tänk på fallet med flera *ServiceTypes* per *ServicePackage*, med en annan *CodePackage* registrera varje *ServiceType*. Låt oss säga att vi har en *ServicePackage* "MultiTypeServicePackage", som har två *CodePackages:*

- "MyCodePackageA", som registrerar *ServiceType* 'MyServiceTypeA'.
- "MyCodePackageB", som registrerar *ServiceType* 'MyServiceTypeB'.

Nu ska vi säga att vi skapar ett program, **tyg: / SpecialApp**. Inuti **tyg:/SpecialApp,** skapar vi följande två tjänster med exclusive process-modellen:

- Tjänst **fabric:/SpecialApp/ServiceEn** av typen "MyServiceTypeA", med två partitioner (till exempel **P1** och **P2**) och tre repliker per partition.
- Tjänst **fabric:/SpecialApp/ServiceB** av typen "MyServiceTypeB", med två partitioner (**P3** och **P4**), och tre repliker per partition.

På en viss nod har båda tjänsterna två repliker vardera. Eftersom vi använde modellen Exclusive Process för att skapa tjänsterna aktiverar Service Fabric en ny kopia av "MyServicePackage" för varje replik. Varje aktivering av "MultiTypeServicePackage" startar en kopia av "MyCodePackageA" och "MyCodePackageB". Men bara en av "MyCodePackageA" eller "MyCodePackageB" är värd för den replik som "MultiTypeServicePackage" aktiverades. I följande diagram visas nodvyn:


![Diagram över nodvyn för distribuerat program][node-view-five]


I aktiveringen av "MultiTypeServicePackage" för repliken av partition **P1** av tjänsten **tyg:/SpecialApp /ServiceA**, "MyCodePackageA" är värd för repliken. "MyCodePackageB" körs. På samma sätt, i aktiveringen av "MultiTypeServicePackage" för repliken av **partitionen P3** av tjänsten **tyg:/SpecialApp / ServiceB**, "MyCodePackageB" är värd för repliken. "MyCodePackageA" körs. Ju fler *CodePackages* (registrering av olika *ServiceTypes)* per *ServicePackage*, desto högre är den redundanta resursanvändningen. 
 
 Men om vi skapar tjänsterna **tyg:/SpecialApp / ServiceA** och **tyg:/SpecialApp / ServiceB** med delad process modell, aktiverar Service Fabric endast en kopia av "MultiTypeServicePackage" för ansökan **tyg:/SpecialApp**. "MyCodePackageA" är värd för alla repliker för **tjänststrukturen:/SpecialApp/ServiceA**. "MyCodePackageB" är värd för alla repliker för **tjänststrukturen:/SpecialApp/ServiceB**. I följande diagram visas nodvyn i den här inställningen: 


![Diagram över nodvyn för distribuerat program][node-view-six]


I föregående exempel kanske du tror att om "MyCodePackageA" registrerar både "MyServiceTypeA" och "MyServiceTypeB", och det inte finns någon "MyCodePackageB", så finns det inget redundant *CodePackage* som körs. Även om detta är korrekt, stämmer inte den här programmodellen överens med den exklusiva processvärdmodellen. Om målet är att placera varje replik i sin egen dedikerade process behöver du inte registrera båda *ServiceTypes* från samma *CodePackage*. Istället lägger du helt enkelt varje *ServiceType* i sitt eget *ServicePackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Tillförlitliga tjänster och aktör forking underprocesser

Service Fabric stöder inte tillförlitliga tjänster och därefter pålitliga aktörer forking underprocesser. Ett exempel på varför det inte stöds är [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) kan inte användas för att registrera en underprocess som inte stöds, och annulleringstoken skickas endast till registrerade processer. vilket resulterar i alla typer av problem, till exempel uppgraderingsfel, när underprocesser inte stängs efter att den överordnade processen har fått en annulleringstoken.

## <a name="next-steps"></a>Nästa steg
[Paketera ett program][a4] och gör det klart att distribuera.

[Distribuera och ta bort program][a5]. I den här artikeln beskrivs hur du använder PowerShell för att hantera programinstanser.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
