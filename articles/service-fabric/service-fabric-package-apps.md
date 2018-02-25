---
title: Paketet en Azure Service Fabric-app | Microsoft Docs
description: Hur du paketerar ett Service Fabric-program innan du distribuerar till ett kluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: 0db49b9bd50c640175292671f813d23d960b52e1
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="package-an-application"></a>Paketera ett program
Den här artikeln beskriver hur du paketerar ett Service Fabric-program och förbereda för distribution.

## <a name="package-layout"></a>Paketet layout
Applikationsmanifestet, en eller flera service manifest och andra nödvändiga paketfilerna måste ordnas i en viss layout för distribution i ett Service Fabric-kluster. I exemplet manifesten i den här artikeln måste du ordnas i följande katalogstruktur:

```
PS D:\temp> tree /f .\MyApplicationType

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

Mapparna namn att matcha den **namn** attribut för varje motsvarande element. Om exempelvis service manifest finns två kod paket med namnen **MyCodeA** och **MyCodeB**, två mappar med samma namn ska innehålla de nödvändiga binärfilerna för varje kodpaketet.

## <a name="use-setupentrypoint"></a>Använd SetupEntryPoint
Vanliga scenarier där **SetupEntryPoint** när du behöver köra en körbar fil innan du startar tjänsten eller som du behöver utföra en åtgärd med förhöjda privilegier. Exempel:

* Ställa in och initierar miljövariabler som behöver körbar fil. Det är inte begränsad till endast körbara filer skrivs via Service Fabric programmeringsmodeller. Exempelvis måste npm.exe miljövariabler som konfigurerats för att distribuera ett node.js-program.
* Konfigurera åtkomstkontroll genom att installera security-certifikat.

Mer information om hur du konfigurerar den **SetupEntryPoint**, se [konfigurera principen för en startpunkt för installationen av tjänsten](service-fabric-application-runas-security.md)

<a id="Package-App"></a>
## <a name="configure"></a>Konfigurera
### <a name="build-a-package-by-using-visual-studio"></a>Skapa ett paket med hjälp av Visual Studio
Om du använder Visual Studio 2015 för att skapa programmet kan använda du kommandot paketet för att automatiskt skapa ett paket som matchar layout som beskrivs ovan.

Om du vill skapa ett paket, högerklicka på programmet projektet i Solution Explorer och välj kommandot paketera enligt nedan:

![Ett program med Visual Studio-paket][vs-package-command]

När paketering är klar, kan du hitta platsen för paketets i den **utdata** fönster. Steget paketering sker automatiskt när du distribuerar eller felsöker programmet i Visual Studio.

### <a name="build-a-package-by-command-line"></a>Skapa ett paket av kommandoraden
Det är också möjligt att programmässigt packa upp appen med `msbuild.exe`. Under huven körs Visual Studio den så att utdata är samma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testa paketet
Du kan verifiera paketet strukturen lokalt via PowerShell med hjälp av den [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) kommando.
Detta kommando kontrollerar manifestet parsning problem och kontrollera alla referenser. Detta kommando kontrollerar endast strukturella riktighet kataloger och filer i paketet.
Går det inte verifiera några paketinnehållet kod eller data utöver att kontrollera att alla nödvändiga filer finns.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Det här felet visas som den *MySetup.bat* fil som refereras i service manifest **SetupEntryPoint** kodpaketet saknas. När filen saknas läggs skickar programmet-verifiering:

```
PS D:\temp> tree /f .\MyApplicationType

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

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Om programmet har [applikationsparametrarna](service-fabric-manage-multiple-environment-app-configuration.md) definierats kan du skicka dem i [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) för rätt validering.

Om du vet klustret där programmet ska distribueras, bör du skicka in den `ImageStoreConnectionString` parameter. I det här fallet kontrolleras också paketet gentemot tidigare versioner av programmet som körs i klustret. Till exempel valideringen kan känna av om ett paket med samma version men olika innehåll har redan distribuerats.  

När programmet levereras på rätt sätt och har klarat valideringen, Överväg att komprimera paketet för snabbare distributionsåtgärder.

## <a name="compress-a-package"></a>Komprimera ett paket
Om ett paket är stora eller många filer, kan du komprimera den för snabbare distribution. Komprimeringen minskar antalet filer och paketstorleken.
För en komprimerad programpaket [ladda upp programpaketet](service-fabric-deploy-remove-applications.md#upload-the-application-package) kan ta längre tid jämfört med överför okomprimerade paketet, särskilt om komprimering görs som en del av kopia. Med komprimering, [registrerar](service-fabric-deploy-remove-applications.md#register-the-application-package) och [avregistrerades programtypen](service-fabric-deploy-remove-applications.md#unregister-an-application-type) är snabbare.

Mekanism för distribution är samma för komprimerade och okomprimerade paket. Om paketet har komprimerats, lagras som sådana i klustret image store och den är okomprimerade på noden innan programmet körs.
Komprimeringen ersätter giltigt Service Fabric-paket med den komprimerade versionen. Mappen måste tillåta skrivbehörighet. Kör komprimering på en redan komprimerade paketet ger inga ändringar.

Du kan komprimera ett paket genom att köra Powershell-kommando [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) med `CompressPackage` växla. Du kan expandera paketet med samma kommando med `UncompressPackage` växla.

Följande kommando komprimerar paketet utan att kopiera den till image store. Du kan kopiera komprimerade paketet till en eller flera Service Fabric-kluster, efter behov, med [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) utan den `SkipCopy` flaggan.
Paketet innehåller nu komprimerade filer för den `code`, `config`, och `data` paket. Applikationsmanifestet och service manifest är inte zippade, eftersom de behövs för många interna åtgärder. Till exempel delning av paket, program namn och version Extraheringen av anslutningstyp behöver för vissa alla verifieringar åtkomst till manifesten. Komprimerar manifesten gör dessa åtgärder ineffektiv.

```
PS D:\temp> tree /f .\MyApplicationType

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
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Du kan komprimera och kopiera paketet med [kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) i ett steg.
Om paketet är stort, ange en tillräckligt hög tidsgräns för både paketet komprimering och överför till klustret.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internt, beräknar Service Fabric kontrollsummor för programpaket för verifiering. När du använder komprimering beräknas av kontrollsummor i komprimerade versioner av varje paket. Generera en ny zip från samma paket som programmet skapar olika kontrollsummor. För att förhindra valideringsfel använda [diff etablering](service-fabric-application-upgrade-advanced.md). Med det här alternativet ingår inte oförändrat paket i den nya versionen. I stället referera till dem direkt från det nya service manifestet.

Om diff-etablering inte är ett alternativ och du måste inkludera paketen, skapa nya versioner för den `code`, `config`, och `data` paket för att undvika felaktig matchning av kontrollsumma. Det är nödvändigt att generera nya versioner för oförändrade paket när komprimerade paketet används, oavsett om tidigare version använder komprimering eller inte.

Paketet är nu paketeras korrekt verifieras och komprimerade (vid behov), så att den är klar för [distribution](service-fabric-deploy-remove-applications.md) till en eller flera Service Fabric-kluster.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprimera paket när du distribuerar med Visual Studio
Du kan instruera Visual Studio för att komprimera paket för distribution, genom att lägga till den `CopyPackageParameters` elementet så att din profil och ange den `CompressPackage` attributet `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Skapa en sfpkg
Från och med version 6.1, kan Service Fabric etablera från en extern butik.
Med det här alternativet behöver programpaketet inte kopieras till image store. Skapa i stället en `sfpkg` och överföra den till en extern butik och sedan ge Service Fabric nedladdningen URI vid etablering. Samma paket kan etableras för flera kluster. Etablering från externa store sparar tid för att kopiera paketet till varje kluster.

Den `sfpkg` filen är en zip som innehåller det första programpaketet och har tillägget ”.sfpkg”.
I zip programpaketet komprimerad eller okomprimerad. Komprimering av programpaket inuti zip görs på kod, konfiguration och data paketet nivåer som [ovan](service-fabric-package-apps.md#compress-a-package).

Så här skapar du en `sfpkg`, börja med en mapp som innehåller det ursprungliga programpaketet, komprimerade eller inte. Använd sedan alla verktyg för att zip-mapp med tillägget ”.sfpkg”. Till exempel använda [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Den `sfpkg` måste överföras till extern butik out of band utanför Service Fabric. Den externa lagringen kan vara en butik som Exponerar en REST http eller https-slutpunkt. Under etableringen, Service Fabric kör en GET-åtgärd för att hämta den `sfpkg` programpaket, så arkivet måste tillåta läsåtkomst för paketet.

Använda externa etablera som kräver hämtning URI och informationen om programmet för att etablera paketet.

>[!NOTE]
> För närvarande inte stöd för etablering baserat på relativ sökväg för image store `sfpkg` filer. Därför kan den `sfpkg` inte ska ha kopierats till image store.

## <a name="next-steps"></a>Nästa steg
[Distribuera och ta bort program] [ 10] beskriver hur du använder PowerShell för att hantera programinstanser

[Hantera programparametrar för miljöer med flera] [ 11] beskriver hur du konfigurerar parametrar och miljövariabler för olika programinstanser.

[Konfigurera säkerhetsprinciper för ditt program] [ 12] beskriver hur du kör tjänster under säkerhetsprinciper för att begränsa åtkomsten.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
