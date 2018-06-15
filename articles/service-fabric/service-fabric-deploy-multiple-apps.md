---
title: Distribuera ett Node.js-program som använder MongoDB till Azure Service Fabric | Microsoft Docs
description: Genomgång av hur du paketerar flera gäst körbara filer ska distribueras till ett Azure Service Fabric-kluster
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 9a7ab3881cd1058a60ff7d5f6e50c296f042e76e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206087"
---
# <a name="deploy-multiple-guest-executables"></a>Distribuera flera körbara gäster
Den här artikeln visar hur du paketera och distribuera flera gäst körbara filer till Azure Service Fabric. Skapa och distribuera en enda Service Fabric-paket finns att [Distribuera gäst körbara till Service Fabric](service-fabric-deploy-existing-app.md).

Den här genomgången visar hur du distribuerar ett program med en Node.js-klientdel som använder MongoDB som datalager, men du kan använda stegen för att alla program som är beroende av ett annat program.   

Du kan använda Visual Studio för att skapa programpaket som innehåller flera gäst körbara filer. Se [med hjälp av Visual Studio för att paketera ett befintligt program](service-fabric-deploy-existing-app.md). När du har lagt till den första körbara fil som gäst, högerklicka på projektet för konsolprogrammet och välj den **Lägg till -> Ny Service Fabric-tjänsten** att lägga till det andra körbara gäst-projektet i lösningen. Obs: Om du väljer att länka källa i Visual Studio-projekt skapar Visual Studio-lösning ska se till att ditt programpaket är uppdaterade med ändringar i källan. 

## <a name="samples"></a>Exempel
* [Exempel för förpackning och distribution av en gäst körbar fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via Naming service med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Paketet flera gäst körbara programmet manuellt
Alternativt kan du paketera gästen körbara manuellt. Manuell förpackningen kan den här artikeln använder verktyget Service Fabric-paket, som finns på [ http://aka.ms/servicefabricpacktool ](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Paketera Node.js-program
Den här artikeln förutsätter att Node.js inte är installerat på noderna i Service Fabric-klustret. Följaktligen behöver du lägga till Node.exe i rotkatalogen för programmet nod innan paketering. Katalogstrukturen för Node.js-program (med snabb webbramverk och Jade mall engine) bör likna exemplet nedan:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Som ett nästa steg kan skapa du ett programpaket för Node.js-program. Koden nedan skapar ett Service Fabric-programpaket som innehåller Node.js-program.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Nedan följer en beskrivning av de parametrar som används:

* **/ source** pekar på katalogen för det program som ska paketeras.
* **/ target** definierar den katalog där paketet ska skapas. Den här katalogen måste vara olika från källkatalogen.
* **/ AppName** definierar namnet på programmet i det befintliga programmet. Det är viktigt att du förstår att detta innebär att tjänstnamnet i manifestet och inte namnet på Service Fabric-programmet.
* **/exe** definierar den körbara filen som Service Fabric ska starta i det här fallet `node.exe`.
* **/Ma** definierar argumentet som används för att starta den körbara filen. Node.js är inte installerad, Service Fabric behöver starta webbservern Node.js genom att köra `node.exe bin/www`.  `/ma:'bin/www'` Anger verktyget paketering för att använda `bin/www` som argument för node.exe.
* **/ AppType** definierar Service Fabric-programmets typnamn.

Om du bläddrar till den katalog som angavs i parametern/Target kan du se att verktyget har skapat ett fullt fungerande Service Fabric-paket som visas nedan:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Den genererade ServiceManifest.xml har nu ett avsnitt som beskriver hur Node.js-webbserver bör startas, som visas i kodfragmentet nedan:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
I det här exemplet lyssnar webbservern Node.js på port 3000, så du behöver uppdatera slutpunktsinformationen i filen ServiceManifest.xml enligt nedan.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Paketera programmet MongoDB
Nu när du har paketerat Node.js-program, kan du gå vidare och paketet MongoDB. Som tidigare nämnts, är de steg som du går igenom nu inte specifika för Node.js och MongoDB. Faktum är gäller de för alla program som är avsedda att vara packade tillsammans som ett Service Fabric-program.  

Om du vill paketera MongoDB, som du vill kontrollera att du paketerar Mongod.exe och Mongo.exe. Båda binärfiler finns i den `bin` för din MongoDB-installationskatalogen. Katalogstrukturen ser ut som nedan.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric måste börja MongoDB med ett kommando som liknar den nedan, så du behöver använda den `/ma` parameter när paketera MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data som sparas inte när det gäller ett nodfel om du placerar katalogen MongoDB data på den lokala katalogen för noden. Du bör använda beständig lagring eller implementera replikuppsättning MongoDB för att förhindra dataförlust.  
>
>

I PowerShell eller Kommandotolken kör vi verktyget paketering med följande parametrar:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

För att lägga till MongoDB till ditt Service Fabric-programpaket måste du se till att/Target-parametern pekar på den katalog som innehåller programmet redan manifest tillsammans med Node.js-program. Du måste också se till att du använder samma ApplicationType namn.

Nu ska vi Bläddra till mappen och undersöka vad verktyget har skapats.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Verktyget till en ny mapp, MongoDB, i den katalog som innehåller MongoDB-binärfiler som du kan se. Om du öppnar den `ApplicationManifest.xml` filen ser du att paketet innehåller nu både Node.js-program och MongoDB. Koden nedan visar innehållet i programmanifestet.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publicera programmet
Det sista steget är att publicera program till det lokala Service Fabric-klustret med hjälp av PowerShell-skript nedan:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

När programmet har publicerats till det lokala klustret kan du komma åt Node.js-program på den port som vi angav i service manifest för Node.js-program – till exempel http://localhost:3000.

I den här kursen har du fått veta hur du enkelt paketera två befintliga program som en Service Fabric-program. Du har lärt dig hur du distribuerar den till Service Fabric så att den kan dra nytta av några av Service Fabric-funktioner, till exempel hög tillgänglighet och integrering med hälsotillstånd.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Att lägga till flera gäst körbara filer i ett befintligt program med hjälp av Yeoman på Linux

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg: 
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfguest:AddService` och ange den nödvändiga informationen.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om hur du distribuerar behållare med [översikt över Service Fabric och behållare](service-fabric-containers-overview.md)
* [Exempel för förpackning och distribution av en gäst körbar fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via Naming service med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)
