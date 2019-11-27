---
title: YAML-referens för container grupp
description: Referens för YAML-filen som stöds av Azure Container Instances att konfigurera en behållar grupp
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 5603f2e0f63c4f83a6d3761feb540abb8b8b7d5c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533494"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-referens: Azure Container Instances

I den här artikeln beskrivs syntax och egenskaper för YAML-filen som stöds av Azure Container Instances att konfigurera en [behållar grupp](container-instances-container-groups.md). Använd en YAML-fil för att ange grupp konfigurationen till kommandot [AZ container Create][az-container-create] i Azure CLI. 

En YAML-fil är ett bekvämt sätt att konfigurera en behållar grupp för åter användning. Det är ett koncist alternativ till att använda en [Resource Manager-mall](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) eller Azure Container instances SDK: er för att skapa eller uppdatera en behållar grupp.

> [!NOTE]
> Den här referensen gäller för YAML-filer för Azure Container Instances REST API version `2018-10-01`.

## <a name="schema"></a>Schema 

Schemat för YAML-filen följer, inklusive kommentarer för att markera nyckel egenskaper. En beskrivning av egenskaperna i det här schemat finns i avsnittet [egenskaps värden](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Egenskapsvärden

I följande tabeller beskrivs de värden som du måste ange i schemat.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. ContainerInstance/containerGroups-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | Namnet på behållar gruppen. |
|  apiVersion | Enum | Ja | 2018-10-01 |
|  location | string | Nej | Resursens plats. |
|  taggar | object | Nej | Resurs taggarna. |
|  identitet | object | Nej | Identiteten för behållar gruppen, om den är konfigurerad. - [ContainerGroupIdentity-objekt](#ContainerGroupIdentity) |
|  Egenskaper | object | Ja | [ContainerGroupProperties-objekt](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  type | Enum | Nej | Typ av identitet som används för behållar gruppen. Typen "SystemAssigned, UserAssigned" innehåller både en implicit skapad identitet och en uppsättning användare tilldelade identiteter. Typen ' none ' tar bort alla identiteter från behållar gruppen. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, ingen |
|  userAssignedIdentities | object | Nej | Listan över användar identiteter som är kopplade till behållar gruppen. Nyckel referenserna för användar identitets ord listan kommer att Azure Resource Manager resurs-ID: n i formatet: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  containers | matris | Ja | Behållarna i behållar gruppen. - [Container-objekt](#Container) |
|  imageRegistryCredentials | matris | Nej | De avbildnings register uppgifter som behållar gruppen skapas från. - [ImageRegistryCredential-objekt](#ImageRegistryCredential) |
|  restartPolicy | Enum | Nej | Starta om principen för alla behållare i behållar gruppen. - `Always` Starta alltid om `OnFailure` starta om vid ett haveri-`Never` aldrig omstart. -Always, OnFailure, Never |
|  Adresser | object | Nej | Typ av IP-adress för behållar gruppen. - [IpAddress-objekt](#IpAddress) |
|  osType | Enum | Ja | Den typ av operativ system som krävs av behållarna i behållar gruppen. – Windows eller Linux |
|  volumes | matris | Nej | Listan över volymer som kan monteras av behållare i den här behållar gruppen. - [volym objekt](#Volume) |
|  diagnostik | object | Nej | Diagnostikinformation för en behållar grupp. - [ContainerGroupDiagnostics-objekt](#ContainerGroupDiagnostics) |
|  networkProfile | object | Nej | Nätverks profil informationen för en behållar grupp. - [ContainerGroupNetworkProfile-objekt](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Nej | DNS-konfigurations information för en behållar grupp. - [DnsConfiguration-objekt](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Container objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | Namnet på behållar instansen som användaren anger. |
|  Egenskaper | object | Ja | Egenskaperna för behållar instansen. - [ContainerProperties-objekt](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  server | string | Ja | Docker-avbildningens register server utan protokoll som "http" och "https". |
|  username | string | Ja | Användar namnet för det privata registret. |
|  password | string | Nej | Lösen ordet för det privata registret. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  ports | matris | Ja | Listan över portar som exponeras i behållar gruppen. - [port-objekt](#Port) |
|  type | Enum | Ja | Anger om IP-adressen exponeras för det offentliga Internet eller privata virtuella nätverket. – Offentligt eller privat |
|  sökning | string | Nej | IP-adressen som exponeras för det offentliga Internet. |
|  dnsNameLabel | string | Nej | DNS-namnets etikett för IP-adressen. |


<a id="Volume" />

### <a name="volume-object"></a>Volym objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | Namnet på volymen. |
|  azureFile | object | Nej | Azure-filvolymen. - [AzureFileVolume-objekt](#AzureFileVolume) |
|  emptyDir | object | Nej | Den tomma katalog volymen. |
|  secret | object | Nej | Den hemliga volymen. |
|  gitRepo | object | Nej | Git lagrings platsen-volymen. - [GitRepoVolume-objekt](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nej | Information om behållar grupps logg analys. - [LogAnalytics-objekt](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  id | string | Ja | Identifieraren för en nätverks profil. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  Namnservrar | matris | Ja | DNS-servrarna för behållar gruppen. – sträng |
|  searchDomains | string | Nej | DNS-sökdomänerna för Sök efter värdnamn i behållar gruppen. |
|  options | string | Nej | DNS-alternativen för behållar gruppen. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  image | string | Ja | Namnet på den avbildning som används för att skapa behållar instansen. |
|  Kommandot | matris | Nej | De kommandon som ska köras i behållar instansen i exec form. – sträng |
|  ports | matris | Nej | Exponerade portar på behållar instansen. - [ContainerPort-objekt](#ContainerPort) |
|  environmentVariables | matris | Nej | Miljövariablerna som ska anges i behållar instansen. - [EnvironmentVariable-objekt](#EnvironmentVariable) |
|  resurser | object | Ja | Resurs kraven för behållar instansen. - [ResourceRequirements-objekt](#ResourceRequirements) |
|  volumeMounts | matris | Nej | Volymen monteras som är tillgänglig för behållar instansen. - [VolumeMount-objekt](#VolumeMount) |
|  livenessProbe | object | Nej | Direktmigrering. - [ContainerProbe-objekt](#ContainerProbe) |
|  readinessProbe | object | Nej | Beredskaps avsökningen. - [ContainerProbe-objekt](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Nej | Det protokoll som är associerat med porten. -TCP eller UDP |
|  port | heltal | Ja | Port numret. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  Resurs | string | Ja | Namnet på Azure-filresursen som ska monteras som en volym. |
|  ReadOnly | Booleskt värde | Nej | Flaggan som anger om Azure-fildelningen som är monterad som en volym är skrivskyddad. |
|  storageAccountName | string | Ja | Namnet på det lagrings konto som innehåller Azure-filresursen. |
|  storageAccountKey | string | Nej | Lagrings kontots åtkomst nyckel som används för åtkomst till Azure-filresursen. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  katalogen | string | Nej | Mål katalog namn. Får inte innehålla eller börja med "..".  Om "." anges blir volym katalogen git-lagringsplatsen.  Annars kommer volymen att innehålla git-lagringsplatsen i under katalogen med det angivna namnet. |
|  repository | string | Ja | URL för databas |
|  revision | string | Nej | Genomför hash för den angivna revisionen. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Ja | Arbetsyte-ID för Log Analytics |
|  workspaceKey | string | Ja | Arbets ytans nyckel för Log Analytics |
|  logType | Enum | Nej | Den logg typ som ska användas. -ContainerInsights eller ContainerInstanceLogs |
|  metadata | object | Nej | Metadata för Log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Nej | Det protokoll som är associerat med porten. -TCP eller UDP |
|  port | heltal | Ja | Port numret som visas i behållar gruppen. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | Namnet på miljö variabeln. |
|  värde | string | Nej | Värdet för miljö variabeln. |
|  secureValue | string | Nej | Värdet för variabeln säker miljö. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  Begäranden | object | Ja | Resurs begär Anden för den här behållar instansen. - [ResourceRequests-objekt](#ResourceRequests) |
|  sten | object | Nej | Resurs gränserna för den här behållar instansen. - [ResourceLimits-objekt](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | Namnet på volym monteringen. |
|  mountPath | string | Ja | Sökvägen i behållaren där volymen ska monteras. Får inte innehålla kolon (:). |
|  ReadOnly | Booleskt värde | Nej | Flaggan indikerar om volym monteringen är skrivskyddad. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  ledn | object | Nej | Körnings kommandot till PROBE- [ContainerExec-objektet](#ContainerExec) |
|  httpGet | object | Nej | Http get-inställningarna till PROBE- [ContainerHttpGet-objektet](#ContainerHttpGet) |
|  initialDelaySeconds | heltal | Nej | Den första fördröjningen i sekunder. |
|  periodSeconds | heltal | Nej | Perioden i sekunder. |
|  failureThreshold | heltal | Nej | Tröskelvärdet för felen. |
|  successThreshold | heltal | Nej | Tröskelvärdet för framgång. |
|  timeoutSeconds | heltal | Nej | Tids gräns i sekunder. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Ja | Minnes förfrågan i GB av den här behållar instansen. |
|  processor | nummer | Ja | PROCESSOR förfrågan för den här behållar instansen. |
|  gpu | object | Nej | GPU-begäran för den här behållar instansen. - [GpuResource-objekt](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Nej | Minnes gränsen i GB för den här behållar instansen. |
|  processor | nummer | Nej | PROCESSOR gränsen för den här behållar instansen. |
|  gpu | object | Nej | GPU-gränsen för den här behållar instansen. - [GpuResource-objekt](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  Kommandot | matris | Nej | De kommandon som ska köras i behållaren. – sträng |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  path | string | Nej | Sökvägen till avsökningen. |
|  port | heltal | Ja | Port numret som ska avsökas. |
|  schema | Enum | Nej | Schemat. -http eller https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource-objekt

|  Name | Typ | Krävs | Value |
|  ---- | ---- | ---- | ---- |
|  count | heltal | Ja | Antalet GPU-resurser. |
|  sku | Enum | Ja | SKU: n för GPU-resursen. -K80, P100, V100 |


## <a name="next-steps"></a>Nästa steg

Se självstudien [distribuera en grupp med flera behållare med hjälp av en yaml-fil](container-instances-multi-container-yaml.md).

Se exempel på hur du använder en YAML-fil för att distribuera behållar grupper i ett [virtuellt nätverk](container-instances-vnet.md) eller som [monterar en extern volym](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

