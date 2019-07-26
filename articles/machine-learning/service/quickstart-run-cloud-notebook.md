---
title: 'Snabbstart: Köra en antecknings bok i molnet'
titleSuffix: Azure Machine Learning service
description: Den här självstudien visar hur du kommer igång med Azure Machine Learning tjänsten och använder en hanterad Notebook-server i molnet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371050"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använd en molnbaserad Notebook-Server för att komma igång med Azure Machine Learning

Den här snabb starten lär dig hur du börjar använda Azure Machine Learning tjänsten med hjälp av en hanterad Notebook-server i molnet. Ingen installation krävs. Om du i stället vill installera SDK: n i din egen python-miljö [, se snabb start: Använd din egen Notebook-Server för att komma igång](quickstart-run-local-notebook.md)med Azure Machine Learning.

Den här snabb starten visar hur du använder [arbets ytan Azure Machine Learning tjänst](concept-azure-machine-learning-architecture.md) för att hålla reda på experimentet med Machine Learning (ml). Du gör detta genom att skapa en virtuell dator för [bärbara datorer (för hands version)](how-to-configure-environment.md#notebookvm): en säker, molnbaserad Azure-arbetsstation som tillhandahåller en Jupyter Notebook-Server, JupyterLab och en helt för beredd ml-miljö. Sedan kör du en python-anteckningsbok på den här virtuella datorn (VM) som loggar värden i arbets ytan.

Det gör du genom att utföra följande åtgärder:

* Skapa en arbets yta.
* Skapa en virtuell dator med en virtuell dator i din arbets yta.
* Öppna Jupyter-webbgränssnittet.
* Öppna en antecknings bok som innehåller kod för att uppskatta Pi och logga fel vid varje iteration.
* Kör antecknings boken.
* Visa de loggade fel värdena i din arbets yta. I följande exempel visas hur arbets ytan hjälper dig att hålla reda på information som genereras i ett skript.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine learnings tjänsten](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du har en Azure Machine Learning service-arbetsyta går du vidare till [Nästa avsnitt](#create-notebook). Annars skapar du en nu.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Skapa en virtuell dator för Notebook

 Från din arbets yta skapar du en moln resurs för att börja använda Jupyter-anteckningsböcker. Den här resursen är en molnbaserad plattform som är förkonfigurerad med allt du behöver för att köra tjänsten Azure Machine Learning.

1. Öppna din arbets yta i [Azure Portal](https://portal.azure.com/). Om du inte är säker på hur du hittar din arbets yta i portalen läser du så här [visar du din arbets yta](how-to-manage-workspace.md#view).

1. På arbets ytans sida väljer du **Notebook VM** : ar till vänster.

1. Välj **+ ny** för att skapa en Notebook VM.  

     ![Välj ny virtuell dator](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Ange ett namn för den virtuella datorn. Välj sedan **Skapa**.

    > [!NOTE]
    > Den virtuella datorns namn måste vara mellan 2 och 16 tecken. Bokstäver, siffror och bindestreck är giltiga tecken. Namnet måste vara unikt i din Azure-prenumeration.

    ![Skapa en ny virtuell dator](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Vänta ungefär 4 till 5 minuter tills statusen ändras till **körs**.


## <a name="open-the-jupyter-web-interface"></a>Öppna Jupyter-webbgränssnittet

När den virtuella datorn har körts använder du avsnittet **Notebook VM** : er för att öppna Jupyter-webbgränssnittet.

1. Välj **Jupyter** i **URI** -kolumnen för den virtuella datorn.  

    ![Starta Jupyter Notebook-servern](./media/quickstart-run-cloud-notebook/start-server.png)

    Länken startar din Notebook-Server och öppnar Jupyter Notebook-webbsida på en ny flik i webbläsaren.  Den här länken fungerar bara för den person som skapar den virtuella datorn.  Varje användare av arbets ytan måste skapa sin egen virtuella dator.

1. På webb sidan för Jupyter Notebook är det översta mappnamnet ditt användar namn. Välj den här mappen.

    > [!TIP]
    > Den här mappen finns på [lagrings behållaren](concept-workspace.md#resources) på din arbets yta i stället för på själva Notebook-datorn.  Du kan ta bort den virtuella Notebook-datorn och fortsätta att ha allt ditt arbete.  När du skapar en ny Notebook VM senare, kommer den att läsa in samma mapp.  Om du delar din arbets yta med andra, kommer de att se din mapp och du ser dem. 

1. Mappnamnet innehåller ett versions nummer (exempel **-1.0.33.1**, t. ex.). Välj mappen Samples.

1. Välj mappen **snabb start** .

## <a name="run-the-notebook"></a>Köra anteckningsboken

Kör en antecknings bok som beräknar Pi och loggar felet till din arbets yta.

1. Välj **01.run-experiment.ipynb** för att öppna anteckningsboken.

1. Om du ser aviseringen "kernel not found" väljer du kernel **Python 3,6-azureml** (cirka halvvägs i listan) och anger kernel.

1. Välj den första kod cellen och välj sedan **Kör**.

    > [!NOTE]
    > Kod cellerna har hakparenteser före varandra. Om hakparenteserna är tomma ( __[]__ ) har koden inte körts. När koden körs visas en asterisk ( __[*]__ ). När koden har slutförts visas talet **[1]** .  Siffran visar i vilken ordning cellerna kördes.
    >
    > Använd **Shift + Retur** som en genväg för att köra en cell.

    ![Kör den första kod cellen](media/quickstart-run-cloud-notebook/cell1.png)

1. Kör den andra kod cellen. Om du ser instruktioner för att autentisera kopierar du koden och följer länken för att logga in. När du har loggat in kommer webbläsaren att komma ihåg den här inställningen.  

    ![Autentisera](media/quickstart-run-cloud-notebook/authenticate.png)

1. När kod cells körningen lyckas visas cell numret __[2]__ . Om du var tvungen att logga in visas ett meddelande om att autentiseringen är klar.   Om du inte behöver logga in visas inga utdata för den här cellen. Du ser bara talet som visas för att visa att cellen har körts.

    ![Meddelande om att något har slutförts](media/quickstart-run-cloud-notebook/success.png)

1. Kör resten av kod cellerna. När varje cell har körts visas dess cell nummer. Endast den sista cellen visar andra utdata.  

    I den största kod cellen `run.log` visas på flera platser. Varje `run.log` lägger till värdet i din arbets yta.

## <a name="view-logged-values"></a>Visa loggade värden

1. Utdata från `run` cellen innehåller en länk tillbaka till Azure Portal där du kan visa experiment resultatet i din arbets yta.

    ![Visa experiment](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Välj **Länka till Azure Portal** för att visa information om körningen i din arbets yta. Den här länken öppnar din arbetsyta i Azure-portalen.

1. De linjer med loggade värden som visas skapades automatiskt på arbetsytan. När du loggar flera värden med samma parameter genereras ett diagram automatiskt åt dig. Här är ett exempel:

   ![Visa historik](./media/quickstart-run-cloud-notebook/web-results.png)

Eftersom koden för att approximera PI använder slumpmässiga värden kan dina ritytor se annorlunda ut.  

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="stop-the-notebook-vm"></a>Stoppa den virtuella Notebook-datorn

Stoppa den virtuella Notebook-datorn när du inte använder den för att minska kostnaderna.  

1. I arbets ytan väljer du **Notebook VM**: ar.

   ![Stoppa VM-servern](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Välj den virtuella datorn i listan.

1. Välj **stoppa**.

1. När du är redo att använda servern igen väljer du **Starta**.

### <a name="delete-everything"></a>Ta bort allt

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

När du har slutfört dessa uppgifter går du till Jupyter Notebook webb sida. I mappen **snabb start** öppnar du och kör den **02. Deploy-web-service. ipynb** Notebook för att lära dig hur du distribuerar en webb tjänst.

När du vill installera andra python-paket i Jupyter-miljön använder du den här koden inuti en bärbar dator:

```
!source activate py36 && pip install <packagename>
```

På sidan Jupyter Notebook kan du också bläddra igenom andra antecknings böcker i mappen exempel om du vill veta mer om tjänsten Azure Machine Learning.

Följ självstudierna om Machine Learning för att träna och distribuera en modell om du vill ha en djupgående arbetsflödesupplevelse:  

> [!div class="nextstepaction"]
> [Självstudier: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)
