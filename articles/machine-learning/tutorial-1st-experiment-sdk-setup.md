---
title: 'Självstudie: kom igång i Jupyter Notebooks (python)'
titleSuffix: Azure Machine Learning
description: Installations program för Jupyter Notebook själv studie kurser.  Skapa en Azure Machine Learning arbets yta, klona Jupyter-anteckningsböcker till arbets ytan och skapa en beräknings instans där du kör antecknings böckerna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896892"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Självstudie: kom igång med Azure Machine Learning i Jupyter Notebooks

I den här självstudien slutför du stegen för att komma igång med Azure Machine Learning med hjälp av Jupyter notebooks på en [hanterad molnbaserad arbets Station (beräknings instans)](concept-compute-instance.md). Den här självstudien är en markör till alla andra Jupyter Notebook själv studie kurser.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en [Azure Machine Learning-arbetsyta](concept-workspace.md) som ska användas i andra Jupyter Notebook självstudier.
> * Klona självstudiernas antecknings böcker till din mapp i arbets ytan.
> * Skapa en molnbaserad beräknings instans med Azure Machine Learning python SDK installerat och förkonfigurerat.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten.

Du skapar en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Kör antecknings boken på din arbets yta

Azure Machine Learning innehåller en molnbaserad Notebook-server i din arbets yta för en installations fri och förkonfigurerad upplevelse. Använd [din egen miljö](tutorial-1st-experiment-sdk-setup-local.md) om du vill ha kontroll över din miljö, dina paket och beroenden.

 Följ tillsammans med den här videon eller Använd de detaljerade stegen nedan för att klona och köra själv studie antecknings boken från din arbets yta.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Klona en Notebook-mapp

Du har slutfört följande experiment med att ställa in och köra steg i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **antecknings böcker** till vänster.

1. Välj fliken **exempel** överst.

1. Öppna mappen **python** .

1. Öppna mappen med ett versions nummer.  Det här talet representerar den aktuella versionen för python SDK.

1. Välj **"..."** till höger om mappen **självstudier** och välj sedan **klona**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Mappen för att klona självstudier":::

1. En lista över mappar visar alla användare som har åtkomst till arbets ytan.  Välj din mapp för att klona **självstudierna**  där.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Öppna den klonade antecknings boken

1. Öppna mappen **självstudier** som precis har stängts i avsnittet **användarfiler** .

    > [!IMPORTANT]
    > Du kan visa antecknings böcker i mappen **exempel** , men du kan inte köra en antecknings bok därifrån.  För att kunna köra en antecknings bok, se till att du öppnar den klonade versionen av antecknings boken i avsnittet **användarfiler** .
    
1. Välj **självstudien – 1st-experiment-SDK-träna. ipynb** -filen i **självstudierna/bild klassificerings-mnist** -datamappen.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Öppna mappen självstudier":::

1. I det översta fältet väljer du en beräknings instans som ska användas för att köra antecknings boken. De här virtuella datorerna är förkonfigurerade med [allt du behöver för att köra Azure Machine Learning](concept-compute-instance.md#contents).

1. Om inga virtuella datorer hittas väljer du **+ Lägg** till för att skapa den virtuella beräknings instansen. 

    1. Följ dessa regler när du skapar en virtuell dator:  
        + Namn måste anges och får inte vara tomt.
        + Namnet måste vara unikt (i ett skift läges okänsligt) över alla befintliga beräknings instanser i Azure-regionen för arbets ytan/beräknings instansen. Du får en avisering om det namn du väljer inte är unikt.
        + Giltiga tecken är gemener och versaler, siffror (0 till 9) och bindestreck (-).
        + Namnet måste vara mellan 3 och 24 tecken långt.
        + Namnet måste börja med en bokstav (inte ett tal eller ett bindestreck).
        + Om bindestrecket används måste det följas av minst en bokstav efter bindestrecket. Exempel: test-, test-0, test-01 är ogiltiga, medan test-a0, test-0a är giltiga instanser.

    1.  Välj den virtuella datorns storlek från de tillgängliga alternativen. För självstudierna är standard-VM ett bra val.

    1. Välj sedan **Skapa**. Det kan ta ungefär fem minuter att konfigurera den virtuella datorn.

1. När den virtuella datorn är tillgänglig visas den i det översta verktygsfältet.  Nu kan du köra antecknings boken med **Kör alla** i verktygsfältet, eller genom att använda **SKIFT + RETUR** i kod cellerna i antecknings boken.

Om du har anpassade widgetar eller vill använda Jupyter/JupyterLab väljer du List rutan **Jupyter** längst till höger och väljer sedan **Jupyter** eller **JupyterLab**. Det nya webbläsarfönstret öppnas.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en utvecklings miljö kan du fortsätta med att träna en modell i en Jupyter Notebook:

> [!div class="nextstepaction"]
> [Självstudie: träna bild klassificerings modeller med MNIST-data och scikit-lär](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Om du inte planerar att följa de här självstudierna nu bör du stoppa den virtuella datorn för moln Notebook-servern när du inte använder den för att minska kostnaderna:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
