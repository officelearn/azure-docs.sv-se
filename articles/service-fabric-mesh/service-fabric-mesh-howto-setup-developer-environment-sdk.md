---
title: Konfigurera en Windows-utvecklingsmiljö för att skapa Service Fabric Mesh-program | Microsoft Docs
description: Ställa in Windows-utvecklingsmiljön så att du kan skapa ett Service Fabric Mesh-program och distribuera det till Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: bec0b9a7e34f1577f80a99f5380795c479c04bc8
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890474"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Konfigurera en Windows-utvecklingsmiljö för att skapa Service Fabric Mesh-program

För att kunna skapa och köra Azure Service Fabric Mesh-program på en Windows-utvecklingsdator måste du installera Service Fabric Mesh-körmiljön, SDK och verktyg.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Operativsystemversioner som stöds

Följande operativsystemversioner stöds för utveckling:

* Windows 10 (Enterprise, Professional, och Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 krävs för att distribuera Service Fabric Mesh-program. [Installera version 15.6.0] [ download-visual-studio] eller senare och aktivera följande arbetsbelastningar:

* ASP.NET och webbutveckling
* Azure Development

## <a name="install-docker"></a>Installera Docker

#### <a name="windows-10"></a>Windows 10

Ladda ned och installera den senaste versionen av [Docker Community Edition för Windows][download-docker] för att stödja de containerbaserade Service Fabric-appar som används av Service Fabric Mesh.

Under installationen väljer du att **använda Windows-container i stället för Linux-container** när frågan dyker upp.

Om Hyper-V inte är aktiverat på datorn kommer installationsprogrammet för Docker att erbjuda att aktivera det. Klicka på **OK** för att göra det om du tillfrågas.

#### <a name="windows-server-2016"></a>Windows Server 2016

Om du inte har Hyper-V-rollen aktiverad öppnar du PowerShell som administratör och kör följande kommando för att aktivera Hyper-V, och startar sedan om datorn. Mer information finns i dokumentationen om [Docker Enterprise Edition för Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Starta om datorn.

Öppna PowerShell som administratör och kör följande kommando för att installera Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK och verktyg

Installera Service Fabric Mesh-runtime, SDK och verktyg i följande ordning.

1. Installera [SDK för Service Fabric Mesh][download-sdkmesh] med installationsprogrammet för webbplattform. Det här installerar dessutom Microsoft Azure Service Fabric SDK och runtime.
2. Installera tillägget [Visual Studio Service Fabric Mesh Tools (förhandsversion)][download-tools] från Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Bygga ett kluster

> [!IMPORTANT]
> Docker **måste** köras innan du kan skapa ett kluster.
> Testa att Docker körs genom att öppna ett terminalfönster och köra `docker ps` för att se om det uppstår något fel. Om svaret inte indikerar något fel körs Docker och du är redo att skapa ett kluster.

Om du använder Visual Studio kan du hoppa över det här avsnittet eftersom Visual Studio skapar ett lokalt kluster om du inte har något.

För bästa felsökningsprestanda när du skapar och kör Service Fabric-program rekommenderar vi att du skapar ett lokalt utvecklingskluster för en nod. Det här klustret måste köras varje gång du distribuerar eller felsöker ett Service Fabric Mesh-projekt.

När du har installerat runtime, SDK:er, Visual Studio Tools, Docker och har Docker igång skapar du ett utvecklingskluster.

1. Stäng PowerShell-fönstret.
2. Öppna ett nytt, upphöjt PowerShell-fönster som administratör. Det här steget behövs för att läsa in de Service Fabric-moduler som installerades nyligen.
3. Kör följande PowerShell-kommando för att skapa till ett utvecklingskluster:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Kör följande PowerShell-kommando för att starta det lokala klusterhanteringsverktyget:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. När tjänstens klusterhanteringsverktyg körs (visas i systemfältet) högerklickar du på det och klickar på **Start Local Cluster** (Starta lokalt cluster).

![Bild 1 – Starta det lokala klustret](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Nu är du redo att skapa Service Fabric Mesh-program!

## <a name="next-steps"></a>Nästa steg

Läs igenom självstudien om att [skapa Azure Service Fabric-program](service-fabric-mesh-tutorial-create-dotnetcore.md).

Få svar på [vanliga frågor](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/