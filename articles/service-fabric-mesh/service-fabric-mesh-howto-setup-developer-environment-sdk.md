---
title: Konfigurera en Windows-utvecklingsmiljö för Service Fabric Mesh-program | Microsoft Docs
description: Ställa in Windows-utvecklingsmiljön så att du kan skapa ett Service Fabric Mesh-program och distribuera det till Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125169"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Konfigurera en Windows-utvecklingsmiljö för att skapa Service Fabric-program

För att kunna skapa och köra Azure Service Fabric-program på en Windows-utvecklingsdator måste du installera Service Fabric-körmiljön, SDK och verktyg.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Operativsystemversioner som stöds

Följande operativsystemversioner stöds för utveckling:

* Windows 10 (Enterprise, Professional, och Education)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Aktivera Hyper-V

Hyper-V måste vara aktiverat för att du ska kunna skapa Service Fabric-program. 

### <a name="windows-10"></a>Windows 10

Öppna PowerShell som administratör och kör följande kommando:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Starta om datorn. Mer information om hur du aktiverar Hyper-V finns i dokumentationen om att [installera Hyper-V i Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="windows-server-2016"></a>Windows Server 2016

Öppna PowerShell som administratör och kör följande kommando:

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Starta om datorn. Mer information om hur du aktiverar Hyper-V finns i dokumentationen om att [installera Hyper-V-rollen i Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="visual-studio"></a>Visual Studio

Du behöver Visual Studio 2017 för att distribuera Service Fabric-program. [Installera version 15.6.0] [ download-visual-studio] eller senare och aktivera följande arbetsbelastningar:

- ASP.NET och webbutveckling
- Azure Development

## <a name="docker"></a>Docker

Installera Docker för att stödja Service Fabric-program i container som används av Service Fabric Mesh.

### <a name="windows-10"></a>Windows 10

Ladda ned och installera den senaste versionen av [Docker Community Edition for Windows][download-docker]. 

Under installationen väljer du att **använda Windows-container istället för Linux-container** när frågan dyker upp. Du måste sedan logga ut och logga in igen. När du har loggat in igen kan du uppmanas att aktivera Hyper-V om du inte redan har gjort det. Du måste aktivera Hyper-V och sedan starta om datorn.

När datorn har startats om uppmanas du av Docker att aktivera funktionen för **container** och sedan starta om datorn.

### <a name="windows-server-2016"></a>Windows Server 2016

Installera Docker med följande PowerShell-kommandon. Mer information finns i dokumentationen om [Docker Enterprise Edition för Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Starta om datorn.

## <a name="sdk-and-tools"></a>SDK och verktyg

Installera Service Fabric Mesh-runtime, SDK och verktyg i beroendeordning.

1. Installera [SDK för Service Fabric Mesh][download-sdkmesh] med installationsprogrammet för webbplattform. Det här installerar dessutom Microsoft Azure Service Fabric SDK och runtime.
2. Installera tillägget [Visual Studio Service Fabric Tools (förhandsversion)][download-tools] från Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Bygga ett kluster

För bästa felsökningsprestanda när du skapar och kör Service Fabric-program rekommenderar vi att du skapar ett lokalt utvecklingskluster för en nod. Det här klustret måste köras varje gång du distribuerar eller felsöker ett Service Fabric Mesh-projekt.

Docker **måste** köras innan du kan skapa ett kluster. Testa att Docker körs genom att öppna ett terminalfönster och köra `docker ps` för att se om det uppstår något fel. Om svaret inte indikerar något fel körs Docker och du är redo att skapa ett kluster.

När du har installerat runtime, SDK:er, och Visual Studio-verktygen ska du skapa ett utvecklingskluster.

1. Stäng PowerShell-fönstret.
2. Öppna ett nytt, upphöjt PowerShell-fönster som administratör. Det här steget behövs för att läsa in Service Fabric-moduler som du har installerat.
3. Kör följande PowerShell-kommando för att skapa till ett utvecklingskluster:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Kör följande PowerShell-kommando för att starta det lokala klusterhanteringsverktyget:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Nu är du redo att skapa Service Fabric Mesh-program!

## <a name="next-steps"></a>Nästa steg

Läs igenom självstudien om att [skapa Azure Service Fabric-program](service-fabric-mesh-tutorial-create-dotnetcore.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/
