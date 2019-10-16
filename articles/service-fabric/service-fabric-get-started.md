---
title: Konfigurera en Windows-utvecklingsmiljö för Azure-mikrotjänster | Microsoft Docs
description: Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program i Windows.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2019
ms.author: atsenthi
ms.openlocfilehash: cc77995e12d756c5878ee62ef6dd0f0229506d4c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333078"
---
# <a name="prepare-your-development-environment-on-windows"></a>Förbereda utvecklingsmiljön i Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Om du vill skapa och köra [Azure Service Fabric-program][1] på Windows Development-datorn installerar du Service Fabric runtime, SDK och verktyg. Du måste även [aktivera körning av Windows PowerShell-skript](#enable-powershell-script-execution) som ingår i SDK:n.

## <a name="prerequisites"></a>Krav
### <a name="supported-operating-system-versions"></a>Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7-support:
> - Windows 7 innehåller bara Windows PowerShell 2.0 som standard. Service Fabric PowerShell-cmdletar kräver PowerShell 3.0 eller senare. Du kan [Hämta Windows PowerShell 5,0][powershell5-download] från Microsoft Download Center.
> - Service Fabric Reverse Proxy är inte tillgänglig på Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Installera SDK och verktyg
Installations programmet för webb plattformen (WebPI) är det rekommenderade sättet att installera SDK och verktyg. Om du får körnings fel med hjälp av WebPI kan du också hitta direkt länkar till installations programmet i viktig information för en viss Service Fabric-version. Viktig information finns i de olika versions meddelandena på [Service Fabric teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Uppgraderingar av lokala Service Fabric utvecklings kluster stöds inte.

### <a name="to-use-visual-studio-2017-or-2019"></a>Använda Visual Studio 2017 eller 2019
Service Fabrics verktygen är en del av arbets belastningen Azure Development i Visual Studio 2017 och 2019. Aktivera den här arbetsbelastningen som en del av Visual Studio-installationen.
Du måste också installera Microsoft Azure Service Fabric SDK:n och körmiljön med hjälp av installationsprogrammet för webbplattform.

* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Använda Visual Studio 2015 (kräver Visual Studio 2015 Update 2 eller senare)
För Visual Studio 2015 installeras Service Fabric-verktygen tillsammans med SDK:n och körmiljön med hjälp av installationsprogrammet för webbplattform:

* [Installera Microsoft Azure Service Fabric SDK och verktyg][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK-installation endast
Om du bara behöver SDK kan du installera det här paketet:
* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

De aktuella versionerna är:
* Service Fabric SDK och verktyg 3.4.676
* Service Fabric runtime-6.5.676
* Service Fabric verktyg för Visual Studio 2015 2.5.20615.1
* Visual Studio 2017 15,9 innehåller Service Fabric verktyg för Visual Studio-2.4.11024.1 
* Visual Studio 2019 16,1 innehåller Service Fabric verktyg för Visual Studio-2.5.20423.3

En lista över versioner som stöds finns i [Service Fabric versioner](service-fabric-versions.md)

> [!NOTE]
> Ett enda dator kluster (Onebox behållaravbildningen) stöds inte för program-eller kluster uppgraderingar. ta bort Onebox behållaravbildningen-klustret och återskapa det om du behöver göra en kluster uppgradering eller om du har problem med att utföra en program uppgradering. 

## <a name="enable-powershell-script-execution"></a>Aktivera körning av PowerShell-skript
Service Fabric använder Windows PowerShell-skript för att skapa ett lokalt utvecklingskluster och för att distribuera program från Visual Studio. Som standard blockerar Windows dessa skript så att de inte kan köras. För att aktivera dem måste du ändra PowerShell-körningsprincipen. Öppna PowerShell som administratör och ange följande kommando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Installera Docker (valfritt)
[Service Fabric är en behållar Orchestrator](service-fabric-containers-overview.md) för att distribuera mikrotjänster i ett kluster med datorer. Om du vill köra Windows-behållar program i ditt lokala utvecklings kluster måste du först installera Docker för Windows. Hämta [Docker CE för Windows (stabilt)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Efter installationen startar du Docker, högerklickar på ikonen för fack och väljer **Switch to Windows containers** (Växla till Windows-behållare). Detta steg krävs för att köra Docker-avbildningar baserade på Windows.

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat utvecklingsmiljön ska du börja bygga och köra appar.

* [Lär dig hur du skapar, distribuerar och hanterar program](service-fabric-tutorial-create-dotnet-app.md)
* [Lär dig mer om programmeringsmodeller: Reliable Services och Reliable Actors](service-fabric-choose-framework.md)
* [Kolla in Service Fabric-kodexemplen på GitHub](https://aka.ms/servicefabricsamples)
* [Visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-kampanjsida"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Länk till VS 2015 WebPI"
[full-bundle-dev15]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Länk till Dev15 WebPI"
[core-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Länk till Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
