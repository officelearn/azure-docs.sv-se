---
title: 'Självstudie: skapa ditt första ML experiment'
titleSuffix: Azure Machine Learning
description: I den här självstudien kommer du att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks.  I del 1 skapar du en arbets yta där du kan hantera experiment och ML-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 125cde85d3caa6fd1a33045e85c5383f83a669bf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982542"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Självstudie: kom igång med att skapa ditt första ML-experiment med python SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien slutför du stegen från slut punkt till slut punkt för att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks. Den här självstudien är **del ett i en själv studie kurs i två delar**, och täcker konfiguration och konfiguration av python-miljön, samt skapa en arbets yta för att hantera experiment och maskin inlärnings modeller. [**Del två**](tutorial-1st-experiment-sdk-train.md) bygger på detta för att träna flera Machine Learning-modeller och introducera modell hanterings processen med hjälp av både Azure Machine Learning Studio och SDK.

I den här kursen har du:

> [!div class="checklist"]
> * Skapa en [Azure Machine Learning-arbetsyta](concept-workspace.md) som ska användas i nästa självstudie.
> * Klona självstudiernas antecknings böcker till din mapp i arbets ytan.
> * Skapa en molnbaserad beräknings instans med Azure Machine Learning python SDK installerat och förkonfigurerat.


Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten. 

Du skapar en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 

## <a name="azure"></a>Kör antecknings boken på din arbets yta

I den här självstudien använder du moln antecknings boken i din arbets yta för en installations fri och förkonfigurerad upplevelse. Använd [din egen miljö](how-to-configure-environment.md#local) om du vill ha kontroll över din miljö, dina paket och beroenden.

Följ med i den här videon eller Använd de detaljerade stegen nedan för att klona och köra självstudien från din arbets yta. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Klona en Notebook-mapp

Du har slutfört följande experiment med att ställa in och köra steg i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **antecknings böcker** till vänster.

1. Öppna mappen **samples** .

1. Öppna mappen **python** .

1. Öppna mappen med ett versions nummer.  Det här talet representerar den aktuella versionen för python SDK.

1. Välj **"..."** till höger om mappen **självstudier** och välj sedan **klona**.

    ![Klona mapp](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. En lista över mappar visar alla användare som har åtkomst till arbets ytan.  Välj din mapp för att klona **självstudierna** där.

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">öppna den klonade antecknings boken

1. Under **användarfiler** öppnar du mappen och öppnar sedan mappen klonade **självstudier** .

    ![Öppna mappen självstudier](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Du kan visa antecknings böcker i mappen **exempel** , men du kan inte köra en antecknings bok därifrån.  För att kunna köra en antecknings bok, se till att du öppnar den klonade versionen av antecknings boken i avsnittet **användarfiler** .
    
1. Välj **självstudien – 1st-experiment-SDK-träna. ipynb** -filen i mappen med **självstudier** .

1. I det översta fältet väljer du en beräknings instans som ska användas för att köra antecknings boken. De här virtuella datorerna är förkonfigurerade med [allt du behöver för att köra Azure Machine Learning](concept-compute-instance.md#contents). Du kan välja en virtuell dator som skapats av valfri användare av din arbets yta. 

1. Om inga virtuella datorer hittas väljer du **+ Lägg** till för att skapa den virtuella beräknings instansen. 

    1. Ange ett namn när du skapar en virtuell dator.  Namnet måste innehålla mellan 2 och 16 tecken. Giltiga tecken är bokstäver, siffror och tecken och måste också vara unika i din Azure-prenumeration.

    1.  Välj den virtuella datorns storlek från de tillgängliga alternativen.

    1. Välj sedan **Skapa**. Det kan ta ungefär fem minuter att konfigurera den virtuella datorn.

1. När den virtuella datorn är tillgänglig visas den i det översta verktygsfältet.  Nu kan du köra antecknings boken med **Kör alla** i verktygsfältet, eller genom att använda **SKIFT + RETUR** i kod cellerna i antecknings boken.

Om du har anpassade widgetar eller vill använda Jupyter/JupyterLab väljer du List rutan **Jupyter** längst till höger och väljer sedan **Jupyter** eller **JupyterLab**. Det nya webbläsarfönstret öppnas.

> [!NOTE]
> Compute instances (för hands version) är för närvarande endast tillgängliga för arbets ytor med en region i **norra centrala USA**, **östra USA 2**, **Nord Europa** eller **Storbritannien, södra**, med stöd för andra regioner som kommer snart.
>Om din arbets yta finns i en annan region kan du fortsätta att skapa och använda en [virtuell dator](concept-compute-instance.md#notebookvm) i stället.  Du kan använda antingen en virtuell dator med en virtuell dator eller en beräknings instans för att köra din bärbara dator. En virtuell dator fortsätter att köra och debiteras om du inte stoppar den. Du kan stoppa den manuellt från fliken **Compute** i Studio eller se [blogg inlägget](https://techcommunity.microsoft.com/t5/educator-developer-blog/azure-virtual-machine-auto-shutdown/ba-p/379342) om att aktivera schemalagd automatisk avstängning för virtuella Azure-datorer.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört följande uppgifter:

* En Azure Machine Learning-arbetsyta har skapats.
* Skapat och konfigurerat en molnbaserad Notebook-server i din arbets yta.

I **del två** av självstudien kör du koden i `tutorial-1st-experiment-sdk-train.ipynb` för att träna en maskin inlärnings modell. 

> [!div class="nextstepaction"]
> [Självstudie: träna din första modell](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Om du inte planerar följande del 2 av den här själv studie kursen eller någon annan själv studie kurs bör du [stoppa den virtuella datorn i molnet Notebook-servern](tutorial-1st-experiment-sdk-train.md#clean-up-resources) när du inte använder den för att minska kostnaderna.


