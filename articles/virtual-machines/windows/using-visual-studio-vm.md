---
title: Med Visual Studio på en virtuell Azure-dator | Microsoft Docs
description: Med Visual Studio på en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 03/02/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 7809e0b9265ec62bded67d0b0ed851ee0ba98315
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301707"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-avbildningar i Azure
Med Visual Studio på en förkonfigurerad Azure virtuell dator (VM) är ett snabbt, enkelt sätt att gå från grunden till en upp och körs utvecklingsmiljö. -Avbildningar med olika konfigurationer för Visual Studio finns i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=%22visual%20studio%202017%22&page=1).

Ny Azure-användare? [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Vilka konfigurationer och versioner är tillgängliga?
Avbildningar för de senaste större versionerna, Visual Studio 2017 och Visual Studio 2015, finns i Azure Marketplace. För varje huvudversion visas den ursprungligen (RTW) versionen och de senaste uppdaterade versionerna. Var och en av dessa versioner erbjuder Visual Studio Enterprise- och Visual Studio Community-utgåvor. Dessa avbildningar uppdateras minst varje månad för att inkludera de senaste uppdateringarna för Visual Studio och Windows. När namnen på avbildningarna förblir detsamma, innehåller varje avbildningsbeskrivningen installerade produktversionen och bildens ”för” datum.

| Versionen                                              | Versioner                     |     Produktversion     |
|:------------------------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017: Senaste (Version 15.7)                    |    Enterprise, Community     |      Version 15.7.3     |
| Visual Studio 2017: Senaste Preview (Version 15.8 Preview 2) |    Enterprise, Community     |      Version 15.8.2     |
|         Visual Studio 2017: RTW                              |    Enterprise, Community     |      Version 15.0.13    |
|   Visual Studio 2015: Senaste (uppdatering 3)                      |    Enterprise, Community     |  Version 14.0.25431.01  |
|         Visual Studio 2015: RTW                              |             Ingen             | (Upphört att gälla för underhåll) |

> [!NOTE]
> I enlighet med Microsoft behandling av princip, den ursprungligen (RTW) versionen av Visual Studio 2015 har upphört att gälla för behandling. Visual Studio 2015 Update 3 är den enda återstående versionen som erbjuds för produktserie Visual Studio 2015.

Mer information finns i [Visual Studio Servicing princip](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Vilka funktioner är installerade?
Varje avbildning innehåller rekommenderade funktionerna i den Visual Studio-versionen. I allmänhet innehåller installationen:

* Alla tillgängliga arbetsbelastningar, inklusive varje arbetsbelastning rekommenderas valfria komponenter
* .NET 4.6.2 och .NET 4.7 SDK: er, målobjekt Pack och utvecklingsverktygen
* Visual F #
* GitHub-tillägget för Visual Studio
* LINQ to SQL-verktyg

Kommandoraden som används för att installera Visual Studio när du bygger avbildningar är följande:

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

Om bilderna inte innehåller en funktion i Visual Studio som du behöver kan du ge feedback via verktyget feedback i det övre högra hörnet på sidan.

## <a name="what-size-vm-should-i-choose"></a>Hur stor VM ska jag välja?
Azure erbjuder en mängd olika storlekar för virtuella datorer. Eftersom Visual Studio är ett kraftfullt, flertrådade program behöver du en VM-storlek som innehåller minst två processorer och 7 GB minne. Vi rekommenderar följande VM-storlekar för Visual Studio-bilder:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Mer information om de senaste datorstorlekar finns [storlekar för Windows-datorer i Azure](/azure/virtual-machines/windows/sizes).

Med Azure, kan du balansera ditt första val om genom att ändra storlek på den virtuella datorn. Du kan etablera en ny virtuell dator med en lämpligare storlek eller ändra storlek på en befintlig virtuell dator till olika underliggande maskinvara. Mer information finns i [ändra storlek på en Windows VM](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>När den virtuella datorn körs, vad kommer härnäst?
Visual Studio följer ”bring your own license” modellen i Azure. Precis som med en installation på Företagsspecifik maskinvara en av de första stegen licensiering installationen av Visual Studio. Att låsa upp Visual Studio, antingen:
- Logga in med ett Microsoft-konto som är kopplad till en prenumeration på Visual Studio 
- Lås upp Visual Studio med produktnyckeln som medföljde din köptillfället

Mer information finns i [logga in i Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) och [så att låsa upp Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hur använder jag spara för framtida utveckling VM eller team?

Spektrum av utvecklingsmiljöer är stort och det är verkliga kostnaden för att skapa mer komplexa miljöer. Oavsett konfiguration för din miljö kan du spara eller avbilda dina konfigurerade VM som en ”grundläggande bild” för framtida användning eller för andra medlemmar i gruppen. Sedan, när du startar en ny virtuell dator du etablerar den från basavbildningen i stället för Azure Marketplace-avbildning.

En snabb översikt: använda systemförberedelseverktyget (Sysprep) och Stäng körande VM och sedan avbilda *(bild 1)* den virtuella datorn som en bild via Användargränssnittet i Azure-portalen. Azure sparar den `.vhd` -fil som innehåller bilden i storage-konto du väljer. Den nya avbildningen visas sedan som en bildresurs i din prenumeration lista över resurser.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Bild 1) Spara en avbildning via Azure portal Användargränssnittet.*</center>

Mer information finns i [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Glöm inte att använda Sysprep för att förbereda den virtuella datorn. Om du missar steget kan inte Azure etablera en virtuell dator från avbildningen.

> [!NOTE]
> Du fortfarande innebära att vissa kostnaden för lagring av avbildningarna, men inkrementella kostnaden kan obetydlig jämfört med omkostnaderna att återskapa den virtuella datorn från grunden för varje medlem i gruppen som behöver en. Det kostar exempelvis några kronor att skapa och spara en avbildning av 127 GB för en månad är återanvändbara genom hela gruppen. Dessa kostnader är dock obetydlig jämfört med timmarna varje medarbetare investerar för att bygga ut och validera en korrekt konfigurerad dev ruta för sin egen användning.

Dessutom behöva utvecklingen aktiviteter eller tekniker mer skala ut av konfigurationer för utveckling och konfigurationer med flera datorer. Du kan använda Azure DevTest Labs för att skapa _recept_ som automatiserar konstruktion av din ”gyllene bild”. Du kan också använda DevTest Labs för att hantera principer för din grupp virtuella datorer som körs. [Med hjälp av Azure DevTest Labs för utvecklare](/azure/devtest-lab/devtest-lab-developer-lab) är den bästa källan för mer information om DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Nu när du vet om de förinställda Visual Studio-avbildningarna, är nästa steg att skapa en ny virtuell dator:

* [Skapa en virtuell dator via Azure portal](quick-create-portal.md)
* [Översikt över Windows-datorer](overview.md)
