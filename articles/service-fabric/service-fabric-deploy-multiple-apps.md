---
title: Distribuera ett Node. js-program som använder MongoDB
description: Genom gång av hur du paketerar flera körbara gäst program för att distribuera till ett Azure Service Fabric-kluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614476"
---
# <a name="deploy-multiple-guest-executables"></a>Distribuera flera körbara gäster
Den här artikeln visar hur du paketerar och distribuerar flera körbara gäst program till Azure Service Fabric. För att skapa och distribuera ett enda Service Fabric-paket läser du så här [distribuerar du en körbar gäst fil till Service Fabric](service-fabric-deploy-existing-app.md).

När den här genom gången visar hur du distribuerar ett program med en Node. js-klient som använder MongoDB som data lager, kan du tillämpa stegen för alla program som har beroenden i ett annat program.   

Du kan använda Visual Studio för att skapa programpaketet som innehåller flera körbara gäst program. Se [använda Visual Studio för att paketera ett befintligt program](service-fabric-deploy-existing-app.md). När du har lagt till den första gäst filen, högerklickar du på programprojektet och väljer **> ny Service Fabric tjänst** för att lägga till det andra körbara gäst programmet till lösningen. Obs! Om du väljer att länka källan i Visual Studio-projektet och skapar Visual Studio-lösningen, ser du till att ditt programpaket är uppdaterat med ändringar i källan. 

## <a name="samples"></a>Exempel
* [Exempel på paketering och distribution av en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två körbara gäst programC# (och NodeJS) som kommunicerar via namngivnings tjänsten med rest](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Paketera det körbara programmet för flera gäster manuellt
Du kan också paketera det körbara gäst programmet manuellt. Mer information finns i [manuellt paket och distribution av en befintlig körbar fil](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Paketera Node. js-programmet
Den här artikeln förutsätter att Node. js inte är installerat på noderna i Service Fabric klustret. Därför måste du lägga till Node. exe i rot katalogen för Node-programmet innan du packar. Katalog strukturen i Node. js-programmet (med Express Web Framework och Jade Template Engine) bör se ut ungefär så här:

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

Som nästa steg skapar du ett programpaket för Node. js-programmet. Koden nedan skapar ett Service Fabric programpaket som innehåller Node. js-programmet.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Nedan visas en beskrivning av de parametrar som används:

* **/Source** pekar på katalogen för det program som ska paketeras.
* **/target** definierar katalogen som paketet ska skapas i. Den här katalogen måste vara en annan än käll katalogen.
* **/APPNAME** definierar program namnet för det befintliga programmet. Det är viktigt att förstå att detta översätter till tjänst namnet i manifestet och inte till namnet på Service Fabric program.
* **/exe** definierar den körbara fil som Service Fabric ska starta, i det här fallet `node.exe`.
* **/ma** definierar det argument som används för att starta den körbara filen. Eftersom Node. js inte är installerat måste Service Fabric starta Node. js-webbservern genom att köra `node.exe bin/www`.  `/ma:'bin/www'` anger att det paketerade verktyget ska använda `bin/www` som argument för Node. exe.
* **/AppType** definierar namnet på Service Fabric program typen.

Om du bläddrar till den katalog som angavs i parametern/Target kan du se att verktyget har skapat ett fullständigt fungerande Service Fabric-paket enligt nedan:

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
Den genererade ServiceManifest. XML har nu ett avsnitt som beskriver hur Node. js-webbservern ska startas, som du ser i kodfragmentet nedan:

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
I det här exemplet lyssnar Node. js-webbservern på port 3000, så du måste uppdatera slut punkts informationen i filen ServiceManifest. xml som visas nedan.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Paketera MongoDB-programmet
Nu när du har paketerat Node. js-programmet kan du gå vidare och paketera MongoDB. Som nämnts tidigare är de steg som du går igenom nu inte bara för Node. js och MongoDB. De gäller faktiskt för alla program som är avsedda att paketeras tillsammans som ett Service Fabric program.  

För att packa MongoDB vill du se till att paketera Mongod. exe och Mongo. exe. Båda binärfilerna finns i katalogen `bin` i installations katalogen för MongoDB. Katalog strukturen liknar den som visas nedan.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric måste starta MongoDB med ett kommando som liknar det som visas nedan, så du måste använda `/ma`-parametern när du packar MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data bevaras inte om det uppstår ett nodfel om du använder MongoDB-datakatalogen på nodens lokala katalog. Du bör antingen använda varaktig lagring eller implementera en MongoDB replik uppsättning för att förhindra data förlust.  
>
>

I PowerShell eller kommando gränssnittet kör vi packnings verktyget med följande parametrar:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

För att kunna lägga till MongoDB i Service Fabric-programpaketet måste du se till att parametern/Target pekar på samma katalog som redan innehåller applikations manifestet tillsammans med Node. js-programmet. Du måste också kontrol lera att du använder samma ApplicationType-namn.

Låt oss bläddra till katalogen och Undersök vad verktyget har skapat.

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
Som du kan se har verktyget lagt till en ny mapp, MongoDB, till den katalog som innehåller binärfilerna för MongoDB. Om du öppnar `ApplicationManifest.xml`-filen kan du se att paketet nu innehåller både Node. js-programmet och MongoDB. Koden nedan visar innehållet i applikations manifestet.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Det sista steget är att publicera programmet till det lokala Service Fabric-klustret med hjälp av PowerShell-skripten nedan:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

När programmet har publicerats till det lokala klustret kan du komma åt Node. js-programmet på den port som vi har angett i tjänst manifestet för Node. js-programmet, till exempel http:\//localhost: 3000.

I den här självstudien har du sett hur du enkelt kan paketera två befintliga program som en Service Fabric program. Du har också lärt dig hur du distribuerar den till Service Fabric så att den kan dra nytta av några av de Service Fabric funktionerna, till exempel hög tillgänglighet och system integrering med hälso tillstånd.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Lägga till fler körbara gäst program i ett befintligt program med Yeoman på Linux

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg: 
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfguest:AddService` och ange nödvändig information.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att distribuera behållare med [Service Fabric-och behållare-översikt](service-fabric-containers-overview.md)
* [Exempel på paketering och distribution av en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två körbara gäst programC# (och NodeJS) som kommunicerar via namngivnings tjänsten med rest](https://github.com/Azure-Samples/service-fabric-containers)
