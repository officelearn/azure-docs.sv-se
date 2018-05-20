---
title: Service Fabric Azure filer volymdrivrutin (förhandsversion) | Microsoft Docs
description: Service Fabric stöder användningen av Azure-filer att säkerhetskopiera volymer från en behållare. Det här är för närvarande under förhandsgranskning.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: 2d98cff1a5869091aa81097bbb34da6e525a2ad5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric Azure Files volymdrivrutin (förhandsgranskning)
Azure Files volym plugin-programmet är en [Docker volym plugin](https://docs.docker.com/engine/extend/plugins_volume/) som ger [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) baserat på volymer för behållare i Docker. Den här volymen plugin-programmet för Docker paketeras som ett Service Fabric-program som kan distribueras till Service Fabric-kluster. Dess syfte är att ge Azure-filer baserat på volymer för andra Service Fabric-behållarprogram som distribueras till klustret.

> [!NOTE]
> Version 6.255.389.9494 av plugin-programmet Azure Files volym är en förhandsversionen som är tillgängliga med det här dokumentet. Som en förhandsversionen är **inte** användas i produktionsmiljöer.
>

## <a name="prerequisites"></a>Förutsättningar
* Windows-versionen av Azure-filer volym plugin-programmet fungerar på [Windows Server version 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 version 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) eller senare operativsystem. Linux-versionen av Azure-filer volym plugin-programmet fungerar på alla versioner av operativsystem som stöds av Service Fabric.

* Följ instruktionerna i den [dokumentation för Azure-filer](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share) att skapa en filresurs för Service Fabric-behållarprogram att använda som volym.

* Du behöver [Powershell med Service Fabric-modulen](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) eller [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) installerad.

## <a name="deploy-the-service-fabric-azure-files-application"></a>Distribuera programmet Azure Service Fabric-filer

Service Fabric-program som innehåller volymerna för behållarna kan hämtas från följande [länk](https://aka.ms/sfvolume). Programmet kan distribueras till klustret via [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) eller [FabricClient APIs](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Med hjälp av kommandoraden, ändra katalogen till rotkatalogen för programpaket hämtas. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Kopiera programpaketet till image store kör kommandot nedan med lämpligt värde för [ApplicationPackagePath] och [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrera programtypen

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Skapa programmet i kommandot för att skapa programmet nedan, notera den **ListenPort** program-parametern. Det här värdet som angetts för parametern för det här programmet är den port där Azure Files volym plugin-programmet lyssnar efter förfrågningar från Docker-daemon. Det är viktigt att se till att den port som angetts för programmet inte står i konflikt med andra port med klustret eller dina program.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter stöder inte mappning SMB monteringar behållare ([som stöds bara på Windows Server version 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Den här begränsningen förhindrar nätverksmappningen för volymen och Azure-filer volym drivrutiner på versioner som är äldre än 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Distribuera programmet på ett kluster för lokal utveckling
Standardinstansantalet för tjänsten för Azure-filer volym plugin-program är -1, vilket innebär att det finns en instans av tjänsten har distribuerats till varje nod i klustret. Men när du distribuerar programmet Azure Files volym plugin-programmet på ett kluster för lokal utveckling instansantalet service anges som 1. Detta kan göras den **InstanceCount** program-parametern. Därför är kommandot när du distribuerar Azure-filer volym plugin-program på en lokal utveckling klustret:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurera ditt program att använda volymen
Följande utdrag visar hur en Azure-filer baserat volym kan anges i programmanifestet för programmet. Specifika elementet intressanta är den **volym** tagg:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
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

Drivrutinsnamn för plugin-programmet Azure Files volymen är **sfazurefile**. Det här värdet anges för den **drivrutinen** attribut för den **volym** element i programmanifestet.

I den **volym** element i fragment ovan Azure Files volym plugin-programmet kräver följande taggar: 
- **Källan** -detta avser källmappen som kan vara en mapp på den virtuella datorn som är värd för behållarna eller en fjärransluten beständiga arkivet
- **Mål** -den här taggen är platsen som den **källa** mappas till i behållaren körs. Därför får inte målet vara en plats som redan finns i din behållaren

Enligt den **DriverOption** element i fragment ovan Azure Files volym plugin-programmet stöder följande alternativ:

- **Resursnamn** -namnet på filresursen Azure-filer som innehåller volymen för behållaren
- **storageAccountName** - namn av Azure storage-konto som innehåller filen Azure-filer
- **storageAccountKey** -åtkomstnyckeln för Azure storage-konto som innehåller filresursen Azure-filer

Samtliga av ovanstående drivrutinsalternativ måste anges. 

## <a name="using-your-own-volume-or-logging-driver"></a>Volymen eller loggning drivrutin
Service Fabric kan också användning av en egen anpassad volym eller loggning drivrutiner. Om Docker volym/loggning drivrutinen inte är installerad på klustret, kan du installera den manuellt med hjälp av RDP/SSH-protokoll. Du kan utföra installationen med dessa protokoll via en [skaluppsättning för virtuell dator uppstart skriptet](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) eller en [SetupEntryPoint skriptet](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Ett exempel på skript för att installera den [Docker volym drivrutinen för Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) är följande:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

I dina program för att använda volymen eller loggning drivrutin som du har installerat, skulle du behöva ange lämpliga värden i den **volym** och **LogConfig** element under  **ContainerHostPolicies** i programmanifestet. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>Nästa steg
* Om du vill visa behållaren sampel, inklusive drivrutinens volymen finns det [prov för Service Fabric-behållare](https://github.com/Azure-Samples/service-fabric-containers)
* Om du vill distribuera behållare till ett Service Fabric-kluster finns i artikeln [distribuera en behållare för Service Fabric](service-fabric-deploy-container.md)


