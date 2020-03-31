---
title: Volymdrivrutin för Azure Files för Service Fabric
description: Service Fabric stöder användning av Azure-filer för säkerhetskopiering av volymer från din behållare.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750040"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Volymdrivrutin för Azure Files för Service Fabric

Azure Files-volymdrivrutinen är ett [Docker-volyminstick som](https://docs.docker.com/engine/extend/plugins_volume/) tillhandahåller [Azure Files-baserade](/azure/storage/files/storage-files-introduction) volymer för Docker-behållare. Den är paketerad som ett Service Fabric-program som kan distribueras till ett Service Fabric-kluster för att tillhandahålla volymer för andra Service Fabric-behållarprogram i klustret.

> [!NOTE]
> Version 6.5.661.9590 av Azure Files volym plugin har släppts för allmän tillgänglighet.
>

## <a name="prerequisites"></a>Krav
* Windows-versionen av volyminsticksprogrammet för Azure Files fungerar endast på [Windows Server version 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 version 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) eller senare operativsystem.

* Linux-versionen av Azure Files volym plugin fungerar på alla operativsystemversioner som stöds av Service Fabric.

* Azure Files volym plugin fungerar bara på Service Fabric version 6.2 och nyare.

* Följ instruktionerna i [Azure Files-dokumentationen](/azure/storage/files/storage-how-to-create-file-share) för att skapa en filresurs för det service fabric-behållarprogram som ska användas som volym.

* Du behöver [Powershell med Service Fabric-modulen](/azure/service-fabric/service-fabric-get-started) eller [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installerat.

* Om du använder Hyper-V-behållare måste följande utdrag läggas till i avsnittet ClusterManifest (lokalt kluster) eller fabricSettings i din Azure Resource Manager-mall (Azure Cluster) eller ClusterConfig.json (fristående kluster).

I ClusterManifest måste följande läggas till i avsnittet Värd. I det här exemplet är volymnamnet **sfazurefile** och porten den lyssnar på i klustret är **19100**. Ersätt dem med rätt värden för klustret.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

I avsnittet fabricSettings i din Azure Resource Manager-mall (för Azure-distributioner) eller ClusterConfig.json (för fristående distributioner) måste följande kodavsnitt läggas till. Återigen, ersätta volymnamn och portvärden med din egen.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Distribuera ett exempelprogram med volymdrivrutinen Service Fabric Azure Files

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Använda Azure Resource Manager via det medföljande Powershell-skriptet (rekommenderas)

Om klustret är baserat i Azure rekommenderar vi att du distribuerar program till den med hjälp av Azure Resource Manager-programresursmodellen för enkel användning och för att hjälpa till att gå mot modellen för att underhålla infrastruktur som kod. Den här metoden eliminerar behovet av att hålla reda på appversionen för Azure Files-volymdrivrutinen. Du kan också underhålla separata Azure Resource Manager-mallar för varje operativsystem som stöds. Skriptet förutsätter att du distribuerar den senaste versionen av Azure Files-programmet och tar parametrar för OS-typ, klusterprenumerations-ID och resursgrupp. Du kan ladda ner skriptet från [nedladdningswebbplatsen För Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Observera att detta automatiskt ställer in ListenPort, som är den port där Azure Files volym plugin lyssnar efter begäranden från Docker-demonen, till 19100. Du kan ändra den genom att lägga till parametern "listenPort". Kontrollera att porten inte står i konflikt med någon annan port som klustret eller dina program använder.
 

Distributionskommando för Azure Resource Manager för Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Distributionskommando för Azure Resource Manager för Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

När du har kört skriptet kan du gå vidare till avsnittet [konfigurera programmet.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Manuell distribution för fristående kluster

Programmet Service Fabric som tillhandahåller volymerna för dina behållare kan hämtas från [nedladdningsplatsen för Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). Programmet kan distribueras till klustret via [PowerShell,](./service-fabric-deploy-remove-applications.md) [CLI](./service-fabric-application-lifecycle-sfctl.md) eller [FabricClient API:er](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Ändra katalogen till rotkatalogen för det hämtade programpaketet med hjälp av kommandoraden.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Kopiera sedan programpaketet till bildarkivet med lämpliga värden för [ApplicationPackagePath] och [ImageStoreConnectionString]:

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

4. Skapa programmet, med stor uppmärksamhet på **parametervärdet för ListenPort-programmet.** Det här värdet är den port där Azure Files volym plugin lyssnar efter begäranden från Docker-demonen. Kontrollera att porten som tillhandahålls till programmet matchar VolumePluginPorts i ClusterManifest och inte står i konflikt med någon annan port som klustret eller dina program använder.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter stöder inte mappning av SMB-fästen till behållare ([Det stöds endast i Windows Server version 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Det här villkoret förhindrar nätverksvolymmappning och Azure Files-volymdrivrutiner i versioner som är äldre än 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Distribuera programmet i ett lokalt utvecklingskluster
Följ steg 1-3 [ovanifrån.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Standardantalet för azure files volyminsticksprogrammet är -1, vilket innebär att det finns en instans av tjänsten som distribueras till varje nod i klustret. När du distribuerar plugin-programmet för Azure Files-volym i ett lokalt utvecklingskluster bör antalet tjänstinstanser anges som 1. Detta kan göras via **application parametern InstanceCount.** Därför är kommandot för att skapa azure files volym plugin-programmet på ett lokalt utvecklingskluster:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurera dina program så att de använder volymen
Följande kodavsnitt visar hur en Azure Files-baserad volym kan anges i programmanifestfilen för ditt program. Det specifika elementet **Volume** av intresse är volymtaggen:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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
                <DriverOption Name="storageAccountFQDN" Value="" />
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

Drivrutinsnamnet för azure files volym plugin är **sfazurefile**. Det här värdet anges för **drivrutinsattributet** för **volymtaggelementet** i programmanifestet.

I **volymtaggen** i kodavsnittet ovan kräver insticksprogrammet för Azure Files-volymen följande attribut:
- **Källa** - Det här är namnet på volymen. Användaren kan välja vilket namn som helst för sin volym.
- **Mål** - Det här attributet är den plats som volymen mappas till i behållaren som körs. Destinationen kan alltså inte vara en plats som redan finns i din behållare

Som visas i **DriverOption-elementen** i kodavsnittet ovan stöder azure files volyminsticksprogrammet följande drivrutinsalternativ:
- **shareName** - Namnet på filresursresursen för Azure Files som tillhandahåller volymen för behållaren.
- **storageAccountName** - Namnet på Azure-lagringskontot som innehåller Filresursresursen för Azure Files.
- **storageAccountKey** - Access-nyckel för Azure-lagringskontot som innehåller Filresursresursen för Azure Files.
- **storageAccountFQDN** - Domännamn som är associerat med lagringskontot. Om storageAccountFQDN inte anges bildas domännamnet med hjälp av standardsuffixet(.file.core.windows.net) med storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Använda din egen volym eller loggningsdrivrutin
Service Fabric tillåter också användning av din egen anpassade [volym](https://docs.docker.com/engine/extend/plugins_volume/) eller [loggning](https://docs.docker.com/engine/admin/logging/overview/) drivrutiner. Om Docker-volym-/loggningsdrivrutinen inte är installerad i klustret kan du installera den manuellt med hjälp av RDP/SSH-protokollen. Du kan utföra installationen med dessa protokoll via ett [startskript för start av virtuell dator](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) eller ett [SetupEntryPoint-skript](/azure/service-fabric/service-fabric-application-model).

Ett exempel på skriptet för att installera [Docker-volymdrivrutinen för Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) är följande:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Om du vill använda den volym eller loggningsdrivrutin som du har installerat i dina program måste du ange lämpliga värden i **elementen Volym** och **LogConfig** under **ContainerHostPolicies** i ditt programmanifest.

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

När du anger ett volympluggin skapar Service Fabric volymen automatiskt med hjälp av de angivna parametrarna. **Volymtaggen** för **volymelementet** är namnet på volymen och **drivrutinstaggen** anger plugin-programmet för volymdrivrutin. **Måltaggen** är den plats som **källan** mappas till i behållaren som körs. Destinationen kan alltså inte vara en plats som redan finns i behållaren. Alternativ kan anges med taggen **DriverOption** enligt följande:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Programparametrar stöds för volymer som visas i föregående `MyStorageVar` manifestutdrag (leta efter exempelvis användning).

Om en Docker-loggdrivrutin har angetts måste du distribuera agenter (eller behållare) för att hantera loggarna i klustret. **DriverOption-taggen** kan användas för att ange alternativ för loggdrivrutinen.

## <a name="next-steps"></a>Nästa steg
* Om du vill se behållarprover, inklusive volymdrivrutinen, besöker du [service fabric-behållarproverna](https://github.com/Azure-Samples/service-fabric-containers)
* Om du vill distribuera behållare till ett Service Fabric-kluster läser du artikeln [Distribuera en behållare på Service Fabric](service-fabric-deploy-container.md)
