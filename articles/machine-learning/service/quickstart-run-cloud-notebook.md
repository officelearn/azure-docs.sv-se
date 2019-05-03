---
title: 'Snabbstart: Kör en anteckningsbok i molnet'
titleSuffix: Azure Machine Learning service
description: Kom igång med Azure Machine Learning-tjänsten. Använda en hanterad notebook-server i molnet för att testa din arbetsyta.  Din arbetsyta är det grundläggande blocket i molnet som används för att experimentera, träna och distribuera machine learning-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3d4127226037bf28ba677a49f6444ca987118cb9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023890"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använd en molnbaserad notebook-server för att komma igång med Azure Machine Learning

Skapa en molnbaserad notebook-server och sedan använda den.  I den här snabbstarten kör du Python-kod som loggar in värden i den [Azure Machine Learning-tjänstens arbetsyta](concept-azure-machine-learning-architecture.md). Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning. 

Den här snabbstarten visar hur du skapar en molnresurs i Azure Machine Learning-arbetsytan som konfigurerats med Python-miljön krävs för att köra Azure Machine Learning. Om du vill använda din egen miljö i stället Se [snabbstarten: Använd din egen notebook-server för att komma igång med Azure Machine Learning](quickstart-run-local-notebook.md).  
 
I den här snabbstarten utför du följande åtgärder:

* Skapa en ny molnbaserade notebook-server i din arbetsyta
* Starta en Jupyter-webbgränssnitt
* Öppna en bärbar dator som innehåller kod för att beräkna pi och loggar fel vid varje iteration.
* Köra anteckningsboken.
* Visa loggade felvärdena i din arbetsyta.  Det här exemplet visar hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure Machine Learning-arbetsyta.  [Skapa din arbetsyta](setup-create-workspace.md#portal) nu om du inte har något.

## <a name="create-a-cloud-based-notebook-server"></a>Skapa en molnbaserad notebook-server

 Från arbetsytan, kan du skapa en molnresurs för att komma igång med Jupyter-anteckningsböcker. Den här resursen ger dig en molnbaserad plattform som är förkonfigurerad med allt du behöver för att köra Azure Machine Learning-tjänsten.

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  Om du inte vet hur du hittar din arbetsyta i portalen, se hur du [hitta din arbetsyta](how-to-manage-workspace.md#view).

1. På arbetsytssidan med i Azure-portalen väljer **Notebook VMs** till vänster.

1. Välj **+ ny** att skapa en anteckningsbok VM.

     ![Välj ny virtuell dator](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Ange ett namn för den virtuella datorn. Välj sedan **Skapa**. 

    ![Skapa en ny virtuell dator](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Väntar du cirka 4 – 5 minuter, och välj sedan **uppdatera**.  Försök att uppdatera med 30 sekunders mellanrum eller så tills statusen blir **kör**.

    ![Uppdatera](media/quickstart-run-cloud-notebook/refresh.png)

## <a name="launch-jupyter-web-interface"></a>Starta Jupyter webbgränssnitt

När Virtuellt datorn körs, använda den **Notebook virtuella datorer** avsnitt för att starta Jupyter-webbgränssnittet.

1. Välj **Jupyter** i den **URI** kolumnen för den virtuella datorn.  

    ![Starta Jupyter notebook-server](./media/quickstart-run-cloud-notebook/start-server.png)

    Länken Starta notebook-server och öppna Jupyter notebook webbsidan i en ny webbläsarflik.  Den här länken fungerar endast för den person som skapar den virtuella datorn.

1. Jupyter notebook webbsidan, Välj den **samples/Snabbstart** mappen för att se Snabbstart anteckningsboken.

## <a name="run-the-notebook"></a>Köra anteckningsboken

Kör en bärbar dator som beräknar pi och loggas felet i din arbetsyta.

1. Välj **01.run-experiment.ipynb** för att öppna anteckningsboken.

1. Du kan se ett meddelande om att kernel inte har angetts.  Välj **Python 3.6 - AzureML**och välj sedan **ange Kernel**.

   ![Ange kernel](./media/quickstart-run-cloud-notebook/set-kernel.png)

1. Meddelandefältet uppmanar dig att vänta tills kerneln har startats. När kerneln är klar försvinner meddelandet.

    ![Vänta tills kerneln har startats](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1.  Klicka på den första kodcellen och välj **kör**.

    > [!NOTE]
    > Kod celler har hakparenteser före. Om hakparenteserna är tomma (__[]__), kod som inte har körts. När koden körs du ser en asterisk (__[*]__). När koden har slutförts, ett tal **[1]** visas.  Antalet visar den ordning i vilken cellerna kördes.
    >
    > Använd **SKIFT-ange** som en genväg för att köra en cell.

    ![Kör den första kodcellen](media/quickstart-run-cloud-notebook/cell1.png)

1. Kör den andra kodcellen. Om du ser instruktionerna för att autentisera, kopiera koden och följ länken för att logga in. När du loggar in kommer webbläsaren ihåg den här inställningen.  

    > [!TIP]
    > Tänk på att inte kopiera utrymmet efter den.  

    ![Autentisera](media/quickstart-run-cloud-notebook/authenticate.png)

1. När du är klar, antalet celler __[2]__ visas.  Om du var tvungen att logga in, visas ett meddelande om lyckad autentisering.   Om du inte behöver logga in kan du se inte några utdata för den här cellen, endast siffran som visas för att indikera att cellen har körts.

    ![Meddelande om att något har slutförts](media/quickstart-run-cloud-notebook/success.png)

1. Kör resten av cellerna kod.  Eftersom varje cell är klar ser du dess cellnummer som visas. Endast den sista cellen visar några andra utdata.  I den största kodcellen visas `run.log` på flera platser. Varje `run.log` lägger till dess värde till din arbetsyta.


## <a name="view-logged-values"></a>Visa loggade värden

1. Utdata från `run`-cellen innehåller en länk tillbaka till Azure-portalen för att visa experimentresultatet på din arbetsyta. 

    ![Visa experiment](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klicka på den **länk till Azure-portalen** att visa information om körningen på din arbetsyta.  Den här länken öppnar din arbetsyta i Azure-portalen.

1. De linjer med loggade värden som visas skapades automatiskt på arbetsytan. När du loggar flera värden med samma parameter genereras ett diagram automatiskt åt dig.

   ![Visa historik](./media/quickstart-run-cloud-notebook/web-results.png)

Eftersom koden för att göra en uppskattning av pi använder slumpmässiga värden visar dina diagram olika värden.  

## <a name="clean-up-resources"></a>Rensa resurser 

### <a name="stop-the-notebook-vm"></a>Stoppa anteckningsboken VM

Stoppa anteckningsboken virtuell dator när du inte använder den för att minska kostnaderna.  

1. På arbetsytan, Välj **Notebook VMs**.

   ![Stoppa VM-servern](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Välj den virtuella datorn i listan.

1. Välj **stoppa**.

1. När du är redo att användaren servern igen, väljer **starta**.

### <a name="delete-everything"></a>Tar bort allt

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har slutfört du uppgifterna:

* Skapa en VM-anteckningsbok
* Starta en Jupyter Notebook-server på den bärbara datorn VM
* Öppna en bärbar dator som innehåller kod för att beräkna pi och loggar fel vid varje iteration.
* Köra anteckningsboken.
* Visa loggade felvärdena i din arbetsyta.  Det här exemplet visar hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

Följ självstudierna om Machine Learning för att träna och distribuera en modell om du vill ha en djupgående arbetsflödesupplevelse:  

> [!div class="nextstepaction"]
> [Självstudie: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)