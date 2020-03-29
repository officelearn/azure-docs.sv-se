---
title: Hitta Azure Service Fabric Mesh-exempel
description: Här är ett index över tillgängliga exempelprogram för Service Fabric Mesh. Källkoden i de här exemplen visar hur du uppnår ett visst scenario med hjälp av Service Fabric-resursmodellen.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461902"
---
# <a name="find-service-fabric-mesh-samples"></a>Hitta Service Fabric Mesh-exempel

I den här tabellen beskrivs tillgängliga Service Fabric Mesh-exempelprogram. Källkoden i de här exemplen visar hur du uppnår ett visst scenario med hjälp av Service Fabric-resursmodellen.

Mer information om hur du distribuerar mallar direkt till Azure finns på [Github-sidan med exempelmallar](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).

|Exempelmall|Scenariobeskrivning|Källkod|Utvecklarverktyg|
|------------|--------------------|----------|----------------------|
| [Hello World-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statisk webbsida i en container. För Linux används nginx, för Windows används IIS | [Källkod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Inga krav |
| [Räknarapp för Azure-filvolymer](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Lagra tillstånd genom att montera Azure Files-baserad volym i containern. <br><br> **Obs!** Den här mallen kräver att en Azure Files-filresurs redan har etablerats [Instruktioner](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Källkod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh-verktyg |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Skapa ett program med en klientdels- och serverdelstjänst som använder DNS-baserad matchning. Används som en självstudie [här](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Källkod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh-verktyg |
| [Visual Objects-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Skala och uppgradera mikrotjänster i ett program. | [Källkod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh-verktyg |
| [Röstningsapp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Skapa ett program med en klientdels- och serverdelstjänst som använder DNS-baserad matchning | [Källkod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio Mesh Tooling för Windows-versionen, VS Kod / dotnet CLI kan användas för Linux-versionen |
| [Räknarapp för Service Fabric Reliable Disk-volymer](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Lagra tillstånd genom att montera Service Fabric Reliable Disk-baserad volym i containern.| [Källkod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh-verktyg |
