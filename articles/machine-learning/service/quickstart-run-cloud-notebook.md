---
title: Snabbstart som kör en anteckningsbok i molnet
titleSuffix: Azure Machine Learning service
description: Kom igång med Azure Machine Learning-tjänsten. Använda en hanterad notebook-server i molnet för att testa din arbetsyta.  Din arbetsyta är det grundläggande blocket i molnet som används för att experimentera, träna och distribuera machine learning-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd643185fb4647b19082980edfd333c507aab8a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266264"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använd en molnbaserad notebook-server för att komma igång med Azure Machine Learning

Skapa en molnbaserad notebook-server och sedan använda den för att köra kod som loggar in värden i Azure Machine Learning-tjänsten [arbetsytan](concept-azure-machine-learning-architecture.md). Din arbetsyta är det grundläggande blocket i molnet som används för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning. 

Den här snabbstarten visar hur du skapar en molnresurs i Azure Machine Learning-arbetsytan som konfigurerats med Python-miljön krävs för att köra Azure Machine Learning. Om du vill använda din egen miljö i stället Se [snabbstarten: Använd din egen notebook-server för att komma igång med Azure Machine Learning](quickstart-run-local-notebook.md).  
 
I den här snabbstarten utför du följande åtgärder:

* Skapa en arbetsstation
* Starta en Jupyter Notebook-server på din arbetsstation
* Öppna en bärbar dator som innehåller kod för att beräkna pi och loggar fel vid varje iteration.
* Köra anteckningsboken.
* Visa loggade felvärdena i din arbetsyta.  Det här exemplet visar hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa en Azure Machine Learning-arbetsyta](setup-create-workspace.md#portal) om du inte har något.

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  Om du inte vet hur du hittar din arbetsyta i portalen, se hur du [hitta din arbetsyta](how-to-manage-workspace.md#view).

## <a name="create-a-workstation"></a>Skapa en arbetsstation 

En arbetsstationen får du en molnbaserad plattform för Jupyter-anteckningsböcker som är förkonfigurerad med allt du behöver för att köra Azure Machine Learning-tjänsten. Från arbetsytan, kan du skapa den här plattformen för att komma igång med Jupyter-anteckningsböcker.

1. På arbetsytssidan med i Azure-portalen väljer **arbetsstationen** till vänster.

1. Välj **skapa anteckningsböcker i en arbetsstation med Azure Machine Learning (förhandsversion)**

   ![Utforska arbetsytan](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Den **Notebook arbetsstationer** avsnittet visas en lista över alla molnbaserade notebook-servrar som finns i din arbetsyta.  Härifrån kan du också hantera dessa resurser och ta bort dem när de inte längre behövs. 

1. Välj **lägga till arbetsstationen** att skapa en arbetsstationen.

     ![Välj Lägg till arbetsstation](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Ge din arbetsstation i avsnittet Lägg till arbetsstationen en **beräkning namn** och välj en **typ av databehandling**. Välj sedan **Skapa**.

    ![Skapa ny arbetsstation](media/quickstart-run-cloud-notebook/create-new-workstation.png)

    > [!NOTE]
    > Det tar ungefär två minuter att skapa din arbetsstation. När du är klar visas uppdateras status till ”körs” och länkar till Jupyter och JupyterLab.

## <a name="launch-jupyter-web-interface"></a>Starta Jupyter webbgränssnitt

När du har skapat din arbetsstation Använd avsnittet Notebook arbetsstationer för att starta Jupyter-webbgränssnittet.

* Välj **Jupyter** eller **Jupyter Lab** i den **starta** kolumn för din arbetsstation.

    ![Starta Jupyter notebook-server](./media/quickstart-run-cloud-notebook/start-notebook-server.png)

    Detta startar notebook-server och öppnas startsidan server i en ny webbläsarflik.  Servern visar exempelanteckningsböcker som du kan använda för att komma igång med Azure Machine Learning-tjänsten.

### <a name="run-the-notebook"></a>Köra anteckningsboken

Kör en bärbar dator som beräknar pi och loggas felet i din arbetsyta.

1. Välj **01.run-experiment.ipynb** för att öppna anteckningsboken.

1. Meddelandefältet uppmanar dig att vänta tills kerneln har startats.  När kerneln är klar försvinner meddelandet.

    ![Vänta tills kerneln har startats](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. När kerneln har startats kör du cellerna en i taget med hjälp av **Skift + RETUR**. Eller så kan du välja **Celler** > **Kör alla** om du vill köra hela anteckningsboken. När du ser en asterisk (__*__) bredvid en cell cellen fortfarande körs. När koden för den cellen är klar visas ett tal.  

När du har kört alla celler i anteckningsboken kan du visa de loggade värdena på arbetsytan.

## <a name="view-logged-values"></a>Visa loggade värden

1. Utdata från `run`-cellen innehåller en länk tillbaka till Azure-portalen för att visa experimentresultatet på din arbetsyta. 

    ![Visa experiment](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klicka på den **länk till Azure-portalen** att visa information om körningen på din arbetsyta.  Den här länken öppnar din arbetsyta i Azure-portalen.

1. De linjer med loggade värden som visas skapades automatiskt på arbetsytan. När du loggar flera värden med samma parameter genereras ett diagram automatiskt åt dig.

   ![Visa historik](./media/quickstart-run-cloud-notebook/web-results.png)

Eftersom koden för att göra en uppskattning av pi använder slumpmässiga värden visar dina diagram olika värden.  

## <a name="clean-up-resources"></a>Rensa resurser 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har slutfört du följande:

* Skapa en arbetsstation
* Starta en Jupyter Notebook-server på din arbetsstation
* Öppna en bärbar dator som innehåller kod för att beräkna pi och loggar fel vid varje iteration.
* Köra anteckningsboken.
* Visa loggade felvärdena i din arbetsyta.  Det här exemplet visar hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

Följ självstudierna om Machine Learning för att träna och distribuera en modell om du vill ha en djupgående arbetsflödesupplevelse:  

> [!div class="nextstepaction"]
> [Självstudier: Träna en modell för klassificering av avbildning](tutorial-train-models-with-aml.md)
