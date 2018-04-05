---
title: Konfigurera en Windows-utvecklingsmiljö för Azure-mikrotjänster | Microsoft Docs
description: Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program i Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2018
ms.author: ryanwi, mikhegn
ms.openlocfilehash: 8e0898cf8046443728f92a8e05f17e51221fe60a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-your-development-environment-on-windows"></a>Förbereda utvecklingsmiljön i Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

För att kunna skapa och köra [Azure Service Fabric-program][1] på en Windows-utvecklingsdator måste du installera Service Fabric-körmiljön, SDK och verktyg. Du måste även [aktivera körning av Windows PowerShell-skript](#enable-powershell-script-execution) som ingår i SDK:n.

## <a name="prerequisites"></a>Nödvändiga komponenter
### <a name="supported-operating-system-versions"></a>Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7-support:
> - Windows 7 innehåller bara Windows PowerShell 2.0 som standard. Service Fabric PowerShell-cmdletar kräver PowerShell 3.0 eller senare. Du kan [ladda ned Windows PowerShell 5.0][powershell5-download] från Microsoft Download Center.
> - Service Fabric Reverse Proxy är inte tillgänglig på Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Installera SDK och verktyg
### <a name="to-use-visual-studio-2017"></a>Använda Visual Studio 2017
Service Fabric-verktygen är en del av arbetsbelastningen i Azure Development i Visual Studio 2017. Aktivera den här arbetsbelastningen som en del av Visual Studio-installationen.
Du måste också installera Microsoft Azure Service Fabric SDK:n och körmiljön med hjälp av installationsprogrammet för webbplattform.

* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Använda Visual Studio 2015 (kräver Visual Studio 2015 Update 2 eller senare)
För Visual Studio 2015 installeras Service Fabric-verktygen tillsammans med SDK:n och körmiljön med hjälp av installationsprogrammet för webbplattform:

* [Installera Microsoft Azure Service Fabric SDK och verktyg][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK-installation endast
Om du bara behöver SDK kan du installera det här paketet:
* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

De aktuella versionerna är:
* Service Fabric SDK och verktyg 3.0.467
* Service Fabric-körning 6.1.467
* Service Fabric Tools för Visual Studio 2015 2.0.10124.2
* Visual Studio 2017 15.5.6 innehåller Service Fabric-verktyg för Visual Studio 2.0.20180124.2  

En lista över versioner som stöds finns i [Service Fabric-stöd](service-fabric-support.md)

## <a name="enable-powershell-script-execution"></a>Aktivera körning av PowerShell-skript
Service Fabric använder Windows PowerShell-skript för att skapa ett lokalt utvecklingskluster och för att distribuera program från Visual Studio. Som standard blockerar Windows dessa skript så att de inte kan köras. För att aktivera dem måste du ändra PowerShell-körningsprincipen. Öppna PowerShell som administratör och ange följande kommando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat utvecklingsmiljön ska du börja bygga och köra appar.

* [Skapa ditt första Service Fabric-program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Lär dig hur du distribuerar och hanterar program i ditt lokala kluster](service-fabric-get-started-with-a-local-cluster.md)
* [Förbereda en Linux-utvecklingsmiljö i Windows](service-fabric-local-linux-cluster-windows.md)
* [Lär dig mer om programmeringsmodeller: Reliable Services och Reliable Actors](service-fabric-choose-framework.md)
* [Kolla in Service Fabric-kodexemplen på GitHub](https://aka.ms/servicefabricsamples)
* [Visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Följ utbildningsvägen för Service Fabric för en bred introduktion till plattformen](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)
* [Automatisera uppdatering av operativsystemet i klustret](service-fabric-patch-orchestration-application.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric-kampanjsida"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Länk till VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Länk till Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Länk till Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
