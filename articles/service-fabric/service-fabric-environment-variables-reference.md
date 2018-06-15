---
title: Azure Service Fabric-miljövariabler | Microsoft Docs
description: I referensdokumentationen för Service Fabric miljövariabler
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: f7c36fec7ff58c225e41899e8264ca1dde95ce7c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213256"
---
# <a name="service-fabric-environment-variables"></a>Miljövariabler för Service Fabric

Service Fabric har inbyggda miljövariabler som anges för varje service-instans. En fullständig lista över miljövariabler understiger:

| Miljövariabeln                         | Beskrivning                                                            | Exempel                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Fabric-uri-namnet för programmet                                 | fabric: / MyApplication                                                |
| Fabric_CodePackageName                       | Namnet på kodpaketet som processen tillhör              | Kod                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Ip-adress eller FQDN för slutpunkten                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Portnummer för slutpunkten                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Loggmapp                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\logg      |
| Fabric_Folder_App_Temp                       | Tillfällig mapp                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Arbetsmappar                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\fungerar     |
| Fabric_Folder_Application                    | Arbetsmappen för program                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Anger om processen är en behållare bool                   | false                                                                |
| Fabric_NodeId                                | Nod-ID för den nod som kör processen                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Manifestfilen IP eller FQDN för noden som anges i klustret. | localhost eller 10.0.0.1                                                |
| Fabric_NodeName                              | Nodnamnet på den nod som kör processen                          | _Node_0                                                              |
| Fabric_ServiceName                           | Namnet på tjänsten om den är värd för tjänsten i ExclusiveProcess läge. Den här variabeln värdet är endast tillgängligt om du skapar tjänsten med ServicePackageActivationMode ExclusiveProcess.  | MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | ETT GUID                                                               |
| Fabric_ServicePackageName                    | Namnet på tjänstepaketet processen är en del av                     | Web1Pkg                                                              |

Internt miljövariabler som används av Service Fabric Runtime:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName