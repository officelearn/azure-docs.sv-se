---
title: Azure AI-galleriet anpassade moduler | Microsoft Docs
description: Identifiera anpassade machine learning-moduler i Azure AI-galleriet.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: c53bab2e838425dfdd124e64c3d7d3114fa30429
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834438"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Identifiera anpassade machine learning-moduler i Azure AI-galleriet
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Anpassade moduler för Machine Learning Studio
AI-galleriet som Azure erbjuder flera [anpassade moduler](https://gallery.cortanaintelligence.com/customModules) som utöka funktionerna hos Azure Machine Learning Studio. Du kan importera modulerna som ska användas i dina experiment, så du kan utveckla mer avancerade förutsägelseanalyslösningar.

För närvarande galleriet erbjuder moduler på *time series analytics*, *association regler*, *clustering algoritmer* (utöver k-means) och *visualiseringar*, och andra bestämmer hög grad verktyget moduler.


## <a name="discover"></a>Utforska
Bläddra anpassade moduler [i galleriet](http://gallery.cortanaintelligence.com)under **mer**väljer **anpassade moduler**.

![Välj Anpassad moduler på startsidan galleri](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

Den **[anpassade moduler](https://gallery.cortanaintelligence.com/customModules)** sidan visar en lista över nyligen tillagda och populära moduler. Om du vill visa alla anpassade moduler, Välj den **se alla** knappen. Om du vill söka efter en anpassad modul, Välj **se alla**, och välj sedan filtreringsvillkor. Du kan även ange sökorden i den **Sök** rutan längst upp på sidan Gallery.

![Välj ”se alla” att bläddra i alla anpassade moduler](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Förstå

Välj anpassad modul för att öppna sidan modulen för att förstå hur en anpassad modul publicerade fungerar. Informationssidan ger en konsekvent och informativa learning-upplevelse. Till exempel informationssidan visar syftet med modulen och visar förväntade indata, utdata och parametrar. Informationssidan har även en länk till den underliggande källkoden som du kan granska och anpassa.

### <a name="comment-and-share"></a>Kommentar och dela
På en anpassad modul information sidan den **kommentarer** avsnitt, du kan kommentera, ge feedback eller ställa frågor om modulen. Du kan också dela modulen med vänner och kollegor på Twitter och LinkedIn. Du kan också e-en länk till sidan modulen information att bjuda in andra användare att visa sidan.

![Dela objektet med vänner](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Lägga till egna kommentarer](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importera
Du kan importera en anpassad modul från galleriet till dina egna experiment.

Azure AI-galleriet finns två sätt att importera en kopia av modulen:

* **Från galleriet**. När du importerar en anpassad modul från galleriet måste få du också en exempelexperimentet som ger ett exempel på hur du använder modulen.
* **Inifrån Maskininlärning Studio**. Du kan importera en anpassad modul när du arbetar i Machine Learning Studio (i detta fall kan du inte får exempelexperimentet).

### <a name="from-the-gallery"></a>Från galleriet

1. Öppna sidan modul i galleriet. 
2. Välj **öppna i Studio**.
   
    ![Öppna anpassad modul från galleriet](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Varje anpassad modul innehåller en exempelexperimentet som visar hur du använder modulen. När du väljer **öppna i Studio**, exempelexperimentet öppnas i Machine Learning Studio-arbetsytan. (Om du inte redan är inloggad till Studio, du uppmanas att först logga in med ditt Microsoft-konto.)

Förutom exempelexperimentet kopieras anpassad modul till arbetsytan. Även placeras den i din modulpaletten med alla inbyggda eller anpassade Machine Learning Studio modulerna. Du kan nu använda den i dina egna experiment, t.ex. en annan modul på arbetsytan.

### <a name="from-within-machine-learning-studio"></a>Inifrån Maskininlärning Studio

1. Välj i Machine Learning Studio **ny**.
2. Välj **modulen**. Du kan välja från en lista med moduler i galleriet eller söka efter en viss modul med det **Sök** rutan.
3. Peka med musen på en modul och välj sedan **Import-Module**. (För att få information om modulen, Välj **vyn i galleriet**. Då kommer du till sidan modulen information i galleriet.)
   
    ![Importera anpassad modul till Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Anpassad modul kopieras till din arbetsyta och placeras i din modulpaletten med din inbyggda eller anpassade Machine Learning Studio-moduler. Du kan nu använda den i dina egna experiment, t.ex. en annan modul på arbetsytan.

## <a name="use"></a>Användning

Oavsett vilken metod du väljer att importera en anpassad modul när du importerar modulen, modulen placeras i din modulpaletten i Machine Learning Studio. Från din modulpaletten kan du använda anpassade modulen i alla experiment på arbetsytan, precis som en annan modul.

Använda en importerade modulen:

1. Skapa ett experiment eller öppna ett befintligt experiment.
2. Om du vill expandera listan över anpassade moduler i ditt arbetsområde på modulpaletten, Välj **anpassade**. Det är modulpaletten till vänster om arbetsytan för experimentet.
   
    ![Anpassade Modullista Studio paletten](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Välj den modul som du har importerat och drar den till experimentet.


**[Gå till galleriet](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

