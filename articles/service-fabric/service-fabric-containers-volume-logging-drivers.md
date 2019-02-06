---
title: Volym-drivrutinen (förhandsversion) för Service Fabric Azure Files | Microsoft Docs
description: Service Fabric stöder användningen av Azure Files att säkerhetskopiera volymer från din behållare. Detta är för närvarande i förhandsversion.
services: service-fabric
documentationcenter: other
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: twhitney, subramar
ms.openlocfilehash: f2636720f6f1faeffb9a63052efdf009668d806f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752083"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric Azure Files volym drivrutinen (förhandsversion)
Plugin-programmet för Azure Files volym är en [Docker volym plugin-programmet](https://docs.docker.com/engine/extend/plugins_volume/) som ger [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) baserat på volymer för Docker-behållare. Det här plugin-program med Docker volymen kommer som ett Service Fabric-program som kan distribueras till Service Fabric-kluster. Dess syfte är att tillhandahålla Azure Files baserat på volymer för andra program i Service Fabric-behållare som distribueras till klustret.

> [!NOTE]
> Version 6.4.571.9494 av plugin-programmet för Azure Files volym är en förhandsversionen som är tillgängliga i det här dokumentet. Som en förhandsversionen är det **inte** stöds för användning i produktionsmiljöer.
>

## <a name="prerequisites"></a>Förutsättningar
* Windows-versionen av Azure Files volym plugin-programmet fungerar på [Windows Server version 1709](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 version 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) eller senare operativsystem. Linux-versionen av Azure Files volym plugin-programmet fungerar på alla versioner av operativsystem som stöds av Service Fabric.

* Azure Files volym plugin-programmet fungerar bara på Service Fabric version 6.2 och senare.

* Följ instruktionerna i den [dokumentation för Azure Files](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) att skapa en filresurs för Service Fabric-behållarprogrammet ska användas som volym.

* Du behöver [Powershell med Service Fabric-modul](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) eller [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installerad.

* Om du använder Hyper-v-behållare, måste följande kodavsnitt som ska läggas till i området ClusterManifest (lokala kluster) eller fabricSettings i ARM-mall (Azure-kluster) eller ClusterConfig.json (fristående kluster). Du måste volymens namn och port som volymen avlyssnar klustret. 

I ClusterManifest följande behov som ska läggas till i avsnittet Hosting. I det här exemplet volymnamnet är **sfazurefile** och porten den lyssnar till i klustret är **19300**.  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19300" />
</Section>
```

Följande fragment måste läggas till i avsnittet fabricSettings i ARM-mall (för Azure-distributioner) eller ClusterConfig.json (för fristående distributioner). 

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19300"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Distribuera Service Fabric Azure Files-programmet

Service Fabric-program som innehåller volymerna som för dina behållare kan hämtas från följande [länk](https://aka.ms/sfvolume6.4). Programmet kan distribueras till klustret via [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) eller [FabricClient APIs](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. I Kommandotolken, ändra katalogen till rotkatalogen för nedladdade.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Kopiera programpaketet till avbildningsarkivet köra kommandot nedan med lämpligt värde för [ApplicationPackagePath] och [ImageStoreConnectionString]:

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

4. Skapa programmet i kommandot för att skapa programmet nedan måste du komma ihåg det **ListenPort** parametr aplikace. Värdet för parametern program är den port där plugin-programmet för Azure Files volym lyssnar efter förfrågningar från Docker-daemon. Det är viktigt att se till att den port som angetts för programmet inte står i konflikt med någon annan port med klustret eller dina program.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter har inte stöd för mappning av SMB monterar till behållare ([som stöds bara på Windows Server version 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Den här begränsningen förhindrar nätverksmappning för volymen och Azure Files volymdrivrutiner på versioner som är äldre än 1709.
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Distribuera programmet på ett lokalt utvecklingskluster
Standardinstansantalet för tjänsten för Azure Files volym-plugin-programmet är 1, vilket innebär att det finns en instans av tjänsten distribueras till varje nod i klustret. Men när du distribuerar programmet Azure Files volym-plugin-programmet på ett lokalt utvecklingskluster instansantalet service anges som 1. Detta kan göras den **InstanceCount** parametr aplikace. Därför är kommandot för att distribuera programmet Azure Files volym-plugin-programmet på ett lokalt utvecklingskluster:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurera ditt program så att använda volymen
Följande kodfragment visar hur en Azure-filer baserat volym kan anges i applikationsmanifestet av ditt program. Specifika elementet av intresse är den **volym** tagg:

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

Drivrutinsnamn för plugin-programmet för Azure Files volymen är **sfazurefile**. Det här värdet har angetts för den **drivrutinen** attributet för den **volym** element i manifestet.

I den **volym** element i kodfragmentet ovan, Azure Files volym plugin-programmet kräver följande taggar:
- **Källan** – det här är namnet på volymen. Användaren kan välja ett namn för volym.
- **Mål** – den här taggen är den plats där volymen har mappats till i behållaren som körs. Därför får inte mål vara en plats som redan finns i din behållare

Enligt den **DriverOption** element i kodfragmentet ovan, Azure Files volym plugin-programmet har stöd för följande alternativ:
- **Resursnamn** -namnet på filresursen för Azure Files med volymen för behållaren.
- **storageAccountName** – namn på Azure-lagringskontot som innehåller filen Azure Files dela.
- **storageAccountKey** -åtkomstnyckel för Azure storage-kontot som innehåller filresursen Azure Files.
- **storageAccountFQDN** -domännamn som är associerade med lagringskontot. Om storageAccountFQDN inte anges kommer domännamnet skapas med hjälp av standard-suffix(.file.core.windows.net) med storageAccountName.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>Med hjälp av dina egna volym eller loggning drivrutin
Service Fabric kan också användningen av dina egna anpassade [volym](https://docs.docker.com/engine/extend/plugins_volume/) eller [loggning](https://docs.docker.com/engine/admin/logging/overview/) drivrutiner. Om Docker volym/loggning drivrutinen inte är installerad på klustret, kan du installera det manuellt med hjälp av RDP/SSH-protokoll. Du kan utföra installationen med dessa protokoll via en [Start-skript för VM-skalningsuppsättningen](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) eller en [SetupEntryPoint skriptet](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model).

Ett exempel på skript för att installera den [Docker volym-drivrutin för Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) är följande:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

I dina program för att använda volymen eller loggning drivrutin som du har installerat kan du behövt ange lämpliga värden i den **volym** och **LogConfig** element under  **ContainerHostPolicies** i programmanifestet.

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

När du anger en volym som plugin-programmet, skapar Service Fabric automatiskt volymen med hjälp av de angivna parametrarna. Den **källa** tagga för den **volym** element är namnet på volymen och **drivrutinen** taggen anger volym drivrutinen plugin-programmet. Den **mål** taggen är platsen som den **källa** mappas till i behållaren som körs. Därför måste ditt mål för en plats som redan finns i din behållare. Alternativen kan specificeras med hjälp av den **DriverOption** tagga på följande sätt:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Programparametrar stöds för volymer som visas i föregående manifest fragment (leta efter `MyStorageVar` exempelvis använda).

Om en drivrutin för Docker-loggen har angetts måste du distribuera agenter (eller behållare) för att hantera loggarna i klustret. Den **DriverOption** tagg kan användas för att ange alternativ för log-drivrutinen.

## <a name="next-steps"></a>Nästa steg
* Om du vill se exempel på behållare, inklusive volym-drivrutinen finns på den [exempel på behållare för Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Om du vill distribuera behållare till ett Service Fabric-kluster finns i artikeln [distribuera en behållare i Service Fabric](service-fabric-deploy-container.md)
