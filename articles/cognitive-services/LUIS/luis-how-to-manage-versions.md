---
title: Hantera versioner
titleSuffix: Language Understanding - Azure Cognitive Services
description: Versioner kan du skapa och publicera olika modeller. Ett bra tips är att klona den aktuella aktiva modellen till en annan version av appen innan du gör ändringar i modellen.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a1594d3dfe449049258e5ee12bc173c413459f8a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132882"
---
# <a name="manage-versions"></a>Hantera versioner

Versioner kan du skapa och publicera olika modeller. Ett bra tips är att klona den aktuella aktiva modellen till en annan [version](luis-concept-version.md) av appen innan du gör ändringar i modellen. 

Om du vill arbeta med versioner, kan du öppna din app genom att välja dess namn på **Mina appar** och välj sedan **hantera** i det översta fältet och markera **versioner** i det vänstra navigeringsfönstret. 

Listan över versioner som visar vilka versioner publiceras, där de publiceras och vilken version som är aktiva för tillfället. 

[![Hanteringsavsnittet, versioner sidan](./media/luis-how-to-manage-versions/versions-import.png "hanteringsavsnittet, versioner sidan")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klona en version

1. Välj den version som du vill klona Välj **klona** från verktygsfältet. 

2. I den **klona version** dialogrutan, ange ett namn för den nya versionen, till exempel ”0.2”.

   ![Dialogrutan för kloning Version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Version ID bestå av tecken, siffror eller '.' och får inte vara längre än 10 tecken.
 
 En ny version med det angivna namnet skapas och Ställ in som den aktiva versionen.

## <a name="set-active-version"></a>Ange active version

Välj en version i listan och välj sedan **göra Active** från verktygsfältet. 

[![Hanteringsavsnittet, versioner sidan, gör en version åtgärd](./media/luis-how-to-manage-versions/versions-other.png "hanteringsavsnittet, versioner sidan, gör en version-åtgärd")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import-version

1. Välj **Import version** från verktygsfältet. 

2. I den **Importera nya version** popup-fönstret, ange namnet på nya tio tecken version. Du behöver bara ange ett versions-ID om versionen i JSON-filen finns redan i appen.

    ![Hantera avsnittet versioner sidan importerar ny version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    När du importerar en version, blir den aktiva versionen i den nya versionen.

<a name = "export-version"></a>

## <a name="other-actions"></a>Andra åtgärder

* Att **ta bort** en version, Välj en version i listan och välj sedan **ta bort** från verktygsfältet. Välj **OK**. 
* Att **Byt namn på** en version, Välj en version i listan och välj sedan **Byt namn på** från verktygsfältet. Ange ett nytt namn och välj **klar**. 
* Att **exportera** en version, Välj en version i listan och välj sedan **Export app** från verktygsfältet. Filen laddas ned till den lokala datorn. 

