---
title: 'Självstudier: Skapa ditt första ML-experiment: Konfiguration'
titleSuffix: Azure Machine Learning service
description: I den här själv studie serien slutför du stegen från slut punkt till slut punkt för att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks.  Del ett omfattar att skapa en bärbar Server miljö för molnet och att skapa en arbets yta för att hantera experiment-och maskin inlärnings modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: b1da94a2644c299447271b447f51ae4f7750774c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231069"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment"></a>Självstudier: Kom igång med att skapa ditt första ML-experiment

I den här självstudien slutför du stegen från slut punkt till slut punkt för att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks. Den här självstudien är **del ett i en själv studie kurs i två delar**, och täcker konfiguration och konfiguration av python-miljön, samt skapa en arbets yta för att hantera experiment och maskin inlärnings modeller. [**Del två**](tutorial-1st-experiment-sdk-train.md) bygger på detta för att träna flera Machine Learning-modeller och introducera modell hanterings processen med hjälp av både Azure Portal och SDK.

I den här kursen för du göra följande:

> [!div class="checklist"]
> * Skapa en [Azure Machine Learning-arbetsyta](concept-workspace.md) som ska användas i nästa självstudie.
> * Skapa en molnbaserad Jupyter Notebook-baserad virtuell dator med Azure Machine Learning python SDK installerat och förkonfigurerat.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) idag.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i SDK: n. Om du redan har en Azure Machine Learning service-arbetsyta går du vidare till [Nästa avsnitt](#azure). Annars skapar du en nu.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Skapa en molnbaserad Notebook-Server

I det här exemplet används en moln antecknings server i din arbets yta för en installation utan kostnad och förkonfigurerad upplevelse. Använd [din egen miljö](how-to-configure-environment.md#local) om du vill ha kontroll över din miljö, dina paket och beroenden.

Från din arbets yta skapar du en moln resurs för att komma igång med Jupyter-anteckningsböcker. Den här resursen är en molnbaserad virtuell Linux-dator som är förkonfigurerad med allt du behöver för att köra Azure Machine Learning-tjänsten.

1. Öppna din arbets yta i [Azure Portal](https://portal.azure.com/).  Om du inte är säker på hur du hittar din arbets yta i portalen kan du läsa om hur du [hittar din arbets yta](how-to-manage-workspace.md#view).

1. På arbets ytans sida i Azure Portal väljer du **Notebook VM** : ar till vänster.

1. Välj **+ ny** för att skapa en Notebook VM.

     ![Välj ny virtuell dator](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Ange ett namn för den virtuella datorn. 
   + Namnet på den virtuella dator datorn måste innehålla mellan 2 och 16 tecken. Giltiga tecken är bokstäver, siffror och tecknet-Character.  
   + Namnet måste också vara unikt i din Azure-prenumeration.

1. Välj sedan **Skapa**. Det kan ta en stund att konfigurera den virtuella datorn.

1. Vänta tills statusen ändras till **körs**.
   När den virtuella datorn har körts använder du avsnittet **Notebook VM** : ar för att starta Jupyter-webbgränssnittet.

1. Välj **Jupyter** i **URI** -kolumnen för den virtuella datorn.

    ![Starta Jupyter Notebook-servern](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Länken startar din Notebook-Server och öppnar Jupyter Notebook-webbsida på en ny flik i webbläsaren.  Den här länken fungerar bara för den person som skapar den virtuella datorn. Varje användare av arbets ytan måste skapa sin egen virtuella dator.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört följande uppgifter:

* Skapade en Azure Machine Learning service-arbetsyta.
* Skapat och konfigurerat en molnbaserad Notebook-server i din arbets yta.

I **del två** av självstudien kör du koden i `tutorial-1st-experiment-sdk-train.ipynb` för att träna en maskin inlärnings modell. 

> [!div class="nextstepaction"]
> [Självstudier: Träna din första modell](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Om du inte planerar följande del 2 av den här själv studie kursen eller någon annan själv studie kurs bör du [stoppa den virtuella datorn i molnet Notebook-servern](tutorial-1st-experiment-sdk-train.md#clean-up-resources) när du inte använder den för att minska kostnaderna.
