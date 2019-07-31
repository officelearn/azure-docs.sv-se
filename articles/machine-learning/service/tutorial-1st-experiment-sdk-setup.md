---
title: 'Självstudier: Installations miljö och arbets yta.'
titleSuffix: Azure Machine Learning service
description: I den här självstudien slutför du stegen från slut punkt till slut punkt för att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: e17d9286453aeb5c7eac688555177c2c42faad21
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668632"
---
# <a name="tutorial-setup-environment-and-workspace"></a>Självstudier: Installations miljö och arbets yta

I den här självstudien slutför du stegen från slut punkt till slut punkt för att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks. Den här självstudien är **del ett i en själv studie kurs i två delar**, och täcker konfiguration och konfiguration av python-miljön, samt skapa en arbets yta för att hantera experiment och maskin inlärnings modeller. [**Del två**](tutorial-1st-experiment-sdk-train.md) bygger på detta för att träna flera Machine Learning-modeller och introducera modell hanterings processen med hjälp av både Azure Portal och SDK.

I den här kursen för du göra följande:

> [!div class="checklist"]
> * Skapa en Machine Learning-arbetsyta som ska användas i nästa självstudie.
> * Skapa en moln antecknings boks Server miljö.

## <a name="prerequisites"></a>Förutsättningar

Den enda förutsättningen för den här självstudien är en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) idag.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i SDK: n. Om du redan har en Azure Machine Learning service-arbetsyta går du vidare till [Nästa avsnitt](#azure). Annars skapar du en nu.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Skapa en molnbaserad Notebook-Server

I det här exemplet används en moln antecknings server i din arbets yta för en installation utan kostnad och förkonfigurerad upplevelse. Använd [din egen miljö](how-to-configure-environment.md#local) om du vill ha kontroll över din miljö, dina paket och beroenden.

Från din arbets yta skapar du en moln resurs för att komma igång med Jupyter-anteckningsböcker. Den här resursen är en molnbaserad virtuell Linux-dator som är förkonfigurerad med allt du behöver för att köra Azure Machine Learning-tjänsten.

1. Öppna din arbets yta i [Azure Portal](https://portal.azure.com/).  Om du inte är säker på hur du hittar din arbets yta i portalen kan du läsa om hur du [hittar din arbets yta](how-to-manage-workspace.md#view).

1. På arbets ytans sida i Azure Portal väljer du **Notebook VM** : ar till vänster.

1. Välj **+ ny** för att skapa en Notebook VM.

     ![Välj ny virtuell dator](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Ange ett namn för den virtuella datorn. Välj sedan **Skapa**.

    > [!NOTE]
    > Namnet på den virtuella dator datorn måste innehålla mellan 2 och 16 tecken. Giltiga tecken är bokstäver, siffror och tecknet-Character.  Namnet måste också vara unikt i din Azure-prenumeration.

    ![Skapa en ny virtuell dator](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Vänta tills statusen ändras till **körs**.

### <a name="launch-jupyter-web-interface"></a>Starta Jupyter-webbgränssnitt

När den virtuella datorn har körts använder du avsnittet **Notebook VM** : ar för att starta Jupyter-webbgränssnittet.

1. Välj **Jupyter** i **URI** -kolumnen för den virtuella datorn.

    ![Starta Jupyter Notebook-servern](./media/quickstart-run-cloud-notebook/start-server.png)

    Länken startar din Notebook-Server och öppnar Jupyter Notebook-webbsida på en ny flik i webbläsaren.  Den här länken fungerar bara för den person som skapar den virtuella datorn. Varje användare av arbets ytan måste skapa sin egen virtuella dator.

1. På webb sidan Jupyter Notebook är det främsta namnet ditt användar namn.  Välj den här mappen.

    > [!TIP]
    > Den här mappen finns på [lagrings behållaren](concept-workspace.md#resources) på din arbets yta i stället för på själva Notebook-datorn.  Du kan ta bort den virtuella Notebook-datorn och fortsätta att ha allt ditt arbete.  När du skapar en ny Notebook VM senare, kommer den att läsa in samma mapp. Om du delar din arbets yta med andra, kommer de att se din mapp och du ser dem.

1. Öppna under katalogen och öppna `tutorials/tutorial-1st-experiment-sdk-train.ipynb` sedan för att köra **del två** av självstudien. `samples-*`

## <a name="end"></a>Rensa resurser

Slutför inte det här avsnittet om du planerar att fortsätta till **del 2** av själv studie kursen.

### <a name="stop-the-notebook-vm"></a>Stoppa den virtuella Notebook-datorn

Om du har använt en molnbaserad Notebook-Server stoppar du den virtuella datorn när du inte använder den för att minska kostnaderna.

1. I arbets ytan väljer du **Notebook VM**: ar.

   ![Stoppa VM-servern](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Välj den virtuella datorn i listan.

1. Välj **stoppa**.

1. När du är redo att använda servern igen väljer du **Starta**.

### <a name="delete-everything"></a>Ta bort allt

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört följande uppgifter:

* Skapade en Azure Machine Learning service-arbetsyta.
* Skapat och konfigurerat en molnbaserad Notebook-server i din arbets yta.

Fortsätt med **del 2** av den här självstudien för att träna en enkel Machine Learning-modell.

> [!div class="nextstepaction"]
> [Självstudier: Träna din första modell](tutorial-1st-experiment-sdk-train.md)
