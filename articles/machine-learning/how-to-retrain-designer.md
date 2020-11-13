---
title: Träna om modeller med hjälp av Azure Machine Learning designer
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar om Machine Learning-modeller med publicerade pipelines i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d8ef4d9f768d6fdcf976c9317d1abec3d4533824
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554809"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Träna modeller med Azure Machine Learning designer


I den här instruktions artikeln får du lära dig hur du använder Azure Machine Learning designer för att omträna en maskin inlärnings modell. Du kommer att använda publicerade pipelines för att automatisera arbets flödet och ange parametrar för att träna din modell på nya data. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Träna en maskin inlärnings modell.
> * Skapa en pipeline-parameter.
> * Publicera din utbildnings pipeline.
> * Träna din modell med nya parametrar.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta
* Slutför del 1 av den här instruktions serien, [transformera data i designern](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Den här artikeln förutsätter också att du har kunskaper om att skapa pipelines i designern. Slutför [självstudien](tutorial-designer-automobile-price-train-score.md)för en guidad introduktion. 

### <a name="sample-pipeline"></a>Exempel på pipeline

Pipelinen som används i den här artikeln är en ändrad version av ett exempel på pipeline- [inkomst förutsägelse](samples-designer.md#classification) på design sidan. I pipelinen används modulen [Importera data](algorithm-module-reference/import-data.md) istället för exempel data uppsättningen för att visa hur du tränar modeller med dina egna data.

![Skärm bild som visar den ändrade exempel pipelinen med en ruta som markerar modulen importera data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Skapa en pipeline-parameter

Skapa pipeline-parametrar för att dynamiskt ange variabler vid körning. I det här exemplet ändrar du övnings data Sök vägen från ett fast värde till en parameter, så att du kan träna modellen på olika data.

1. Välj modulen **Importera data** .

    > [!NOTE]
    > I det här exemplet används modulen importera data för att komma åt data i ett registrerat data lager. Du kan dock följa liknande steg om du använder alternativa mönster för data åtkomst.

1. I informations fönstret modul, till höger om arbets ytan, väljer du din data källa.

1. Ange sökvägen till dina data. Du kan också välja **Bläddra sökväg** för att bläddra i fil trädet. 

1. Mouseover fältet **sökväg** och välj ellipserna ovanför **Sök vägs** fältet som visas.

    ![Skärm bild som visar hur du skapar en pipeline-parameter](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Välj **Lägg till i pipeline-parameter**.

1. Ange ett parameter namn och ett standardvärde.

   > [!NOTE]
   > Du kan granska och redigera dina pipeline-parametrar genom att välja kugg hjuls ikonen **Inställningar** bredvid rubriken för ditt pipeline-utkast. 

1. Välj **Spara**.

1. Skicka pipeline-körningen.

## <a name="find-a-trained-model"></a>Hitta en utbildad modell

Designern sparar alla pipeline-utdata, inklusive utbildade modeller, till standard lagrings platsen. Du kan också komma åt utbildade modeller direkt i designern:

1. Vänta tills pipelinen har slutförts.
1. Välj **träningsmodellmodulen**.
1. I informations fönstret för moduler till höger om arbets ytan väljer du **utdata + loggar**.
1. Du kan hitta din modell i **andra utdata** tillsammans med kör loggar.
1. Du kan också välja ikonen **Visa utdata** . Härifrån kan du följa anvisningarna i dialog rutan för att navigera direkt till ditt data lager. 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar hur du laddar ned den tränade modellen](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publicera en utbildnings pipeline

Publicera en pipeline till en pipeline-slutpunkt så att du enkelt kan återanvända dina pipeliner i framtiden. En pipeline-slutpunkt skapar en REST-slutpunkt för att anropa pipelinen i framtiden. I det här exemplet kan du använda din pipeline-slutpunkt för att omträna en modell på olika data.

1. Välj **publicera** ovanför designer-arbetsytan.
1. Välj eller skapa en pipeline-slutpunkt.

   > [!NOTE]
   > Du kan publicera flera pipelines till en enda slut punkt. Varje pipeline i en specifik slut punkt får ett versions nummer, som du kan ange när du anropar pipelinen.

1. Välj **Publicera**.

## <a name="retrain-your-model"></a>Träna om din modell

Nu när du har en publicerad utbildnings pipeline kan du använda den för att träna om din modell på nya data. Du kan skicka körningar från en pipeline-slutpunkt från Studio-arbetsytan eller program mässigt.

### <a name="submit-runs-by-using-the-studio-portal"></a>Skicka körningar med hjälp av Studio-portalen

Använd följande steg för att skicka en slut punkt för en parametriserad pipeline som körs från Studio-portalen:

1. Gå till sidan **slut punkter** i din Studio-arbetsyta.
1. Välj fliken **pipelines-slutpunkter** . Välj sedan din pipeline-slutpunkt.
1. Välj fliken **publicerade pipeliner** . Välj sedan den pipeline-version som du vill köra.
1. Välj **Skicka**.
1. I dialog rutan konfiguration kan du ange parameter värden för körningen. I det här exemplet ska du uppdatera data Sök vägen för att träna din modell med en icke-US-datauppsättning.

![Skärm bild som visar hur du konfigurerar en parametriserad pipeline som körs i designern](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Skicka körningar med hjälp av kod

Du kan hitta REST-slutpunkten för en publicerad pipeline i översikts panelen. Genom att anropa slut punkten kan du omträna den publicerade pipelinen.

Om du vill göra ett REST-anrop behöver du ett OAuth 2,0-värde för autentisering med OAuth-typ. Information om hur du konfigurerar autentisering till din arbets yta och gör en parameter REST-anrop finns i [skapa en pipeline för att skapa en Azure Machine Learning pipeline för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en slut punkt för en parametriserad utbildning med hjälp av designern.

En fullständig genom gång av hur du kan distribuera en modell för att göra förutsägelser finns i [hand boken för designern](tutorial-designer-automobile-price-train-score.md) för att träna och distribuera en Regressions modell.
