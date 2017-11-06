---
title: "Azure Service Fabric värdmodell | Microsoft Docs"
description: "Beskriver relationen mellan repliker (eller instanser) av en distribuerad Servic Fabric-tjänst och värdprocessen för tjänsten."
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
ms.openlocfilehash: ecc9038cf895ddaeb06dd0e4e9852d5ef4a4513a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="service-fabric-hosting-model"></a>Service Fabric värdmodell
Den här artikeln innehåller en översikt över program som är värd för modeller som tillhandahålls av Service Fabric och beskrivs skillnaderna mellan de **delad Process** och **exklusiv processen** modeller. Beskriver hur ett distribuerat program visas på ett Service Fabric-noden och förhållandet mellan repliker (eller instanser) och service host-processen.

Innan du fortsätter, kontrollera att du är bekant med [Service Fabric-programmodell] [ a1] och förstå olika begrepp och relationen mellan dem. 

> [!NOTE]
> I den här artikeln för enkelhetens skull, såvida inte uttryckligen anges:
>
> - All användning av word *replik* refererar till både en replik av en tillståndskänslig tjänst eller en instans av en tillståndslös tjänst.
> - *CodePackage* är behandlade motsvarar *ServiceHost* process som registrerar en *ServiceType* och värdar repliker tjänster av som *ServiceType*.
>

För att förstå värdmodellen, låt oss gå igenom ett exempel. Låt oss säga vi har en *ApplicationType* 'MyAppType' som har en *ServiceType* 'MyServiceType' som tillhandahålls av *ServicePackage* 'MyServicePackage' som har en *CodePackage* 'MyCodePackage' som registrerar *ServiceType* 'MyServiceType' när den körs.

Anta att vi har ett kluster med 3 noder och skapar vi ett *programmet* **fabric: / App1** av typen 'MyAppType'. I detta *programmet* **fabric: / App1** vi skapa en tjänst **fabric: / App1/ServiceA** av typen 'MyServiceType' som har 2 partitioner (säg **P1** & **P2**) och 3 repliker per partition. Följande diagram visar vyn i det här programmet eftersom det hamnar distribuerad på en nod.

<center>
![Noden vy av distribuerade program][node-view-one]
</center>

Service Fabric aktiverat 'MyServicePackage' som startade 'MyCodePackage' som är värd repliker från båda partitionerna d.v.s. **P1** & **P2**. Observera att alla noder i klustret har samma vy eftersom vi valde antal repliker per partition som är lika med antalet noder i klustret. Nu ska vi skapa en annan tjänst **fabric: / App1/ServiceB** i programmet **fabric: / App1** som har 1 partition (säg **P3**) och 3 repliker per partition. Följande diagram visar den nya vyn på noden:

<center>
![Noden vy av distribuerade program][node-view-two]
</center>

Som vi ser Service Fabric placeras den nya repliken för partition **P3** för tjänsten **fabric: / App1/ServiceB** i den befintliga 'MyServicePackage'-aktiveringen. Nu kan skapa en ny *programmet* **fabric: / App2** av typen 'MyAppType' och inuti **fabric: / App2** skapa tjänst **fabric: / App2/ServiceA** som har 2 partitioner (säger **P4** & **P5**) och 3 repliker per partition. Följande diagram visar den nya nod vyn:

<center>
![Noden vy av distribuerade program][node-view-three]
</center>

Den här gången Service Fabric har aktiverat en ny kopia av 'MyServicePackage ”som startar en ny kopia av 'MyCodePackage' och repliker från både partitioner för tjänsten **fabric: / App2/ServiceA** (d.v.s. **P4** & **P5**) placeras i den här nya kopian 'MyCodePackage'.

## <a name="shared-process-model"></a>Delade processmodell
Vi såg ovan är standard värdmodell som tillhandahålls av Service Fabric och kallas **delad Process** modell. I den här modellen för en given *programmet*, bara en kopia av en angivna *ServicePackage* aktiveras på en *nod* (som startar alla den *CodePackages* finns i den) och alla repliker av alla tjänster i en angivna *ServiceType* placeras i den *CodePackage* som registrerar som *ServiceType*. Med andra ord alla repliker för alla tjänster på en nod i ett givet *ServiceType* delar samma process.

## <a name="exclusive-process-model"></a>Exklusiv processmodell
Den andra värdmodell som tillhandahålls av Service Fabric är **exklusiv processen** modell. I den här modellen på en viss *nod*, placera varje replik Service Fabric aktiverar en ny kopia av *ServicePackage* (som startar alla den *CodePackages* finns i den) och repliken placeras i den *CodePackage* som registrerats i *ServiceType* för tjänsten som repliken tillhör. Med andra ord bor varje replik i sin egen dedikerade process. 

Den här modellen stöds startversion 5.6 i Service Fabric. **Exklusiv processen** modell kan väljas vid tidpunkten för skapandet av tjänsten (med hjälp av [PowerShell][p1], [REST] [ r1] eller [FabricClient][c1]) genom att ange **ServicePackageActivationMode** som 'ExclusiveProcess'.

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

Om du har en standardtjänst i programmanifestet kan du välja **exklusiv processen** modellen genom att ange **ServicePackageActivationMode** attribut som visas nedan:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Fortsätter med vårt exempel ovan kan skapa en annan tjänst **fabric: / App1/ServiceC** i programmet **fabric: / App1** som har 2 partitioner (säg **P6** & **P7**) och 3 repliker per partition med **ServicePackageActivationMode** inställd på 'ExclusiveProcess'. Följande diagram visar ny vy på noden:

<center>
![Noden vy av distribuerade program][node-view-four]
</center>

Som du kan se Service Fabric aktiverat två nya kopior av 'MyServicePackage' (en för varje replik från partition **P6** & **P7**) och placeras varje replik i den dedikerade kopian av *CodePackage*. Märke här är till när **exklusiv processen** modellen används för en viss *program*, flera kopior av en viss *ServicePackage* kan vara aktiva i en *nod*. I ovanstående exempel vi se att tre kopior av 'MyServicePackage' är aktiva för **fabric: / App1**. Var och en av dessa active kopior av 'MyServicePackage' har en **ServicePackageActivationId** som är associerade med den som identifierar kopian i *programmet* **fabric: / App1**.

När bara **delad Process** mall används för ett *programmet*, till exempel **fabric: / App2** i ovanstående exempel det finns endast en aktiv kopia av *ServicePackage*  på en *nod* och **ServicePackageActivationId** för den här aktiveringen av *ServicePackage* ' tom sträng ”.

> [!NOTE]
>- **Delad Process** värdmodell motsvarar **ServicePackageActivationMode** lika **SharedProcess**. Detta är standard värdmodell och **ServicePackageActivationMode** behöver inte anges vid tidpunkten för skapandet av tjänsten.
>
>- **Exklusiv processen** värdmodell motsvarar **ServicePackageActivationMode** lika **ExclusiveProcess** och måste uttryckligen anges vid tidpunkten för skapandet av tjänsten. 
>
>- Värdmodell av en tjänst kan identifieras genom att fråga den [tjänstbeskrivningen] [ p2] och titta på värdet för **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Arbeta med distribuerat tjänstpaket
En aktiv kopia av en *ServicePackage* på en nod kallas [distribuerat tjänstpaket][p3]. Som tidigare nämnts ovan, när **exklusiv processen** modellen används för att skapa tjänster, för en given *programmet*, det kan finnas flera distribuerad tjänstpaket för samma *ServicePackage*. När du utför åtgärder som är specifika för distribuerade tjänstpaket som [reporting hälsotillståndet för ett distribuerat tjänstpaket] [ p4] eller [att starta om kodpaketet för en distribuerad tjänstepaketet] [ p5] osv., **ServicePackageActivationId** måste anges för att identifiera en viss distribuerade tjänstpaket.

 **ServicePackageActivationId** för en distribuerad tjänst paketet kan erhållas genom en fråga till listan över [distribuerat tjänstpaket] [ p3] på en nod. När du frågar [distribueras tjänsttyper][p6], [distribueras repliker] [ p7] och [distribuerade kod paket] [ p8] på en nod, frågeresultatet innehåller även den **ServicePackageActivationId** i det överordnade distribuerade abonnemang.

> [!NOTE]
>- Under **delad Process** värdmodell, på en angivna *nod*, för en given *programmet*, bara en kopia av en *ServicePackage* har aktiverats. Den har **ServicePackageActivationId** lika med *tom sträng* och behöver inte anges när utför distribuerat tjänstpaket relaterade åtgärder. 
>
> - Under **exklusiv processen** värdmodell, på en angivna *nod*, för en given *programmet*, en eller flera kopior av en *ServicePackage* kan vara aktiv. Varje aktivering har en *icke-tom* **ServicePackageActivationId** och måste anges när du utför distribuerade tjänsten paketet relaterade åtgärder. 
>
> - Om **ServicePackageActivationId** är utelämnas den 'tom sträng ”som standard. Om en distribuerad tjänst paketet som har aktiverats **delad Process** modellen finns och kommer att utföra åtgärden på den, annars misslyckas åtgärden.
>
> - Det rekommenderas inte att fråga en gång och cachelagra **ServicePackageActivationId** eftersom det genereras dynamiskt och ändra av olika skäl. Innan du utför en åtgärd som måste **ServicePackageActivationId**, bör du först läsa listan [distribuerat tjänstpaket] [ p3] på en nod och sedan använda  **ServicePackageActivationId** från frågeresultat ursprungliga utföras.
>
>

## <a name="guest-executable-and-container-applications"></a>Gästen körbar fil och en behållare program
Service Fabric behandlar [gäst körbara] [ a2] och [behållare] [ a3] program som statless tjänster som är fristående dvs. det finns ingen Service Fabric-körningsmiljön i *ServiceHost* (en process eller behållaren). Eftersom dessa tjänster är fristående, antal repliker per *ServiceHost* gäller inte för dessa tjänster. Den vanligaste konfigurationen används med tjänsterna är enskild partition med [InstanceCount] [ c2] lika med-1 (dvs. en kopia av den kod som körs på varje nod i klustret). 

Standard **ServicePackageActivationMode** tjänsterna är **SharedProcess** då Service Fabric endast aktiverar en kopia av *ServicePackage* på en *nod* för en given *programmet* vilket innebär att endast en kopia av tjänstkoden körs en *nod*. Om du vill att flera kopior av koden för tjänsten att köra på en *nod* när du skapar flera tjänster (*Service1* till *ServiceN*) av *ServiceType* (anges i *ServiceManifest*) eller när tjänsten är multi-partitionerat, bör du ange **ServicePackageActivationMode** som **ExclusiveProcess** vid tidpunkten för skapandet av tjänsten.

## <a name="changing-hosting-model-of-an-existing-service"></a>Ändra värdmodell en befintlig tjänst
Ändra värdmodell en befintlig tjänst från **delad Process** till **exklusiv processen** och vice versa via uppgradera eller uppdatera mekanism (eller tjänst i specifikationen i programmanifestet) stöds inte för närvarande. Stöd för den här funktionen kommer i framtida versioner.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Att välja mellan delad process och exklusiv processmodell
Båda dessa värd modeller har dess för- och nackdelar och användare måste utvärdera vilket som bäst passar deras behov. **Delad Process** modellen ger bättre användning av OS-resurser eftersom färre processer har initierat, flera repliker i samma process kan dela portar, osv. Om en av replikerna träffar ett fel där som behövs för att sätta ned tjänstvärden påverkar dock alla repliker i samma process.

 **Exklusiv processen** modellen ger bättre isolering med varje replik i en egen process och en replik som påverkar inte andra repliker. Det är praktiskt i de fall där portdelning inte stöds av kommunikationsprotokollet. Det underlättar möjligheten att använda resursen styrning på replik-nivå. Å andra sidan **exklusiv processen** förbrukar mer OS-resurser som den skapas en process för varje replik på noden.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exklusiv processmodellen och programmet modellen överväganden
Det rekommenderade sättet att modellera ditt program i Service Fabric är att behålla en *ServiceType* per *ServicePackage* och den här modellen fungerar bra för de flesta program. 

Är avsedda för vissa användningsfall, Service Fabric kan också flera *ServiceType* per *ServicePackage* (och en *CodePackage* kan registrera fler än en  *ServiceType*). Följande är några scenarier där de här konfigurationerna kan vara användbara:

- Vill du optimera OS resursutnyttjande genom att skapa färre processer och med tätare replik processer.
- Repliker från olika ServiceTypes måste du dela vissa vanliga data som har en hög initiering eller minne kostnad.
- Du har ett ledigt tjänsterbjudande och du vill publicera en gräns på resursutnyttjande genom att placera alla repliker för tjänsten i samma process.

**Exklusiv processen** värdmodell är inte konsekvent med programmodell att flera *ServiceTypes* per *ServicePackage*. Detta beror på att flera *ServiceTypes* per *ServicePackage* är utformat för att uppnå högre resursdelning mellan replikerna och möjliggör tätare replik processer. Detta är strider mot vad **exklusiv processen** modellen för att uppnå.

Ta fallet med flera *ServiceTypes* per *ServicePackage* med olika *CodePackage* registrera varje *ServiceType*. Anta att vi har en *ServicePackage* 'MultiTypeServicePackge' som har två *CodePackages*:

- 'MyCodePackageA' som registrerar *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB' som registrerar *ServiceType* 'MyServiceTypeB'.

Nu kan exempelvis skapar vi ett *programmet* **fabric: / SpecialApp** och i **fabric: / SpecialApp** vi skapa följande två tjänster med **exklusiv processen** modellen:

- Tjänsten **fabric: / SpecialApp/ServiceA** av typen 'MyServiceTypeA' med två partitioner (säg **P1** och **P2**) och 3 repliker per partition.
- Tjänsten **fabric: / SpecialApp/ServiceB** av typen 'MyServiceTypeB' med två partitioner (säg **P3** och **P4**) och 3 repliker per partition.

På en viss nod, kommer båda tjänsterna har två repliker. Eftersom vi använde **exklusiv processen** modellen för att skapa tjänster, Service Fabric aktiverar en ny kopia av 'MyServicePackge' för varje replik. Varje aktivering av 'MultiTypeServicePackge' startar en kopia av 'MyCodePackageA' och 'MyCodePackageB'. Endast en av 'MyCodePackageA' eller 'MyCodePackageB' ska dock vara värd för repliken som 'MultiTypeServicePackge' har aktiverats. Följande diagram visar vyn nod:

<center>
![Noden vy av distribuerade program][node-view-five]
</center>

 Som vi kan se i aktivering av 'MultiTypeServicePackge' för replik av partitionen **P1** för tjänsten **fabric: / SpecialApp/ServiceA**'MyCodePackageA' är värd för repliken och 'MyCodePackageB' är bara igång. På samma sätt i aktivering av 'MultiTypeServicePackge' för replik av partitionen **P3** för tjänsten **fabric: / SpecialApp/ServiceB**, 'MyCodePackageB' är värd för repliken och 'MyCodePackageA' är bara Kära och så vidare. Därför mer antalet *CodePackages* (registrera olika *ServiceTypes*) per *ServicePackage*, högre är redundant Resursanvändning. 
 
 Å andra sidan om vi skapa tjänster **fabric: / SpecialApp/ServiceA** och **fabric: / SpecialApp/ServiceB** med **delad Process** modellen, Service Fabric aktiveras bara en kopia av 'MultiTypeServicePackge' för *programmet* **fabric: / SpecialApp** (som vi såg tidigare). 'MyCodePackageA' ska vara värd för alla repliker för tjänsten **fabric: / SpecialApp/ServiceA** (eller för en tjänst av typen 'MyServiceTypeA' ska vara mer exakt) och 'MyCodePackageB' ska vara värd för alla repliker för tjänsten **fabric: / SpecialApp/ServiceB** (eller för en tjänst av typen 'MyServiceTypeB' ska vara mer exakt). Följande diagram visar vyn nod i den här inställningen: 

<center>
![Noden vy av distribuerade program][node-view-six]
</center>

I exemplet ovan kan du tror om 'MyCodePackageA' registrerar både 'MyServiceTypeA' och 'MyServiceTypeB' och det finns inga MyCodePackageB sedan det blir inte redundant *CodePackage* körs. Det här är korrekt, men som tidigare nämnts är det här programmodell inte överensstämmer med **exklusiv processen** värdmodell. Om målet är att placera varje replik i sin egen dedikerade bearbetar registrera både *ServiceTypes* från samma *CodePackage* inte behövs och placera varje *ServiceType* i sin egen *ServicePacakge* är ett mer naturligt val.

## <a name="next-steps"></a>Nästa steg
[Paketerar ett program] [ a4] och redo att distribuera.

[Distribuera och ta bort program] [ a5] beskriver hur du använder PowerShell för att hantera programinstanser.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-deploy-existing-app.md
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
