---
title: Miljövariabler för Azure Service Fabric
description: Lär dig mer om miljövariabler i Azure Service Fabric. Innehåller en referens på en fullständig lista över variabler och deras användning.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645692"
---
# <a name="service-fabric-environment-variables"></a>Miljövariabler för Service Fabric

Service Fabric har inbyggda miljövariabler för varje tjänstinstans. Den fullständiga listan över miljövariabler finns nedan:

| Miljövariabel                         | Beskrivning                                                            | Exempel                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Tyget uri namnet på ansökan                                 | tyg:/MyApplication                                                |
| Fabric_CodePackageName                       | Namnet på det kodpaket som processen tillhör              | Kod                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Ip-adressen eller FQDN för slutpunkten                                 | 10.0.0.1                                                     |
| \_Fabric-slutpunktstjänstEndpointName\_*ServiceEndpointName*              | Portnummer för slutpunkten                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Loggmapp                                                             | C:\\\\\\\\Data _App\\\\_Node_0\\\\MyApplicationType_App12\\\\logg      |
| Fabric_Folder_App_Temp                       | Temp-mapp                                                            | C:\\\\\\\\\\\\Data\\\\_App _Node_0 MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Arbetsmapp                                                            | C:\\\\\\\\Data _App\\\\_Node_0\\\\MyApplicationType_App12\\\\arbete     |
| Fabric_Folder_Application                    | Programens hemmapp                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | En bool som anger om processen är en behållare                   | false                                                                |
| Fabric_NodeId                                | Nod-ID för noden som kör processen                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP- eller FQDN för noden, enligt vad som anges i klustermanifestfilen. | localhost eller 10.0.0.1                                                |
| Fabric_NodeName                              | Nodnamnet på noden som kör processen                          | _Node_0                                                              |
| Fabric_ServiceName                           | Tjänstens fabric uri-namn, om tjänsten finns i ExclusiveProcess-läge. Det här variabelvärdet är endast tillgängligt om du skapar tjänsten med ServicePackageActivationMode ExclusiveProcess.  | tyg:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | Ett GUID                                                               |
| Fabric_ServicePackageName                    | Namnet på det servicepaket som processen är en del av                     | Web1Pkg (Olikart)                                                              |

Interna miljövariabler som används av Service Fabric Runtime:

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
- Värdtjänstnamn
