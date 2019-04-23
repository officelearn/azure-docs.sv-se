---
title: Paketera en Azure Service Fabric-app | Microsoft Docs
description: Hur du paketerar ett Service Fabric-program innan du distribuerar till ett kluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: b8e66a9d5bba0c48f15b1ccd3f2d47e5405db792
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785606"
---
# <a name="package-an-application"></a>Paketera ett program

Den här artikeln beskriver hur du paketerar ett Service Fabric-program och förbereda för distribution.

## <a name="package-layout"></a>Paketet layout

Applikationsmanifestet, en eller flera tjänstmanifest och andra nödvändiga paketfiler måste ordnas i en viss layout för distribution till Service Fabric-kluster. I exemplet manifesten i den här artikeln måste du ordnas i följande katalogstruktur:

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

Mapparna är namngivna så att den matchar den **namn** attribut för varje motsvarande element. Exempel: om tjänstmanifestet finns två kodpaket med namnen **MyCodeA** och **MyCodeB**, sedan två mappar med samma namn innehåller nödvändiga binärfilerna för varje kodpaketet.

## <a name="use-setupentrypoint"></a>Använda SetupEntryPoint

Vanliga scenarier där **SetupEntryPoint** när du behöver köra en körbar fil innan du startar tjänsten eller som du behöver utföra en åtgärd med förhöjd behörighet. Exempel:

* Konfigurera och initiering av miljövariabler som behöver körbar fil. Det är inte begränsad till endast körbara filer skrivs via programmeringsmodeller för Service Fabric. Till exempel måste npm.exe vissa miljövariabler som konfigurerats för att distribuera ett node.js-program.
* Konfigurera åtkomstkontroll genom att installera säkerhetscertifikat.

Mer information om hur du konfigurerar den **SetupEntryPoint**, se [konfigurera principen för en tjänstens konfigurationsstartpunkt](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurera

### <a name="build-a-package-by-using-visual-studio"></a>Skapa ett paket med hjälp av Visual Studio

Om du använder Visual Studio 2015 för att skapa ditt program kan använda du kommandot paketet för att automatiskt skapa ett paket som matchar layouten som beskrivs ovan.

Om du vill skapa ett paket, högerklicka på programprojektet i Solution Explorer och välj kommandot paketera enligt nedan:

![Paketera ett program med Visual Studio][vs-package-command]

När paketering är klar, hittar du platsen för paketet i den **utdata** fönster. Paketering steg sker automatiskt när du skapar eller felsöka programmet i Visual Studio.

### <a name="build-a-package-by-command-line"></a>Skapa ett paket via kommandoraden

Det går också att programmässigt packa upp ditt program med hjälp av `msbuild.exe`. Under huven körs Visual Studio den så att resultatet är samma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testa paketet

Du kan kontrollera hur paketet lokalt via PowerShell genom att använda den [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) kommando.
Det här kommandot söker efter manifest parsning problem och kontrollera alla referenser. Det här kommandot kontrollerar endast strukturella riktighet kataloger och filer i paketet.
Den verifierar inte någon av kod eller paketinnehållet utöver att kontrollera att alla nödvändiga filer finns.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Det här felet visas som den *MySetup.bat* fil som refereras till i tjänstmanifestet **SetupEntryPoint** saknas kodpaketet. När filen som saknas har lagts till, skickar programmet-verifiering:

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

Om programmet har [programparametrar](service-fabric-manage-multiple-environment-app-configuration.md) definierats kan du skicka dem i [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) för rätt validering.

Om du vet klustret där programmet ska distribueras, bör du skickar in den `ImageStoreConnectionString` parametern. I det här fallet kontrolleras också paketet gentemot tidigare versioner av programmet som redan körs i klustret. Till exempel verifieringen kan identifiera om ett paket med samma version men olika innehåll redan har distribuerats.  

När programmet är paketerat korrekt och godkänns vid kan du överväga att komprimera paketet för snabbare distributionsåtgärder.

## <a name="compress-a-package"></a>Komprimera ett paket

När ett paket är stor eller har många filer, kan du komprimera den för snabbare distribution. Komprimering minskar antalet filer och paketstorleken.
För ett komprimerade programpaket [ladda upp programpaketet](service-fabric-deploy-remove-applications.md#upload-the-application-package) kan ta längre tid jämfört med att ladda upp okomprimerade paketet, särskilt om komprimering görs som en del av kopia. Vid komprimering [registrerar](service-fabric-deploy-remove-applications.md#register-the-application-package) och [Avregistrerar programtypen](service-fabric-deploy-remove-applications.md#unregister-an-application-type) är snabbare.

Mekanismen för distribution är samma för komprimerade och okomprimerade paket. Om paketet har komprimerats, den är lagrad som sådana i avbildningsarkivet klustret och det är okomprimerade på noden innan programmet körs.
Komprimeringen ersätter giltigt Service Fabric-paket med den komprimerade versionen. Mappen måste tillåta skrivbehörighet. Kör komprimering på en redan komprimerade paketet ger inga ändringar.

Du kan komprimera ett paket genom att köra Powershell-kommandot [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) med `CompressPackage` växla. Du kan expandera paketet med samma kommando med `UncompressPackage` växla.

Följande kommando komprimerar paketet utan att kopiera den till avbildningsarkivet. Du kan kopiera en komprimerade paketet till en eller flera Service Fabric-kluster efter behov med [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) utan den `SkipCopy` flaggan.
Paketet innehåller nu komprimerade filer för den `code`, `config`, och `data` paket. Manifestet och i tjänstmanifest är inte zippade, eftersom de behövs för många interna åtgärder. Till exempel delning av paket, program och specifik produktversion Extraheringen av anslutningstyp behöver för vissa alla verifieringar åtkomst till manifest. Komprimeringen manifesten säkerställer åtgärderna ineffektiv.

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

Du kan också du komprimera och kopiera paketet med [kopia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) i ett enda steg.
Om paketet är stort, ange en tidsgräns som är tillräckligt högt för både paketet komprimering och överföringen till klustret.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Service Fabric beräknar internt kontrollsummor för programpaket för verifiering. När du använder komprimering, beräknas kontrollsummor i komprimerade versioner av varje paket. Generera en ny zip från samma programpaketet skapar olika kontrollsummor. Du kan förhindra verifieringsfel använda [diff etablering](service-fabric-application-upgrade-advanced.md). Med det här alternativet omfattar inte paket som oförändrat i den nya versionen. I stället referera till dem direkt från det nya tjänstmanifestet.

Om diff etablering är inte ett alternativ och du måste inkludera paketen, skapa nya versioner för den `code`, `config`, och `data` paket för att undvika felaktig matchning av kontrollsumma. Det är nödvändigt att generera nya versioner för oförändrade paket när en komprimerade paketet används, oavsett om tidigare version använder komprimering eller inte.

Paketet är nu paketeras korrekt verifieras och komprimeras (vid behov), så att den är redo för [distribution](service-fabric-deploy-remove-applications.md) till en eller flera Service Fabric-kluster.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprimera paket när du distribuerar med hjälp av Visual Studio

Du kan instruera Visual Studio för att komprimera paket för distribution, genom att lägga till den `CopyPackageParameters` elementet så att din publiceringsprofil och ange den `CompressPackage` attributet `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Skapa en sfpkg

Från och med version 6.1, kan Service Fabric etablering från en extern lagring.
Med det här alternativet har inte programpaketet som ska kopieras till avbildningsarkivet. Skapa i stället en `sfpkg` och överföra den till en extern lagring och ange sedan nedladdningen URI till Service Fabric vid etablering. Samma paket kan etableras för flera kluster. Etablera från extern lagring sparar tid att kopiera paketet till varje kluster.

Den `sfpkg` filen är en zip som innehåller det första programpaketet och har tillägget ”.sfpkg”.
I ZIP-filen, programpaketet komprimerad eller okomprimerad. Komprimering av programpaket i ZIP-filen är klar på kod, konfiguration och nivåer för paketet, som [nämnts tidigare](service-fabric-package-apps.md#compress-a-package).

Skapa en `sfpkg`, börja med en mapp som innehåller det ursprungliga programpaketet komprimerat eller inte. Sedan kan använda alla för att zip-mapp med tillägget ”.sfpkg”. Till exempel använda [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Den `sfpkg` måste överföras till extern lagring out of band, utanför Service Fabric. Extern lagring kan vara inga store som Exponerar en REST http eller https-slutpunkt. Under etableringen, Service Fabric utför en åtgärd för hämtning av att ladda ned den `sfpkg` programpaket, så arkivet måste tillåta läsåtkomst för paketet.

Använda externa etablera, vilket kräver nedladdningen URI och informationen om programmet för att etablera paketet.

>[!NOTE]
> För närvarande inte stöd för etablering baserat på avbildningen store relativ sökväg `sfpkg` filer. Därför kan den `sfpkg` inte ska ha kopierats till avbildningsarkivet.

## <a name="next-steps"></a>Nästa steg

[Distribuera och ta bort program] [ 10] beskriver hur du använder PowerShell för att hantera programinstanser

[Hantera programparametrar för flera miljöer] [ 11] beskriver hur du konfigurerar parametrar och miljövariabler för olika programinstanser.

[Ställer in säkerhetsprinciper för ditt program] [ 12] beskriver hur du kör tjänsterna under säkerhetsprinciper för att begränsa åtkomsten.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md