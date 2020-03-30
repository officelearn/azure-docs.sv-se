---
title: Uppgradering av Service Fabric App med PowerShell
description: Den här artikeln går igenom upplevelsen av att distribuera ett Service Fabric-program, ändra koden och distribuera en uppgradering med PowerShell.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: b113b5a1042518e3b0d86e53796c5fe49afed418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426789"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Uppgradering av Service Fabric-programmet med PowerShell
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Den mest använda och rekommenderade uppgraderingsmetoden är den övervakade rullande uppgraderingen.  Azure Service Fabric övervakar hälsotillståndet för det program som uppgraderas baserat på en uppsättning hälsoprinciper. När en uppdateringsdomän (UD) har uppgraderats utvärderar Service Fabric programmets hälsotillstånd och fortsätter antingen till nästa uppdateringsdomän eller misslyckas uppgraderingen beroende på hälsoprinciperna.

En övervakad programuppgradering kan utföras med hanterade eller inbyggda API:er, PowerShell, Azure CLI, Java eller REST. Instruktioner om hur du utför en uppgradering med Visual Studio finns i [Uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md).

Med Service Fabric övervakad rullande uppgraderingar kan programadministratören konfigurera hälsoutvärderingsprincipen som Service Fabric använder för att avgöra om programmet är felfritt. Dessutom kan administratören konfigurera åtgärden som ska vidtas när hälsoutvärderingen misslyckas (till exempel gör en automatisk återställning.) Det här avsnittet går igenom en övervakad uppgradering för ett av SDK-exemplen som använder PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Steg 1: Skapa och distribuera exemplet visuella objekt
Skapa och publicera programmet genom att högerklicka på programprojektet **VisualObjectsApplication** och välja kommandot **Publicera.**  Mer information finns i [självstudiekursen för uppgradering av Programuppgradering](service-fabric-application-upgrade-tutorial.md)av Service Fabric .  Du kan också använda PowerShell för att distribuera ditt program.

> [!NOTE]
> Innan något av serviceinfrastrukturkommandona kan användas i PowerShell måste du först `Connect-ServiceFabricCluster` ansluta till klustret med hjälp av cmdleten. På samma sätt antas det att klustret redan har konfigurerats på den lokala datorn. Se artikeln om [hur du konfigurerar utvecklingsmiljön för Service Fabric](service-fabric-get-started.md).
> 
> 

När du har byggt projektet i Visual Studio kan du använda powershell-kommandot [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) för att kopiera programpaketet till ImageStore. Om du vill verifiera appaketet lokalt använder du cmdleten [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage) Nästa steg är att registrera programmet till Service Fabric-körningen med [cmdleten Register-ServiceFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype) Följande steg är att starta en instans av programmet med hjälp av cmdleten [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)  Dessa tre steg är analoga med att använda **menyalternativet Distribuera** i Visual Studio.  När etableringen är klar bör du rensa det kopierade programpaketet från avbildningsarkivet för att minska de resurser som förbrukas.  Om en programtyp inte längre behövs bör den avregistreras av samma anledning. Mer information [finns i Distribuera och ta bort program som använder PowerShell.](service-fabric-application-upgrade-tutorial-powershell.md)

Nu kan du använda [Service Fabric Explorer för att visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet har en webbtjänst som kan navigeras till [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) i Internet Explorer genom att skriva i adressfältet.  Du bör se några flytande visuella objekt röra sig på skärmen.  Dessutom kan du använda [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) för att kontrollera programstatus.

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: Uppdatera exemplet med visuella objekt
Du kanske märker att med den version som distribuerades i steg 1 roterar inte de visuella objekten. Låt oss uppgradera det här programmet till ett där de visuella objekten också roterar.

Välj Projektet VisualObjects.ActorService i VisualObjects-lösningen och öppna filen StatefulVisualObjectActor.cs. I filen navigerar du `MoveObject`till `this.State.Move()`metoden , kommentar `this.State.Move(true)`ut och avkommentar . Den här ändringen roterar objekten när tjänsten har uppgraderats.

Vi måste också uppdatera *filen ServiceManifest.xml* (under PackageRoot) för projektet **VisualObjects.ActorService**. Uppdatera *CodePackage* och tjänstversionen till 2.0 och motsvarande rader i filen *ServiceManifest.xml.*
Du kan använda alternativet *Redigera manifestfiler* i Visual Studio när du högerklickat på lösningen för att göra manifestet filändringar.

När ändringarna har gjorts ska manifestet se ut så här (markerade delar visar ändringarna):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu uppdateras *filen ApplicationManifest.xml* (som finns under **VisualObjects-projektet** under **VisualObjects-lösningen)** till version 2.0 av **VisualObjects.ActorService-projektet.** Dessutom uppdateras programversionen till 2.0.0.0 från 1.0.0.0. *ApplicationManifest.xml* ska se ut så här:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nu skapar du projektet genom att bara välja **ActorService-projektet** och högerklicka och välja alternativet **Bygg** i Visual Studio. Om du väljer **Återskapa alla**bör du uppdatera versionerna för alla projekt, eftersom koden skulle ha ändrats. Låt oss sedan paketera det uppdaterade programmet genom att högerklicka på ***VisualObjectsApplication,*** välja menyn Service Fabric och välja **Paket**. Den här åtgärden skapar ett programpaket som kan distribueras.  Det uppdaterade programmet är klart att distribueras.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Steg 3: Besluta om hälsopolicyer och uppgraderingsparametrar
Bekanta dig med [programuppgraderingsparametrarna](service-fabric-application-upgrade-parameters.md) och [uppgraderingsprocessen](service-fabric-application-upgrade.md) för att få en god förståelse för de olika uppgraderingsparametrarna, time-outs och hälsokriterium som tillämpas. För den här genomgången anges utvärderingsvillkoret för tjänsthälsa till standardvärdena (och rekommenderade) värdena, vilket innebär att alla tjänster och instanser bör vara *felfria* efter uppgraderingen.  

Låt oss dock öka *HealthCheckStableDuration* till 180 sekunder (så att tjänsterna är felfria i minst 120 sekunder innan uppgraderingen fortsätter till nästa uppdateringsdomän).  Låt oss också ställa in *UpgradeDomainTimeout* till 1200 sekunder och *UpgradeTimeout* till 3000 sekunder.

Slutligen, låt oss också ställa in *UpgradeFailureAction* till återställning. Det här alternativet kräver att Service Fabric återställer programmet till den tidigare versionen om det uppstår problem under uppgraderingen. När du startar uppgraderingen (i steg 4) anges följande parametrar:

FailureAction = Återställning

HealthCheckstableDurationSec = 180

UppgraderaDomänTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Steg 4: Förbered program för uppgradering
Nu är programmet byggt och redo att uppgraderas. Om du öppnar ett PowerShell-fönster som administratör och skriver [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)bör du veta att det är programtyp 1.0.0.0 av **VisualObjects** som har distribuerats.  

Programpaketet lagras under följande relativa sökväg där du okomprimerade Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Du bör hitta en "Paket"-mapp i den katalogen, där programpaketet lagras. Kontrollera tidsstämplarna för att säkerställa att det är den senaste versionen (du kan behöva ändra sökvägarna på rätt sätt också).

Nu ska vi kopiera det uppdaterade programpaketet till Service Fabric ImageStore (där programpaketen lagras av Service Fabric). Parametern *ApplicationPackagePathInImageStore* informerar Service Fabric där den kan hitta programpaketet. Vi har lagt den uppdaterade ansökan i\_"VisualObjects V2" med följande kommando (du kan behöva ändra sökvägar igen på lämpligt sätt).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Nästa steg är att registrera det här programmet med Service Fabric, som kan utföras med kommandot [Register-ServiceFabricApplicationType:](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Om föregående kommando inte lyckas är det troligt att du behöver en ombyggnad av alla tjänster. Som nämns i steg 2 kan du behöva uppdatera webservice-versionen också.

Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Om du tar bort programpaket från avbildningsarkivet frigörs systemresurser.  Om du behåller oanvända programpaket förbrukar disklagring och leder till problem med programmets prestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Steg 5: Starta programuppgraderingen
Nu är vi alla inställda på att starta programuppgraderingen med kommandot [Start-ServiceFabricApplicationUpgrade:](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps)

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Programnamnet är detsamma som det beskrevs i filen *ApplicationManifest.xml.* Service Fabric använder det här namnet för att identifiera vilket program som uppgraderas. Om du ställer in tidsavsluten för att vara för kort kan du stöta på ett felmeddelande som anger problemet. Gå till felsökningsavsnittet eller öka time-outs.

Nu, när programuppgraderingen fortsätter, kan du övervaka den med Hjälp av Service Fabric Explorer eller med kommandot [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

På några minuter bör den status som du fick med kommandot föregående PowerShell ange att alla uppdateringsdomäner har uppgraderats (slutförts). Och du bör upptäcka att de visuella objekten i webbläsarfönstret har börjat rotera!

Du kan prova att uppgradera från version 2 till version 3, eller från version 2 till version 1 som en övning. Att gå från version 2 till version 1 anses också vara en uppgradering. Spela med time-outs och hälsopolicyer för att bekanta dig med dem. När du distribuerar till ett Azure-kluster måste parametrarna ställas in på rätt sätt. Det är bra att ställa in time-outs konservativt.

## <a name="next-steps"></a>Nästa steg
[Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.

Styr hur programmet uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör dina programuppgraderingar kompatibla genom att lära dig hur du använder [data serialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

