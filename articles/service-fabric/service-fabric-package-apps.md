---
title: Paketera en Azure Service Fabric-app
description: Lär dig mer om hur du paketerar ett Azure Service Fabric-program och hur du förbereder distributionen till ett kluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258348"
---
# <a name="package-an-application"></a>Paketera ett program

I den här artikeln beskrivs hur du paketerar ett Service Fabric-program och gör det redo för distribution.

## <a name="package-layout"></a>Paketlayout

Programmanifestet, ett eller flera tjänstmanifest och andra nödvändiga paketfiler måste ordnas i en viss layout för distribution till ett Service Fabric-kluster. Exempelvisytsst i den här artikeln måste ordnas i följande katalogstruktur:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Mapparna namnges för att matcha **attributen Namn för** varje motsvarande element. Om tjänstmanifestet till exempel innehöll två kodpaket med namnen **MyCodeA** och **MyCodeB,** skulle två mappar med samma namn innehålla nödvändiga binärfiler för varje kodpaket.

## <a name="use-setupentrypoint"></a>Använda SetupEntryPoint

Typiska scenarier för att använda **SetupEntryPoint** är när du behöver köra en körbar innan tjänsten startar eller om du behöver utföra en åtgärd med förhöjda privilegier. Ett exempel:

* Ställa in och initiera miljövariabler som tjänsten körsbara behov. Det är inte begränsat till endast körbara filer skrivna via Service Fabric programmeringsmodeller. Npm.exe behöver till exempel vissa miljövariabler som konfigurerats för att distribuera ett node.js-program.
* Konfigurera åtkomstkontroll genom att installera säkerhetscertifikat.

Mer information om hur du konfigurerar **SetupEntryPoint**finns i [Konfigurera principen för en startpunkt för tjänstinställningar](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurera

### <a name="build-a-package-by-using-visual-studio"></a>Skapa ett paket med Visual Studio

Om du använde Visual Studio för att skapa ditt program kan du använda kommandot *Paket* för att automatiskt skapa ett paket som matchar layouten som beskrivs ovan.

Om du vill skapa ett paket högerklickar du på programprojektet i *Solution Explorer* och väljer kommandot **Paket:**

![Paketera ett program med Visual Studio][vs-package-command]

När förpackningen är klar hittar du paketets plats i **utdatafönstret.** Förpackningssteget sker automatiskt när du distribuerar eller felsöker ditt program i Visual Studio.

### <a name="build-a-package-by-command-line"></a>Skapa ett paket efter kommandorad

Det är också möjligt att programmässigt `msbuild.exe`paketera upp ditt program med . Under huven kör Visual Studio den så att utgången är densamma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testa paketet

Du kan verifiera paketstrukturen lokalt via PowerShell med kommandot [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)
Det här kommandot söker efter problem med manifesttolkar och verifierar alla referenser. Det här kommandot verifierar bara den strukturella korrektheten hos kataloger och filer i paketet.
Det verifierar inte någon av koden eller datapaketets innehåll utöver att kontrollera att alla nödvändiga filer finns.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Det här felet visar att filen *MySetup.bat* som refereras i tjänstmanifestet **SetupEntryPoint** saknas i kodpaketet. När den saknade filen har lagts till skickas programverifieringen:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Om ditt program har definierat [programparametrar](service-fabric-manage-multiple-environment-app-configuration.md) kan du skicka dem i [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) för korrekt validering.

Om du känner till klustret där programmet ska `ImageStoreConnectionString` distribueras, rekommenderar vi att du skickar in parametern. I det här fallet valideras paketet också mot tidigare versioner av programmet som redan körs i klustret. Valideringen kan till exempel identifiera om ett paket med samma version men olika innehåll redan har distribuerats.  

När programmet har paketerats korrekt och skickar validering, överväg att komprimera paketet för snabbare distributionsåtgärder.

## <a name="compress-a-package"></a>Komprimera ett paket

När ett paket är stort eller har många filer kan du komprimera det för snabbare distribution. Komprimering minskar antalet filer och paketstorleken.
För ett komprimerat programpaket kan det ta längre tid att [ladda upp programpaketet](service-fabric-deploy-remove-applications.md#upload-the-application-package) jämfört med att ladda upp det okomprimerade paketet, särskilt om komprimeringen görs som en del av kopian. Med [komprimering, registrering](service-fabric-deploy-remove-applications.md#register-the-application-package) och avregistrering av [programtypen](service-fabric-deploy-remove-applications.md#unregister-an-application-type) är snabbare.

Distributionsmekanismen är densamma för komprimerade och okomprimerade paket. Om paketet komprimeras lagras det som sådant i klusteravbildningsarkivet och det är okomprimerat på noden innan programmet körs.
Komprimeringen ersätter det giltiga Service Fabric-paketet med den komprimerade versionen. Mappen måste tillåta skrivbehörighet. Om du kör komprimering på ett redan komprimerat paket får inga ändringar.

Du kan komprimera ett paket genom att köra Powershell-kommandot [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) med `CompressPackage` växel. Du kan packa upp paketet med samma `UncompressPackage` kommando med växeln.

Följande kommando komprimerar paketet utan att kopiera det till bildarkivet. Du kan kopiera ett komprimerat paket till ett eller flera Service Fabric-kluster efter behov med `SkipCopy` [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) utan flaggan.
Paketet innehåller nu zippade `code`filer `config`för `data` , och paket. Programmanifestet och tjänstmanifesten är inte zippade, eftersom de behövs för många interna åtgärder. Till exempel paketdelning, programtypnamn och versionsutvinning för vissa valideringar behöver alla komma åt manifesten. Zippa manifesten skulle göra dessa operationer ineffektiva.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Alternativt kan du komprimera och kopiera paketet med [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) i ett steg.
Om paketet är stort tillhandahåller du en tillräckligt hög timeout för att ge tid för både paketkomprimeringen och överföringen till klustret.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internt beräknar Service Fabric kontrollsummar för programpaketen för validering. När du använder komprimering beräknas kontrollsummen på de zippade versionerna av varje paket. Om du skapar en ny zip från samma programpaket skapas olika kontrollsummar. Använd [diff-etablering](service-fabric-application-upgrade-advanced.md)om du vill förhindra valideringsfel . Med det här alternativet ska du inte inkludera de oförändrade paketen i den nya versionen. Referera dem i stället direkt från det nya tjänstmanifestet.

Om diff-etablering inte är ett alternativ och du måste inkludera `code` `config`paketen, generera nya versioner för , och `data` paket för att undvika kontrollsumman som inte stämmer överens. Det är nödvändigt att generera nya versioner för oförändrade paket när ett komprimerat paket används, oavsett om i tidigare versioner används komprimering eller inte.

Paketet är nu korrekt paketerat, validerat och komprimerat (om det behövs), så det är klart för [distribution](service-fabric-deploy-remove-applications.md) till ett eller flera Service Fabric-kluster.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprimera paket när du distribuerar med Visual Studio

Du kan instruera Visual Studio att komprimera paket `CopyPackageParameters` vid distributionen genom att `CompressPackage` lägga `true`till elementet i publiceringsprofilen och ange attributet till .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Skapa en sfpkg

Från och med version 6.1 tillåter Service Fabric etablering från en extern butik.
Med det här alternativet behöver programpaketet inte kopieras till bildarkivet. I stället kan `sfpkg` du skapa en och ladda upp den till en extern butik och sedan tillhandahålla hämtnings-URI:n till Service Fabric vid etablering. Samma paket kan etableras till flera kluster. Etablering från det externa arkivet sparar den tid som behövs för att kopiera paketet till varje kluster.

Filen `sfpkg` är en zip som innehåller det ursprungliga programpaketet och har tillägget ".sfpkg".
Inuti dragkedjan kan programpaketet komprimeras eller okomprimeras. Komprimeringen av programpaketet inuti postningen görs på kod-, config- och datapaketnivåer, som [tidigare nämnts.](service-fabric-package-apps.md#compress-a-package)

Om du `sfpkg`vill skapa en börjar du med en mapp som innehåller det ursprungliga programpaketet, komprimerat eller inte. Använd sedan vilket verktyg som helst för att zip mappen med tillägget ".sfpkg". Använd till exempel [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Måste `sfpkg` laddas upp till den externa butiken utanför bandet, utanför Service Fabric. Det externa arkivet kan vara ett arkiv som exponerar en REST http- eller https-slutpunkt. Under etableringen kör Service Fabric en GET-åtgärd för att hämta `sfpkg` programpaketet, så butiken måste tillåta LÄS-åtkomst för paketet.

För att etablera paketet använder du extern etablering, vilket kräver hämtnings-URI och programtypsinformation.

>[!NOTE]
> Etablering baserat på den relativa sökvägen `sfpkg` för bildarkivet stöder för närvarande inte filer. Därför `sfpkg` bör inte kopieras till bildarkivet.

## <a name="next-steps"></a>Nästa steg

[Distribuera och ta bort program][10] beskriver hur du använder PowerShell för att hantera programinstanser

[Om du hanterar programparametrar för flera miljöer][11] beskrivs hur du konfigurerar parametrar och miljövariabler för olika programinstanser.

[Konfigurera säkerhetsprinciper för ditt program][12] beskriver hur du kör tjänster under säkerhetsprinciper för att begränsa åtkomsten.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md