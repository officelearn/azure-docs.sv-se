---
title: Azure Service Fabric Docker Compose Preview | Microsoft Docs
description: "Azure Service Fabric accepterar Docker Compose format för att göra det lättare att samordna exsiting behållare med hjälp av Service Fabric. Detta stöd är för närvarande under förhandsgranskning."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 7464611e669165d9ec1f0de7422b20b3f3b8c2b5
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Med hjälp av plugin-program för volymen och loggning drivrutiner i din behållaren

Service Fabric har stöd för att ange [volym plugin-program för Docker](https://docs.docker.com/engine/extend/plugins_volume/) och [Docker loggning drivrutiner](https://docs.docker.com/engine/admin/logging/overview/) för behållartjänsten. 

## <a name="install-volumelogging-driver"></a>Installera drivrutinen för volym-loggning

Om Docker volym/loggning driver inte har installerats på datorn, installera manuellt via RDP/SSH-ing till datorn eller ett VMSS uppstart skript. Till exempel för att installera drivrutinens Docker volym SSH till datorn och köra:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Ange plugin-programmet eller drivrutinen i manifestet
De plugin-program har angetts i applikationsmanifestet som visas i följande manifestet:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

I föregående exempel är den `Source` taggen för den `Volume` refererar till källmappen. Källmappen kan vara en mapp på den virtuella datorn som är värd för behållarna eller en fjärransluten beständiga arkivet. Den `Destination` taggen är platsen som den `Source` mappas till i behållaren körs. 

När du anger ett volym plugin-program, skapar Service Fabric automatiskt volymen med de angivna parametrarna. Den `Source` taggen är namnet på volymen och `Driver` anger volymen drivrutinen plugin-programmet. Alternativen kan specificeras med hjälp av den `DriverOption` tagg som visas i följande utdrag:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Om en drivrutin för Docker-loggen har angetts, är det nödvändigt att distribuera agenter (eller behållare) för att hantera loggarna i klustret.  Den `DriverOption` tagg kan användas för att ange drivrutinen Loggalternativ samt.

Se följande artiklar för att distribuera behållare till ett Service Fabric-kluster:


[Distribuera en behållare för Service Fabric](service-fabric-deploy-container.md)

