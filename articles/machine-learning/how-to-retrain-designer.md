---
title: Träna modeller med Azure Machine Learning designer
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar om modeller med publicerade pipelines i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 12/15/2019
ms.openlocfilehash: d1382da739fd8ca56d4cc53c2c302331bdfbf1c3
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311877"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Träna modeller med Azure Machine Learning designer
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här instruktionen får du lära dig hur du använder Azure Machine Learning designer för att omträna en maskin inlärnings modell. Lär dig hur du använder publicerade pipelines för att automatisera Machine Learning-arbetsflöden för omskolning.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Träna en maskin inlärnings modell.
> * Skapa en pipeline-parameter.
> * Publicera din utbildnings pipeline.
> * Träna om din modell.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).

* En Azure Machine Learning-arbetsyta med Enterprise SKU.

Den här instruktionen förutsätter att du har grundläggande kunskaper om att skapa pipelines i designern. För en guidad introduktion till designern, slutför du [själv studie kursen](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exempel på pipeline

Pipelinen som används i den här artikeln är en ändrad version av den som finns i [exempel 3 – inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md). Modulen [Importera data](algorithm-module-reference/import-data.md) används i stället för exempel data uppsättningen för att visa hur du tränar en modell med dina egna data.

![Skärm bild som visar den ändrade exempel pipelinen med en ruta som markerar modulen importera data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Träna en Machine Learning-modell

För att omträna en modell behöver du en inledande modell. I det här avsnittet får du lära dig hur du tränar en modell och kommer åt den sparade modellen med hjälp av designern.

1. Välj modulen **Importera data** .
1. I fönstret Egenskaper anger du en data källa.

    ![Skärm bild som visar en exempel konfiguration av modulen importera data](./media/how-to-retrain-designer/import-data-settings.png)

    I det här exemplet lagras data i ett Azure- [datalager](how-to-access-data.md). Om du inte redan har ett data lager kan du skapa ett nu genom att välja **nytt data lager**.

1. Ange sökvägen till dina data. Du kan också välja **Browse-sökväg** för att visuellt navigera i ditt data lager. 

1. Välj **Kör** överst på arbets ytan.
    
    > [!NOTE]
    > Om du redan har angett en standard beräkning för pipeline-utkastet körs pipelinen automatiskt. Annars kan du följa anvisningarna i fönstret inställningar som visas för att ange en nu.

### <a name="locate-your-trained-model"></a>Hitta din utbildade modell

Designern sparar alla pipeline-utdata, inklusive utbildade modeller, på standard lagrings kontot. Du kan också komma åt den tränade modellen direkt i designern:

1. Vänta tills pipelinen har slutförts.

1. Välj modulen **träna modell** .

1. I fönstret Inställningar väljer du **utdata**.

1. Välj **Trained_model** för att ladda ned modellen.

![Skärm bild som visar hur du laddar ned den tränade modellen](./media/how-to-retrain-designer/download-model.png)

## <a name="create-a-pipeline-parameter"></a>Skapa en pipeline-parameter

Lägg till pipeline-parametrar för att dynamiskt ange variabler vid körning. För den här pipelinen lägger du till en parameter för övnings data Sök vägen så att du kan träna modellen på en ny data uppsättning.

1. Välj modulen **Importera data** .
1. I fönstret Inställningar väljer du ellipserna ovanför **Sök vägs** fältet.
1. Välj **Lägg till i pipeline-parameter**.
1. Ange ett parameter namn och standardvärde.

    > [!NOTE]
    > Du kan granska och redigera dina pipeline-parametrar genom att välja **kugg hjuls ikonen Inställningar** bredvid rubriken för ditt pipeline-utkast. 

![Skärm bild som visar hur du skapar en pipeline-parameter](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publicera en utbildnings pipeline

När du publicerar en pipeline skapas en pipeline-slutpunkt. Med pipelinens slut punkter kan du återanvända och hantera dina pipeliner för repeterbarhet och automatisering. I det här exemplet har du konfigurerat din pipeline för omträning.

1. Välj **publicera** ovanför designer-arbetsytan.
1. Välj eller skapa en ny pipeline-slutpunkt.

    > [!NOTE]
    > Du kan publicera flera pipelines till en enda slut punkt. Varje pipeline i slut punkten tilldelas ett versions nummer, som du kan ange när du anropar pipelinen.

1. Välj **Publicera**.

## <a name="retrain-your-model"></a>Träna om din modell

Nu när du har en publicerad utbildnings pipeline kan du använda den för att träna din modell med nya data. Du kan skicka körningar från en pipeline-slutpunkt från antingen portal eller program mässigt.

### <a name="submit-runs-with-the-designer"></a>Skicka körningar med designern

Använd följande steg för att skicka en slut punkt för pipelinen som körs från designern:

1. Gå till sidan **slut punkter** .

1. Välj fliken **pipelines-slutpunkter** .

1. Välj din pipeline-slutpunkt.

1. Välj fliken **publicerade pipeliner** .

1. Välj den pipeline som du vill köra.

1. Välj **Kör**.

1. I dialog rutan konfigurera kan du ange ett nytt värde för indata-sökväg, som pekar på din nya data uppsättning.

![Skärm bild som visar hur du konfigurerar en parametriserad pipeline som körs i designern](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Skicka körningar med kod

Det finns flera sätt att komma åt din REST-slutpunkt program mässigt beroende på din utvecklings miljö. Du kan hitta kod exempel som visar hur du skickar pipeline-körningar med parametrar på fliken **förbrukare** i din pipeline.

## <a name="next-steps"></a>Nästa steg

Följ [själv studie kursen](tutorial-designer-automobile-price-train-score.md) om du vill träna och distribuera en Regressions modell.
