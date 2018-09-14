---
title: Med Visual Studio på en Azure-dator | Microsoft Docs
description: Med Visual Studio på en Azure-dator.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 09/12/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 934f9a7eb626c94ed35a17a5b385b850fbbbe537
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542658"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-avbildningar på Azure
Med Visual Studio på en förkonfigurerad Azure-dator (VM) är ett snabbt, enkelt sätt att gå från inget till en upp-igång-utvecklingsmiljö. Systemavbildningar med olika konfigurationer för Visual Studio finns i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=%22visual%20studio%202017%22&page=1).

Ny Azure-användare? [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Vilka konfigurationer och versioner är tillgängliga?
Avbildningar för de senaste huvudversioner, Visual Studio 2017 och Visual Studio 2015 finns på Azure Marketplace. För varje större version visas den ursprungligen utgivna versionen (RTW) och de senaste uppdaterade versionerna. Var och en av dessa versioner erbjuder Visual Studio Enterprise och Visual Studio Community-versionerna. Dessa avbildningar uppdateras minst varje månad för att inkludera de senaste uppdateringarna för Visual Studio och Windows. När namnen på avbildningarna är desamma, innehåller varje Bildbeskrivning installerade produktversionen och avbildningens ”datum som anges efter”.

| Slutversionen                                              | Utgåvor                     |     Produktversion     |
|:------------------------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017: Senaste (Version 15.8)                    |    Enterprise, Community     |      Version 15.8.4     |
| Visual Studio 2017: Senaste förhandsversionen av (Version 15.9 Preview 2) |    Enterprise, Community     |      Version 15.9.0     |
|         Visual Studio 2017: RTW                              |    Enterprise, Community     |      Version 15.0.18    |
|   Visual Studio 2015: Senaste (Aktualizace 3)                      |    Enterprise, Community     |  Version 14.0.25431.01  |
|         Visual Studio 2015: RTW                              |             Ingen             | (Har gått ut för att underhålla) |

> [!NOTE]
> I enlighet med Microsofts hanteringsprincip, den ursprungligen (RTW) versionen av Visual Studio 2015 har upphört att gälla för behandling. Visual Studio 2015 Update 3 är den enda återstående versionen som erbjuds för Visual Studio 2015 produktserie.

Mer information finns i den [princip för underhåll av Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Vilka funktioner är installerade?
Varje avbildning innehåller rekommenderade funktionerna i den Visual Studio-versionen. I allmänhet innehåller installationen:

* Alla tillgängliga arbetsbelastningar, inklusive varje arbetsbelastning rekommenderas valfria komponenter
* .NET 4.6.2 och .NET 4.7 SDK: er och Målpaket utvecklarverktyg
* Visual F #
* GitHub-tillägget för Visual Studio
* LINQ till SQL-verktyg

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

Om bilderna inte innehåller en funktion i Visual Studio som du behöver kan du ge feedback med verktyget feedback i det övre högra hörnet på sidan.

## <a name="what-size-vm-should-i-choose"></a>Hur stor virtuell dator ska jag välja?
Azure erbjuder en mängd olika storlekar för virtuella datorer. Eftersom Visual Studio är ett kraftfullt och flertrådiga program behöver du en VM-storlek som innehåller minst två processorer och 7 GB minne. Vi rekommenderar följande VM-storlekar för Visual Studio-avbildningar:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Mer information om de senaste datorstorlekar finns [storlekar för Windows-datorer i Azure](/azure/virtual-machines/windows/sizes).

Med Azure kan du balansera om ditt första val genom att ändra storlek på den virtuella datorn. Du kan etablera en ny virtuell dator med en mer passande storlek eller ändra storlek på den befintliga virtuella datorn till olika underliggande maskinvara. Mer information finns i [ändra storlek på en virtuell Windows-dator](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>När Virtuellt datorn körs, vad händer nu?
Visual Studio följer ”ta med din egen licens”-modell i Azure. Precis som med en installation på upphovsrättsskyddad maskinvara, är en av de första stegen licensiering Visual Studio-installationen. Att låsa upp Visual Studio, antingen:
- Logga in med ett Microsoft-konto som är associerat med ett Visual Studio-prenumeration 
- Lås upp Visual Studio med produktnyckeln som medföljde din ursprungliga köpet

Mer information finns i [logga in i Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) och [hur du låser upp Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hur använder jag spara virtuell utvecklingsdator framtiden eller team?

Spektrum av utvecklingsmiljöer är stor och det är verkliga kostnaden för att skapa mer komplexa miljöer. Du kan spara eller samla in, den Konfigurera virtuella datorn som en ”grundläggande avbildning” för framtida användning eller för andra medlemmar i din grupp oavsett konfigurationen för din miljö. Sedan, när du startar en ny virtuell dator du etablerar den från basavbildningen i stället för Azure Marketplace-avbildning.

En snabb Sammanfattning: använda systemförberedelseverktyget (Sysprep) och Stäng av virtuell dator som körs och sedan avbilda *(bild 1)* den virtuella datorn som en bild via Användargränssnittet i Azure-portalen. Azure sparar den `.vhd` -fil som innehåller bilden i storage-konto du väljer. Den nya avbildningen visas sedan som en bildresurs i din prenumeration listan över resurser.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Bild 1) Spara en avbildning via Azure-portalens användargränssnitt.*</center>

Mer information finns i [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Glöm inte för att förbereda den virtuella datorn med hjälp av Sysprep. Om du missar det steget kan inte Azure etablera en virtuell dator från avbildningen.

> [!NOTE]
> Du kan fortfarande medföra några kostnader för lagring av avbildningarna, men att ökad kostnad obetydlig jämfört med omkostnaderna att återskapa den virtuella datorn från grunden för varje medlem i gruppen som behöver något. Det kostar exempelvis några kronor i att skapa och lagra en 127 GB-avbildning i en månad som är återanvändbara genom hela arbetsgruppen. Dessa kostnader är dock obetydlig jämfört med timmar varje medarbetare investerar för att bygga ut och validera en korrekt konfigurerad maskin för sin egen användning.

Dessutom kan din utvecklingsuppgifter eller tekniker behöver mer skala, t.ex. av konfigurationer för utveckling och konfigurationer för flera datorer. Du kan använda Azure DevTest Labs för att skapa _recept_ som automatiserar konstruktion av din ”gyllene bild”. Du kan också använda DevTest Labs för att hantera principer för virtuella datorer som körs i ditt team. [Med Azure DevTest Labs för utvecklare](/azure/devtest-lab/devtest-lab-developer-lab) är den bästa källan för mer information om DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Nu när du vet om de förkonfigurerade avbildningarna för Visual Studio, är nästa steg att skapa en ny virtuell dator:

* [Skapa en virtuell dator via Azure portal](quick-create-portal.md)
* [Översikt över Windows-datorer](overview.md)
