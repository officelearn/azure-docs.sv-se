---
title: Miljövariabler för Azure Service Fabric | Microsoft Docs
description: Referensdokumentation för Service Fabric-miljövariabler
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
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268012"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric-miljövariabler

Service Fabric har inbyggda miljövariabler som anges för varje tjänstinstans. En fullständig lista över miljövariablerna understiger:

| Miljövariabel                         | Beskrivning                                                            | Exempel                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Fabric uri-namnet på programmet                                 | fabric: / MyApplication                                                |
| Fabric_CodePackageName                       | Namnet på kodpaketet som processen tillhör              | Kod                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Ip-adress eller FQDN för slutpunkten                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Portnummer för slutpunkten                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Loggmapp                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Tillfällig mapp                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Arbetsmapp                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\fungerar     |
| Fabric_Folder_Application                    | Arbetsmapp för program                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | En bool som anger om processen är en behållare                   | false                                                                |
| Fabric_NodeId                                | Nod-ID: T för noden som kör processen                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP-Adressen eller FQDN för noden som anges i klustret-manifestfilen. | localhost eller 10.0.0.1                                                |
| Fabric_NodeName                              | Namnet på noden för noden som kör processen                          | _Node_0                                                              |
| Fabric_ServiceName                           | Fabric uri namnet på tjänsten, om den är värd för tjänsten i ExclusiveProcess läge. Det här värdet på variabeln är endast tillgänglig om du har skapat tjänsten med ServicePackageActivationMode ExclusiveProcess.  | fabric: / MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | ETT GUID                                                               |
| Fabric_ServicePackageName                    | Namnet på tjänstpaketet processen är en del av                     | Web1Pkg                                                              |

Intern miljövariabler som används av Service Fabric-körningen:

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
