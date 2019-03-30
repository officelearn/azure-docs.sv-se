---
title: Azure Service Fabric-värdmodell | Microsoft Docs
description: Beskriver relationen mellan repliker (eller instanser) för en distribuerad Service Fabric-tjänst och hur värden för tjänsten.
services: service-fabric
documentationcenter: .net
author: harahma
manager: chackdan
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d2d958a89bff40483e1cd473538f7d1a6971d266
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663289"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric-värdmodell
Den här artikeln innehåller en översikt över program som är värd för modeller som tillhandahålls av Azure Service Fabric och beskrivs skillnaderna mellan de **delad Process** och **exklusiv processen** modeller. Beskriver hur ett distribuerat program ser ut på en Service Fabric-nod och relationen mellan repliker (eller instanser) för tjänsten och hur värden för tjänsten.

Innan du fortsätter, Tänk på att du förstår de olika begreppen och relationer som beskrivs i [modellera ett program i Service Fabric][a1]. 

> [!NOTE]
> I den här artikeln om du inte uttryckligen anges som något annat:
>
> - *Repliken* refererar till både en replik av en tillståndskänslig tjänst och en instans av en tillståndslös tjänst.
> - *CodePackage* behandlas som motsvarar en *ServiceHost* process som registrerar en *ServiceType*, och värdar repliker av tjänster som *ServiceType*.
>

För att förstå värdmodellen, låt oss gå igenom ett exempel. Anta att vi har en *ApplicationType* ”MyAppType” som har en *ServiceType* 'MyServiceType'. 'MyServiceType' tillhandahålls av den *ServicePackage* ”MyServicePackage” som har en *CodePackage* 'MyCodePackage'. 'MyCodePackage' registrerar *ServiceType* 'MyServiceType' när den körs.

Anta att vi har ett kluster med tre noder och skapar vi en *programmet* **fabric: / App1** av typen 'MyAppType'. I det här programmet **fabric: / App1**, vi skapa en tjänst **fabric: / App1/ServiceA** av typen 'MyServiceType'. Den här tjänsten har två partitioner (till exempel **P1** och **P2**), och tre repliker per partition. Följande diagram visar vyn för det här programmet som det slutar distribuerad på en nod.


![Diagram över noden vy för distribuerade program][node-view-one]


Service Fabric aktiveras 'MyServicePackage' som startade 'MyCodePackage' som är värd för repliker från båda partitionerna. Alla noder i klustret har samma vy, eftersom vi valde antalet repliker per partition som ska vara samma som antalet noder i klustret. Nu ska vi skapa en annan tjänst, **fabric: / App1/ServiceB**, i programmet **fabric: / App1**. Den här tjänsten har en partition (till exempel **P3**), och tre repliker per partition. Följande diagram visar den nya vyn på noden:


![Diagram över noden vy för distribuerade program][node-view-two]


Service Fabric placeras den nya repliken för partition **P3** tjänsternas **fabric: / App1/ServiceB** i den befintliga 'MyServicePackage'-aktiveringen. Nu. Nu ska vi skapa ett annat program **fabric: / App2** av typen 'MyAppType'. Inuti **fabric: / App2**, skapa en tjänst **fabric: / App2/ServiceA**. Den här tjänsten har två partitioner (**P4** och **P5**), och tre repliker per partition. Följande diagram visar den nya nod vyn:


![Diagram över noden vy för distribuerade program][node-view-three]


Service Fabric aktiverar en ny kopia av ”MyServicePackage” som startar en ny kopia av 'MyCodePackage'. Repliker från båda partitionerna i tjänsten **fabric: / App2/ServiceA** (**P4** och **P5**) placeras i den här nya kopian 'MyCodePackage'.

## <a name="shared-process-model"></a>Delade processmodell
I föregående avsnitt beskrivs standard värdmodell som tillhandahålls av Service Fabric, som kallas delad processmodellen. I den här modellen för ett visst program bara en kopia av en viss *ServicePackage* är aktiverat på en nod (som börjar alla de *CodePackages* finns i den). Alla repliker av alla tjänster på en viss *ServiceType* placeras i den *CodePackage* som registrerar som *ServiceType*. Med andra ord alla repliker för alla tjänster på en nod i en viss *ServiceType* delar samma process.

## <a name="exclusive-process-model"></a>Exklusiva processmodell
Andra värdmodellen tillhandahålls av Service Fabric är exklusiva processmodellen. I den här modellen på en viss nod bor varje replik i en egen dedikerad process. Service Fabric aktiverar en ny kopia av *ServicePackage* (som börjar alla de *CodePackages* finns i den). Repliker placeras i den *CodePackage* som registrerade den *ServiceType* av tjänsten som repliken tillhör. 

Om du använder Service Fabric version 5.6 eller senare, kan du välja exklusiv processmodellen när du skapar en tjänst (med hjälp av [PowerShell][p1], [REST] [ r1], eller [FabricClient][c1]). Ange **ServicePackageActivationMode** som ”ExclusiveProcess”.

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

Om du har en standardtjänst i programmanifestet, kan du välja exklusiv processmodellen genom att ange den **ServicePackageActivationMode** attribut:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nu ska vi skapa en annan tjänst, **fabric: / App1/ServiceC**, i programmet **fabric: / App1**. Den här tjänsten har två partitioner (till exempel **P6** och **P7**), och tre repliker per partition. Du ställer in **ServicePackageActivationMode** till 'ExclusiveProcess'. Följande diagram visar ny vy på noden:


![Diagram över noden vy för distribuerade program][node-view-four]


Som du ser Service Fabric aktiverat två nya kopior av ”MyServicePackage” (en för varje replik från partition **P6** och **P7**). Service Fabric placeras varje replik i den dedikerade kopian av *CodePackage*. När du använder uteslutande processmodellen för ett visst program flera kopior av en viss *ServicePackage* kan vara aktiv på en nod. I föregående exempel tre kopior av 'MyServicePackage' är aktiva för **fabric: / App1**. Var och en av dessa active kopior av 'MyServicePackage' har en **ServicePackageActivationId** kopplade till den. Detta ID identifierar kopian i programmet **fabric: / App1**.

När du använder bara delas processmodellen för ett program, det finns endast en aktiv kopia av *ServicePackage* på en nod. Den **ServicePackageActivationId** för den här aktiveringen av *ServicePackage* är en tom sträng. Detta är fallet, till exempel med **fabric: / App2**.

> [!NOTE]
>- Delade processen värdmodell motsvarar **ServicePackageActivationMode** är lika med **SharedProcess**. Detta är standardinställningen värdmodell, och **ServicePackageActivationMode** behöver inte anges vid tidpunkten för skapandet av tjänsten.
>
>- Exklusiva processen värdmodell motsvarar **ServicePackageActivationMode** är lika med **ExclusiveProcess**. Om du vill använda den här inställningen, bör du ange den uttryckligen vid tidpunkten för skapandet av tjänsten. 
>
>- Om du vill visa värdmodell av en tjänst, fråga den [tjänstbeskrivningen][p2], och titta på värdet för **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Arbeta med en distribuerad tjänst-paket
En aktiv kopia av en *ServicePackage* på en nod kallas en [distribuerat tjänstpaket][p3]. När du använder uteslutande processmodellen för att skapa tjänster, för ett visst program, det kan finnas flera distribuerade tjänstpaket för samma *ServicePackage*. Om du utför åtgärder som är specifika för en distribuerad tjänst-paketet, bör du ge **ServicePackageActivationId** att identifiera ett paket för specifika distribuerade tjänsten. Till exempel ange det ID: T om du är [reporting hälsotillståndet för ett distribuerat tjänstpaket] [ p4] eller [startar om kodpaketet i en distribuerad abonnemang] [p5].

Du hittar den **ServicePackageActivationId** för en distribuerad tjänst-paketet genom att fråga över [distribuerat tjänstpaket] [ p3] på en nod. När du frågar för den [distribueras tjänsttyper][p6], [distribueras repliker][p7], och [distribueras kodpaket ] [ p8] på en nod, frågeresultatet innehåller även den **ServicePackageActivationId** i det överordnade distribuerade-abonnemang.

> [!NOTE]
>- Under delas som värd processmodellen, på en viss nod för ett visst program bara en kopia av en *ServicePackage* har aktiverats. Den har en **ServicePackageActivationId** lika *tom sträng*, och behöver inte anges när du utför åtgärder som rör distribuerat tjänstpaket. 
>
> - Under den exklusiva Process som är värd på en viss nod för ett visst program modellera en eller flera kopior av en *ServicePackage* kan vara aktiv. Varje aktivering har en *icke-tomma* **ServicePackageActivationId**, som anges när du utför åtgärder som rör distribuerat tjänstpaket. 
>
> - Om **ServicePackageActivationId** är utelämnas används som standard *tom sträng*. Om en distribuerad tjänst-paket som har aktiverats under delade processmodellen finns utföra åtgärden på den. Annars misslyckas åtgärden.
>
> - Inte fråga en gång och cache i **ServicePackageActivationId**. ID: T genereras dynamiskt och kan ändra av olika anledningar. Innan du utför en åtgärd som behöver **ServicePackageActivationId**, bör du först läsa listan över [distribuerat tjänstpaket] [ p3] på en nod. Använd sedan den **ServicePackageActivationId** från frågeresultat att utföra den ursprungliga åtgärden.
>
>

## <a name="guest-executable-and-container-applications"></a>Gästen körbar fil och en behållare program
Service Fabric att hantera [gästfil] [ a2] och [behållare] [ a3] program som tillståndslösa tjänster som är fristående. Det finns inga Service Fabric-körning i *ServiceHost* (en process eller behållare). Eftersom de här tjänsterna är fristående, antalet repliker per *ServiceHost* gäller inte för dessa tjänster. Den vanligaste konfigurationen används med dessa tjänster är en partition, med [InstanceCount] [ c2] lika med-1 (en kopia av den kod som körs på varje nod i klustret). 

Standard **ServicePackageActivationMode** för dessa tjänster är **SharedProcess**, i vilket fall Service Fabric endast aktiverar en kopia av *ServicePackage* på en nod för ett visst program.  Det innebär att bara en kopia av koden körs på en nod. Om du vill att flera kopior av koden för tjänsten att köra på en nod, ange **ServicePackageActivationMode** som **ExclusiveProcess** vid tidpunkten för skapandet av tjänsten. Exempel: du kan göra detta när du skapar flera tjänster (*Service1* till *ServiceN*) av *ServiceType* (anges i *ServiceManifest*), eller när tjänsten är multi-partitionerad. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Ändra den som värd för en befintlig tjänst
Just nu, kan inte du ändra modellen som värd för en befintlig tjänsts från delade Process till exklusiv Process (eller vice versa).

## <a name="choose-between-the-hosting-models"></a>Välj mellan värd
Du ska utvärdera vilka värdmodell som bäst passar dina behov. Delade processmodellen använder operativsystemresurser bättre, eftersom färre processer är värdprocessen och flera repliker i samma process kan dela portar. Om en av replikerna har ett fel där de behövs och påverkar tjänstevärden påverkar det alla repliker i samma process.

 Exklusiva processmodellen ger bättre isolering med varje replik i en egen process. Om en av replikerna har ett fel påverkar inte andra repliker. Den här modellen är användbart i de fall där delning av port inte stöds av kommunikationsprotokollet. Det underlättar möjligheten att använda resursstyrning på replik-nivå. Exklusiva Process förbrukar dock mer systemresurser, som det tio en process för varje replik på noden.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exklusiva processmodellen och programmet modellera överväganden
För de flesta program kan du utforma ditt program i Service Fabric genom att hålla en *ServiceType* per *ServicePackage*. 

För vissa fall, Service Fabric kan också fler än en *ServiceType* per *ServicePackage* (en *CodePackage* kan registrera mer än en  *ServiceType*). Här följer några scenarier där de här konfigurationerna kan vara användbara:

- Du vill optimera resursutnyttjandet genom att skapa färre processer och med högre densitet för repliken processer.
- Repliker från olika *ServiceTypes* behöver dela vissa gemensamma data som har en hög initieringen eller minne kostnad.
- Du har en kostnadsfri tjänst som erbjuder och du vill publicera en gräns på Resursanvändning genom att ange alla repliker för tjänsten i samma process.

Exklusiva processen värdmodell är inte konsekvent med en modell för program med flera *ServiceTypes* per *ServicePackage*. Detta beror på flera *ServiceTypes* per *ServicePackage* är utformade för att uppnå högre resursdelning mellan repliker och möjliggör större repliken densitet processer. Exklusiva processmodellen är utformad för att få olika resultat.

Ta fallet med flera *ServiceTypes* per *ServicePackage*, med en annan *CodePackage* registrera var och en *ServiceType*. Anta att vi har en *ServicePackage* ”MultiTypeServicePackage” som har två *CodePackages*:

- ”MyCodePackageA” som registrerar *ServiceType* 'MyServiceTypeA'.
- ”MyCodePackageB” som registrerar *ServiceType* 'MyServiceTypeB'.

Nu kan anta att vi skapar ett program, **fabric: / SpecialApp**. Inuti **fabric: / SpecialApp**, skapar vi följande två tjänster med exklusiva processmodellen:

- Tjänsten **fabric: / SpecialApp/ServiceA** av typen MyServiceTypeA, med två partitioner (till exempel **P1** och **P2**), och tre repliker per partition.
- Tjänsten **fabric: / SpecialApp/ServiceB** av typen MyServiceTypeB, med två partitioner (**P3** och **P4**), och tre repliker per partition.

Båda tjänsterna har två repliker på en viss nod. Eftersom vi kan användas exklusivt processmodellen för att skapa tjänsterna, aktiverar Service Fabric en ny kopia av 'MyServicePackage' för varje replik. Varje aktivering av ”MultiTypeServicePackage-startar en kopia av 'MyCodePackageA' och 'MyCodePackageB'. Men endast en av 'MyCodePackageA' eller 'MyCodePackageB' är värd för repliken som 'MultiTypeServicePackage' har aktiverats. Följande diagram visar vyn nod:


![Diagram över en nod-bild av distribuerade program][node-view-five]


I aktiveringen av 'MultiTypeServicePackage' för repliken av partitionen **P1** tjänsternas **fabric: / SpecialApp/ServiceA**, 'MyCodePackageA' är värd för repliken. 'MyCodePackageB' körs. På samma sätt i aktiveringen av 'MultiTypeServicePackage' för repliken av partitionen **P3** tjänsternas **fabric: / SpecialApp/ServiceB**, 'MyCodePackageB' är värd för repliken. 'MyCodePackageA' körs. Därför kan ju fler *CodePackages* (registrera olika *ServiceTypes*) per *ServicePackage*, desto högre redundant Resursanvändning. 
 
 Men om vi skapar tjänsterna **fabric: / SpecialApp/ServiceA** och **fabric: / SpecialApp/ServiceB** med delade processmodellen, Service Fabric aktiverar bara en kopia av ” MultiTypeServicePackage' för programmet **fabric: / SpecialApp**. Alla repliker för tjänsten är värd för 'MyCodePackageA' **fabric: / SpecialApp/ServiceA**. Alla repliker för tjänsten är värd för 'MyCodePackageB' **fabric: / SpecialApp/ServiceB**. Följande diagram visar vyn noden i den här inställningen: 


![Diagram över en nod-bild av distribuerade program][node-view-six]


I föregående exempel kan ses som om 'MyCodePackageA' registrerar både 'MyServiceTypeA' och 'MyServiceTypeB', och det finns inga MyCodePackageB så är det inte redundant *CodePackage* körs. Även om detta stämmer överensstämmer den här programmodell inte med exklusiva processen värdmodell. Om målet är att placera varje replik i en egen dedikerad process kan du inte behöver registrera både *ServiceTypes* från samma *CodePackage*. I stället kan du enkelt uttryckt varje *ServiceType* i sin egen *ServicePackage*.

## <a name="next-steps"></a>Nästa steg
[Paketera ett program] [ a4] och redo att distribuera.

[Distribuera och ta bort program][a5]. Den här artikeln beskriver hur du använder PowerShell för att hantera programinstanser.

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
