---
title: Kontrollera händelseloggarna för behållare i Azure Service Fabric | Microsoft Docs
description: Beskriver hur du visar behållaren loggar för ett Service Fabric-behållartjänster som körs med hjälp av Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: c8b6bc791700e6811f5681ee70329e4d2ac05991
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824619"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visa loggfiler för en tjänst för Service Fabric-behållare
Azure Service Fabric är en behållare orchestrator och stöder både [behållare för Linux och Windows](service-fabric-containers-overview.md).  Den här artikeln beskriver hur du visar behållaren loggar av en pågående behållartjänst eller en döda behållare så att du kan diagnostisera och felsöka problem.

## <a name="access-the-logs-of-a-running-container"></a>Åtkomst till loggar i en behållare som körs
Behållaren loggar kan nås med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  I en webbläsare, öppna Service Fabric Explorer från klusterslutpunkten management genom att gå till [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Behållaren loggfilerna finns på den noden i klustret som container service-instans som körs på. Exempelvis hämta loggarna för web front-end behållaren med den [Linux röstning exempelprogrammet](service-fabric-quickstart-containers-linux.md). I trädvyn expanderar **klustret**>**program**>**VotingType**>**fabric: / Röstningsdatabasen / azurevotefront**.  Sedan expandera partitionen (d1aa737e-f22a-e347-be16-eec90be24bc1, i det här exemplet) och att behållaren körs på klusternoden *_lnxvm_0*.

Hitta kodpaketet i trädvyn på den *_lnxvm_0* nod genom att expandera **noder**>**_lnxvm_0**>**fabric: / röstning**  > **azurevotfrontPkg**>**kod paket**>**kod**.  Välj sedan den **behållare loggar** alternativet för att visa loggar för behållaren.

![Service Fabric-plattform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Åtkomst till loggar i en behållare för döda eller krasch
Från och med v6.2, du kan också hämta loggarna för en behållare för döda eller krasch med hjälp av [REST API: er](/rest/api/servicefabric/sfclient-index) eller [Service Fabric CLI (SFCTL)](service-fabric-cli.md) kommandon.

### <a name="set-container-retention-policy"></a>Ange bevarandeprincip för behållare
Service Fabric (version 6.1 eller senare) har stöd för bevarande av behållare som har avslutats eller inte kunde starta, vilket underlättar diagnostisering av startfel. Den här principen kan anges i filen **ApplicationManifest.xml**, vilket visas i följande kodavsnitt:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Inställningen **ContainersRetentionCount** anger antalet behållare som ska bevaras när de får fel. Om ett negativt värde anges kommer alla behållare med fel att bevaras. När den **ContainersRetentionCount** attributet har inte angetts, ingen behållare ska behållas. Attributet **ContainersRetentionCount** har även stöd för programparametrar, så att användarna kan ange olika värden för test- och produktionskluster. Använd placeringsbegränsningar för att rikta in behållartjänsten på en viss nod när den här funktionen används för att förhindra att behållartjänsten flyttas till andra noder. Alla behållare som bevaras med den här funktionen måste tas bort manuellt.

### <a name="rest"></a>REST
Använd den [hämta behållaren distribueras på noden](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) åtgärden att hämta loggarna för en krasch behållare. Ange namnet på den nod som körs på behållaren, programnamn, manifestet tjänstnamn och paketnamnet kod.  Ange `&Previous=true`. Svaret innehåller behållaren loggar för behållaren döda av koden paketet instansen.

URI-begäran har följande format:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Exempelbegäran:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 svarstexten:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Använd den [sfctl get-behållaren-tjänstloggar](service-fabric-sfctl-service.md) kommando för att hämta loggarna för en krasch behållare.  Ange namnet på den nod som körs på behållaren, programnamn, manifestet tjänstnamn och paketnamnet kod. Ange den `-previous` flaggan.  Svaret innehåller behållaren loggar för behållaren döda av koden paketet instansen.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
Svar:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Nästa steg
- Gå igenom den [skapa ett Linux-behållaren självstudien](service-fabric-tutorial-create-container-images.md).
- Lär dig mer om [Service Fabric och behållare](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
