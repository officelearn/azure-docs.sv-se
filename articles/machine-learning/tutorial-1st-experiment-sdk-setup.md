---
title: 'Självstudiekurs: Skapa ditt första ML-experiment'
titleSuffix: Azure Machine Learning
description: I den här självstudien ska du komma igång med Azure Machine Learning Python SDK som körs i Jupyter-anteckningsböcker.  I del 1 skapar du en arbetsyta där du hanterar experiment och ML-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 820332b0692c0c863ed23912fe9913c419769155
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273009"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Självstudiekurs: Kom igång med att skapa ditt första ML-experiment med Python SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien slutför du steg från slutna till slutna steg för att komma igång med Azure Machine Learning Python SDK som körs i Jupyter-anteckningsböcker. Den här självstudien är **del ett i en självstudieserie**i två delar och täcker installation och konfiguration av Python-miljön samt skapar en arbetsyta för att hantera dina experiment och maskininlärningsmodeller. [**Del två**](tutorial-1st-experiment-sdk-train.md) bygger på detta för att träna flera maskininlärningsmodeller och introducera modellhanteringsprocessen med både Azure Machine Learning studio och SDK.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en [Azure Machine Learning Workspace](concept-workspace.md) som ska användas i nästa självstudiekurs.
> * Klona anteckningsboken för självstudier till mappen på arbetsytan.
> * Skapa en molnbaserad beräkningsinstans med Azure Machine Learning Python SDK installerat och förkonfigurerat.


Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning-arbetsyta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskininlärningsmodeller. Det binder din Azure-prenumeration och resursgrupp till ett lätt förbrukat objekt i tjänsten. 

Du skapar en arbetsyta via Azure-portalen, en webbaserad konsol för hantering av dina Azure-resurser. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Ta del av din **arbetsyta** och **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Köra anteckningsbok på arbetsytan

Den här självstudien använder molnservern för bärbara datorer på arbetsytan för en installationsfri och förkonfigurerad upplevelse. Använd [din egen miljö](how-to-configure-environment.md#local) om du föredrar att ha kontroll över din miljö, dina paket och beroenden.

Följ med den här videon eller använd de detaljerade stegen nedan för att klona och köra självstudien från arbetsytan. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Klona en anteckningsboksmapp

Du slutför följande experimentinstallations- och körningssteg i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller verktygsinlärningsverktyg för att utföra datavetenskapsscenarier för datavetenskapsutövare på alla färdighetsnivåer.

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com/).

1. Välj din prenumeration och arbetsytan som du skapade.

1. Välj **Anteckningsböcker** till vänster.

1. Öppna mappen **Exempel.**

1. Öppna mappen **Python.**

1. Öppna mappen med ett versionsnummer på den.  Det här numret representerar den aktuella versionen för Python SDK.

1. Välj **"..."** till höger om **självstudiemappen** och välj sedan **Klona**.

    ![Klona mapp](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. En lista med mappar visar varje användare som kommer åt arbetsytan.  Välj din mapp för att klona **självstudiemappen** där.

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">Öppna den klonade anteckningsboken

1. Öppna mappen under **Användarfiler** och öppna sedan mappen klonade **självstudier.**

    ![Öppna mappen Självstudier](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Du kan visa anteckningsböcker i **exempelmappen,** men du kan inte köra en anteckningsbok därifrån.  Om du vill köra en anteckningsbok kontrollerar du att du öppnar den klonade versionen av anteckningsboken i avsnittet **Användarfiler.**
    
1. Välj filen **tutorial-1st-experiment-sdk-train.ipynb** i din **självstudier/create-first-ml-experiment-mapp.**

1. I det övre fältet väljer du en beräkningsförekomst som ska användas för att köra anteckningsboken. Dessa virtuella datorer är förkonfigurerade med [allt du behöver för att köra Azure Machine Learning](concept-compute-instance.md#contents). 

1. Om inga virtuella datorer hittas väljer du **+ Lägg till** för att skapa den virtuella datorn för beräkningsinstansen. 

    1. När du skapar en virtuell dator anger du ett namn.  Namnet måste vara mellan 2 och 16 tecken. Giltiga tecken är bokstäver, siffror och - tecken, och måste också vara unika i hela din Azure-prenumeration.

    1.  Välj storleken på den virtuella datorn bland de tillgängliga alternativen.

    1. Välj sedan **Skapa**. Det kan ta cirka 5 minuter att konfigurera den virtuella datorn.

1. När den virtuella datorn är tillgänglig visas den i det övre verktygsfältet.  Du kan nu köra anteckningsboken antingen genom att använda **Kör alla** i verktygsfältet eller genom att använda **Skift+Retur** i anteckningsbokens kodceller.

Om du har anpassade widgets eller föredrar att använda Jupyter / JupyterLab välja **Jupyter** rullgardinsmenyn längst till höger, välj sedan **Jupyter** eller **JupyterLab**. Det nya webbläsarfönstret öppnas.

## <a name="next-steps"></a>Nästa steg

I den här självstudien slutförde du följande uppgifter:

* Skapade en Azure Machine Learning-arbetsyta.
* Skapade och konfigurerade en molnanteckningsserver på arbetsytan.

I **del två** av självstudien `tutorial-1st-experiment-sdk-train.ipynb` kör du koden i för att träna en maskininlärningsmodell. 

> [!div class="nextstepaction"]
> [Handledning: Träna din första modell](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Om du inte planerar att följa del 2 i den här självstudien eller andra självstudier, bör du [stoppa moln notebook server VM](tutorial-1st-experiment-sdk-train.md#clean-up-resources) när du inte använder den för att minska kostnaderna.


