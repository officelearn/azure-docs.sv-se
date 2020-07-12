---
title: Hitta Azure Service Fabric nät-exempel
description: Här är ett index över tillgängliga Service Fabric nät-exempel program. Källkoden i de här exemplen visar hur du uppnår ett visst scenario med hjälp av Service Fabric-resursmodellen.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 64d9e4e112f26f83eedd02b034c8191c6f677618
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261282"
---
# <a name="find-service-fabric-mesh-samples"></a>Hitta Service Fabric Mesh-exempel

I den här tabellen beskrivs tillgängliga Service Fabric Mesh-exempelprogram. Källkoden i de här exemplen visar hur du uppnår ett visst scenario med hjälp av Service Fabric-resursmodellen.

Mer information om hur du distribuerar mallar direkt till Azure finns på [Github-sidan med exempelmallar](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).

|Exempelmall|Scenariobeskrivning|Källkod|Utvecklarverktyg|
|------------|--------------------|----------|----------------------|
| [Hello World-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statisk webbsida i en container. För Linux används nginx, för Windows används IIS | [Käll kod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Inga krav |
| [Räknarapp för Azure-filvolymer](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Lagra tillstånd genom att montera Azure Files-baserad volym i containern. <br><br> **Obs!** Den här mallen kräver att en Azure Files-filresurs redan har etablerats [Instruktioner](../storage/files/storage-how-to-create-file-share.md) | [Käll kod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh-verktyg |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Skapa ett program med en klientdels- och serverdelstjänst som använder DNS-baserad matchning. Används som en självstudie [här](./service-fabric-mesh-tutorial-create-dotnetcore.md) | [Käll kod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh-verktyg |
| [Visual Objects-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Skala och uppgradera mikrotjänster i ett program. | [Käll kod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh-verktyg |
| [Röstningsapp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Skapa ett program med en klientdels- och serverdelstjänst som använder DNS-baserad matchning | [Käll kod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Verktyg för Visual Studio-nätverk för Windows-versionen, VS Code/dotNet CLI kan användas för Linux-versionen |
| [Räknarapp för Service Fabric Reliable Disk-volymer](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Lagra tillstånd genom att montera Service Fabric Reliable Disk-baserad volym i containern.| [Käll kod](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh-verktyg |
