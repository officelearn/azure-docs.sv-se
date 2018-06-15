---
title: Så här containerize din Azure Service Fabric-mikrotjänster (förhandsgranskning)
description: Azure Service Fabric har lagt till nya funktioner som containerize Service Fabric-mikrotjänster. Den här funktionen är för närvarande en förhandsversion.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 3741e74e70769d186da2757b43ca60bbb1e78a1f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212661"
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Så här containerize Service Fabric tillförlitliga tjänster och Reliable Actors (förhandsgranskning)

Service Fabric stöder containerizing Service Fabric-mikrotjänster (Reliable Services och tillförlitlig baserat aktörstjänster). Mer information finns i [service fabric-behållare](service-fabric-containers-overview.md).

Den här funktionen är i förhandsversionen och den här artikeln innehåller de olika stegen för att få din tjänst som körs i en behållare.  

> [!NOTE]
> Den här funktionen är i förhandsvisning och stöds inte i produktion. Den här funktionen fungerar för närvarande endast för Windows. Om du vill köra behållare körs klustret på Windows Server 2016 med behållare.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Steg för att containerize tillämpningsprogrammet Service Fabric

1. Öppna Service Fabric-program i Visual Studio.

2. Lägg till klass [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) i projektet. Koden i den här klassen är en hjälp för att läsa in Service Fabric runtime-binärfiler i ditt program korrekt vid körning i en behållare.

3. För varje kodpaket som du vill ha containerize initiera inläsaren på posten programmet punkt. Lägg till statisk konstruktor som visas i följande kodavsnitt i filen programmet post punkt.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Skapa och [paketet](service-fabric-package-apps.md#Package-App) projektet. För att bygga och skapa ett paket, högerklicka på programmet projektet i Solution Explorer och välj den **paketet** kommando.

5. För varje kodpaketet måste du containerize, Kör PowerShell-skript [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Användningen är följande:
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  Skriptet skapar en mapp med Docker-artefakter på $dockerPackageOutputDirectoryPath. Ändra den genererade Dockerfile att exponera alla portar, köra installationsskript etc. baserat på dina behov.

6. Därefter måste du [skapa](service-fabric-get-started-containers.md#Build-Containers) och [push](service-fabric-get-started-containers.md#Push-Containers) Docker behållare paketet till databasen.

7. Ändra ApplicationManifest.xml och ServiceManifest.xml för att lägga till din behållaren bild, databasen information, registret autentisering och port-till-värd-mappning. För att ändra manifesten finns [skapar ett program för Azure Service Fabric-behållaren](service-fabric-get-started-containers.md). Koden paketdefinition i service manifest måste ersättas med motsvarande behållaren bild. Se till att ändra EntryPoint till typen ContainerHost.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. Lägga till en port-till-värd-mappning för replikatorn och tjänstslutpunkten. Eftersom båda dessa portar tilldelas vid körning av Service Fabric, nollställs ContainerPort för att använda tilldelad port för mappningen.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Om du vill testa det här programmet måste du distribuera den till ett kluster som kör version 5.7 eller högre. Dessutom måste du redigera och uppdatera inställningarna för klustret om du vill aktivera den här funktionen för förhandsgranskning. Följ stegen i den här [artikel](service-fabric-cluster-fabric-settings.md) och Lägg till inställning visas nästa.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. Nästa [distribuera](service-fabric-deploy-remove-applications.md) redigerade programpaket till det här klustret.

Du bör nu ha en container Service Fabric-program som körs i klustret.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [behållare i Service Fabric](service-fabric-get-started-containers.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
