---
title: Uppgradering av Service Fabric-App med hjälp av PowerShell | Microsoft Docs
description: Den här artikeln beskriver upplevelsen av att distribuera ett Service Fabric-program, ändra koden och distribution av en uppgradering med hjälp av PowerShell.
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
ms.openlocfilehash: e11ac55afe41231fcbc3aabb3ef54b46108eb49c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185867"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Uppgradering av Service Fabric-programmet med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

De vanligaste och rekommenderade metoden för uppgraderingen är övervakade löpande uppgradering.  Azure Service Fabric övervakar hälsotillståndet för programmet som ska uppgraderas baserat på en uppsättning hälsoprinciper. När en uppdateringsdomän (UD) har uppgraderats, Service Fabric utvärderar programmets hälsotillstånd och fortsätter till nästa uppdateringsdomän eller misslyckas uppgraderingen beroende på vilka hälsotillstånd principer.

En uppgradering av övervakade programmet kan utföras med hjälp av den hanterade interna API: er, PowerShell, Azure CLI, Java eller REST. Anvisningar om hur du utför en uppgradering med Visual Studio finns i [uppgradering av program med Visual Studio](service-fabric-application-upgrade-tutorial.md).

Med Service Fabric övervakas löpande uppgraderingar, kan programadministratören konfigurera utvärderingen hälsoprincip som Service Fabric använder för att avgöra om programmet är felfri. Dessutom kan konfigurera administratören åtgärden som ska vidtas när hälsotillståndet utvärderingen misslyckas (till exempel göra en automatisk återställning.) Det här avsnittet beskriver en övervakade uppgradering för en av SDK-prov som använder PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Steg 1: Skapa och distribuera exemplet visuella objekt
Skapa och publicera programmet genom att högerklicka på programprojektet, **VisualObjectsApplication,** och välja den **publicera** kommando.  Mer information finns i [självstudier för uppgradering av Service Fabric-program](service-fabric-application-upgrade-tutorial.md).  Du kan också använda PowerShell för att distribuera ditt program.

> [!NOTE]
> Innan någon av Service Fabric-kommandon kan användas i PowerShell, måste du först ansluta till klustret med hjälp av den `Connect-ServiceFabricCluster` cmdlet. På samma sätt förutsätts det att klustret redan har ställts in på den lokala datorn. Läsa artikeln om [ställa in din utvecklingsmiljö för Service Fabric](service-fabric-get-started.md).
> 
> 

När du har skapat projektet i Visual Studio, kan du använda PowerShell-kommandot [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) att kopiera programpaketet till ImageStore. Om du vill kontrollera app-paketet lokalt, använda den [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) cmdlet. Nästa steg är att registrera programmet i Service Fabric runtime med hjälp av den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet. Följande steg är att starta en förekomst av programmet med hjälp av den [New ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet.  De här tre stegen är analoga med hjälp av den **distribuera** menyalternativ i Visual Studio.  När etableringen har slutförts ska du rensa det kopierade programpaketet från avbildningsarkivet för att minska de resurser som används.  Om det krävs inte längre programtyp, bör den vara avregistrera av samma orsak. Se [distribuera och ta bort program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) för mer information.

Du kan nu använda [Service Fabric Explorer för att visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet har en webbtjänst som kan navigera till i Internet Explorer genom att skriva [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) i adressfältet.  Du bör se vissa flytande visuella objekt som flyttas runt på skärmen.  Du kan dessutom använda [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) att kontrollera statusen för programmet.

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: Uppdatera exemplet visuella objekt
Du kanske märker att med den version som har distribuerats i steg 1, de visuella objekten inte rotera. Nu ska vi uppgradera det här programmet till en där de visuella objekten också rotera.

Välj VisualObjects.ActorService-projekt i lösningen VisualObjects och öppna filen StatefulVisualObjectActor.cs. Navigera till metoden i filen, `MoveObject`, kommentera ut `this.State.Move()`, och ta bort kommentarerna `this.State.Move(true)`. Den här ändringen roterar objekten när tjänsten har uppgraderats.

Vi också behöva uppdatera den *ServiceManifest.xml* fil (under PackageRoot) projektets **VisualObjects.ActorService**. Uppdatera den *CodePackage* och service-version 2.0 och motsvarande rader i den *ServiceManifest.xml* fil.
Du kan använda Visual Studio *redigera Manifest filer* alternativ när du högerklickar på lösningen att ändringarna manifestfilen.

När ändringarna sparas manifestet bör se ut så här (markerade delar visa ändringarna):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu den *ApplicationManifest.xml* fil (finns under den **VisualObjects** projektet den **VisualObjects** lösning) har uppdaterats till version 2.0 av **VisualObjects.ActorService** projekt. Dessutom uppdateras programversionen till 2.0.0.0 från 1.0.0.0. Den *ApplicationManifest.xml* bör se ut som följande kodavsnitt:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nu kan skapa projektet genom att välja bara **ActorService** -projektet och sedan högerklicka och välja den **skapa** alternativet i Visual Studio. Om du väljer **återskapa alla**, bör du uppdatera versionerna för alla projekt, eftersom koden skulle ha ändrats. Nu ska vi paketera det uppdaterade programmet genom att högerklicka på ***VisualObjectsApplication***, välja den Service Fabric-menyn och sedan välja **paketet**. Den här åtgärden skapar ett programpaket som kan distribueras.  Uppdaterade programmet är redo att distribueras.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Steg 3:  Besluta om hälsoprinciper och Uppgraderingsparametrar
Bekanta dig med den [programuppgraderingsparametrar](service-fabric-application-upgrade-parameters.md) och [uppgraderingsprocessen](service-fabric-application-upgrade.md) att få en god förståelse av de olika Uppgraderingsparametrar, tidsgränser och hälsotillstånd villkor tillämpas. Den här genomgången leverantörsutvärdering för service health är inställt på standardvärdet (och rekommenderas) värden, vilket innebär att alla tjänster och instanser ska vara *felfri* efter uppgraderingen.  

Men vi öka den *HealthCheckStableDuration* till 180 sekunder (så att tjänsterna fungerar felfritt för minst 120 sekunder innan uppgraderingen fortsätter till nästa uppdateringsdomän).  Nu ska vi också ange den *UpgradeDomainTimeout* vara 1200 sekunder och *UpgradeTimeout* vara 3000 sekunder.

Slutligen ska vi också ange den *UpgradeFailureAction* att återställa. Det här alternativet kräver Service Fabric för att återställa programmet till föregående version om det uppstår problem under uppgraderingen. När du startar uppgraderingen (i steg 4) anges därför följande parametrar:

FailureAction = återställning

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Steg 4: Förbereda ett program för uppgradering
Programmet är nu inbyggda och är redo att uppgraderas. Om du öppna ett PowerShell-fönster som administratör och skriv [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), bör det att du vet att det är programtyp 1.0.0.0 av **VisualObjects** som har distribuerats.  

Programpaketet lagras under följande relativa sökväg där du okomprimerad Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Du bör hitta en ”paketmapp” i den katalog där programpaketet lagras. Kontrollera tidsstämplar för att säkerställa att det är den senaste versionen (du kan behöva ändrar du sökvägarna samt).

Nu ska vi kopierar du det uppdaterade programmet till Service Fabric-ImageStore (där programpaket lagras av Service Fabric). Parametern *ApplicationPackagePathInImageStore* informerar Service Fabric där den kan hitta programpaketet. Vi har gjort det uppdaterade programmet ”VisualObjects\_V2” med följande kommando (du kan behöva ändra sökvägarna igen på rätt sätt).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Nästa steg är att registrera programmet med Service Fabric, som kan utföras med hjälp av den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) kommando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Om det föregående kommandot inte lyckas är det troligt att du behöver en återskapning av alla tjänster. Som vi nämnde i steg 2, som du kan behöva uppdatera din WebService-version.

Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Ta bort programpaket från avbildningsarkivet frigör systemresurser.  Att hålla oanvända programpaket förbrukar disklagring och leder till problem med programprestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Steg 5: Börja uppgradera programmet
Nu kan vi börja uppgradera programmet med hjälp av den [Start ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) kommando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Programnamnet är samma som det beskrivs i den *ApplicationManifest.xml* fil. Service Fabric använder det här namnet för att identifiera vilket program som har uppgraderats. Ett felmeddelande som säger problemet kan uppstå om du ställer in timeout är för kort. Referera till avsnittet om felsökning eller öka timeout-alternativen.

Nu, som uppgraderingen fortsätter programmet kan du övervaka den med hjälp av Service Fabric Explorer eller genom att använda den [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell-kommando: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Om några minuter bör status som du fick med hjälp av PowerShell-kommando som föregående tillstånd att alla uppdateringsdomäner har uppgraderats (slutfört). Och du bör hitta de visuella objekten i webbläsarfönstret började rotera!

Du kan uppgradera från version 2 version 3 eller från version 2 för version 1 som en övning. Flytta från version 2 för version 1 anses också en uppgradering. Experimentera med tidsgränser och hälsoprinciper att bli bekant med dem. När du distribuerar till en Azure-kluster, måste vara korrekt inställda för parametrarna. Det är bra att ange timeout-alternativen var.

## <a name="next-steps"></a>Nästa steg
[Uppgradering av program med Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.

Styra hur programmet uppgraderas med hjälp av [Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibel genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

