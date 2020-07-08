---
title: YAML-referens för container grupp
description: Referens för YAML-filen som stöds av Azure Container Instances att konfigurera en behållar grupp
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: be78c7d498187486a1502da17faa2b8faa5a0982
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730534"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-referens: Azure Container Instances

I den här artikeln beskrivs syntax och egenskaper för YAML-filen som stöds av Azure Container Instances att konfigurera en [behållar grupp](container-instances-container-groups.md). Använd en YAML-fil för att ange grupp konfigurationen till kommandot [AZ container Create][az-container-create] i Azure CLI. 

En YAML-fil är ett bekvämt sätt att konfigurera en behållar grupp för åter användning. Det är ett koncist alternativ till att använda en [Resource Manager-mall](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) eller Azure Container instances SDK: er för att skapa eller uppdatera en behållar grupp.

> [!NOTE]
> Den här referensen gäller för YAML-filer för Azure Container Instances REST API version `2018-10-01` .

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

## <a name="property-values"></a>Egenskaps värden

I följande tabeller beskrivs de värden som du måste ange i schemat.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. ContainerInstance/containerGroups-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Ja | Namnet på behållar gruppen. |
|  apiVersion | räkning | Ja | 2018-10-01 |
|  location | sträng | No | Resursens plats. |
|  tags | objekt | No | Resurs taggarna. |
|  identity | objekt | No | Identiteten för behållar gruppen, om den är konfigurerad. - [ContainerGroupIdentity-objekt](#containergroupidentity-object) |
|  properties | objekt | Ja | [ContainerGroupProperties-objekt](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  typ | räkning | No | Typ av identitet som används för behållar gruppen. Typen "SystemAssigned, UserAssigned" innehåller både en implicit skapad identitet och en uppsättning användare tilldelade identiteter. Typen ' none ' tar bort alla identiteter från behållar gruppen. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, ingen |
|  userAssignedIdentities | objekt | No | Listan över användar identiteter som är kopplade till behållar gruppen. Nyckel referenserna för användar identitets ord listan kommer att Azure Resource Manager resurs-ID: n i formatet: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  containrar | matris | Ja | Behållarna i behållar gruppen. - [Container objekt](#container-object) |
|  imageRegistryCredentials | matris | No | De avbildnings register uppgifter som behållar gruppen skapas från. - [ImageRegistryCredential-objekt](#imageregistrycredential-object) |
|  restartPolicy | räkning | No | Starta om principen för alla behållare i behållar gruppen. - `Always`Starta alltid om, starta om datorn `OnFailure` vid haveri- `Never` Starta inte om. -Always, OnFailure, Never |
|  Adresser | objekt | No | Typ av IP-adress för behållar gruppen. - [IpAddress-objekt](#ipaddress-object) |
|  osType | räkning | Ja | Den typ av operativ system som krävs av behållarna i behållar gruppen. – Windows eller Linux |
|  volumes | matris | No | Listan över volymer som kan monteras av behållare i den här behållar gruppen. - [Volym objekt](#volume-object) |
|  diagnostik | objekt | No | Diagnostikinformation för en behållar grupp. - [ContainerGroupDiagnostics-objekt](#containergroupdiagnostics-object) |
|  networkProfile | objekt | No | Nätverks profil informationen för en behållar grupp. - [ContainerGroupNetworkProfile-objekt](#containergroupnetworkprofile-object) |
|  dnsConfig | objekt | No | DNS-konfigurations information för en behållar grupp. - [DnsConfiguration-objekt](#dnsconfiguration-object) |




### <a name="container-object"></a>Container objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Ja | Namnet på behållar instansen som användaren anger. |
|  properties | objekt | Ja | Egenskaperna för behållar instansen. - [ContainerProperties-objekt](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  server | sträng | Ja | Docker-avbildningens register server utan protokoll som "http" och "https". |
|  användarnamn | sträng | Ja | Användar namnet för det privata registret. |
|  password | sträng | No | Lösen ordet för det privata registret. |




### <a name="ipaddress-object"></a>IpAddress-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  ports | matris | Ja | Listan över portar som exponeras i behållar gruppen. - [Port objekt](#port-object) |
|  typ | räkning | Ja | Anger om IP-adressen exponeras för det offentliga Internet eller privata virtuella nätverket. – Offentligt eller privat |
|  sökning | sträng | No | IP-adressen som exponeras för det offentliga Internet. |
|  dnsNameLabel | sträng | No | DNS-namnets etikett för IP-adressen. |




### <a name="volume-object"></a>Volym objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Ja | Namnet på volymen. |
|  azureFile | objekt | No | Azure-filvolymen. - [AzureFileVolume-objekt](#azurefilevolume-object) |
|  emptyDir | objekt | No | Den tomma katalog volymen. |
|  hemlighet | objekt | No | Den hemliga volymen. |
|  gitRepo | objekt | No | Git lagrings platsen-volymen. - [GitRepoVolume-objekt](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | No | Information om behållar grupps logg analys. - [LogAnalytics-objekt](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  id | sträng | Ja | Identifieraren för en nätverks profil. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Namnservrar | matris | Ja | DNS-servrarna för behållar gruppen. – sträng |
|  searchDomains | sträng | No | DNS-sökdomänerna för Sök efter värdnamn i behållar gruppen. |
|  alternativ | sträng | No | DNS-alternativen för behållar gruppen. |




### <a name="containerproperties-object"></a>ContainerProperties-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  image | sträng | Ja | Namnet på den avbildning som används för att skapa behållar instansen. |
|  command | matris | No | De kommandon som ska köras i behållar instansen i exec form. – sträng |
|  ports | matris | No | Exponerade portar på behållar instansen. - [ContainerPort-objekt](#containerport-object) |
|  environmentVariables | matris | No | Miljövariablerna som ska anges i behållar instansen. - [EnvironmentVariable-objekt](#environmentvariable-object) |
|  resources | objekt | Ja | Resurs kraven för behållar instansen. - [ResourceRequirements-objekt](#resourcerequirements-object) |
|  volumeMounts | matris | No | Volymen monteras som är tillgänglig för behållar instansen. - [VolumeMount-objekt](#volumemount-object) |
|  livenessProbe | objekt | No | Direktmigrering. - [ContainerProbe-objekt](#containerprobe-object) |
|  readinessProbe | objekt | No | Beredskaps avsökningen. - [ContainerProbe-objekt](#containerprobe-object) |




### <a name="port-object"></a>Port objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  protokollhanterare | räkning | No | Det protokoll som är associerat med porten. -TCP eller UDP |
|  port | heltal | Ja | Portnumret. |




### <a name="azurefilevolume-object"></a>AzureFileVolume-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  Resurs | sträng | Ja | Namnet på Azure-filresursen som ska monteras som en volym. |
|  readOnly | boolean | No | Flaggan som anger om Azure-fildelningen som är monterad som en volym är skrivskyddad. |
|  storageAccountName | sträng | Ja | Namnet på det lagrings konto som innehåller Azure-filresursen. |
|  storageAccountKey | sträng | No | Lagrings kontots åtkomst nyckel som används för åtkomst till Azure-filresursen. |




### <a name="gitrepovolume-object"></a>GitRepoVolume-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  katalog | sträng | No | Mål katalog namn. Får inte innehålla eller börja med "..".  Om "." anges blir volym katalogen git-lagringsplatsen.  Annars kommer volymen att innehålla git-lagringsplatsen i under katalogen med det angivna namnet. |
|  lagrings platsen | sträng | Ja | URL för databas |
|  revision | sträng | No | Genomför hash för den angivna revisionen. |




### <a name="loganalytics-object"></a>LogAnalytics-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sträng | Ja | Arbetsyte-ID för Log Analytics |
|  workspaceKey | sträng | Ja | Arbets ytans nyckel för Log Analytics |
|  logType | räkning | No | Den logg typ som ska användas. -ContainerInsights eller ContainerInstanceLogs |
|  metadata | objekt | No | Metadata för Log Analytics. |




### <a name="containerport-object"></a>ContainerPort-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  protokollhanterare | räkning | No | Det protokoll som är associerat med porten. -TCP eller UDP |
|  port | heltal | Ja | Port numret som visas i behållar gruppen. |




### <a name="environmentvariable-object"></a>EnvironmentVariable-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Ja | Namnet på miljö variabeln. |
|  värde | sträng | No | Värdet för miljö variabeln. |
|  secureValue | sträng | No | Värdet för variabeln säker miljö. |




### <a name="resourcerequirements-object"></a>ResourceRequirements-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  autentiseringsbegäran | objekt | Ja | Resurs begär Anden för den här behållar instansen. - [ResourceRequests-objekt](#resourcerequests-object) |
|  sten | objekt | No | Resurs gränserna för den här behållar instansen. - [ResourceLimits-objekt](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  name | sträng | Ja | Namnet på volym monteringen. |
|  mountPath | sträng | Ja | Sökvägen i behållaren där volymen ska monteras. Får inte innehålla kolon (:). |
|  readOnly | boolean | No | Flaggan indikerar om volym monteringen är skrivskyddad. |




### <a name="containerprobe-object"></a>ContainerProbe-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  ledn | objekt | No | Körnings kommandot till PROBE- [ContainerExec-objektet](#containerexec-object) |
|  httpGet | objekt | No | Http get-inställningarna till PROBE- [ContainerHttpGet-objektet](#containerhttpget-object) |
|  initialDelaySeconds | heltal | No | Den första fördröjningen i sekunder. |
|  periodSeconds | heltal | No | Perioden i sekunder. |
|  failureThreshold | heltal | No | Tröskelvärdet för felen. |
|  successThreshold | heltal | No | Tröskelvärdet för framgång. |
|  timeoutSeconds | heltal | No | Tids gräns i sekunder. |




### <a name="resourcerequests-object"></a>ResourceRequests-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Ja | Minnes förfrågan i GB av den här behållar instansen. |
|  registrera | nummer | Ja | PROCESSOR förfrågan för den här behållar instansen. |
|  grafik | objekt | No | GPU-begäran för den här behållar instansen. - [GpuResource-objekt](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | No | Minnes gränsen i GB för den här behållar instansen. |
|  registrera | nummer | No | PROCESSOR gränsen för den här behållar instansen. |
|  grafik | objekt | No | GPU-gränsen för den här behållar instansen. - [GpuResource-objekt](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  command | matris | No | De kommandon som ska köras i behållaren. – sträng |




### <a name="containerhttpget-object"></a>ContainerHttpGet-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  path | sträng | No | Sökvägen till avsökningen. |
|  port | heltal | Ja | Port numret som ska avsökas. |
|  schema | räkning | No | Schemat. -http eller https |




### <a name="gpuresource-object"></a>GpuResource-objekt

|  Name | Typ | Obligatorisk | Värde |
|  ---- | ---- | ---- | ---- |
|  count | heltal | Ja | Antalet GPU-resurser. |
|  sku | räkning | Ja | SKU: n för GPU-resursen. -K80, P100, V100 |


## <a name="next-steps"></a>Nästa steg

Se självstudien [distribuera en grupp med flera behållare med hjälp av en yaml-fil](container-instances-multi-container-yaml.md).

Se exempel på hur du använder en YAML-fil för att distribuera behållar grupper i ett [virtuellt nätverk](container-instances-vnet.md) eller som [monterar en extern volym](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

