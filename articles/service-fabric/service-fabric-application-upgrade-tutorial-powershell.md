---
title: Uppgradering av Service Fabric-appen med PowerShell | Microsoft Docs
description: Den här artikeln beskriver hur upplevelse av att distribuera ett Service Fabric-program, ändra koden och lansera uppgradering med hjälp av PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 44f4dc3a9c876e383a6e4df8ef5f467f2b93eaa9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205492"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Uppgradering av Service Fabric-programmet med PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

De vanligaste och rekommenderade uppgradera metoden är övervakade uppgraderingen.  Azure Service Fabric övervakar hälsotillståndet för programmet som ska uppgraderas baserat på en uppsättning hälsoprinciper. När en uppdateringsdomän (UD) har uppgraderats, Service Fabric utvärderar programmets hälsotillstånd och fortsätter till nästa uppdatering domänen eller så misslyckas uppgraderingen beroende på vilka hälsotillstånd principer.

En Programuppgradering av övervakade kan utföras med hjälp av den hanterade interna API: er, PowerShell eller REST. Anvisningar för att uppgradera med hjälp av Visual Studio finns [uppgradera ditt program med Visual Studio](service-fabric-application-upgrade-tutorial.md).

Med Service Fabric övervakas samlade uppgraderingar konfigurera programadministratören utvärdering hälsoprincipen som Service Fabric använder för att avgöra om programmet är felfritt. Dessutom kan konfigurera administratören åtgärden som ska vidtas när hälsotillståndet utvärderingen misslyckas (till exempel göra en automatisk återställning.) Det här avsnittet går igenom en övervakade uppgradering för en av de SDK-exempel som använder PowerShell. I följande Microsoft Virtual Academy video vägleder dig även genom en app-uppgradering: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Steg 1: Skapa och distribuera visuella objekt-exempel
Skapa och publicera programmet genom att högerklicka på projektet för konsolprogrammet **VisualObjectsApplication,** och välja den **publicera** kommando.  Mer information finns i [Service Fabric uppgradera självstudien](service-fabric-application-upgrade-tutorial.md).  Du kan också använda PowerShell för att distribuera ditt program.

> [!NOTE]
> Innan någon av Service Fabric-kommandon kan användas i PowerShell, måste du först ansluta till klustret med hjälp av den `Connect-ServiceFabricCluster` cmdlet. På liknande sätt, förutsätts det att klustret har redan konfigurerats på den lokala datorn. Se artikeln på [ställa in din utvecklingsmiljö för Service Fabric](service-fabric-get-started.md).
> 
> 

När du har skapat projektet i Visual Studio kan du använda PowerShell-kommandot [kopiera ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) att kopiera programpaketet till ImageStore. Om du vill verifiera app-paketet lokalt använder den [Test ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet. Nästa steg är att registrera programmet till Service Fabric runtime med den [registrera ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet. Följande steg är att starta en instans av programmet med hjälp av den [ny ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet.  De här tre stegen är analoga med hjälp av den **distribuera** menyalternativet i Visual Studio.  När etableringen har slutförts ska du rensa kopierade programpaketet från avbildningsarkivet för att minska de resurser som används.  Om en typ av program inte längre behövs, bör det att avregistrera av samma skäl. Se [distribuera och ta bort program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) för mer information.

Nu kan du använda [Service Fabric Explorer för att visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet har en webbtjänst som kan öppnas efter att i Internet Explorer genom att skriva [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) i adressfältet.  Du bör se vissa flytande visuella objekt flytta runt på skärmen.  Du kan dessutom använda [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) att kontrollera statusen för programmet.

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: Uppdatera visuella objekt-exempel
Du kan se att med den version som har distribuerats i steg 1 visuella objekt inte rotera. Vi uppgradera det här programmet till en där visual objekt också rotera.

Välj VisualObjects.ActorService-projekt i lösningen VisualObjects och öppna filen StatefulVisualObjectActor.cs. Navigera till metoden i filen, `MoveObject`, kommentera ut `this.State.Move()`, och Avkommentera `this.State.Move(true)`. Den här ändringen roterar objekten när tjänsten har uppgraderats.

Vi behöver uppdatera den *ServiceManifest.xml* fil (under PackageRoot) av projektet **VisualObjects.ActorService**. Uppdatering av *CodePackage* och service-version 2.0 och motsvarande rader i den *ServiceManifest.xml* fil.
Du kan använda Visual Studio *redigera Manifest filer* alternativ när du högerklickar på lösningen som ändrar manifestfilen.

När ändringarna sparas manifestet bör se ut ungefär så här (markerade delar visa ändringarna):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu den *ApplicationManifest.xml* fil (finns under den **VisualObjects** projektet den **VisualObjects** lösning) har uppdaterats till version 2.0 av den **VisualObjects.ActorService** projekt. Dessutom uppdateras programversionen till 2.0.0.0 från 1.0.0.0. Den *ApplicationManifest.xml* bör se ut som följande utdrag:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nu skapa projektet genom att välja just den **ActorService** -projektet och högerklicka sedan och välja den **bygga** alternativet i Visual Studio. Om du väljer **återskapa alla**, bör du uppdatera versioner för alla projekt, eftersom koden skulle ha ändrats. Sedan paketera vi uppdaterade programmet genom att högerklicka på ***VisualObjectsApplication***, att Service Fabric-menyn och välja **paketet**. Den här åtgärden skapar ett programpaket som kan distribueras.  Uppdaterade programmet är redo att distribueras.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Steg 3: Bestäm hälsoprinciper och uppgradering av parametrar
Bekanta dig med de [uppgradera applikationsparametrarna](service-fabric-application-upgrade-parameters.md) och [uppgraderingsprocessen](service-fabric-application-upgrade.md) att få en god förståelse av de olika uppgradera parametrar, timeout och hälsotillstånd kriterium tillämpas. I den här genomgången service hälsa utvärdering kriteriet är som standard (och rekommenderas) värden, vilket innebär att alla tjänster och instanser ska vara *felfri* efter uppgraderingen.  

Men vi öka den *HealthCheckStableDuration* till 60 sekunder (så att tjänsterna som är felfri för minst 20 sekunder innan uppgraderingen fortsätter till nästa uppdatering domänen).  Vi ska också ange den *UpgradeDomainTimeout* vara 1200 sekunder och *UpgradeTimeout* ska 3000 sekunder.

Slutligen ska vi också ställa in den *UpgradeFailureAction* att återställa. Det här alternativet kräver Service Fabric att återställa programmet till föregående version om det uppstår problem under uppgraderingen. När du startar uppgraderingen (i steg 4) anges innebär följande parametrar:

FailureAction = återställning

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Steg 4: Förbereda program för uppgradering
Programmet är nu inbyggda och redo att uppgraderas. Om du öppnar ett PowerShell-fönster som administratör och skriv [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), bör den gör att du vet att den är programtyp 1.0.0.0 av **VisualObjects** som har distribuerats.  

Programpaketet lagras under följande relativa sökväg där du okomprimerade Service Fabric-SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Katalogen där programpaketet lagras bör du hitta en ”paketmapp”. Kontrollera tidsstämplar för att säkerställa att den senaste versionen (du kan behöva ändra samt).

Nu ska vi kopiera det uppdaterade programpaketet till Service Fabric-Avbildningsarkiv (där programpaketen lagras av Service Fabric). Parametern *ApplicationPackagePathInImageStore* informerar Service Fabric där den kan hitta programpaketet. Vi har gjort det uppdaterade programmet ”VisualObjects\_V2” med följande kommando (du kan behöva ändra sökvägarna igen på lämpligt sätt).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Nästa steg är att registrera programmet med Service Fabric som kan utföras med hjälp av den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) kommando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Om föregående kommando inte lyckas är det troligt att du behöver en återskapning av alla tjänster. Som anges i steg 2, kanske du behöver uppdatera din WebService-version.

Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Ta bort programpaket från avbildningsarkivet frigör systemresurser.  Att hålla oanvända programpaket förbrukar disklagring och leder till problem med prestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Steg 5: Starta uppgraderingen av programmet
Nu ska vi allt är klart att starta uppgraderingen av program med hjälp av den [Start ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) kommando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Programnamnet är samma som det beskrivs i den *ApplicationManifest.xml* fil. Service Fabric använder det här namnet för att identifiera vilket program komma uppgraderas. Om du anger timeout vara för kort kan stöta du på ett felmeddelande om problemet. Finns i avsnittet om felsökning, eller öka timeout-alternativen.

Nu som uppgraderingen fortsätter programmet kan du övervaka den med hjälp av Service Fabric Explorer eller genom att använda den [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell-kommando: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Om några minuter status som du fick med hjälp av kommandot ovan PowerShell bör ange att alla domäner som uppdateringen har uppgraderats (slutförd). Och du finner att visual objekten i webbläsarfönstret har startat rotera!

Du kan uppgradera från version 2 till version 3 eller från version 2 till version 1 som en övning. Flytta från version 2 till version 1 anses också vara en uppgradering. Spela upp med tidsgränser och hälsoprinciper ska bli bekant med dem. När du distribuerar till ett Azure-kluster måste vara korrekt konfigurerade parametrar. Det är bra att ange timeout-alternativen hänsyn.

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

Styra hur programmet ska uppgraderas med hjälp av [Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade alternativ](service-fabric-application-upgrade-advanced.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

