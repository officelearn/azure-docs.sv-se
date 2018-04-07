---
title: Azure Service Fabric-värdmodell | Microsoft Docs
description: Beskriver relationen mellan repliker (eller instanser) av en distribuerad tjänst för Service Fabric och tjänstvärden processen.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: b2ba019f21256ee98276ef30847c43709b9b3462
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric-värdmodell
Den här artikeln innehåller en översikt över program som är värd för modeller som tillhandahålls av Azure Service Fabric och beskrivs skillnaderna mellan de **delad Process** och **exklusiv processen** modeller. Beskriver hur ett distribuerat program ser ut på en Service Fabric-nod och relationen mellan repliker (eller instanser) och service host-processen.

Innan du fortsätter, Tänk på att du förstår de olika principerna och relationer som beskrivs i [modellen är ett program i Service Fabric][a1]. 

> [!NOTE]
> I den här artikeln om du inte uttryckligen anges som något annat:
>
> - *Replik* refererar till både en replik av en tillståndskänslig tjänst och en instans av en tillståndslös tjänst.
> - *CodePackage* behandlas som motsvarar en *ServiceHost* process som registrerar en *ServiceType*, och värdar repliker tjänster av som *ServiceType*.
>

För att förstå värdmodellen, låt oss gå igenom ett exempel. Anta att vi har en *ApplicationType* 'MyAppType' som har en *ServiceType* 'MyServiceType'. 'MyServiceType' tillhandahålls av den *ServicePackage* 'MyServicePackage' som har en *CodePackage* 'MyCodePackage'. 'MyCodePackage' registrerar *ServiceType* 'MyServiceType' när den körs.

Anta att vi har ett kluster med tre noder och skapar vi ett *programmet* **fabric: / App1** av typen 'MyAppType'. I det här tillämpningsprogrammet **fabric: / App1**, skapar vi en tjänst **fabric: / App1/ServiceA** av typen 'MyServiceType'. Den här tjänsten har två partitioner (till exempel **P1** och **P2**), och tre repliker per partition. Följande diagram visar vyn i det här programmet eftersom det hamnar distribuerad på en nod.


![Diagram över nod vy av distribuerade program][node-view-one]


Service Fabric aktiverad 'MyServicePackage', som startade 'MyCodePackage' som är värd för repliker från båda partitionerna. Alla noder i klustret har samma, eftersom vi valde antal repliker per partition ska vara lika med antalet noder i klustret. Nu ska vi skapa en annan tjänst **fabric: / App1/ServiceB**, i programmet **fabric: / App1**. Den här tjänsten har en partition (till exempel **P3**), och tre repliker per partition. Följande diagram visar den nya vyn på noden:


![Diagram över nod vy av distribuerade program][node-view-two]


Service Fabric placeras den nya repliken för partition **P3** för tjänsten **fabric: / App1/ServiceB** i den befintliga 'MyServicePackage'-aktiveringen. Nu. Nu ska vi skapa ett annat program **fabric: / App2** av typen 'MyAppType'. I **fabric: / App2**, skapar du en tjänst **fabric: / App2/ServiceA**. Den här tjänsten har två partitioner (**P4** och **P5**), och tre repliker per partition. Följande diagram visar den nya nod vyn:


![Diagram över nod vy av distribuerade program][node-view-three]


Service Fabric aktiverar en ny kopia av 'MyServicePackage', som startar en ny kopia av 'MyCodePackage'. Repliker från både partitioner för tjänsten **fabric: / App2/ServiceA** (**P4** och **P5**) placeras i den här nya kopian 'MyCodePackage'.

## <a name="shared-process-model"></a>Delade processmodell
I föregående avsnitt beskrivs standard värdmodell som tillhandahålls av Service Fabric kallas delad processmodellen. I den här modellen för ett visst program endast en kopia av en viss *ServicePackage* har aktiverats på en nod (som startar alla de *CodePackages* finns i den). Alla repliker av alla tjänster i en viss *ServiceType* placeras i den *CodePackage* som registrerar som *ServiceType*. Med andra ord alla repliker för alla tjänster på en nod i ett givet *ServiceType* delar samma process.

## <a name="exclusive-process-model"></a>Exklusiv processmodell
Den andra värdmodell som tillhandahålls av Service Fabric är exklusiv processmodellen. I den här modellen på en viss nod bor varje replik i sin egen dedikerade process. Service Fabric aktiverar en ny kopia av *ServicePackage* (som startar alla de *CodePackages* finns i den). Repliker placeras i den *CodePackage* som registrerats i *ServiceType* för tjänsten som repliken tillhör. 

Om du använder Service Fabric version 5.6 eller senare, kan du välja exklusiv processmodellen när du skapar en tjänst (med hjälp av [PowerShell][p1], [REST] [ r1], eller [FabricClient][c1]). Ange **ServicePackageActivationMode** som 'ExclusiveProcess'.

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

Om du har en standardtjänst i programmanifestet kan du välja exklusiv processmodellen genom att ange den **ServicePackageActivationMode** attribut:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nu ska vi skapa en annan tjänst **fabric: / App1/ServiceC**, i programmet **fabric: / App1**. Den här tjänsten har två partitioner (till exempel **P6** och **P7**), och tre repliker per partition. Du ställer in **ServicePackageActivationMode** till 'ExclusiveProcess'. Följande diagram visar ny vy på noden:


![Diagram över nod vy av distribuerade program][node-view-four]


Som du kan se Service Fabric aktiverat två nya kopior av 'MyServicePackage' (en för varje replik från partition **P6** och **P7**). Service Fabric placeras varje replik i den dedikerade kopian av *CodePackage*. När du använder uteslutande processmodellen för ett visst program flera kopior av en viss *ServicePackage* kan vara aktiv på en nod. I föregående exempel tre kopior av 'MyServicePackage' är aktiva för **fabric: / App1**. Var och en av dessa active kopior av 'MyServicePackage' har en **ServicePackageActivationId** som är kopplade till den. Detta ID identifierar kopian i programmet **fabric: / App1**.

När du använder bara delade processmodellen för ett program, det finns endast en aktiv kopia av *ServicePackage* på en nod. Den **ServicePackageActivationId** för den här aktiveringen av *ServicePackage* är en tom sträng. Detta är fallet, till exempel med **fabric: / App2**.

> [!NOTE]
>- Processen delad värdmodell motsvarar **ServicePackageActivationMode** är lika med **SharedProcess**. Detta är standard värdmodell, och **ServicePackageActivationMode** behöver inte anges vid tidpunkten för skapandet av tjänsten.
>
>- Exklusiv processen värdmodell motsvarar **ServicePackageActivationMode** är lika med **ExclusiveProcess**. Om du vill använda den här inställningen ska du ange det explicit vid tidpunkten för skapandet av tjänsten. 
>
>- Om du vill visa värdmodell av en tjänst, fråga den [tjänstbeskrivningen][p2], och titta på värdet för **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Arbeta med en distribuerad tjänst-paket
En aktiv kopia av en *ServicePackage* på en nod kallas för en [distribuerat tjänstpaket][p3]. När du använder uteslutande processmodellen för att skapa tjänster, för ett visst program, det kan finnas flera distribuerad tjänstpaket för samma *ServicePackage*. Om du utför åtgärder som är specifika för en distribuerad tjänst-paketet, bör du ge **ServicePackageActivationId** att identifiera ett paket med specifika distribuerade tjänsten. Exempelvis ange ID om du är [reporting hälsotillståndet för ett distribuerat tjänstpaket] [ p4] eller [att starta om kodpaketet för en distribuerad tjänstepaketet] [p5].

Du kan ta reda på **ServicePackageActivationId** för en distribuerad tjänstepaketet genom att fråga listan över [distribuerat tjänstpaket] [ p3] på en nod. När du frågar för den [distribueras tjänsttyper][p6], [distribueras repliker][p7], och [distribuerade kod paket ] [ p8] på en nod, frågeresultatet innehåller även den **ServicePackageActivationId** i det överordnade distribuerade abonnemang.

> [!NOTE]
>- Under den delade värd processmodellen på en viss nod för ett visst program endast en kopia av en *ServicePackage* har aktiverats. Den har en **ServicePackageActivationId** lika med *tom sträng*, och behöver inte anges när du utför åtgärder som rör paketets distribuerade tjänsten. 
>
> - Under exklusiv processen värd på en viss nod för ett visst program Modellerar en eller flera kopior av en *ServicePackage* kan vara aktiv. Varje aktivering har en *icke-tom* **ServicePackageActivationId**, som anges när du utför åtgärder som rör paketets distribuerade tjänsten. 
>
> - Om **ServicePackageActivationId** är utelämnas används standardvärdet *tom sträng*. Om det finns en distribuerad tjänst-paket som har aktiverats under delade processmodellen utföra åtgärden på den. Annars misslyckas åtgärden.
>
> - Inte fråga en gång och cache på **ServicePackageActivationId**. ID som genereras dynamiskt och kan ändra av olika skäl. Innan du utför en åtgärd som måste **ServicePackageActivationId**, bör du först läsa listan [distribuerat tjänstpaket] [ p3] på en nod. Använd sedan den **ServicePackageActivationId** från frågeresultat ursprungliga utföras.
>
>

## <a name="guest-executable-and-container-applications"></a>Gästen körbar fil och en behållare program
Service Fabric behandlar [gäst körbara] [ a2] och [behållare] [ a3] program som tillståndslösa tjänster som är fristående. Det finns ingen Service Fabric-körningsmiljön i *ServiceHost* (en process eller behållaren). Eftersom dessa tjänster är fristående, antal repliker per *ServiceHost* gäller inte för dessa tjänster. Den vanligaste konfigurationen används med tjänsterna är enskild partition, med [InstanceCount] [ c2] lika med-1 (en kopia av den kod som körs på varje nod i klustret). 

Standard **ServicePackageActivationMode** tjänsterna är **SharedProcess**, i vilket fall Service Fabric endast aktiverar en kopia av *ServicePackage* på en nod för ett visst program.  Det innebär att endast en kopia av Tjänstkod körs på en nod. Om du vill att flera kopior av koden för tjänsten körs på en nod, ange **ServicePackageActivationMode** som **ExclusiveProcess** vid tidpunkten för skapandet av tjänsten. Exempel: du kan göra detta när du skapar flera tjänster (*Service1* till *ServiceN*) av *ServiceType* (anges i *ServiceManifest*), eller om din tjänst är partitionerad med flera. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Ändra en befintlig tjänst värd modellen
Just nu, kan inte du ändra värdmodell en befintlig tjänst från delad Process exklusiv processen (eller vice versa).

## <a name="choose-between-the-hosting-models"></a>Välj mellan värd
Du bör utvärdera vilka värdmodell som bäst passar dina krav. Delade processmodellen använder operativsystemresurser bättre, eftersom färre processer har initierat och flera repliker i samma process kan dela portar. Om en av replikerna har ett fel där som behövs för att sätta ned tjänstvärden påverkar den alla repliker i samma process.

 Exklusiv processmodellen ger bättre isolering med varje replik i en egen process. Om en av replikerna har ett fel påverkar inte andra repliker. Den här modellen är användbart i fall där portdelning inte stöds av kommunikationsprotokollet. Det underlättar möjligheten att använda resursen styrning på replik-nivå. Exklusiv Process förbrukar dock mer systemresurser, eftersom det skapas en process för varje replik på noden.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exklusiv processmodellen och programmet modellen överväganden
För de flesta program kan du modellera ditt program i Service Fabric genom att hålla en *ServiceType* per *ServicePackage*. 

I vissa fall Service Fabric kan också flera *ServiceType* per *ServicePackage* (och en *CodePackage* kan registrera fler än en  *ServiceType*). Följande är några scenarier där de här konfigurationerna kan vara användbara:

- Vill du optimera resursutnyttjande genom att skapa färre processer och med tätare replik processer.
- Repliker från olika *ServiceTypes* måste dela vissa vanliga data som har en hög initiering eller minne kostnad.
- Du har en kostnadsfri tjänsterbjudande och du vill publicera en gräns på resursutnyttjande genom att placera alla repliker för tjänsten i samma process.

Exklusiv processen värdmodell är inte konsekvent med en programmodell att flera *ServiceTypes* per *ServicePackage*. Detta beror på att flera *ServiceTypes* per *ServicePackage* är utformade för att uppnå högre resursdelning mellan replikerna och möjliggör tätare replik processer. Exklusiv processmodellen är utformat för att uppnå olika resultat.

Ta fallet med flera *ServiceTypes* per *ServicePackage*, med en annan *CodePackage* registrera varje *ServiceType*. Anta att vi har en *ServicePackage* 'MultiTypeServicePackge' som har två *CodePackages*:

- 'MyCodePackageA', som registrerar *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', som registrerar *ServiceType* 'MyServiceTypeB'.

Nu kan anta att vi skapa ett program, **fabric: / SpecialApp**. I **fabric: / SpecialApp**, skapar vi följande två tjänster med exklusiv processmodellen:

- Tjänsten **fabric: / SpecialApp/ServiceA** av typen MyServiceTypeA, med två partitioner (till exempel **P1** och **P2**), och tre repliker per partition.
- Tjänsten **fabric: / SpecialApp/ServiceB** av typen MyServiceTypeB, med två partitioner (**P3** och **P4**), och tre repliker per partition.

Båda tjänsterna har två repliker på en viss nod. Eftersom vi användas exklusivt processmodellen för att skapa tjänster aktiverar Service Fabric en ny kopia av 'MyServicePackage' för varje replik. Varje aktivering av 'MultiTypeServicePackge' startar en kopia av 'MyCodePackageA' och 'MyCodePackageB'. Dock endast en av 'MyCodePackageA' eller 'MyCodePackageB' är värd för repliken som 'MultiTypeServicePackge' har aktiverats. Följande diagram visar vyn nod:


![Diagram över vyn nod för distribuerade program][node-view-five]


I aktivering av 'MultiTypeServicePackge' för repliken av partitionen **P1** för tjänsten **fabric: / SpecialApp/ServiceA**, 'MyCodePackageA' är värd för repliken. 'MyCodePackageB' is running. På samma sätt i aktivering av 'MultiTypeServicePackge' för repliken av partitionen **P3** för tjänsten **fabric: / SpecialApp/ServiceB**, 'MyCodePackageB' är värd för repliken. 'MyCodePackageA' is running. Därför större antalet *CodePackages* (registrera olika *ServiceTypes*) per *ServicePackage*, desto högre redundant Resursanvändning. 
 
 Men om vi skapar tjänsterna **fabric: / SpecialApp/ServiceA** och **fabric: / SpecialApp/ServiceB** med delade processmodellen, Service Fabric aktiveras bara en kopia av ' MultiTypeServicePackge' för programmet **fabric: / SpecialApp**. Alla repliker för tjänsten är värd för 'MyCodePackageA' **fabric: / SpecialApp/ServiceA**. Alla repliker för tjänsten är värd för 'MyCodePackageB' **fabric: / SpecialApp/ServiceB**. Följande diagram visar vyn nod i den här inställningen: 


![Diagram över vyn nod för distribuerade program][node-view-six]


I föregående exempel du tror som om 'MyCodePackageA' registrerar både 'MyServiceTypeA' och 'MyServiceTypeB', och det finns inga MyCodePackageB sedan det är inte redundant *CodePackage* körs. Även om detta stämmer överensstämmer den här programmodell inte med exklusiv processen värdmodell. Om målet är att placera varje replik i sin egen dedikerade process, behöver du inte registrera både *ServiceTypes* från samma *CodePackage*. I stället du kort sagt varje *ServiceType* i sin egen *ServicePackage*.

## <a name="next-steps"></a>Nästa steg
[Paketerar ett program] [ a4] och redo att distribuera.

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

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
