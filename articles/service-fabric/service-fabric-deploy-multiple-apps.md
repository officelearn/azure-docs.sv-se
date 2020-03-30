---
title: Distribuera ett Node.js-program som använder MongoDB
description: Genomgång om hur du paketerar flera körbara gästar att distribuera till ett Azure Service Fabric-kluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614476"
---
# <a name="deploy-multiple-guest-executables"></a>Distribuera flera körbara gäster
Den här artikeln visar hur du paketerar och distribuerar flera gäst körbara filer till Azure Service Fabric. För att skapa och distribuera ett enda Service Fabric-paket läs om hur [du distribuerar en gäst körbar till Service Fabric](service-fabric-deploy-existing-app.md).

Även om den här genomgången visar hur du distribuerar ett program med en Node.js-klientdel som använder MongoDB som datalager, kan du använda stegen för alla program som har beroenden för ett annat program.   

Du kan använda Visual Studio för att skapa programpaketet som innehåller flera körbara gästar. Se [Använda Visual Studio för att paketera ett befintligt program](service-fabric-deploy-existing-app.md). När du har lagt till den första körbara gästen högerklickar du på programprojektet och väljer **tjänsten Add->New Service Fabric** för att lägga till det andra körbara gästprojektet i lösningen. Om du väljer att länka källan i Visual Studio-projektet, bygga Visual Studio-lösningen, kontrollerar du att programpaketet är uppdaterat med ändringar i källan. 

## <a name="samples"></a>Exempel
* [Prov för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två körbara gästar (C# och nodejs) som kommunicerar via namngivningstjänsten med REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Paketera det körbara programmet för flera gästarvbara
Alternativt kan du manuellt paketera gästens körbara gäst. Mer information finns i [Paketera manuellt och distribuera en befintlig körbar](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Paketera nod.js-programmet
Den här artikeln förutsätter att Node.js inte är installerat på noderna i service fabric-klustret. Därför måste du lägga till Node.exe i rotkatalogen för nodprogrammet innan du paketerar. Katalogstrukturen för Node.js-programmet (med Hjälp av Express web framework och Jade mallmotor) bör se ut ungefär som den nedan:

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

Som ett nästa steg skapar du ett programpaket för nod.js-programmet. Koden nedan skapar ett Programpaket för Service Fabric som innehåller nod.js-programmet.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Nedan följer en beskrivning av de parametrar som används:

* **/source** pekar på katalogen för programmet som ska paketeras.
* **/target** definierar katalogen där paketet ska skapas. Den här katalogen måste skilja sig från källkatalogen.
* **/appname** definierar programnamnet för det befintliga programmet. Det är viktigt att förstå att detta översätts till tjänstnamnet i manifestet och inte till programnamnet Service Fabric.
* **/exe** definierar den körbara fil som Service Fabric `node.exe`ska starta i det här fallet .
* **/ma** definierar argumentet som används för att starta den körbara filen. Eftersom Node.js inte är installerat måste Service Fabric starta webbservern `node.exe bin/www`Node.js genom att köra .  `/ma:'bin/www'`talar om för `bin/www` förpackningsverktyget att använda som argument för node.exe.
* **/AppType** definierar programtypen Service Fabric./AppType defines the Service Fabric application type name.

Om du bläddrar till katalogen som angavs i parametern /target kan du se att verktyget har skapat ett fullt fungerande Service Fabric-paket enligt nedan:

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
Den genererade TjänstenManifest.xml har nu ett avsnitt som beskriver hur nod.js-webbservern ska startas, vilket visas i kodavsnittet nedan:

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
I det här exemplet lyssnar webbservern Node.js på port 3000, så du måste uppdatera slutpunktsinformationen i filen ServiceManifest.xml enligt nedan.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Paketera MongoDB-applikationen
Nu när du har paketerat Node.js ansökan, kan du gå vidare och paketera MongoDB. Som tidigare nämnts är de steg som du går igenom nu inte specifika för Node.js och MongoDB. Faktum är att de gäller för alla program som är avsedda att paketeras tillsammans som en Service Fabric-applikation.  

Om du vill paketera MongoDB vill du se till att du paketerar Mongod.exe och Mongo.exe. Båda binärfilerna finns `bin` i katalogen för din MongoDB-installationskatalog. Katalogstrukturen liknar den nedan.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric måste starta MongoDB med ett kommando som liknar det `/ma` nedan, så du måste använda parametern när du paketerar MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data bevaras inte vid ett nodfel om du placerar Datakatalogen MongoDB på den lokala katalogen för noden. Du bör antingen använda varaktig lagring eller implementera en MongoDB-replikuppsättning för att förhindra dataförlust.  
>
>

I PowerShell eller kommandoskalet kör vi förpackningsverktyget med följande parametrar:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

För att lägga till MongoDB i ditt Programpaket för Service Fabric måste du se till att parametern /target pekar på samma katalog som redan innehåller programmanifestet tillsammans med nod.js-programmet. Du måste också se till att du använder samma ApplicationType-namn.

Låt oss bläddra till katalogen och undersöka vad verktyget har skapat.

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
Som du kan se lade verktyget till en ny mapp, MongoDB, i katalogen som innehåller MongoDB-binärfilerna. Om du `ApplicationManifest.xml` öppnar filen kan du se att paketet nu innehåller både nod.js-programmet och MongoDB. Koden nedan visar innehållet i programmanifestet.

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

När programmet har publicerats till det lokala klustret kan du komma åt nod.js-programmet på den port som vi har angett\/i tjänstmanifestet för Node.js-programmet - till exempel http: /localhost:3000.

I den här självstudien har du sett hur du enkelt paketerar två befintliga program som ett Service Fabric-program. Du har också lärt dig hur du distribuerar den till Service Fabric så att den kan dra nytta av några av Service Fabric-funktionerna, till exempel hög tillgänglighet och integrering av hälsosystem.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Lägga till fler gästblanksvärda filer i ett befintligt program med Yeoman på Linux

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg: 
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfguest:AddService` och ge nödvändiga detaljer.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att distribuera behållare med [översikt över Service Fabric och behållare](service-fabric-containers-overview.md)
* [Prov för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två körbara gästar (C# och nodejs) som kommunicerar via namngivningstjänsten med REST](https://github.com/Azure-Samples/service-fabric-containers)
