---
title: 'Snabbstart: Skapa en arbetsyta för maskininlärning – Azure'
description: Använd Azure-portalen för att skapa en Azure Machine Learning-arbetsyta. Den här arbetsytan är själva grunden i molnet för att experimentera, träna och distribuera maskininlärningsmodeller med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: c1610291e06255e2c724268f63d740f7e4debea4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959996"
---
# <a name="quickstart-get-started-with-azure-machine-learning-service"></a>Snabbstart: Kom igång med Azure Machine Learning-tjänsten

I den här snabbstarten ska du använda Azure-portalen för att skapa en Azure Machine Learning-arbetsyta. Den här arbetsytan är själva grunden i molnet för att experimentera, träna och distribuera maskininlärningsmodeller med Azure Machine Learning-tjänsten. 

I den här kursen ska du:

* Skapa en arbetsyta i din Azure-prenumeration
* Prova den med Python i en Azure-anteckningsbok och logga värden över flera iterationer
* Visa de loggade värdena på din arbetsyta

För att underlätta för dig läggs följande Azure-resurser till automatiskt till din arbetsyta om de är tillgängliga i din region: [containerregister](https://azure.microsoft.com/services/container-registry/), [lagring](https://azure.microsoft.com/services/storage/), [programinsikter](https://azure.microsoft.com/services/application-insights/) och [nyckelvalv](https://azure.microsoft.com/services/key-vault/).

Du kan använda de resurser som du skapar i andra självstudier och instruktionsartiklar om Azure Machine Learning. Som med andra Azure-tjänster finns det begränsningar för vissa resurser (för t.ex. BatchAI-klusterstorleken) som är associerade med Azure Machine Learning-tjänsten. Läs [den här](how-to-manage-quotas.md) artikeln om standardgränserna och hur du begär högre kvot.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-a-workspace"></a>Skapa en arbetsyta 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

På sidan för arbetsytan klickar du på `Explore your Azure Machine Learning Workspace`

 ![Utforska arbetsyta](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Använda arbetsytan

Nu ska vi se hur en arbetsyta hjälper dig att hantera dina maskininlärningsskript. I det här avsnittet ska du:

* Öppna en anteckningsbok i Azure Notebooks
* Köra kod som skapar vissa loggade värden
* Visa de loggade värdena på din arbetsyta

Det här är ett exempel på hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

### <a name="open-a-notebook"></a>Öppna en anteckningsbok 

Azure Notebooks tillhandahåller en kostnadsfri molnplattform för Jupyter-anteckningsböcker som är förkonfigurerad med allt du behöver för att köra Azure Machine Learning-tjänsten.  

Klicka på knappen `Open Azure Notebooks` för att testa ditt första experiment.

 ![Starta Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

När du har loggat in öppnas en ny flik och en `Clone Library`-fråga visas.  Klicka på `Clone`


### <a name="run-the-notebook"></a>Köra anteckningsboken

Tillsammans med två anteckningsböcker ser du en `config.json`-fil.  Den här konfigurationsfilen innehåller information om arbetsytan som du nyss skapade.  

Klicka på `01.run-experiment.ipynb` för att öppna anteckningsboken.

Du kan köra cellerna en i taget genom att använda `Shift`+`Enter`.  Eller så kan du använda menyn `Cells` > `Run All` för att köra hela anteckningsboken.

Du kan uppmanas att logga in.  Kopiera koden i meddelandet, klicka på länken och klistra sedan in koden i det nya fönstret.  Glöm inte att kopiera ett blanksteg före eller efter koden.

 ![inloggning](./media/quickstart-get-started/login.png)

I anteckningsboken läser den andra cellen från `config.json` för att ansluta till din arbetsyta.
```
ws = Workspace.from_config()
```

Den tredje cellen med kod startar ett experiment med namnet ”my-first-experiment”.  Du använder det här namnet för att söka efter information om körningen när du är tillbaka på din arbetsyta.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Observera värdena som skrivs till en loggfil i den sista cellen i anteckningsboken.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Du kan visa dessa värden på din arbetsyta när koden har körts.

## <a name="view-logged-values"></a>Visa loggade värden

När alla celler i anteckningsboken har slutförts kan du gå tillbaka till portalsidan.  

Klicka på `View Experiments`.

![visa experiment](./media/quickstart-get-started/view_exp.png)

Stäng popup-fönstret `Reports`.

Klicka på `my-first-experiment`.

Visa information om körningen som du precis har utfört.  Rulla ned på sidan tills du kommer till tabellen och klicka på körningsnummerlänken.

 ![länk för körningshistorik](./media/quickstart-get-started/report.png)

Du ser linjer som skapades automatiskt för de loggade värdena:

   ![visa historik](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>Rensa resurser 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen, men ta bort en enskild arbetsyta genom att visa arbetsyteegenskaperna och välja Ta bort.

## <a name="next-steps"></a>Nästa steg

Du har nu skapat resurserna som krävs för att börja experimentera och distribuera modeller. Du har också kört lite kod i en anteckningsbok och utforskat körningshistoriken från koden på arbetsytan i molnet.

För mer djupgående beskrivningar av arbetsflödet rekommenderar vi att du går självstudiekurserna om Azure Machine Learning för att träna och distribuera en modell.  

> [!div class="nextstepaction"]
> [Självstudie: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)
