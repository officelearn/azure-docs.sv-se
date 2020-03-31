---
title: Azure Service Fabric-distribution med PowerShell
description: Lär dig mer om hur du tar bort och distribuerar program i Azure Service Fabric och hur du utför dessa åtgärder i Powershell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e3fdd194f2949f1246e991968e02b3278f33f7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282515"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuera och ta bort program med PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

När en [programtyp har paketerats][10]är den klar för distribution till ett Azure Service Fabric-kluster. Distributionen omfattar följande tre steg:

1. Ladda upp programpaketet till bildarkivet.
2. Registrera programtypen med den relativa sökvägen för bildarkivet.
3. Skapa programinstansen.

När det distribuerade programmet inte längre behövs kan du ta bort programinstansen och dess programtyp. Så här tar du bort ett program helt från klustret:

1. Ta bort (eller ta bort) den programinstans som körs.
2. Avregistrera programtypen om du inte längre behöver den.
3. Ta bort programpaketet från bildarkivet.

Om du använder Visual Studio för att distribuera och felsöka program i det lokala utvecklingsklustret hanteras alla föregående steg automatiskt via ett PowerShell-skript.  Det här skriptet finns i mappen *Skript* i programprojektet. Den här artikeln innehåller bakgrund om vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 

Ett annat sätt att distribuera ett program är att använda extern etablering. Programpaketet kan [paketeras `sfpkg` som](service-fabric-package-apps.md#create-an-sfpkg) och laddas upp till en extern butik. I det här fallet behövs inte uppladdning till bildarkivet. Distributionen behöver följande steg:

1. Ladda `sfpkg` upp den till en extern butik. Det externa arkivet kan vara ett arkiv som exponerar en REST http- eller https-slutpunkt.
2. Registrera programtypen med hjälp av den externa nedladdnings-URI:n och programtypsinformationen.
2. Skapa programinstansen.

För rensning tar du bort programinstanserna och avregistrerar programtypen. Eftersom paketet inte har kopierats till bildarkivet finns det ingen tillfällig plats att rensa. Etablering från extern butik är tillgänglig från och med Service Fabric version 6.1.

>[!NOTE]
> Visual Studio stöder för närvarande inte extern etablering.

 

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Innan du kör några PowerShell-kommandon i den här artikeln, börja alltid med att använda [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) för att ansluta till Service Fabric-klustret. Om du vill ansluta till det lokala utvecklingsklustret kör du följande:

```powershell
Connect-ServiceFabricCluster
```

Exempel på hur du ansluter till ett fjärrkluster eller kluster som är skyddat med Azure Active Directory, X509-certifikat eller Windows Active Directory finns [i Anslut till ett säkert kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Ladda upp programpaketet

Om du laddar upp programpaketet placeras det på en plats som är tillgänglig för interna Service Fabric-komponenter.
Om du vill verifiera programpaketet lokalt använder du cmdleten [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

Kommandot [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) överför programpaketet till klusteravbildningsarkivet.

Anta att du skapar och paketerar ett program med namnet *MyApplication* i Visual Studio 2015. Som standard är programtypnamnet i ApplicationManifest.xml "MyApplicationType".  Programpaketet, som innehåller det nödvändiga programmanifestet, tjänstmanifesten och kod-/config/datapaketen, finns i *C:\Users\<användarnamn\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

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

Om programpaketet är stort och/eller har många filer kan du [komprimera det](service-fabric-package-apps.md#compress-a-package). Komprimeringen minskar storleken och antalet filer.
Bieffekten är att det går snabbare att registrera och avregistrera programtypen. Uppladdningstiden kan vara långsammare för närvarande, särskilt om du inkluderar tid att komprimera paketet. 

Om du vill komprimera ett paket använder du samma [copy-serviceFabricApplicationPackage-kommando.](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Komprimering kan göras separat `SkipCopy` från uppladdning, med hjälp av flaggan eller tillsammans med uppladdningen. Att använda komprimering på ett komprimerat paket är no-op.
Om du vill ta bort komprimerat paket använder du samma [copy-serviceFabricApplicationPackage-kommando](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) med växeln. `UncompressPackage`

Följande cmdlet komprimerar paketet utan att kopiera det till bildarkivet. Paketet innehåller nu zippade `Code` filer `Config` för och paket. Programmet och tjänstmanifesten är inte zippade, eftersom de behövs för många interna åtgärder (som paketdelning, programtypnamn och versionsutvinning för vissa valideringar). Zippa manifesten skulle göra dessa operationer ineffektiva.

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

För stora programpaket tar komprimeringen tid. För bästa resultat, använd en snabb SSD-enhet. Komprimeringstiderna och storleken på det komprimerade paketet skiljer sig också beroende på paketinnehållet.
Här finns till exempel komprimeringsstatistik för vissa paket, som visar den ursprungliga och den komprimerade paketstorleken, med komprimeringstiden.

|Ursprunglig storlek (MB)|Antal filer|Komprimeringstid|Komprimerad paketstorlek (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

När ett paket har komprimerats kan det överföras till ett eller flera Service Fabric-kluster efter behov. Distributionsmekanismen är densamma för komprimerade och okomprimerade paket. Komprimerade paket lagras som sådana i klusteravbildningsarkivet. Paketen är okomprimerade på noden innan programmet körs.


I följande exempel överförs paketet till bildarkivet till en mapp med namnet "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Om du inte anger parametern *-ApplicationPackagePathInImageStore* kopieras programpaketet till mappen "Debug" i bildarkivet.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** identifierar automatiskt lämplig anslutningssträng för bildlagring om PowerShell-sessionen är ansluten till ett Service Fabric-kluster. För Service Fabric-versioner som är äldre än 5.6 måste argumentet **-ImageStoreConnectionString** uttryckligen anges.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>**Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, som är en del av Modulen Service Fabric SDK PowerShell, används för att hämta anslutningssträngen för bildarkivet.  Om du vill importera SDK-modulen kör du:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Se [Förstå anslutningssträngen](service-fabric-image-store-connection-string.md) för bildlagring för kompletterande information om anslutningssträngen för bildarkivet och bildarkivet.
>
>
>

Hur tid det tar att ladda upp ett paket skiljer sig beroende på flera faktorer. Några av dessa faktorer är antalet filer i paketet, paketstorleken och filstorlekarna. Nätverkshastigheten mellan källmaskinen och Service Fabric-klustret påverkar också uppladdningstiden. Standardtidsgränsen för [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) är 30 minuter.
Beroende på de beskrivna faktorerna kan du behöva öka tidsgränsen. Om du komprimerar paketet i kopieringsanropet måste du också överväga komprimeringstiden.



## <a name="register-the-application-package"></a>Registrera ansökningspaketet

Programtypen och versionen som deklareras i programmanifestet blir tillgängliga för användning när programpaketet registreras. Systemet läser paketet som laddades upp i föregående steg, verifierar paketet, bearbetar paketets innehåll och kopierar det bearbetade paketet till en intern systemplats.  

Kör [cmdleten Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) för att registrera programtypen i klustret och göra den tillgänglig för distribution:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrera programpaketet som kopierats till bildarkivet

När ett paket tidigare har kopierats till bildarkivet anger registeråtgärden den relativa sökvägen i bildarkivet.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" är mappen i bildarkivet där programpaketet finns. Programtypen med namnet "MyApplicationType" och version "1.0.0" (båda finns i programmanifestet) registreras nu i klustret.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrera programpaketet som kopierats till ett externt arkiv

Från och med Service Fabric version 6.1 stöder etableringen att hämta paketet från en extern butik. Hämtnings-URI:n representerar sökvägen till [ `sfpkg` programpaketet](service-fabric-package-apps.md#create-an-sfpkg) där programpaketet kan hämtas med HTTP- eller HTTPS-protokoll. Paketet måste ha överförts tidigare till den här externa platsen. URI måste tillåta LÄS-åtkomst så att Service Fabric kan hämta filen. Filen `sfpkg` måste ha tillägget ".sfpkg". Etableringsåtgärden bör innehålla information om programtypen, som finns i programmanifestet.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Kommandot [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) returneras först när systemet har registrerat programpaketet. Hur lång registreringen tar beror på ansökningspaketets storlek och innehåll. Om det behövs kan parametern **-TimeoutSec** användas för att ange en längre timeout (standardtidsgränsen är 60 sekunder).

Om du har ett stort programpaket eller om du upplever timeout använder du parametern **-Async.** Kommandot returneras när klustret accepterar kommandot register. Registeråtgärden fortsätter vid behov.
Kommandot [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) listar programtypsversionerna och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett programpaket från bildarkivet

Om ett paket har kopierats till bildarkivet bör du ta bort det från den tillfälliga platsen när programmet har registrerats. Om du tar bort programpaket från avbildningsarkivet frigörs systemresurser. Om du behåller oanvända programpaket förbrukar disklagring och leder till problem med programmets prestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Skapa programmet

Du kan instansiera ett program från valfri programtypsversion som har registrerats med hjälp av cmdleten [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) Namnet på varje program måste börja med *schemat "fabric:"* och måste vara unikt för varje programinstans. Alla standardtjänster som definieras i programmanifestet för målprogramtypen skapas också.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Flera programinstanser kan skapas för en viss version av en registrerad programtyp. Varje programinstans körs isolerat, med en egen arbetskatalog och process.

Om du vill se vilka namngivna appar och tjänster som körs i klustret kör du cmdletsna [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) och [Get-ServiceFabricService:](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps)

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

När en programinstans inte längre behövs kan du ta bort den permanent med namnet med cmdlet [remove-ServiceFabricApplication.](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) tar automatiskt bort alla tjänster som tillhör programmet också, och tar bort alla tjänsttillstånd permanent. 

> [!WARNING]
> Den här åtgärden kan inte återföras och programtillståndet kan inte återställas.

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

När en viss version av en programtyp inte längre behövs bör du avregistrera programtypen med cmdlet [unregister-ServiceFabricApplicationType.](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) Om du avregistrerar oanvända programtyper frigörs lagringsutrymme som används av bildarkivet genom att programtypfilerna tas bort. Om du avregistrerar en programtyp tas inte programpaketet som kopierats till den tillfälliga platsen för bildarkivet bort om kopian till bildarkivet användes. En programtyp kan avregistreras så länge inga program instansieras mot den och inga väntande programuppgraderingar refererar till den.

Kör [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) för att se de programtyper som för närvarande är registrerade i klustret:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Kör [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) för att avregistrera en viss programtyp:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Felsökning

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage ber om en ImageStoreConnectionString

Service Fabric SDK-miljön bör redan ha rätt standardinställningar inställda. Men om det behövs bör ImageStoreConnectionString för alla kommandon matcha det värde som Service Fabric-klustret använder. Du hittar imagestoreConnectionString i klustermanifestet, hämtat med kommandona [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) och Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, som är en del av Modulen Service Fabric SDK PowerShell, används för att hämta anslutningssträngen för bildarkivet.  Om du vill importera SDK-modulen kör du:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString finns i klustermanifestet:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [Förstå anslutningssträngen](service-fabric-image-store-connection-string.md) för bildlagring för kompletterande information om anslutningssträngen för bildarkivet och bildarkivet.

### <a name="deploy-large-application-package"></a>Distribuera ett stort programpaket

Problem: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) time out för ett stort programpaket (order på GB).
Försök:
- Ange en större timeout för [kommandot Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) med `TimeoutSec` parameter. Som standard är tidsgränsen 30 minuter.
- Kontrollera nätverksanslutningen mellan källdatorn och klustret. Om anslutningen är långsam bör du överväga att använda en dator med en bättre nätverksanslutning.
Om klientdatorn finns i en annan region än klustret bör du överväga att använda en klientdator i en närmare eller samma region som klustret.
- Kontrollera om du slår på extern begränsning. När avbildningsarkivet till exempel är konfigurerat för att använda azure storage kan överföringen begränsas.

Problem: Ladda upp paketet har slutförts, men [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) time out. Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till bildarkivet.
Komprimeringen minskar storleken och antalet filer, vilket i sin tur minskar mängden trafik och arbete som Service Fabric måste utföra. Uppladdningen kan vara långsammare (särskilt om du inkluderar komprimeringstiden), men registrera och avmarkera programtypen är snabbare.
- Ange en större timeout för [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) med `TimeoutSec` parametern.
- Ange `Async` växel för [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Kommandot returneras när klustret accepterar kommandot och registreringen av programtypen fortsätter asynkront. Därför finns det ingen anledning att ange en högre timeout i det här fallet. Kommandot [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) listar alla registrerade programtypversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

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

Problem: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) time out för ett programpaket med många filer (ordning på tusentals).
Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till bildarkivet. Komprimeringen minskar antalet filer.
- Ange en större timeout för [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) med `TimeoutSec` parametern.
- Ange `Async` växel för [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Kommandot returneras när klustret accepterar kommandot och registreringen av programtypen fortsätter asynkront.
Därför finns det ingen anledning att ange en högre timeout i det här fallet. Kommandot [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) listar alla registrerade programtypversioner och deras registreringsstatus. Du kan använda det här kommandot för att avgöra när registreringen är klar.

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

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

[Service Fabric hälsa introduktion](service-fabric-health-introduction.md)

[Diagnostisera och felsöka en service med servicevävnad](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellera ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md