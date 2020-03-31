---
title: Skapa och publicera a.Net Core-appen till ett avlägset Linux-kluster
description: Skapa och publicera .Net Core-appar som riktar sig till ett fjärran Linux-kluster från Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614357"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Använda Visual Studio för att skapa och publicera .Net Core-program som riktar sig till ett avlägset Linux Service Fabric-kluster
Med Visual Studio-verktyg kan du utveckla och publicera Service Fabric .Net Core-program som riktar sig till ett Linux Service Fabric-kluster. SDK-versionen måste vara 3.4 eller högre för att kunna distribuera ett .Net Core-program som riktar sig till Linux Service Fabric-kluster från Visual Studio.

> [!Note]
> Visual Studio stöder inte felsökning av Service Fabric-program som riktar sig till Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Skapa ett program för tjänstinfrastruktur som är inriktade på .Net Core
1. Starta Visual Studio som **administratör**.
2. Skapa ett projekt med **>>Project**.
3. Välj **Cloud -> Service Fabric Application**i dialogrutan Nytt **projekt** .
![skapa-program]
4. Namnge programmet och klicka på **Ok**.
5. På sidan **Ny tjänstinfrastrukturtjänst** väljer du den typ av tjänst som du vill skapa under **avsnittet .Net Core**.
![skapa-tjänst]

## <a name="deploy-to-a-remote-linux-cluster"></a>Distribuera till ett fjärr-Linux-kluster
1. Högerklicka på programmet i lösningsutforskaren och välj **Bygg**.
![build-program]
2. När byggprocessen för programmet har slutförts högerklickar du på tjänsten och väljer redigera **csproj-filen**.
![redigera-csproj]
3. Redigera egenskapen UpdateServiceFabricManifestEnabled från Sant till **Falskt** om tjänsten är en **aktörsprojekttyp**. Om ditt program inte har någon aktörstjänst går du vidare till steg 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Ställa in UpdateServiceFabricManifestEnabled till false, kommer att inaktivera uppdateringar av ServiceManifest.xml under en version. Ändringar som att lägga till, ta bort eller byta namn på tjänsten återspeglas inte i ServiceManifest.xml. Om några ändringar görs måste du antingen uppdatera ServiceManifest manuellt eller tillfälligt ställa in UpdateServiceFabricManifestEnabled till true och bygga tjänsten som kommer att uppdatera ServiceManifest.xml och sedan återställa den till false.
>

4. Uppdatera RuntimeIndetifier från win7-x64 till målplattformen i serviceprojektet.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Uppdatera entrypoint-programmet för att ta bort .exe i ServiceManifest. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. Högerklicka på programmet i Solution Explorer och välj **Publicera**. Dialogrutan **Publish** (Publicera) visas.
7. I **Anslutningsslutpunkten**väljer du slutpunkten för det fjärrprogram Linux-kluster för Service Fabric som du vill rikta in dig på.
![publicera-ansökan]

<!--Image references-->
[skapa-program]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[skapa-tjänst]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-program]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[redigera-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publicera-ansökan]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Hur du kommer igång med Service Fabric med .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
