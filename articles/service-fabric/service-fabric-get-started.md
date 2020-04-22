---
title: Konfigurera en Windows-utvecklingsmiljö
description: Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program i Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/20/2020
ms.custom: sfrev
ms.openlocfilehash: 1b43c838537e46ffbaf6c4adcfb117f6718bd046
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731759"
---
# <a name="prepare-your-development-environment-on-windows"></a>Förbereda utvecklingsmiljön i Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>

För att kunna skapa och köra [Azure Service Fabric-program][1] på en Windows-utvecklingsdator måste du installera Service Fabric-körmiljön, SDK och verktyg. Du måste också [aktivera körning av Windows PowerShell-skript](#enable-powershell-script-execution) som ingår i SDK.

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
> - Windows 7 innehåller bara Windows PowerShell 2.0 som standard. Service Fabric PowerShell-cmdletar kräver PowerShell 3.0 eller senare. Du kan [hämta Windows PowerShell 5.1][powershell5-download] från Microsoft Download Center.
> - Service Fabric Reverse Proxy är inte tillgänglig på Windows 7.

## <a name="install-the-sdk-and-tools"></a>Installera SDK och verktyg

WebPi (Web Platform Installer) är det rekommenderade sättet att installera SDK och verktyg. Om du får körningsfel med WebPI kan du även hitta direkta länkar till installationsapparna i viktig information för en viss Service Fabric-version. Den viktig information kan hittas i de olika release meddelanden på [Service Fabric team blogg](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Uppgraderingar av lokal tjänst fabric-utvecklingskluster stöds inte.

### <a name="to-use-visual-studio-2017-or-2019"></a>Så här använder du Visual Studio 2017 eller 2019

Service Fabric Tools är en del av Azure Development-arbetsbelastningen i Visual Studio 2017 och 2019. Aktivera den här arbetsbelastningen som en del av Visual Studio-installationen.
Du måste också installera Microsoft Azure Service Fabric SDK:n och körmiljön med hjälp av installationsprogrammet för webbplattform.

* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Använda Visual Studio 2015 (kräver Visual Studio 2015 Update 2 eller senare)

För Visual Studio 2015 installeras Service Fabric-verktygen tillsammans med SDK:n och körmiljön med hjälp av installationsprogrammet för webbplattform:

* [Installera Microsoft Azure Service Fabric SDK och Verktyg][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK-installation endast

Om du bara behöver SDK kan du installera det här paketet:

* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

De aktuella versionerna är:

* Service Fabric SDK och verktyg 4.1.409
* Service Fabric körtid 7.1.409

En lista över versioner som stöds finns i [Service Fabric-versioner](service-fabric-versions.md)

> [!NOTE]
> OneBox (Single Machine Clusters) stöds inte för uppgraderingar av program eller kluster. ta bort OneBox-klustret och återskapa det om du behöver utföra en klusteruppgradering eller har några problem med att utföra en programuppgradering. 

## <a name="enable-powershell-script-execution"></a>Aktivera körning av PowerShell-skript

Service Fabric använder Windows PowerShell-skript för att skapa ett lokalt utvecklingskluster och för att distribuera program från Visual Studio. Som standard blockerar Windows dessa skript så att de inte kan köras. För att aktivera dem måste du ändra PowerShell-körningsprincipen. Öppna PowerShell som administratör och ange följande kommando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Installera Docker (valfritt)

[Service Fabric är en behållare orchestrator](service-fabric-containers-overview.md) för distribution av mikrotjänster över ett kluster av datorer. Om du vill köra Windows-behållarprogram i det lokala utvecklingsklustret måste du först installera Docker för Windows. Skaffa [Docker CE för Windows (stabil)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Efter installationen startar du Docker, högerklickar på ikonen för fack och väljer **Switch to Windows containers** (Växla till Windows-behållare). Detta steg krävs för att köra Docker-avbildningar baserade på Windows.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat utvecklingsmiljön ska du börja bygga och köra program.

* [Lär dig hur du skapar, distribuerar och hanterar program](service-fabric-tutorial-create-dotnet-app.md)
* [Lär dig mer om programmeringsmodeller: Reliable Services och Reliable Actors](service-fabric-choose-framework.md)
* [Kolla in Service Fabric-kodexemplen på GitHub](https://aka.ms/servicefabricsamples)
* [Visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-kampanjsida"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Länk till VS 2015 WebPI"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Länk till Dev15 WebPI"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Länk till Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
