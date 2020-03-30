---
title: Behålla dina Azure Service Fabric-tjänster i Windows
description: Lär dig hur du gör det för att behålla dina reliable services och reliable actors-tjänster för service på Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 9fe5980c13f655f8f30cc42771971a5015460420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466186"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Containeranpassa Service Fabric Reliable Services och Reliable Actors i Windows

Service Fabric stöder mikrotjänster för serviceinfrastruktur (Reliable Services och Reliable Actor-baserade tjänster). Mer information finns i [servicebegyr behållare](service-fabric-containers-overview.md).

Det här dokumentet innehåller vägledning för att få tjänsten att köras i en Windows-behållare.

> [!NOTE]
> För närvarande fungerar den här funktionen bara för Windows. Om du vill köra behållare måste klustret köras på Windows Server 2016 med Behållare.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Steg för att behålla ditt Service Fabric-program

1. Öppna programmet Service Fabric i Visual Studio.

2. Lägg till [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) i projektet. Koden i den här klassen är en hjälpare att korrekt läsa in Service Fabric-körningsbinärer i ditt program när du kör inuti en behållare.

3. För varje kodpaket som du vill behålla initierar du inläsaren vid programmets startpunkt. Lägg till den statiska konstruktorn som visas i följande kodavsnitt i programstartpunkten.

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

4. Bygg och [paketera](service-fabric-package-apps.md#Package-App) ditt projekt. Om du vill skapa och skapa ett paket högerklickar du på programprojektet i Solution Explorer och väljer kommandot **Paket.**

5. För varje kodpaket som du behöver för att behålla kör du PowerShell-skriptet [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Användningen är följande:

    Fullständigt .NET
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
      Skriptet skapar en mapp med Docker-artefakter på $dockerPackageOutputDirectoryPath. Ändra den genererade Dockerfile till `expose` alla portar, kör installationsskript och så vidare. baserat på dina behov.

6. Därefter måste du [bygga](service-fabric-get-started-containers.md#Build-Containers) och [skicka](service-fabric-get-started-containers.md#Push-Containers) docker-behållarpaketet till din databas.

7. Ändra ApplicationManifest.xml och ServiceManifest.xml för att lägga till behållaravbildning, databasinformation, registerautentisering och mappning från port till värd. Information om hur du ändrar manifesten finns i [Skapa ett Azure Service Fabric-behållarprogram](service-fabric-get-started-containers.md). Kodpaketdefinitionen i tjänstmanifestet måste ersättas med motsvarande behållaravbildning. Kontrollera att Du ändrar EntryPoint till en ContainerHost-typ.

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

8. Lägg till mappningen från port till värd för replikator- och tjänstslutpunkten. Eftersom båda dessa portar tilldelas vid körning av Service Fabric är ContainerPort inställd på noll för att använda den tilldelade porten för mappning.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Om du konfigurerar isoleringsläge för behållare finns i [Konfigurera isoleringsläge]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). Windows stöder två isoleringslägen för containrar: process och Hyper-V. Följande utdrag visar hur isoleringsläget anges i programmanifestfilen.

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

> [!NOTE] 
> Som standard har Service Fabric-program åtkomst till Service Fabric-körningen, i form av en slutpunkt som accepterar programspecifika begäranden. Överväg att inaktivera den här åtkomsten när programmet är värd för kod som inte är betrodd. Mer information finns [i metodtips för säkerhet i Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Om du vill inaktivera åtkomsten till service fabric-körningen lägger du till följande inställning i avsnittet Principer i programmanifestet som motsvarar det importerade tjänstmanifestet enligt följande:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Om du vill testa det här programmet måste du distribuera det till ett kluster som kör version 5.7 eller senare. För körningsversioner 6.1 eller lägre måste du redigera och uppdatera klusterinställningarna för att aktivera den här förhandsgranskningsfunktionen. Följ stegen i den här [artikeln](service-fabric-cluster-fabric-settings.md) för att lägga till den inställning som visas nästa.
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

11. Distribuera [sedan](service-fabric-deploy-remove-applications.md) det redigerade programpaketet till det här klustret.

Du bör nu ha ett containeriserat Service Fabric-program som kör klustret.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-get-started-containers.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
