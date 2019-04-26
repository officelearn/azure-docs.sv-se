---
title: Azure Service Fabric-programdistribution | Microsoft Docs
description: Hur du distribuerar och ta bort program i Service Fabric med PowerShell.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: f0f66cd32721e277cbd6e4578b0e58bb201ee966
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393278"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuera och ta bort program med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

När en [programtypen har paketerats][10], den är klar för distribution till ett Azure Service Fabric-kluster. Distribution omfattar följande tre steg:

1. Ladda upp programpaketet till avbildningsarkivet.
2. Registrera programtypen med bild store relativa sökvägen.
3. Skapa instansen för programmet.

När det distribuerade programmet inte längre behövs, kan du ta bort programinstansen och dess programtypen. Omfattar följande steg för att helt ta bort ett program från klustret:

1. Ta bort (eller ta bort) löpande programinstans.
2. Avregistrera programtypen om du inte längre behöver den.
3. Ta bort programpaketet från avbildningsarkivet.

Om du använder Visual Studio för att distribuera och felsöka program på det lokala utvecklingsklustret hanteras alla steg ovan automatiskt via ett PowerShell-skript.  Det här skriptet finns i den *skript* mappen programprojektet. Den här artikeln innehåller bakgrunden på vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 

Ett annat sätt att distribuera ett program är att använda externa etablera. Programpaketet kan vara [paketerade som `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) och överförs till en extern lagring. I det här fallet behövs inte överföring till avbildningsarkivet. Distributionen måste följande steg:

1. Ladda upp den `sfpkg` till en extern lagring. Extern lagring kan vara inga store som Exponerar en REST http eller https-slutpunkt.
2. Registrera vilken typ av program med hjälp av externa nedladdningen URI och informationen om programmet.
2. Skapa instansen för programmet.

Ta bort instanser av programmet för rensning av och avregistrera programtypen. Eftersom paketet inte har kopierats till avbildningsarkivet, finns det ingen tillfällig plats för att rensa. Etablera från extern lagring är tillgänglig från och med Service Fabric version 6.1.

>[!NOTE]
> Visual Studio stöder för närvarande inte externa etablera.

 

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Innan du kör alla PowerShell-kommandon i den här artikeln måste alltid starta med hjälp av [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) att ansluta till Service Fabric-klustret. Om du vill ansluta till det lokala utvecklingsklustret, kör du följande:

```powershell
Connect-ServiceFabricCluster
```

Exempel för att ansluta till ett fjärrkluster eller kluster som skyddas med Azure Active Directory, X509 certifikat eller Windows Active Directory finns i [Anslut till ett säkert kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Ladda upp programpaketet

Ladda upp programpaketet placerar den på en plats som kan nås av interna Service Fabric-komponenter.
Om du vill kontrollera programpaket lokalt, använda den [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

Den [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommando laddar upp programpaketet till klustrets avbildningsarkiv.

Anta att du bygga och paketera ett program som heter *MyApplication* i Visual Studio 2015. Som standard är namnet på programmet som anges i ApplicationManifest.xml ”MyApplicationType”.  Programpaket som innehåller den nödvändiga programmanifestet och tjänstmanifest kod/config/data paket, finns i *C:\Users\<användarnamn\>\Documents\Visual Studio 2015\Projects\ MyApplication\MyApplication\pkg\Debug*. 

Följande kommando visar innehållet i programpaketet:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
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

Om programpaketet är stor och/eller har många filer, kan du [komprimera](service-fabric-package-apps.md#compress-a-package). Komprimeringen minskar storleken och antalet filer.
Sidoeffekt är att registrera och Avregistrerar programtypen är snabbare. Tid för kan vara långsammare för närvarande, särskilt om du inkluderar det hög tid att komprimera paketet. 

Om du vill komprimera ett paket, kan du använda samma [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommando. Komprimering kan göras separat från överföra, genom att använda den `SkipCopy` flagga eller tillsammans med överföringen. Att använda komprimering på komprimerade paketet är icke-alternativ.
Om du vill expandera en komprimerade paketet kan du använda samma [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) med den `UncompressPackage` växla.

Följande cmdlet komprimerar paketet utan att kopiera den till avbildningsarkivet. Paketet innehåller nu komprimerade filer för den `Code` och `Config` paket. Programmet och tjänstmanifest är inte zippade, eftersom de behövs för många interna åtgärder (t.ex. paketera delning, program och specifik produktversion Extraheringen av anslutningstyp för vissa verifieringar). Komprimeringen manifesten säkerställer åtgärderna ineffektiv.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
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

Komprimeringen tar tid för stora programpaket. För bästa resultat bör du använda en snabb SSD-enhet. Komprimering gånger och storleken på det komprimerade paketet också variera beroende på paketinnehållet.
Här är till exempel komprimering statistik för vissa paket, vilket visar första och den komprimerade paketstorleken med komprimering tid.

|Ursprunglig storlek (MB)|Antal filer|Tid för komprimering|Komprimerade paketstorlek (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

När ett paket har komprimerats, kan den överföras till en eller flera Service Fabric-kluster efter behov. Mekanismen för distribution är samma för komprimerade och okomprimerade paket. Komprimerade paket lagras som sådana i avbildningsarkivet kluster. Paket som har expanderats på noden och innan programmet körs.


I följande exempel överförs paketet till avbildningsarkivet till en mapp med namnet ”MyApplicationV1”:

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Om du inte anger den *- ApplicationPackagePathInImageStore* parametern programpaketet har kopierats till mappen ”felsökning” i avbildningsarkivet.

>[!NOTE]
>**Kopiera ServiceFabricApplicationPackage** identifierar automatiskt anslutningssträngen lämplig avbildning med store om PowerShell-session som är ansluten till ett Service Fabric-kluster. För Service Fabric-versioner som är äldre än 5.6, den **- ImageStoreConnectionString** argumentet måste anges uttryckligen.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Den **Get-ImageStoreConnectionStringFromClusterManifest** cmdleten, som är en del av Service Fabric SDK PowerShell-modulen används för att hämta anslutningssträngen för avbildning store.  Om du vill importera SDK-modulen kör du:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Se [förstå anslutningssträngen bild store](service-fabric-image-store-connection-string.md) aviserar om ytterligare information om avbildningsarkivet och avbildning lagra anslutningssträngen.
>
>
>

Den tid det tar att ladda upp ett paket skiljer sig beroende på flera faktorer. Vissa av dessa faktorer är antalet filer i paketet och paketstorleken filstorleken. Nätverkshastighet mellan källdatorn och Service Fabric-klustret påverkar också tid. Standardvärdet för timeout för [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) är 30 minuter.
Beroende på faktorer som beskrivs kan du behöva öka tidsgränsen. Om du komprimerar paketet i anropet kopia, måste du även överväga komprimering tid.



## <a name="register-the-application-package"></a>Registrera programpaketet

Programtypen och versionen deklarerats i manifestet blir tillgängliga för användning när programpaketet registreras. Systemet läser det paket som laddades upp i föregående steg, verifierar paketet, bearbetar paketinnehållet och kopierar bearbetade paketet till ett internt system.  

Kör den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet för att registrera programtypen i klustret och gör den tillgänglig för distribution:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrera det programpaket som kopieras till avbildningsarkivet

När ett paket har tidigare har kopierats till avbildningsarkivet, anger registrera åtgärden den relativa sökvägen i avbildningsarkivet.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

”MyApplicationV1” är mappen i avbildningsarkivet där programpaketet finns. Vilken typ av program med namnet ”MyApplicationType” och versionen ”1.0.0” (båda finns i applikationsmanifestet) har nu registrerats i klustret.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrera det programpaket som kopieras till en extern lagring

Från och med Service Fabric version 6.1, etablera stöder Hämta paketet från en extern lagring. Hämta URI representerar sökvägen till den [ `sfpkg` programpaket](service-fabric-package-apps.md#create-an-sfpkg) varifrån programpaketet kan hämtas med HTTP eller HTTPS-protokoll. Paketet måste ha tidigare laddats upp till den här extern plats. URI: N måste tillåta läsbehörighet så att Service Fabric kan ladda ned filen. Den `sfpkg` filen måste ha filnamnstillägget ”.sfpkg”. Etableringsåtgärden bör innehålla programinformationen, som hittades i manifestet.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Den [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) kommando returnerar bara när systemet har registrerat programpaketet. Hur länge registrering tar beror på storleken och innehållet i programpaketet. Om det behövs, de **- TimeoutSec** parametern kan användas för att ange en längre tidsgräns (standardvärdet för timeout är 60 sekunder).

Om du har en stor tillämpning paketera eller om du har tidsgränser kan använda den **- Async** parametern. Kommandot returnerar när klustret accepterar registreringskommandot. Registrera dig igen fortsätter efter behov.
Den [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) kommandot listar typen programversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett programpaket från avbildningsarkivet

Om ett paket har kopierats till avbildningsarkivet, bör du bort den från den tillfälliga platsen när programmet har registrerats. Ta bort programpaket från avbildningsarkivet frigör systemresurser. Att hålla oanvända programpaket förbrukar disklagring och leder till problem med programprestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Skapa programmet

Du kan skapa en instans av ett program från valfri version av programtyp som har registrerats med hjälp av den [New ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet. Namnet på varje program måste börja med den *”fabric”:* system och måste vara unikt för varje programinstans. Alla standardtjänster som är definierade i applikationsmanifestet av mål-programtyp skapas också.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Flera programinstanser kan skapas efter en viss version av en registrerad programtypen. Varje programinstans körs i isolering med sin egen arbetskatalog och processen.

För att se vilket med namnet appar och tjänster som körs i klustret, kör den [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) och [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdletar:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Ta bort ett program

När en programinstans inte längre behövs kan du permanent bort den med hjälp av namnet på [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet. [Ta bort ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatiskt att ta bort alla tjänster som hör till programmet även, permanent tar du bort alla tjänsttillstånd. 

> [!WARNING]
> Den här åtgärden kan inte ångras och programmets tillstånd kan inte återställas.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Avregistrera en programtyp

När en viss version av programtyp inte längre behövs, bör du avregistrera den typ som använder den [avregistrera ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet. Avregistrera oanvända programtyper Frigör lagringsutrymme som används av avbildningsarkivet genom att ta bort programfiler för typen. Avregistrera en typ av program tas inte bort programpaketet kopieras till tillfällig plats bild store om Kopiera till avbildningsarkivet användes. En typ av program kan att avregistrera så länge inga program instansieras mot dem och inte väntar på programmet uppgraderingar hänvisar till den.

Kör [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) att se programtyper som är registrerade i klustret:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Kör [avregistrera ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) att avregistrera en specifik typ:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Felsökning

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage asks for an ImageStoreConnectionString

Service Fabric SDK-miljö bör redan ha rätt standardvärdena ställa in. Men om det behövs ImageStoreConnectionString för alla kommandon bör matcha det värde som Service Fabric-klustret använder. Du hittar ImageStoreConnectionString i klustermanifestet, hämtas med hjälp av den [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) och Get-ImageStoreConnectionStringFromClusterManifest kommandon:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Den **Get-ImageStoreConnectionStringFromClusterManifest** cmdleten, som är en del av Service Fabric SDK PowerShell-modulen används för att hämta anslutningssträngen för avbildning store.  Om du vill importera SDK-modulen kör du:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString hittas i klustermanifestet:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [förstå anslutningssträngen bild store](service-fabric-image-store-connection-string.md) aviserar om ytterligare information om avbildningsarkivet och avbildning lagra anslutningssträngen.

### <a name="deploy-large-application-package"></a>Distribuera stora programpaket

Ärende: [Kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) tidsgränsen uppnås för ett stort programpaket (efter GB).
Prova:
- Ange en längre tidsgräns för [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kommandot med `TimeoutSec` parametern. Tidsgränsen är som standard 30 minuter.
- Kontrollera nätverksanslutningen mellan källdatorn och kluster. Om anslutningen är långsam, Överväg att använda en dator med en bättre nätverksanslutning.
Om klientdatorn är i en annan region än i klustret, du använda en klientdator i en närmare eller samma region som klustret.
- Kontrollera om du nått externa begränsning. Till exempel när avbildningsarkivet är konfigurerad för att använda azure storage, kan ladda upp vara begränsad.

Ärende: Ladda upp paketet slutfördes, men [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) når sin tidsgräns. Prova:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till avbildningsarkivet.
Komprimeringen minskar storleken och antalet filer, vilket i sin tur minskar mängden trafik och fungerar som Service Fabric måste utföra. Överföringen kan vara långsammare (särskilt om du inkluderar komprimering-tid), men registrera och avregistrera programtypen är snabbare.
- Ange en längre tidsgräns för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) med `TimeoutSec` parametern.
- Ange `Async` växla för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Kommandot returnerar när klustret accepterar kommandon och registreringen av programtypen fortsätter asynkront. Därför är det behöver inte ange en tidsgräns som senare i det här fallet. Den [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) kommandot listar alla har registrerats programtypversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Distribuera programpaket med många filer

Ärende: [Registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) tidsgränsen uppnås för ett programpaket med många filer (efter tusentals).
Prova:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till avbildningsarkivet. Komprimeringen minskar antalet filer.
- Ange en längre tidsgräns för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) med `TimeoutSec` parametern.
- Ange `Async` växla för [registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Kommandot returnerar när klustret accepterar kommandon och registreringen av programtypen fortsätter asynkront.
Därför är det behöver inte ange en tidsgräns som senare i det här fallet. Den [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) kommandot listar alla har registrerats programtypversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Nästa steg

[Paketera ett program](service-fabric-package-apps.md)

[Service Fabric-Programuppgradering](service-fabric-application-upgrade.md)

[Service Fabric health introduktion](service-fabric-health-introduction.md)

[Diagnostisera och felsöka ett Service Fabric-tjänst](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellera ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md