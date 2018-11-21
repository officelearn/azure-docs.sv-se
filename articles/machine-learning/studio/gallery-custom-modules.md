---
title: Azure AI-galleriet anpassade moduler | Microsoft Docs
description: Upptäck anpassade machine learning-moduler i Azure AI-galleriet.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 9ca07adecaa8d6e8b51d20014c95d4147a4a5eb8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263511"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Identifiera anpassade machine learning-moduler i Azure AI-galleriet

[Azure AI-galleriet](http://gallery.cortanaintelligence.com) tillhandahåller flera [anpassade moduler](https://gallery.cortanaintelligence.com/customModules) som utöka funktionerna hos Azure Machine Learning Studio. Du kan importera modulerna som ska användas i experiment, så att du kan utveckla mer avancerade lösningar för förutsägelseanalys.

Galleriet erbjuder för närvarande, moduler på *time series analytics*, *association regler*, *klustring algoritmer* (utöver k-means) och  *visualiseringar*, och andra bestämmer hög grad verktyget moduler.


## <a name="discover"></a>Utforska
Bläddra anpassade moduler [i galleriet](http://gallery.cortanaintelligence.com)under **mer**väljer **anpassade moduler**.

![Välj anpassade moduler på startsidan för galleriet](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

Den **[anpassade moduler](https://gallery.cortanaintelligence.com/customModules)** sidan visar en lista med moduler som nyligen har lagts till och populära. Om du vill visa alla anpassade moduler, Välj den **se alla** knappen. För att söka efter en specifik anpassade modul, Välj **se alla**, och välj sedan filtreringsvillkor. Du kan även ange sökorden i den **Search** rutan längst upp på sidan för galleriet.

![Välj ”se alla” och bläddra alla anpassade moduler](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Förstå

För att förstå hur en anpassad modul publicerade fungerar, väljer du modulen anpassad för att öppna sidan med modulen. Sidan ger en konsekvent och informativa inlärningsupplevelse. Till exempel informationssidan visar syftet med modulen och visar förväntade indata, utdata och parametrar. Sidan har också en länk till den underliggande källkoden som du kan granska och anpassa.

### <a name="comment-and-share"></a>Kommentar och dela
På en anpassad modul information sidan den **kommentarer** avsnitt, du kan kommentera, ge feedback eller ställa frågor om modulen. Du kan också dela modulen med vänner och kollegor på Twitter och LinkedIn. Du kan också e-en länk till sidan modulen information att bjuda in andra användare att visa sidan.

![Dela det här objektet med vänner](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Lägga till egna kommentarer](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importera
Du kan importera alla anpassade moduler från galleriet till dina egna experiment.

Azure AI-galleriet erbjuder två sätt att importera en kopia av modulen:

* **Från galleriet**. När du importerar en anpassad modul från galleriet kan få du också en exempelexperimentet som ger dig ett exempel på hur du använder modulen.
* **Machine Learning Studio inifrån**. Du kan importera alla anpassade moduler när du arbetar i Machine Learning Studio (i detta fall använder du inte får exempelexperimentet).

### <a name="from-the-gallery"></a>Från galleriet

1. Öppna sidan med modulen i galleriet. 
2. Välj **öppna i Studio**.
   
    ![Öppna anpassad modul från galleriet](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Varje anpassad modul innehåller en exempelexperimentet som visar hur du använder modulen. När du väljer **öppna i Studio**, exempelexperimentet öppnas i Machine Learning Studio-arbetsyta. (Om du inte redan är inloggad till Studio uppmanas du att loggar in första med hjälp av ditt Microsoft-konto.)

Förutom exempelexperimentet kopieras anpassad modul till din arbetsyta. Den placeras också i din modulpaletten, med alla dina inbyggda eller anpassade Machine Learning Studio-moduler. Du kan nu använda dina egna experiment som en annan modul i din arbetsyta.

### <a name="from-within-machine-learning-studio"></a>Inifrån Machine Learning Studio

1. Välj i Machine Learning Studio, **NEW**.
2. Välj **modulen**. Du kan välja från en lista över galleriets moduler eller söka efter en specifik modul med det **Search** box.
3. Peka med musen på en modul och välj sedan **Importmodul**. (Välj för att få information om modulen **vyn i galleriet**. Då kommer du till sidan modulen i galleriet.)
   
    ![Importera anpassad modul till Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Anpassad modul kopieras till din arbetsyta och placeras i din modulpaletten med dina inbyggda eller anpassade Machine Learning Studio-moduler. Du kan nu använda dina egna experiment som en annan modul i din arbetsyta.

## <a name="use"></a>Användning

Oavsett vilken metod du väljer att importera en anpassad modul när du importerar modulen, modulen placeras i din modulpaletten i Machine Learning Studio. Från din modulpaletten kan du använda anpassad modul i alla experiment på arbetsytan och precis som andra moduler.

Använda en importerade modulen:

1. Skapa ett experiment eller öppna ett befintligt experiment.
2. Om du vill expandera listan över anpassade moduler i din arbetsyta på modulpaletten, Välj **anpassade**. Det är modulpaletten till vänster om arbetsytan för experimentet.
   
    ![Anpassad modul listan i Studio palette](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Välj den modul som du har importerat och drar den till ditt experiment.


**[Gå till galleriet](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

