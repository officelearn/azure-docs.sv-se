---
title: "Med Visual Studio på en virtuell Azure-dator | Microsoft Docs"
description: "Med Visual Studio på en virtuell Azure-dator."
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
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a id="top"></a> Visual Studio-avbildningar i Azure
Med Visual Studio körs i en förkonfigurerad Azure virtuell dator (VM) är det enklaste och snabbaste sättet att gå från grunden till en utvecklingsmiljö upp och körs.  -Avbildningar med olika konfigurationer för Visual Studio finns i den [Azure Marketplace](https://portal.azure.com/). Bara starta en virtuell dator och av du fortsätter.

Ny Azure-användare? [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Vilka konfigurationer och versioner är tillgängliga?
I Azure Marketplace hitta avbildningar för de senaste versionerna av större: 2017 för Visual Studio och Visual Studio 2015.  Varje huvudversion finns du ursprungligen utgiven (aka RTW) version och de ”senaste” uppdaterade versionerna.  För var och en av dessa olika versioner kan du hitta i Visual Studio Enterprise och Visual Studio Community-versionerna.

|               Versionen              |          Versioner            |    Produktversion    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| Visual Studio 2017 - Latest (version 15.5) |    Enterprise, Community     |     Version 15.5.3    |
|         Visual Studio 2017 - RTW           |    Enterprise, Community     |     Version 15.0.7    |
|   Visual Studio 2015 - Latest (Update 3)   |    Enterprise, Community     | Version 14.0.25431.01 |
|         Visual Studio 2015 - RTW           | Ingen (har upphört att gälla för underhåll) |          ---          |

> [!NOTE]
> I enlighet med Microsoft behandling av princip, ursprungligen utgiven (aka RTW) version av Visual Studio 2015 har upphört att gälla för behandling.  Visual Studio 2015 Update 3 är därför den enda återstående versionen som erbjuds för produktserie Visual Studio 2015.

Mer information finns i [Visual Studio Servicing princip](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Vilka funktioner är installerade?
Varje avbildning innehåller rekommenderade funktionerna i den Visual Studio-versionen.  I allmänhet innehåller installationen:

* Alla tillgängliga arbetsbelastningar, inklusive att arbetsbelastningar rekommenderade valfria komponenter
* .NET 4.6.2 och .NET 4.7 SDK: er, målobjekt Pack och utvecklingsverktygen
* Visual F#
* GitHub-tillägget för Visual Studio
* LINQ to SQL-verktyg

Det här är den kommandorad som vi använder för att installera Visual Studio när du bygger avbildningar:

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * Lägg till Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * Lägg till Microsoft.Net.Component.4.6.2.SDK ^
   * Lägg till Microsoft.Net.Component.4.6.2.TargetingPack ^
   * Lägg till Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

Om bilderna inte innehåller en funktion för Visual Studio som du behöver kan du ange denna feedback via verktyget feedback (övre högra hörnet på sidan).

## <a name="what-size-vm-should-i-choose"></a>Hur stor VM ska jag välja?
Det är enkelt att etablera en ny virtuell dator och Azure erbjuder en mängd olika storlekar för virtuella datorer.  Precis som med alla maskinvara förvärv, som du vill utjämna prestanda jämfört med kostnaden.  Eftersom Visual Studio är ett kraftfullt, flertrådade program, som du vill att en VM-storlek som innehåller minst två processorer och 7 GB minne.  Mer information om de senaste datorstorlekar finns [storlekar för Windows-datorer i Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).

Med Azure, du låst inte med din första Välj – du kan balansera ditt första val om du ändrar storlek på den virtuella datorn.  Du kan antingen etablera en ny virtuell dator med en lämpligare storlek eller ändra storlek på en befintlig virtuell dator till olika underliggande maskinvara.  Mer information finns i [ändra storlek på en Windows VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm).

## <a name="after-i-get-the-vm-running-then-what"></a>När jag den virtuella datorn körs, sedan vad?
Visual Studio följer ”får du egen licens” modellen i Azure.  Så, på samma sätt i en installation på egna maskinvara, en av de första stegen licensiering installationen av Visual Studio.  Du kan låsa upp Visual Studio genom att antingen logga in med ett Microsoft-konto som är kopplad till en prenumeration på Visual Studio eller Visual Studio med produktnyckeln kan låsas upp med inledande köpet.  Mer information finns i [inloggning till Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) och [så att låsa upp Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>När jag bygga ut dev VM, hur kan jag spara (avbilda) den för framtida eller team används?

Spektrum av utvecklingsmiljöer är stort och det är verkliga kostnaden för att skapa mer komplexa miljöer.  Oavsett konfiguration för din miljö, Azure är dock bevarar att enkelt genom att spara/samla in din perfekt konfigurerade virtuella dator som en ”basavbildning' för framtida användning – själv och/eller för andra medlemmar i gruppen.  Sedan, när du startar en ny virtuell dator, etablera den från basavbildningen i stället för Marketplace-avbildning.

Som en snabb sammanfattning måste du sysprep och Stäng VM körs sedan *avbildning (bild 1)* den virtuella datorn som en bild via Användargränssnittet för den Azure-portalen.  Azure sparar den `.vhd` -fil som innehåller bilden i storage-konto du väljer.  Sedan visas den nya avbildningen som en bildresurs i din prenumeration lista över resurser.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Bild 1) Spara en avbildning via Användargränssnittet för den Azure-portalen.*</center>

Mer information finns i [avbildning av en virtuell dator till en bild](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource).

  **Påminnelse:** Glöm inte att köra sysprep VM!  Om du missar steget kan inte Azure etablera en virtuell dator från avbildningen.

> [!NOTE]
> Du fortfarande innebära att vissa kostnaden för lagring av bilder, men den inkrementella kostnaden är sannolikt obetydlig jämfört med kostnaderna för personal att återskapa den virtuella datorn från grunden – för varje användare i arbetsgruppen måste en virtuell dator.  Det kostar exempelvis några kronor att skapa och spara en avbildning av 127 GB för en månad är återanvändbara av alla medlemmar i gruppen.  Dessa kostnader är dock obetydlig jämfört med timmarna varje medarbetare investerar för att bygga ut och validera en korrekt konfigurerad dev ruta för sin egen användning.

Dessutom behöva utvecklingen aktiviteter eller tekniker mer skala – som av konfigurationer för utveckling och konfigurationer med flera datorer.  Du kan använda Azure DevTest Labs för att skapa _recept_ som automatiserar konstruktionen av den gyllene bilden och hantera principer för din grupp med virtuella datorer.  [Med hjälp av Azure DevTest Labs för utvecklare](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab) är den bästa källan för mer information om DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Nu när du vet om de förkonfigurerade avbildningarna för Visual Studio är nästa steg att skapa en ny virtuell dator:

* [Skapa en virtuell dator via Azure portal](quick-create-portal.md)
* [Översikt över Windows-datorer](overview.md)
