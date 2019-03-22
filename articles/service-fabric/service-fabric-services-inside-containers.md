---
title: Behållaranpassa dina Azure Service Fabric-tjänster på Windows
description: Lär dig hur du behållaranpassa dina Service Fabric Reliable Services och Reliable Actors-tjänster på Windows.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: aljo, anmola
ms.openlocfilehash: 147607bbea65199ff97459711ad6301a4ae93aa4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079834"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Behållaranpassa dina Service Fabric Reliable Services och Reliable Actors i Windows

Service Fabric har stöd för Service Fabric användning mikrotjänster (Reliable Services och Reliable Actor baserade tjänster). Mer information finns i [service fabric-behållare](service-fabric-containers-overview.md).

Det här dokumentet innehåller riktlinjer för att få din tjänst som körs i en Windows-behållare.

> [!NOTE]
> Den här funktionen fungerar för närvarande endast för Windows. För att köra behållare, måste klustret köras på Windows Server 2016 med behållare.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Steg för att behållaranpassa dina Service Fabric-program

1. Öppna Service Fabric-programmet i Visual Studio.

2. Lägg till klass [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) i projektet. Koden i den här klassen är en hjälp för att läsa in Service Fabric runtime-binärfiler i programmet korrekt när du kör i en behållare.

3. För varje kodpaketet som du skulle vilja behållaranpassa, initiera inläsaren på program-posten pekar du. Lägg till statisk konstruktor visas i följande kodfragment i filen programmet post punkt.

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

4. Skapa och [paketet](service-fabric-package-apps.md#Package-App) ditt projekt. För att bygga och skapa ett paket, högerklicka på programprojektet i Solution Explorer och välj den **paketet** kommando.

5. För varje kodpaketet måste du behållaranpassa, Kör PowerShell-skript [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Användningen är följande:

    Full .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Skriptet skapar en mapp med Docker-artefakter på $dockerPackageOutputDirectoryPath. Ändra den genererade Dockerfile till `expose` alla portar, köra installationsskript och så vidare. utifrån dina behov.

6. Sedan måste du [skapa](service-fabric-get-started-containers.md#Build-Containers) och [push](service-fabric-get-started-containers.md#Push-Containers) ditt Docker container-paket till din databas.

7. Ändra ApplicationManifest.xml och ServiceManifest.xml för att lägga till din behållaravbildning, lagringsinformationen, registerautentisering och port till värd-mappning. För att ändra manifesten Se [skapa en Azure Service Fabric-behållarapp](service-fabric-get-started-containers.md). Paketdefinition kod i tjänstmanifestet måste ersättas med motsvarande behållaravbildning. Se till att ändra EntryPoint till en ContainerHost.

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

8. Lägga till en port till värd-mappning för replikatorn och tjänstslutpunkten. Eftersom båda portarna tilldelas vid körning av Service Fabric, nollställs ContainerPort för att använda tilldelad port för mappning.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Konfigurera isoleringsläge för behållare kan se [konfigurera isoleringsläge]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). Windows stöder två isoleringslägen för containrar: process och Hyper-V. Följande kodfragment visar hur isoleringsläget har angetts i applikationsmanifestfilen.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

10. Om du vill testa det här programmet, måste du distribuera den till ett kluster som kör version 5.7 eller högre. För körningsversioner 6.1 eller lägre måste du redigera och uppdatera inställningarna för klustret om du vill aktivera den här funktionen för förhandsgranskning. Följ stegen i den här [artikeln](service-fabric-cluster-fabric-settings.md) och Lägg till inställning visas nästa.
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

11. Nästa [distribuera](service-fabric-deploy-remove-applications.md) redigerade programpaketet i det här klustret.

Du bör nu ha ett container Service Fabric-program som körs ditt kluster.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-get-started-containers.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
