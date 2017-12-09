---
title: "Distribuera ett ASP.NET Core Linux Docker-behållare till Docker fjärrvärden | Microsoft Docs"
description: "Lär dig hur du använder Visual Studio Tools för Docker för att distribuera en ASP.NET Core-webbapp till en dockerbehållare som körs på en virtuell Azure Docker värden Linux-dator"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Distribuera ett ASP.NET-behållare till Docker fjärrvärden
## <a name="overview"></a>Översikt
Docker är en förenklad behållaren motor likheter till en virtuell dator som du kan använda till värd-program och tjänster.
Den här självstudiekursen vägleder dig genom med hjälp av den [Visual Studio Tools för Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) tillägg för att distribuera en ASP.NET Core app till en Docker-värd i Azure med hjälp av PowerShell.

## <a name="prerequisites"></a>Krav
Följande krävs för att slutföra den här självstudiekursen:

* Skapa en Azure Docker värden VM enligt beskrivningen i [använda docker-datorn med Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Installera den senaste versionen av [Visual Studio](https://www.visualstudio.com/downloads/)
* Hämta den [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
* Installera [Docker för Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Skapa ett webbprogram med ASP.NET Core
Följande steg hur du skapar en grundläggande app i ASP.NET Core som ska användas i den här självstudiekursen.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Lägga till Docker-stöd
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Använda DockerTask.ps1 PowerShell-skript
1. Öppna ett PowerShell-Kommandotolken till rotkatalogen för projektet. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Verifiera fjärransluten värden körs. Du bör se tillstånd = körs 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. Bygga appen med parametern - version
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Kör appen med hjälp av parametern - kör
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   När docker är klar bör du se resultat som liknar följande:
   
   ![Visa din app][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
