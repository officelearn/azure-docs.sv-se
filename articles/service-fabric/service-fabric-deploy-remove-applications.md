---
title: Azure Service Fabric-programdistribution | Microsoft Docs
description: "Hur du distribuerar och ta bort program i Service Fabric med hjälp av PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: f19141919b3c61123e0e94c4513f872e095620c1
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuera och ta bort program med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

När en [programtyp har paketerats][10], den är klar för distribution till Azure Service Fabric-kluster. Distributionen omfattar följande tre steg:

1. Ladda upp programpaketet image store
2. Registrera programtypen
3. Skapa programinstansen

När ett program distribueras och en instans som körs i klustret kan du ta bort programinstansen och dess programtyp. Om du vill ta bort ett program från klustret omfattar följande steg:

1. Ta bort (eller ta bort) för att köra programinstansen
2. Avregistrera programtypen om du inte längre behöver
3. Ta bort programmet paketet från avbildningsarkivet

Om du använder Visual Studio för att distribuera och felsöka program i klustret lokal utveckling, hanteras de föregående stegen automatiskt via ett PowerShell-skript.  Det här skriptet finns i den *skript* mappen i projektet för konsolprogrammet. Den här artikeln innehåller bakgrundsinformation om vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Anslut till klustret
Innan du kör PowerShell-kommandon i den här artikeln måste alltid starta med hjälp av [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) att ansluta till Service Fabric-kluster. För att ansluta till lokal utveckling klustret, kör du följande:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Exempel på att ansluta till ett kluster eller kluster som skyddas med Azure Active Directory, X509 certifikat eller Windows Active Directory finns [Anslut till en säker kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Ladda upp programpaketet
Ladda upp programpaketet placeras på en plats som kan nås av interna Service Fabric-komponenter.
Om du vill kontrollera programpaket lokalt använder den [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

Den [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommandot överför programpaketet till klustret image store.

Anta att du skapar och paketerar ett program med namnet *MyApplication* i Visual Studio 2015. Som standard är namnet på programmet som anges i ApplicationManifest.xml ”MyApplicationType”.  Programpaket som innehåller nödvändiga programmanifestet, service manifest och paket i kod-config-data, finns i *C:\Users\<användarnamn\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

Följande kommando visar innehållet i programpaketet:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Om programmet paketet är stort och/eller har många filer, kan du [komprimera](service-fabric-package-apps.md#compress-a-package). Komprimeringen minskar storleken och antalet filer.
Bieffekten är att registrera och Avregistrerar programtypen är snabbare. Tid för gå långsammare för närvarande, särskilt om du tar dig tid att komprimera paketet. 

Om du vill komprimera ett paket kan du använda samma [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommando. Komprimering kan göras separat från överföra, genom att använda den `SkipCopy` flagga eller tillsammans med överföringen. Tillämpa komprimering på komprimerade paketet är ingen op.
Om du vill expandera en komprimerade paketet, använder du samma [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommandot med de `UncompressPackage` växla.

Följande cmdlet komprimerar paketet utan att kopiera den till image store. Paketet innehåller nu komprimerade filer för den `Code` och `Config` paket. Programmet och service manifest är inte zippade, eftersom de behövs för många interna åtgärder (t.ex. paketet fildelning, program namn och version Extraheringen av anslutningstyp för vissa verifieringar). Komprimerar manifesten gör dessa åtgärder ineffektiv.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

För stora programpaket tidskrävande komprimeringen. Använda en snabb SSD-enhet för bästa resultat. Komprimering gånger och storleken på det komprimerade paketet också variera beroende på paketinnehållet.
Här är till exempel komprimering statistik för vissa paket som visar första och den komprimerade paketstorleken med komprimering tid.

|Ursprunglig storlek (MB)|Antal filer|Tid för komprimering|Komprimerade paketet storlek (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

När ett paket har komprimerats, kan de överföras till en eller flera Service Fabric-kluster efter behov. Mekanism för distribution är samma för komprimerade och okomprimerade paket. Om paketet har komprimerats, lagras som sådana i klustret image store och den är okomprimerade på noden innan programmet körs.


I följande exempel laddar upp paketet i bildarkivet för i en mapp med namnet ”MyApplicationV1”:

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Om du inte anger den *- ApplicationPackagePathInImageStore* parameter, programpaketet kopieras till mappen ”felsökning” i image store.

>[!NOTE]
>**Kopiera ServiceFabricApplicationPackage** identifierar automatiskt lämpliga image store anslutningssträngen om PowerShell-session som är ansluten till ett Service Fabric-kluster. För Service Fabric-versioner äldre än 5.6, den **- ImageStoreConnectionString** argument måste anges explicit.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Den **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, som är del av Service Fabric SDK PowerShell-modulen används för att hämta anslutningssträngen för image store.  Om du vill importera modulen SDK, kör du:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Se [förstå anslutningssträngen för image store](service-fabric-image-store-connection-string.md) för ytterligare information om image store och avbildningen lagra anslutningssträngen.
>
>
>

Den tid det tar för att överföra ett paket skiljer sig beroende på flera faktorer. Vissa av dessa faktorer är antalet filer i paketet och paketstorleken filstorlekarna. Nätverkshastigheten mellan källdatorn och Service Fabric-klustret påverkar också tid. Standardvärdet för timeout för [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) är 30 minuter.
Du kan behöva öka tidsgränsen beroende på faktorer som beskrivs. Om du komprimerar paketet i Kopiera anropet måste du även överväga komprimering tid.



## <a name="register-the-application-package"></a>Registrera programpaketet
Programtypen och versionen har deklarerats i programmanifestet blir tillgängliga för användning när programpaketet har registrerats. Systemet läser det paket som överförts i det föregående steget, verifierar paketet, bearbetar paketinnehållet och kopierar bearbetade paketet till en plats för internt system.  

Kör den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) för att registrera programtypen i klustret och gör den tillgänglig för distribution:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

”MyApplicationV1” är mappen i image store där programpaketet finns. Programtyp med namnet ”MyApplicationType” och versionen ”1.0.0” (båda finns i programmanifestet) har nu registrerats i klustret.

Den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) kommando returnerar bara när systemet har registrerat programpaketet. Hur länge registrering tar beror på storleken och innehållet i programpaketet. Om det behövs, de **- TimeoutSec** parametern kan användas för att ange en längre tidsgräns (standardvärdet är 60 sekunder).

Om du har en stor program paketet eller om du har tidsgränser, använda den **- asynkrona** parameter. Kommandot returnerar när klustret accepterar registreringskommandot och bearbetningen fortsätter efter behov.
Den [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) kommando visar alla registrerades typen programversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett programpaket från avbildningsarkivet
Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Ta bort programpaket från avbildningsarkivet frigör systemresurser.  Att hålla oanvända programpaket förbrukar disklagring och leder till problem med prestanda.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Skapa programmet
Du kan skapa en instans av ett program från en version av programmet som har registrerats med hjälp av den [ny ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet. Namnet på varje program måste börja med den *”fabric”:* schemat och måste vara unikt för varje förekomst av programmet. Alla standardtjänster som definierats i programmanifestet av programmet måltypen skapas också.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Flera instanser av programmet kan skapas för en viss version av typen registrerade programmet. Varje instans av programmet körs i isolering med sin egen arbetskatalog och processen.

För att se vilket med namnet program och tjänster som körs i klustret, kör den [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) och [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdlets:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Ta bort ett program
När en instans av programmet inte längre behövs, om du permanent ta bort den med hjälp av namnet på [ta bort ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet. [Ta bort ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) tar automatiskt bort alla tjänster som hör till programmet även, permanent tar du bort alla tillstånd för tjänsten. 

> [!WARNING]
> Den här åtgärden kan inte ångras och går inte att återställa programtillstånd.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Avregistrera en programtyp
När en viss version av en typ av program inte längre behövs, bör du avregistrera den typ som använder den [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet. Avregistrerar oanvända typer versioner lagringsutrymme används av image store genom att ta bort binärfilerna för programmet. Avregistrering av programtyp tas inte bort programpaketet. En typ av program kan avregistreras så länge inga program instansieras mot det och inte väntar programmet uppgraderingar hänvisar till den.

Kör [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) att se vilka programtyper som är registrerade i klustret:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Kör [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) att avregistrera en specifik typ:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Felsökning
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Kopiera ServiceFabricApplicationPackage begär en ImageStoreConnectionString
Service Fabric SDK-miljö bör redan ha rätt standardvärdena ställa in. Men om det behövs, ImageStoreConnectionString för alla kommandon bör matcha värdet som Service Fabric-klustret. Du hittar ImageStoreConnectionString i klustret-manifestet hämtades med den [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) och Get-ImageStoreConnectionStringFromClusterManifest kommandon:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Den **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, som är del av Service Fabric SDK PowerShell-modulen används för att hämta anslutningssträngen för image store.  Om du vill importera modulen SDK, kör du:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString hittades i klustermanifestet:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [förstå anslutningssträngen för image store](service-fabric-image-store-connection-string.md) för ytterligare information om image store och avbildningen lagra anslutningssträngen.

### <a name="deploy-large-application-package"></a>Distribuera stora programpaket
Problem: [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) timeout för stora programpaket (ordning GB).
Försök:
- Ange en längre tidsgräns för [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommandot med `TimeoutSec` parameter. Tidsgränsen är som standard 30 minuter.
- Kontrollera nätverksanslutningen mellan källdatorn och kluster. Om anslutningen är långsam, Överväg att använda en dator med en bättre nätverksanslutning.
Om klientdatorn är i en annan region än klustret, Överväg att använda en klientdator i en närmare eller samma region som klustret.
- Kontrollera om du träffa externa begränsning. Till exempel när image store är konfigurerad för att använda azure storage, kan överför att begränsas.

Problem: Överför paketet slutfördes, men [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) timeout. Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till image store.
Komprimeringen minskar storleken och det antal filer, vilket i sin tur minskar mängden trafik och fungerar som Service Fabric måste utföra. Överföringen kan ta längre tid (särskilt om du inkluderar komprimering-tid), men registrera och avregistrera programtypen är snabbare.
- Ange en längre tidsgräns för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) med `TimeoutSec` parameter.
- Ange `Async` växla för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Kommandot returnerar när klustret accepterar kommandon och registrering av programtypen fortsätter asynkront. Det finns därför behöver du inte ange en högre tidsgräns i det här fallet. Den [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) kommando visar alla registrerades typen programversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Distribuera programpaket med många filer
Problem: [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) tidsgränsen uppnås för ett programpaket med många filer (efter tusentalsavgränsare).
Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till image store. Komprimeringen minskar antalet filer.
- Ange en längre tidsgräns för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) med `TimeoutSec` parameter.
- Ange `Async` växla för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Kommandot returnerar när klustret accepterar kommandon och registrering av programtypen fortsätter asynkront.
Det finns därför behöver du inte ange en högre tidsgräns i det här fallet. Den [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) kommando visar alla registrerades typen programversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Nästa steg
[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

[Service Fabric hälsa introduktion](service-fabric-health-introduction.md)

[Diagnostisera och felsöka en Service Fabric-tjänst](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellen är ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
