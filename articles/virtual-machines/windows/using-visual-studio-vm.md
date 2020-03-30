---
title: Använda Visual Studio på en virtuell Azure-dator
description: Använda Visual Studio på en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cathysull
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 12/04/2019
ms.author: cathys
keywords: Visualstudio
ms.openlocfilehash: 2977fb6f14468429eb651e4f8a6034c060cdee2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895951"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-avbildningar på Azure
Att använda Visual Studio i en förkonfigurerad virtuell Azure-dator (VM) är ett snabbt och enkelt sätt att gå från ingenting till en upp-och-fungerande utvecklingsmiljö. Systemavbildningar med olika Visual Studio-konfigurationer är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Ny Azure-användare? [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free).

> [!NOTE]
> Alla prenumerationer är inte berättigade att distribuera Windows 10-avbildningar. Mer information finns [i Använda Windows-klienten i Azure för utvecklings-/testscenarier](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)

## <a name="what-configurations-and-versions-are-available"></a>Vilka konfigurationer och versioner är tillgängliga?
Avbildningar för de senaste huvudversionerna, Visual Studio 2019, Visual Studio 2017 och Visual Studio 2015, finns på Azure Marketplace.  För varje utgiven huvudversion ser du den ursprungligen "släppt till webben" (RTW) version och de senaste uppdaterade versionerna.  Var och en av dessa versioner erbjuder Visual Studio Enterprise och Visual Studio Community-utgåvorna.  Dessa bilder uppdateras minst varje månad för att inkludera de senaste Visual Studio- och Windows-uppdateringarna.  Även om namnen på bilderna förblir desamma, innehåller varje bilds beskrivning den installerade produktversionen och bildens "från och med" datum.

| Version av utgivning                                                                                                                                                | Utgåvor              | Produktversion   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Senaste (Version 16.4)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Företag, Gemenskapen | Version 16.4.0    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Företag, Gemenskapen | Version 16.0.9    |
| [Visual Studio 2017: Senaste (Version 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Företag, Gemenskapen | Version 15.9.17   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Företag, Gemenskapen | Version 15.0.27  |
| [Visual Studio 2015: Senaste (Uppdatering 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Företag, Gemenskapen | Version 14.0.25431.01 |

> [!NOTE]
> I enlighet med Microsofts servicepolicy har den ursprungligen utgivna (RTW) versionen av Visual Studio 2015 gått ut för service. Visual Studio 2015 Update 3 är den enda återstående versionen som erbjuds för visual studio 2015-produktlinjen.

Mer information finns i [Visual Studio-serviceprincipen](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Vilka funktioner är installerade?
Varje bild innehåller den rekommenderade funktionsuppsättningen för den Visual Studio-utgåvan. I allmänhet omfattar installationen:

* Alla tillgängliga arbetsbelastningar, inklusive varje arbetsbelastnings rekommenderade valfria komponenter
* .NET 4.6.2 och .NET 4.7 SDK:er, inriktningspaket och utvecklarverktyg
* Visuell f #
* GitHub-tillägg för Visual Studio
* LINQ till SQL-verktyg

Den kommandorad som används för att installera Visual Studio när du skapar avbildningarna är följande:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Om bilderna inte innehåller en Visual Studio-funktion som du behöver kan du ge feedback via feedbackverktyget i det övre högra hörnet på sidan.

## <a name="what-size-vm-should-i-choose"></a>Vilken virtuell dator i storlek ska jag välja?
Azure erbjuder ett komplett utbud av storlekar för virtuella datorer. Eftersom Visual Studio är ett kraftfullt program med flera trådar vill du ha en vm-storlek som innehåller minst två processorer och 7 GB minne. Vi rekommenderar följande VM-storlekar för Visual Studio-avbildningar:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Mer information om de senaste datorstorlekarna finns i [Storlekar för virtuella Windows-datorer i Azure](/azure/virtual-machines/windows/sizes).

Med Azure kan du balansera om ditt första val genom att ändra storlek på den virtuella datorn. Du kan antingen etablera en ny virtuell dator med en lämpligare storlek eller ändra storlek på den befintliga virtuella datorn till olika underliggande maskinvara. Mer information finns i [Ändra storlek på en Virtuell Dator](/azure/virtual-machines/windows/resize-vm)för Windows .

## <a name="after-the-vm-is-running-whats-next"></a>Vad händer nu när den virtuella datorn har körts?
Visual Studio följer modellen "Ta med din egen licens" i Azure. Precis som med en installation på proprietär maskinvara är ett av de första stegen att licensiera visual studio-installationen. Så här låser du upp Visual Studio:
- Logga in med ett Microsoft-konto som är kopplat till en Visual Studio-prenumeration 
- Lås upp Visual Studio med produktnyckeln som medföljde ditt första köp

Mer information finns [i Logga in i Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) och så här [låser du upp Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hur sparar jag utvecklingsdasssen för framtida eller teamanvändning?

Spektrumet av utvecklingsmiljöer är enormt, och det finns verkliga kostnader i samband med att bygga ut de mer komplexa miljöer. Oavsett konfigurationen av din miljö kan du spara eller fånga den konfigurerade virtuella datorn som en "basavbildning" för framtida bruk eller för andra medlemmar i ditt team. När du sedan startar en ny virtuell dator etablerar du den från basavbildningen i stället för Azure Marketplace-avbildningen.

En snabb sammanfattning: Använd systemförberedelseverktyget (Sysprep) och stäng av den virtuella datorn som körs och ta sedan *(bild 1)* den virtuella datorn som en avbildning via användargränssnittet i Azure-portalen. Azure sparar `.vhd` filen som innehåller avbildningen i det lagringskonto du väljer. Den nya bilden visas sedan som en imageresurs i prenumerationens resurslista.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figur 1) Ta en avbildning via Azure Portal UI.*</center>

Mer information finns i [Skapa en hanterad avbildning av en allmän virtuell dator i Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Glöm inte att använda Sysprep för att förbereda den virtuella datorn. Om du missar det steget kan Azure inte etablera en virtuell dator från avbildningen.

> [!NOTE]
> Du ådrar dig fortfarande en viss kostnad för lagring av avbildningarna, men den inkrementella kostnaden kan vara obetydlig jämfört med omkostnaderna för att återskapa den virtuella datorn från grunden för varje gruppmedlem som behöver en. Till exempel kostar det några dollar att skapa och lagra en 127 GB bild för en månad som är återanvändbara av hela teamet. Dessa kostnader är dock obetydliga jämfört med timmar varje anställd investerar för att bygga ut och validera en korrekt konfigurerad utvecklingsruta för deras individuella användning.

Dessutom kan dina utvecklingsuppgifter eller tekniker behöva mer skala, till exempel sorter av utvecklingskonfigurationer och flera datorkonfigurationer. Du kan använda Azure DevTest Labs för att skapa recept som _automatiserar_ byggandet av din "gyllene avbildning". Du kan också använda DevTest Labs för att hantera principer för teamets virtuella datorer som körs. [Att använda Azure DevTest Labs för utvecklare](/azure/devtest-lab/devtest-lab-developer-lab) är den bästa källan för mer information om DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Nu när du känner till de förkonfigurerade Visual Studio-avbildningarna är nästa steg att skapa en ny virtuell dator:

* [Skapa en virtuell dator via Azure-portalen](quick-create-portal.md)
* [Översikt över virtuella datorer i Windows](overview.md)
