---
title: "Konfigurera en utvecklingsmiljö för Azure-mikrotjänster | Microsoft Docs"
description: "Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: ryanwi, mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 91049ee124999269a326106cbd6fc211cc027d26
ms.lasthandoff: 03/22/2017


---
# <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 För att kunna skapa och köra [Azure Service Fabric-program][1] på en utvecklingsdator måste du installera runtime, SDK och verktyg. Du måste även aktivera körning av Windows PowerShell-skript som ingår i SDK.

## <a name="prerequisites"></a>Krav
### <a name="supported-operating-system-versions"></a>Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 innehåller bara Windows PowerShell 2.0 som standard. Service Fabric PowerShell-cmdletar kräver PowerShell 3.0 eller senare. Du kan [ladda ned Windows PowerShell 5.0][powershell5-download] från Microsoft Download Center.
> 
> 

## <a name="install-the-sdk-and-tools"></a>Installera SDK och verktyg
### <a name="to-use-visual-studio-2017"></a>Använda Visual Studio 2017
Service Fabric-verktyg är en del av arbetsbelastningen i Azure Development och Management i Visual Studio 2017. Aktivera den här arbetsbelastningen som en del av Visual Studio-installationen.
Du måste också installera Microsoft Azure Service Fabric SDK, med hjälp av installationsprogrammet för webbplattform.

* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Använda Visual Studio 2015 (kräver Visual Studio 2015 Update 2 eller senare)
För Visual Studio 2015 installeras Service Fabric-verktyg tillsammans med SDK med hjälp av installationsprogrammet för webbplattform:

* [Installera Microsoft Azure Service Fabric SDK och verktyg][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK-installation endast
Om du bara behöver SDK kan du installera det här paketet:
* [Installera Microsoft Azure Service Fabric SDK][core-sdk]

> [!WARNING]
> Kunder har rapporterat fel under installationen när dessa startlänkar används, eller när länkarna används i webbläsaren Chrome. Dessa fel är kända problem i Web Platform Installer som håller på att åtgärdas.  Prova följande lösningar:
>- Starta de föregående länkarna i Internet Explorer eller Edge-webbläsaren, eller
>- Starta Web Platform Installer från Start-menyn, sök efter ”Service Fabric” och installera SDK
> 
> Vi ber om ursäkt för besväret. 

De aktuella versionerna är:
* Service Fabric SDK 2.5.216
* Service Fabric Runtime 5.5.216
* Visual Studio 2015 Tools 1.5.50311.1

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
* [Lär dig mer om programmeringsmodeller: Reliable Services och Reliable Actors](service-fabric-choose-framework.md)
* [Kolla in Service Fabric-kodexemplen på GitHub](https://aka.ms/servicefabricsamples)
* [Visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Följ utbildningsvägen för Service Fabric för en bred introduktion till plattformen](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric-kampanjsida"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Länk till VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Länk till Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Länk till Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

