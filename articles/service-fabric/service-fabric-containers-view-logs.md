---
title: Visa behållare loggar i Azure Service Fabric
description: Beskriver hur du visar behållarloggar för en tjänst fabric-behållartjänster som körs med Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458177"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visa loggar för en service för service i servicevävnadsbehållare
Azure Service Fabric är en behållare orchestrator och stöder både [Linux och Windows-behållare](service-fabric-containers-overview.md).  I den här artikeln beskrivs hur du visar behållarloggar för en behållartjänst som körs eller en död behållare så att du kan diagnostisera och felsöka problem.

## <a name="access-the-logs-of-a-running-container"></a>Komma åt loggarna för en behållare som körs
Behållarloggar kan nås med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Öppna Service Fabric Explorer från klustrets hanteringsslutpunkt i `http://mycluster.region.cloudapp.azure.com:19080/Explorer`en webbläsare genom att navigera till .  

Behållarloggar finns på klusternoden som behållartjänstinstansen körs på. Som ett exempel, få loggarna för webben front-end behållaren för [Linux Röstning exempelprogram](service-fabric-quickstart-containers-linux.md). Expandera>**Klusterprogram**>**VotingType-infrastruktur**>i trädvyn:/Röstning/azurevotefront . **Cluster****fabric:/Voting/azurevotefront**  Expandera sedan partitionen (d1aa737e-f22a-e347-be16-eec90be24bc1, i det här exemplet) och se till att behållaren körs på *klusternoden _lnxvm_0*.

Leta reda på kodpaketen på *noden _lnxvm_0 i* trädvyn genom att expandera **noder**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg-kodpaketkod**>**Code Packages**>**code**.  Välj sedan alternativet **Behållarloggar** för att visa behållarloggarna.

![Service Fabric-plattform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Komma åt loggarna för en död eller kraschad behållare
Från och med v6.2 kan du också hämta loggarna för en död eller kraschad behållare med HJÄLP AV [REST API:er](/rest/api/servicefabric/sfclient-index) eller [SFCTL-kommandon (Service Fabric CLI).](service-fabric-cli.md)

### <a name="set-container-retention-policy"></a>Ange bevarandeprincip för container
Service Fabric (version 6.1 eller senare) har stöd för bevarande av containrar som har avslutats eller inte kunde starta, vilket underlättar diagnostisering av startfel. Den här principen kan anges i filen **ApplicationManifest.xml**, vilket visas i följande kodavsnitt:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Inställningen **ContainersRetentionCount** anger antalet behållare som ska bevaras när de får fel. Om ett negativt värde anges kommer alla containrar med fel att bevaras. När attributet **ContainersRetentionCount** inte har angetts behålls inga behållare. Attributet **ContainersRetentionCount** har även stöd för programparametrar, så att användarna kan ange olika värden för test- och produktionskluster. Använd placeringsbegränsningar för att rikta in containertjänsten på en viss nod när den här funktionen används för att förhindra att containertjänsten flyttas till andra noder. Alla containrar som bevaras med den här funktionen måste tas bort manuellt.

Inställningen **RunInteractive** motsvarar Dockers `--interactive` och `tty` [flaggor](https://docs.docker.com/engine/reference/commandline/run/#options). När den här inställningen är inställd på true i manifestfilen används dessa flaggor för att starta behållaren.  

### <a name="rest"></a>REST
Använd åtgärden [Hämta behållarloggar som har distribuerats på nod](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) för att hämta loggarna för en kraschad behållare. Ange namnet på den nod som behållaren kördes på, programnamn, tjänstmanifestnamn och kodpaketnamnet.  Ange `&Previous=true`. Svaret innehåller behållarloggarna för den döda behållaren för kodpaketinstansen.

URI för begäran har följande formulär:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Exempel begäran:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 Svarsorgan:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service tyg (SFCTL)
Använd kommandot [sfctl service get-container-logs](service-fabric-sfctl-service.md) för att hämta loggarna för en kraschad behållare.  Ange namnet på den nod som behållaren kördes på, programnamn, tjänstmanifestnamn och kodpaketnamnet. Ange `--previous` flaggan.  Svaret innehåller behållarloggarna för den döda behållaren för kodpaketinstansen.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Svar:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Nästa steg
- Arbeta igenom [självstudiekursen skapa en Linux-behållare.](service-fabric-tutorial-create-container-images.md)
- Läs mer om [Service Fabric och behållare](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
