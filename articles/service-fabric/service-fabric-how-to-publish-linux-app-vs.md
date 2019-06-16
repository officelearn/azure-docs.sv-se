---
title: Lär dig mer om att skapa och publicera .net Core program till ett Azure Service Fabric Linux-fjärrkluster | Microsoft Docs
description: Skapa och publicera .net Core appar som riktar in sig på ett fjärranslutet Linux-kluster från Visual Studio
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078669"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Använda Visual Studio för att skapa och publicera .net Core program som riktar in sig på ett fjärranslutet Linux Service Fabric-kluster
Med Visual Studio verktyg du kan utveckla och publicera Service Fabric .net Core program som riktar in sig på ett Linux Service Fabric-kluster. SDK-version måste vara 3.4 eller senare för att distribuera en .net Core-program som riktar in sig på Linux Service Fabric-kluster från Visual Studio.

> [!Note]
> Visual Studio stöder inte felsökning Service Fabric-program som riktar Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Skapa ett Service Fabric-program som riktar in sig på .net Core
1. Starta Visual Studio som **administratör**.
2. Skapa ett projekt med **File -> Nytt projekt ->** .
3. I den **nytt projekt** dialogrutan Välj **moln > Service Fabric-program**.
![create-application]
4. Ge programmet namnet och klickar på **Ok**.
5. På den **ny Service Fabric-tjänst** väljer du den typ av tjänst som du vill skapa under den **.Net Core avsnittet**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Distribuera till ett fjärrkluster med Linux
1. I solution explorer högerklickar du på programmet och välj **skapa**.
![build-application]
2. När build-process för programmet är klar högerklickar du på tjänsten och välj Redigera den **csproj-filen**.
![edit-csproj]
3. Redigera egenskapen UpdateServiceFabricManifestEnabled från SANT till **FALSKT** om tjänsten är en **aktören projekttyp**. Om ditt program inte har en actor-tjänst, kan du gå vidare till steg 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Ange UpdateServiceFabricManifestEnabled till false, inaktiverar uppdateringar till ServiceManifest.xml under en version. Ändringar sådana visas lägger till, ta bort eller byta namn på att tjänsten inte i ServiceManifest.xml. Om några ändringar görs du måste antingen uppdatera ServiceManifest tillfälligt eller manuellt ange UpdateServiceFabricManifestEnabled till true och bygg in tjänsten som kommer att uppdateras ServiceManifest.xml och sedan återgår den tillbaka till false.
>

4. Uppdatera RuntimeIndetifier från win7 x64 för mål-plattformen i service-projekt.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Uppdatera entrypoint-program för att ta bort .exe i ServiceManifest. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. I Solution Explorer högerklickar du på programmet och välj **publicera**. Dialogrutan **Publish** (Publicera) visas.
7. I **Anslutningsslutpunkten**, väljer du slutpunkten för Service Fabric Linux-fjärrklustret som du vill använda som mål.
![publicera program]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publicera program]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [komma igång med Service Fabric med .net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
