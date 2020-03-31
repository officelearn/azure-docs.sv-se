---
title: YAML-referens för behållargrupp
description: Referens för YAML-filen som stöds av Azure Container Instances för att konfigurera en behållargrupp
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896574"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-referens: Azure Container-instanser

Den här artikeln beskriver syntaxen och egenskaperna för YAML-filen som stöds av Azure Container Instances för att konfigurera en [behållargrupp](container-instances-container-groups.md). Använd en YAML-fil för att mata in gruppkonfigurationen till kommandot [az container create][az-container-create] i Azure CLI. 

En YAML-fil är ett bekvämt sätt att konfigurera en behållargrupp för reproducerbara distributioner. Det är ett koncist alternativ till att använda en [Resource Manager-mall](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) eller Azure Container Instances SDK:er för att skapa eller uppdatera en behållargrupp.

> [!NOTE]
> Den här referensen gäller YAML-filer för `2018-10-01`REST API-version för Azure Container Instances .

## <a name="schema"></a>Schema 

Schemat för YAML-filen följer, inklusive kommentarer för att markera viktiga egenskaper. En beskrivning av egenskaperna i det här schemat finns i avsnittet [Egenskapsvärden.](#property-values)

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

I följande tabeller beskrivs de värden som du behöver ange i schemat.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på behållargruppen. |
|  apiVersion | Enum | Ja | 2018-10-01 |
|  location | sträng | Inga | Resursplatsen. |
|  tags | objekt | Inga | Resurstaggarna. |
|  identity | objekt | Inga | Behållarens identitet, om den är konfigurerad. - [Object containergroupidentity](#ContainerGroupIdentity) |
|  properties | objekt | Ja | [Object ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Object containergroupidentity

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  typ | Enum | Inga | Den typ av identitet som används för behållargruppen. Typen SystemAssigned, UserAssigned innehåller både en implicit skapad identitet och en uppsättning användartilldelade identiteter. Typen "Ingen" tar bort alla identiteter från behållargruppen. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, Ingen |
|  userAssignedId entiteter | objekt | Inga | Listan över användaridentiteter som är associerade med behållargruppen. Nyckelreferenserna för användaridentitetsordlista är Azure Resource Manager-resurs-ID:er i formuläret :'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Object ContainerGroupProperties

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  containrar | matris | Ja | Behållarna i behållargruppen. - [Behållarobjekt](#Container) |
|  bildRegistryCredentials | matris | Inga | De autentiseringsuppgifter för avbildningsregistret som behållargruppen skapas från. - [BildregisterRedential-objekt](#ImageRegistryCredential) |
|  restartPolicy(restartPolicy) | Enum | Inga | Starta om principen för alla behållare i behållargruppen. - `Always`Starta alltid `OnFailure` om- `Never` Starta om vid fel- Starta aldrig om. - Alltid, OnFailure, aldrig |
|  Ip | objekt | Inga | IP-adresstypen för behållargruppen. - [IpAddress-objekt](#IpAddress) |
|  osType (olika) | Enum | Ja | Den operativsystemtyp som krävs av behållarna i behållargruppen. - Windows eller Linux |
|  volumes | matris | Inga | Listan över volymer som kan monteras av behållare i den här behållargruppen. - [Volymobjekt](#Volume) |
|  diagnostik | objekt | Inga | Diagnostikinformationen för en behållargrupp. - [ContainerGroupDiagnostics-objekt](#ContainerGroupDiagnostics) |
|  nätverkProfile | objekt | Inga | Nätverksprofilinformationen för en behållargrupp. - [Object ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig (1) | objekt | Inga | DNS-konfigurationsinformationen för en behållargrupp. - [DnsConfiguration-objekt](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Behållarobjekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på behållarinstansen som tillhandahålls av användaren. |
|  properties | objekt | Ja | Egenskaperna för behållarinstansen. - [Object ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>BildregisterRedential-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  server | sträng | Ja | Docker-avbildningsregisterservern utan protokoll som "http" och "https". |
|  användarnamn | sträng | Ja | Användarnamnet för det privata registret. |
|  password | sträng | Inga | Lösenordet för det privata registret. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  ports | matris | Ja | Listan över portar som visas för behållargruppen. - [Port-objekt](#Port) |
|  typ | Enum | Ja | Anger om IP-adressen är exponerad för det offentliga internet eller det privata virtuella nätverket. - Offentliga eller privata |
|  Ip | sträng | Inga | Ip utsätts för det offentliga internet. |
|  dnsNameLabel (1) | sträng | Inga | Dns-namnetiketten för IP-adressen. |


<a id="Volume" />

### <a name="volume-object"></a>Volymobjekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på volymen. |
|  azureFile | objekt | Inga | Azure-filvolymen. - [AzureFileVolume-objekt](#AzureFileVolume) |
|  emptyDir | objekt | Inga | Den tomma katalogvolymen. |
|  hemlighet | objekt | Inga | Den hemliga volymen. |
|  gitRepo | objekt | Inga | Git-repovolymen. - [GitRepoVolume-objekt](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | Inga | Information om analys av logganalys för behållargrupp. - [LogAnalytics-objekt](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Object ContainerGroupNetworkProfile

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  id | sträng | Ja | Identifieraren för en nätverksprofil. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Namnservrar | matris | Ja | DNS-servrarna för behållargruppen. - sträng |
|  sökDomäner | sträng | Inga | DNS-sökdomänerna efter uppslag på värdnamn i behållargruppen. |
|  alternativ | sträng | Inga | DNS-alternativen för behållargruppen. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Object ContainerProperties

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  image | sträng | Ja | Namnet på den bild som används för att skapa behållarförekomsten. |
|  command | matris | Inga | De kommandon som ska köras i behållarinstansen i exec-formulär. - sträng |
|  ports | matris | Inga | De exponerade portarna på behållarinstansen. - [ContainerPort-objekt](#ContainerPort) |
|  miljöVariabler | matris | Inga | De miljövariabler som ska anges i behållarinstansen. - [MiljöVarierbart objekt](#EnvironmentVariable) |
|  resources | objekt | Ja | Resurskraven för behållarinstansen. - [ResourceRequirements-objekt](#ResourceRequirements) |
|  volymFästen | matris | Inga | Den volym som är tillgänglig för behållarinstansen. - [VolumeMount-objekt](#VolumeMount) |
|  livlighetProbe | objekt | Inga | Livssonden. - [ContainerProbe-objekt](#ContainerProbe) |
|  beredskapProbe | objekt | Inga | Beredskapssonden. - [ContainerProbe-objekt](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Protokollet | Enum | Inga | Protokollet som är associerat med porten. - TCP eller UDP |
|  port | heltal | Ja | Portnumret. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Resursnamn | sträng | Ja | Namnet på Azure File-resursen som ska monteras som en volym. |
|  Readonly | boolean | Inga | Flaggan som anger om Den delade Azure-filen är skrivskyddad. |
|  storageAccountName | sträng | Ja | Namnet på lagringskontot som innehåller Azure File-resursen. |
|  lagringSförståe att | sträng | Inga | Åtkomstnyckeln för lagringskonto som används för åtkomst till Azure File-resursen. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  katalog | sträng | Inga | Målkatalognamn. Får inte innehålla eller börja med '....  Om '.' levereras kommer volymkatalogen att vara git-databasen.  Annars, om det anges, kommer volymen att innehålla git-databasen i underkatalogen med förnamnet. |
|  Databasen | sträng | Ja | Url till databas |
|  revision | sträng | Inga | Commit-hash för den angivna ändringen. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sträng | Ja | Arbetsyte-ID för logganalys |
|  workspaceKey (arbetsytaNyckel) | sträng | Ja | Arbetsytenyckeln för logganalys |
|  logType (loggtyp) | Enum | Inga | Den loggtyp som ska användas. - ContainerInsights eller ContainerInstanceLogs |
|  metadata | objekt | Inga | Metadata för logganalys. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Protokollet | Enum | Inga | Protokollet som är associerat med porten. - TCP eller UDP |
|  port | heltal | Ja | Portnumret som visas i behållargruppen. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>MiljöVarierbart objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på miljövariabeln. |
|  värde | sträng | Inga | Värdet på miljövariabeln. |
|  säkertVärde | sträng | Inga | Värdet för variabeln säker miljö. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Begäranden | objekt | Ja | Resursbegäranden för den här behållarinstansen. - [ResourceRequests-objekt](#ResourceRequests) |
|  Gränser | objekt | Inga | Resursgränserna för den här behållarinstansen. - [ResourceLimits-objekt](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  namn | sträng | Ja | Namnet på volymfästet. |
|  mountPath (monteringsväg) | sträng | Ja | Banan i behållaren där volymen ska monteras. Får inte innehålla kolon (:). |
|  Readonly | boolean | Inga | Flaggan som anger om volymfästet är skrivskyddat. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  Exec | objekt | Inga | Körningskommandot att avsöka - [ContainerExec-objekt](#ContainerExec) |
|  httpGet (på) | objekt | Inga | Inställningarna för Http Get till avsökning - [ContainerHttpGet-objekt](#ContainerHttpGet) |
|  initialDelaySeconds | heltal | Inga | De första fördröjningssekunderna. |
|  periodSeconds | heltal | Inga | Perioden sekunder. |
|  felDetröser | heltal | Inga | Tröskelvärdet för fel. |
|  framgångDethold | heltal | Inga | Tröskelvärdet för framgång. |
|  timeoutSeconds | heltal | Inga | Tidsgränsen sekunder. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Ja | Minnesbegäran i GB för den här behållarinstansen. |
|  Cpu | nummer | Ja | CPU-begäran för den här behållarinstansen. |
|  Gpu | objekt | Inga | GPU-begäran för den här behållarinstansen. - [GpuResource-objekt](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Inga | Minnesgränsen i GB för den här behållarinstansen. |
|  Cpu | nummer | Inga | CPU-gränsen för den här behållarinstansen. |
|  Gpu | objekt | Inga | GPU-gränsen för den här behållarinstansen. - [GpuResource-objekt](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  command | matris | Inga | De kommandon som ska köras i behållaren. - sträng |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>BehållarehttpGet-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  path | sträng | Inga | Vägen till sonden. |
|  port | heltal | Ja | Portnumret att söka. |
|  System | Enum | Inga | Systemet. - http eller https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource-objekt

|  Namn | Typ | Krävs | Värde |
|  ---- | ---- | ---- | ---- |
|  count | heltal | Ja | Antalet GPU-resurser. |
|  sku | Enum | Ja | SKU för GPU-resursen. - K80, P100, V100 |


## <a name="next-steps"></a>Nästa steg

Se självstudien [Distribuera en grupp med flera behållare med hjälp av en YAML-fil](container-instances-multi-container-yaml.md).

Se exempel på hur du använder en YAML-fil för att distribuera behållargrupper i ett [virtuellt nätverk](container-instances-vnet.md) eller som [monterar en extern volym](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

