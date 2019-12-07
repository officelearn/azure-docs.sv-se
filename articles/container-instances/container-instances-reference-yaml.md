---
title: YAML-referens för container grupp
description: Referens för YAML-filen som stöds av Azure Container Instances att konfigurera en behållar grupp
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896574"
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
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
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

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på behållar gruppen. |
|  apiVersion | Enum | Ja | 2018-10-01 |
|  location | sträng | Nej | Resursens plats. |
|  tags | objekt | Nej | Resurs taggarna. |
|  identitet | objekt | Nej | Identiteten för behållar gruppen, om den är konfigurerad. - [ContainerGroupIdentity-objekt](#ContainerGroupIdentity) |
|  properties | objekt | Ja | [ContainerGroupProperties-objekt](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  typ | Enum | Nej | Typ av identitet som används för behållar gruppen. Typen "SystemAssigned, UserAssigned" innehåller både en implicit skapad identitet och en uppsättning användare tilldelade identiteter. Typen ' none ' tar bort alla identiteter från behållar gruppen. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, ingen |
|  userAssignedIdentities | objekt | Nej | Listan över användar identiteter som är kopplade till behållar gruppen. Nyckel referenserna för användar identitets ord listan kommer att Azure Resource Manager resurs-ID: n i formatet: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  containers | matris | Ja | Behållarna i behållar gruppen. - [Container-objekt](#Container) |
|  imageRegistryCredentials | matris | Nej | De avbildnings register uppgifter som behållar gruppen skapas från. - [ImageRegistryCredential-objekt](#ImageRegistryCredential) |
|  restartPolicy | Enum | Nej | Starta om principen för alla behållare i behållar gruppen. - `Always` Starta alltid om `OnFailure` starta om vid ett haveri-`Never` aldrig omstart. -Always, OnFailure, Never |
|  Adresser | objekt | Nej | Typ av IP-adress för behållar gruppen. - [IpAddress-objekt](#IpAddress) |
|  osType | Enum | Ja | Den typ av operativ system som krävs av behållarna i behållar gruppen. – Windows eller Linux |
|  volumes | matris | Nej | Listan över volymer som kan monteras av behållare i den här behållar gruppen. - [volym objekt](#Volume) |
|  diagnostik | objekt | Nej | Diagnostikinformation för en behållar grupp. - [ContainerGroupDiagnostics-objekt](#ContainerGroupDiagnostics) |
|  networkProfile | objekt | Nej | Nätverks profil informationen för en behållar grupp. - [ContainerGroupNetworkProfile-objekt](#ContainerGroupNetworkProfile) |
|  dnsConfig | objekt | Nej | DNS-konfigurations information för en behållar grupp. - [DnsConfiguration-objekt](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Container objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på behållar instansen som användaren anger. |
|  properties | objekt | Ja | Egenskaperna för behållar instansen. - [ContainerProperties-objekt](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  server | sträng | Ja | Docker-avbildningens register server utan protokoll som "http" och "https". |
|  användarnamn | sträng | Ja | Användar namnet för det privata registret. |
|  lösenord | sträng | Nej | Lösen ordet för det privata registret. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  ports | matris | Ja | Listan över portar som exponeras i behållar gruppen. - [port-objekt](#Port) |
|  typ | Enum | Ja | Anger om IP-adressen exponeras för det offentliga Internet eller privata virtuella nätverket. – Offentligt eller privat |
|  sökning | sträng | Nej | IP-adressen som exponeras för det offentliga Internet. |
|  dnsNameLabel | sträng | Nej | DNS-namnets etikett för IP-adressen. |


<a id="Volume" />

### <a name="volume-object"></a>Volym objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på volymen. |
|  azureFile | objekt | Nej | Azure-filvolymen. - [AzureFileVolume-objekt](#AzureFileVolume) |
|  emptyDir | objekt | Nej | Den tomma katalog volymen. |
|  hemlighet | objekt | Nej | Den hemliga volymen. |
|  gitRepo | objekt | Nej | Git lagrings platsen-volymen. - [GitRepoVolume-objekt](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | Nej | Information om behållar grupps logg analys. - [LogAnalytics-objekt](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  id | sträng | Ja | Identifieraren för en nätverks profil. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Namnservrar | matris | Ja | DNS-servrarna för behållar gruppen. – sträng |
|  searchDomains | sträng | Nej | DNS-sökdomänerna för Sök efter värdnamn i behållar gruppen. |
|  options | sträng | Nej | DNS-alternativen för behållar gruppen. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  mallar | sträng | Ja | Namnet på den avbildning som används för att skapa behållar instansen. |
|  command | matris | Nej | De kommandon som ska köras i behållar instansen i exec form. – sträng |
|  ports | matris | Nej | Exponerade portar på behållar instansen. - [ContainerPort-objekt](#ContainerPort) |
|  environmentVariables | matris | Nej | Miljövariablerna som ska anges i behållar instansen. - [EnvironmentVariable-objekt](#EnvironmentVariable) |
|  resurser | objekt | Ja | Resurs kraven för behållar instansen. - [ResourceRequirements-objekt](#ResourceRequirements) |
|  volumeMounts | matris | Nej | Volymen monteras som är tillgänglig för behållar instansen. - [VolumeMount-objekt](#VolumeMount) |
|  livenessProbe | objekt | Nej | Direktmigrering. - [ContainerProbe-objekt](#ContainerProbe) |
|  readinessProbe | objekt | Nej | Beredskaps avsökningen. - [ContainerProbe-objekt](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Nej | Det protokoll som är associerat med porten. -TCP eller UDP |
|  port | heltal | Ja | portnumret. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Resurs | sträng | Ja | Namnet på Azure-filresursen som ska monteras som en volym. |
|  readOnly | boolesk | Nej | Flaggan som anger om Azure-fildelningen som är monterad som en volym är skrivskyddad. |
|  storageAccountName | sträng | Ja | Namnet på det lagrings konto som innehåller Azure-filresursen. |
|  storageAccountKey | sträng | Nej | Lagrings kontots åtkomst nyckel som används för åtkomst till Azure-filresursen. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  katalog | sträng | Nej | Mål katalog namn. Får inte innehålla eller börja med "..".  Om "." anges blir volym katalogen git-lagringsplatsen.  Annars kommer volymen att innehålla git-lagringsplatsen i under katalogen med det angivna namnet. |
|  repository | sträng | Ja | URL för databas |
|  revision | sträng | Nej | Genomför hash för den angivna revisionen. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sträng | Ja | Arbetsyte-ID för Log Analytics |
|  workspaceKey | sträng | Ja | Arbets ytans nyckel för Log Analytics |
|  logType | Enum | Nej | Den logg typ som ska användas. -ContainerInsights eller ContainerInstanceLogs |
|  metadata | objekt | Nej | Metadata för Log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Nej | Det protokoll som är associerat med porten. -TCP eller UDP |
|  port | heltal | Ja | Port numret som visas i behållar gruppen. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på miljö variabeln. |
|  värde | sträng | Nej | Värdet för miljö variabeln. |
|  secureValue | sträng | Nej | Värdet för variabeln säker miljö. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Begäranden | objekt | Ja | Resurs begär Anden för den här behållar instansen. - [ResourceRequests-objekt](#ResourceRequests) |
|  gränser | objekt | Nej | Resurs gränserna för den här behållar instansen. - [ResourceLimits-objekt](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på volym monteringen. |
|  mountPath | sträng | Ja | Sökvägen i behållaren där volymen ska monteras. Får inte innehålla kolon (:). |
|  readOnly | boolesk | Nej | Flaggan indikerar om volym monteringen är skrivskyddad. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  ledn | objekt | Nej | Körnings kommandot till PROBE- [ContainerExec-objektet](#ContainerExec) |
|  httpGet | objekt | Nej | Http get-inställningarna till PROBE- [ContainerHttpGet-objektet](#ContainerHttpGet) |
|  initialDelaySeconds | heltal | Nej | Den första fördröjningen i sekunder. |
|  periodSeconds | heltal | Nej | Perioden i sekunder. |
|  failureThreshold | heltal | Nej | Tröskelvärdet för felen. |
|  successThreshold | heltal | Nej | Tröskelvärdet för framgång. |
|  timeoutSeconds | heltal | Nej | Tids gräns i sekunder. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Ja | Minnes förfrågan i GB av den här behållar instansen. |
|  processor | nummer | Ja | PROCESSOR förfrågan för den här behållar instansen. |
|  gpu | objekt | Nej | GPU-begäran för den här behållar instansen. - [GpuResource-objekt](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Nej | Minnes gränsen i GB för den här behållar instansen. |
|  processor | nummer | Nej | PROCESSOR gränsen för den här behållar instansen. |
|  gpu | objekt | Nej | GPU-gränsen för den här behållar instansen. - [GpuResource-objekt](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  command | matris | Nej | De kommandon som ska köras i behållaren. – sträng |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Sökväg | sträng | Nej | Sökvägen till avsökningen. |
|  port | heltal | Ja | Port numret som ska avsökas. |
|  schema | Enum | Nej | Schemat. -http eller https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  count | heltal | Ja | Antalet GPU-resurser. |
|  sku | Enum | Ja | SKU: n för GPU-resursen. -K80, P100, V100 |


## <a name="next-steps"></a>Nästa steg

Se självstudien [distribuera en grupp med flera behållare med hjälp av en yaml-fil](container-instances-multi-container-yaml.md).

Se exempel på hur du använder en YAML-fil för att distribuera behållar grupper i ett [virtuellt nätverk](container-instances-vnet.md) eller som [monterar en extern volym](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

