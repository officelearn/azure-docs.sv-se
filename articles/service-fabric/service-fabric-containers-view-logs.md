---
title: Visa loggar för behållare i Azure Service Fabric | Microsoft Docs
description: Beskriver hur du visar behållarloggarna för en aktiv Service Fabric-behållartjänster med Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 0408010a49b8ec83aa02c74887139f663788ad80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881496"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visa loggar för en tjänst för Service Fabric-behållare
Azure Service Fabric är en orkestrerare för behållare och stöder både [Linux och Windows-behållare](service-fabric-containers-overview.md).  Den här artikeln beskriver hur du visar behållarloggarna av en pågående container-tjänst eller en döda behållare så att du kan diagnostisera och felsöka problem.

## <a name="access-the-logs-of-a-running-container"></a>Komma åt loggarna för en behållare som körs
Behållarloggar kan användas med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  I en webbläsare, öppnar du Service Fabric Explorer från klustrets hanteringsslutpunkt genom att gå till [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Behållarloggar finns på klusternoden som tjänstinstansen behållare körs på. Till exempel hämta loggarna för webb-behållaren på klientsidan för den [Linux rösta exempelprogrammet](service-fabric-quickstart-containers-linux.md). I trädvyn expanderar **kluster**>**program**>**VotingType**>**fabric: / Voting / azurevotefront**.  Sedan expandera partition (d1aa737e-f22a-e347-be16-eec90be24bc1, i det här exemplet) och se att behållaren körs på noden i klustret *_lnxvm_0*.

Hitta kodpaketet i trädvyn på den *_lnxvm_0* noden genom att expandera **noder**>**_lnxvm_0**>**fabric: / Voting**  > **azurevotfrontPkg**>**Kodpaket**>**kod**.  Välj sedan den **Behållarloggarna** alternativet för att visa behållarloggarna.

![Service Fabric-plattform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Komma åt loggar för en döda eller kraschat behållare
Från och med v6.2, du kan också hämta loggar för ett döda eller kraschat behållaren med hjälp av [REST API: er](/rest/api/servicefabric/sfclient-index) eller [Service Fabric CLI (SFCTL)](service-fabric-cli.md) kommandon.

### <a name="set-container-retention-policy"></a>Ange bevarandeprincip för container
Service Fabric (version 6.1 eller senare) har stöd för bevarande av containrar som har avslutats eller inte kunde starta, vilket underlättar diagnostisering av startfel. Den här principen kan anges i filen **ApplicationManifest.xml**, vilket visas i följande kodavsnitt:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Inställningen **ContainersRetentionCount** anger antalet behållare som ska bevaras när de får fel. Om ett negativt värde anges kommer alla containrar med fel att bevaras. När den **ContainersRetentionCount** attributet inte anges, inga behållare att bevaras. Attributet **ContainersRetentionCount** har även stöd för programparametrar, så att användarna kan ange olika värden för test- och produktionskluster. Använd placeringsbegränsningar för att rikta in containertjänsten på en viss nod när den här funktionen används för att förhindra att containertjänsten flyttas till andra noder. Alla containrar som bevaras med den här funktionen måste tas bort manuellt.

Inställningen **RunInteractive** motsvarar Dockers `--interactive` och `tty` [flaggor](https://docs.docker.com/engine/reference/commandline/run/#options). När den här inställningen anges till true i manifestfilen, dessa flaggor används för att starta behållaren.  

### <a name="rest"></a>REST
Använd den [hämta behållare distribueras på noden för](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) åtgärd för att hämta loggarna för en krasch behållare. Ange namnet på den nod som behållaren körs på, programnamn, manifest tjänstnamn och paketnamnet kod.  Ange `&Previous=true`. Svaret innehåller behållarloggarna för obeställbara kod paketet instans-behållaren.

Begärande-URI har följande format:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Exempelbegärande:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 svarstext:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Använd den [sfctl get-container-tjänstloggar](service-fabric-sfctl-service.md) kommando för att hämta loggarna för en krasch behållare.  Ange namnet på den nod som behållaren körs på, programnamn, manifest tjänstnamn och paketnamnet kod. Ange den `--previous` flaggan.  Svaret innehåller behållarloggarna för obeställbara kod paketet instans-behållaren.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Svar:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Nästa steg
- Gå igenom den [skapa en Linux-behållare självstudien](service-fabric-tutorial-create-container-images.md).
- Läs mer om [Service Fabric och behållare](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
