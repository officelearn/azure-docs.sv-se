---
title: Distribuera en Node.js-program som använder MongoDB till Azure Service Fabric | Microsoft Docs
description: Genomgång om hur du paketerar flera körbara gäster för att distribuera till ett Azure Service Fabric-kluster
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
ms.openlocfilehash: ed8159f38e2763cb897212ae4f51fecb7343a683
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315599"
---
# <a name="deploy-multiple-guest-executables"></a>Distribuera flera körbara gäster
Den här artikeln visar hur du paketera och distribuera flera körbara gäster till Azure Service Fabric. Läs om hur för att skapa och distribuera ett enda Service Fabric-paket till [distribuera en körbar Gäst till Service Fabric](service-fabric-deploy-existing-app.md).

Den här genomgången visar hur du distribuerar ett program med en Node.js-klientdel som använder MongoDB som datalager, men du kan använda stegen för att alla program som har beroenden på ett annat program.   

Du kan använda Visual Studio för att skapa det programpaket som innehåller flera körbara gäster. Se [med hjälp av Visual Studio för att paketera ett befintligt program](service-fabric-deploy-existing-app.md). När du har lagt till den första körbar Gäst, högerklicka på programprojektet och väljer den **Lägg till ny Service Fabric-tjänst ->** att lägga till det andra körbar gäst-projektet i lösningen. Obs! Om du vill länka källa i Visual Studio-projektet gör att skapa Visual Studio-lösning att programpaketet är uppdaterade med ändringar i källan. 

## <a name="samples"></a>Exempel
* [Exempel för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via tjänsten namngivning med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Manuellt paketera flera körbart gästprogram
Du kan också manuellt paketera den körbara gästfiler. Manuell förpackningen kan den här artikeln använder verktyget Service Fabric-paketering, som finns på [ http://aka.ms/servicefabricpacktool ](https://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Paketera Node.js-program
Den här artikeln förutsätter att Node.js inte installeras på noderna i Service Fabric-klustret. Det betyder att du behöver lägga till Node.exe till rotkatalogen för nodprogrammet innan paketering. Katalogstrukturen på Node.js-program (med hjälp av Express-webbramverk och Jade motorn) bör likna följande:

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

I nästa steg, kan du skapa ett programpaket för Node.js-program. Koden nedan skapar ett Service Fabric-programpaket som innehåller Node.js-program.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Nedan följer en beskrivning av de parametrar som används:

* **/ source** pekar på katalogen för det program som ska paketeras.
* **/ target** definierar den katalog där paketet ska skapas. Den här katalogen måste skilja sig från källkatalogen.
* **/ AppName** definierar namnet på programmet för det befintliga programmet. Det är viktigt att förstå detta innebär att namnet på tjänsten i manifestet och inte namnet på Service Fabric-programmet.
* **/exe** definierar den körbara filen som Service Fabric ska starta i det här fallet `node.exe`.
* **/Ma** definierar argumentet som används för att starta den körbara filen. Node.js är inte installerad, Service Fabric behöver starta webbservern Node.js genom att köra `node.exe bin/www`.  `/ma:'bin/www'` talar om verktyget paketering för att använda `bin/www` som argument för node.exe.
* **/ Programtyp** definierar namnet på Service Fabric-program.

Om du bläddrar till den katalog som har angetts i parametern/Target, kan du se att verktyget har skapat ett fullt fungerande Service Fabric-paket som visas nedan:

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
Genererade ServiceManifest.xml har nu ett avsnitt som beskriver hur Node.js webbservern ska startas, som du ser i kodfragmentet nedan:

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
I det här exemplet lyssnar Node.js servern på port 3000, så måste du uppdatera information om slutpunkten i filen servicemanifest.xml enligt nedan.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Paketera program för MongoDB
Nu när du har förpackat Node.js-program, kan du gå vidare och paketera MongoDB. Som tidigare nämnts, är de steg som du går igenom nu inte specifika för Node.js- och MongoDB. I själva verket gäller villkoren för alla program som är avsedda att vara packade tillsammans som ett Service Fabric-program.  

Om du vill paketera MongoDB, som du vill kontrollera att du paketera Mongod.exe och Mongo.exe. Båda binärfiler finns i den `bin` för din MongoDB-installationskatalogen. Katalogstrukturen ser ut ungefär som den nedan.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric behöver för att börja MongoDB med ett kommando liknande den nedan, så måste du använda den `/ma` parameter när paketering MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data som bevaras inte när det gäller ett nodfel om du placerar datakatalog MongoDB på den lokala katalogen för noden. Du bör använda beständig lagring, eller så kan du implementera en MongoDB-replikuppsättning för att förhindra dataförlust.  
>
>

I PowerShell eller Kommandotolken kör vi paketering verktyget med följande parametrar:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

För att lägga till MongoDB till ditt Service Fabric-programpaket måste du se till att den/Target-parameter pekar på samma katalog som innehåller programmet redan manifest tillsammans med Node.js-program. Du måste också se till att du använder samma ApplicationType namn.

Nu ska vi Bläddra till mappen och granska vad verktyget har skapats.

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
Som du kan se verktyget för att lägga till en ny mapp, MongoDB, till den katalog som innehåller MongoDB-binärfiler. Om du öppnar den `ApplicationManifest.xml` fil, kan du se att paketet innehåller nu både MongoDB och Node.js-program. Koden nedan visar innehållet i programmanifestet.

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
Det sista steget är att publicera programmet till det lokala Service Fabric-klustret med hjälp av PowerShell-skript nedan:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

När programmet har publicerats till det lokala klustret kan du komma åt Node.js-program på den port som vi har angett i tjänstmanifestet för Node.js-program – till exempel http:\//localhost:3000.

I den här självstudien har du sett hur du enkelt paketera två befintliga program som en Service Fabric-program. Du har också lärt dig hur du distribuerar den till Service Fabric, så att den kan dra nytta av några av Service Fabric-funktioner, till exempel hög tillgänglighet och systemintegrering för hälsotillstånd.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Att lägga till fler gästfiler till ett befintligt program med hjälp av Yeoman på Linux

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg: 
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfguest:AddService` och ange informationen som krävs.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du distribuerar behållare med [översikt över Service Fabric och behållare](service-fabric-containers-overview.md)
* [Exempel för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via tjänsten namngivning med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)
