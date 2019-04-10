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
ms.openlocfilehash: 0672d90a25bc4c879d28512ab212f98f29efbf3b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358217"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använd en molnbaserad notebook-server för att komma igång med Azure Machine Learning

I den här artikeln använder du Azure-datorer för att köra kod som loggas i Azure Machine Learning-tjänsten [arbetsytan](concept-azure-machine-learning-architecture.md). Din arbetsyta är det grundläggande blocket i molnet som används för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning. 

Den här snabbstarten använder molnresurser och kräver ingen installation. Om du vill använda din egen miljö i stället Se [snabbstarten: Använd din egen notebook-server för att komma igång med Azure Machine Learning](quickstart-run-local-notebook.md).  
 
I den här snabbstarten utför du följande åtgärder:

* Anslut till din arbetsyta med Python i en Jupyter-anteckningsbok. Anteckningsboken innehåller kod för att beräkna pi och loggar fel vid varje iteration. 
* Visa loggade felvärdena i din arbetsyta.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisite"></a>Krav

1. [Skapa en Azure Machine Learning-arbetsyta](setup-create-workspace.md#portal) om du inte har något.

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  Se hur du [hitta din arbetsyta](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Använda arbetsytan

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Lär dig hur en arbetsyta hjälper dig att hantera dina machine learning-skript. I det här avsnittet gör du följande:

* Öppna en anteckningsbok i Azure Notebooks.
* Köra kod som skapar vissa loggade värden.
* Visa de loggade värdena på din arbetsyta.

Det här exemplet visar hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

### <a name="open-a-notebook"></a>Öppna en anteckningsbok 

[Azure Notebooks](https://notebooks.azure.com) tillhandahåller en kostnadsfri molnplattform för Jupyter-anteckningsböcker som är förkonfigurerad med allt du behöver för att köra Machine Learning. Du kan starta den här plattformen för att komma igång med din arbetsyta för Azure Machine Learning-tjänsten från din arbetsyta.

1. På översiktssidan för arbetsytan väljer **Kom igång Azure anteckningsböcker** att testa ditt första experiment i Azure-anteckningsböcker.  Azure Notebooks är en separat tjänst som gör att du kan kör Jupyter-anteckningsböcker kostnadsfritt i molnet.  När du använder den här länken till tjänsten läggs information om hur du ansluter till din arbetsyta till i det bibliotek som du skapar i Azure Notebooks.

   ![Utforska arbetsytan](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Logga in på Azure Notebooks.  Kontrollera att du loggar in med samma konto som du använde för att logga in på Azure-portalen. Din organisation kan kräva [administratörens godkännande](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) innan du kan logga in.

1. När du har loggat in öppnas en ny flik och en `Clone Library`-fråga visas. När det här biblioteket klonas läser en uppsättning anteckningsböcker och andra filer in på ditt Azure Notebooks-konto.  De här filerna hjälper dig att utforska funktionerna i Azure Machine Learning.

1. Avmarkera **Offentligt** så att du inte delar information om arbetsytan med andra.

1. Välj **Klona**.

   ![Klona ett bibliotek](./media/quickstart-run-cloud-notebook/clone.png)

1. Om du ser att projektstatusen har stoppats klickar du på **Run on Free Computer** (Kör på kostnadsfri dator) för att använda den kostnadsfria anteckningsboksservern.

    ![Köra ett projekt på kostnadsfri beräkning](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>Köra anteckningsboken

I listan över filer för det här projektet finns en `config.json`-fil. Den här konfigurationsfilen innehåller information om den arbetsyta som du skapade i Azure-portalen.  Den här filen gör att din kod kan ansluta till och lägga till information i arbetsytan.

1. Välj **01.run-experiment.ipynb** för att öppna anteckningsboken.

1. Meddelandefältet uppmanar dig att vänta tills kerneln har startats.  När kerneln är klar försvinner meddelandet.

    ![Vänta tills kerneln har startats](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. När kerneln har startats kör du cellerna en i taget med hjälp av **Skift + RETUR**. Eller så kan du välja **Celler** > **Kör alla** om du vill köra hela anteckningsboken. När du ser en asterisk, __*__, intill en cell innebär det att cellen fortfarande körs. När koden för den cellen är klar visas ett tal. 

1. Följ anvisningarna i anteckningsboken för att autentisera din Azure-prenumeration.

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

Du har skapat nödvändiga resurser för att experimentera med och distribuera modeller. Du har också kört lite kod i en anteckningsbok. Dessutom har du utforskat körningshistoriken från koden på arbetsytan i molnet.

Följ självstudierna om Machine Learning för att träna och distribuera en modell om du vill ha en djupgående arbetsflödesupplevelse:  

> [!div class="nextstepaction"]
> [Självstudier: Träna en modell för klassificering av avbildning](tutorial-train-models-with-aml.md)