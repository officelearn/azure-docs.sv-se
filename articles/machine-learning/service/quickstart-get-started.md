---
title: 'Snabbstart: Skapa en arbetsyta för maskininlärning i Azure-portalen – Azure Machine Learning'
description: Använd Azure-portalen för att skapa en Azure Machine Learning-arbetsyta. Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b81e40298eae0f0b44f37e7f8f16beaddad999a5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456821"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Snabbstart: Använda Azure-portalen för att komma igång med Azure Machine Learning

I den här snabbstarten ska du använda Azure-portalen för att skapa en Azure Machine Learning-arbetsyta. Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

I den här snabbstarten kommer du att göra följande:

* Skapa en arbetsyta i din Azure-prenumeration.
* Prova den med Python i en Azure-anteckningsbok och logga värden över flera iterationer.
* Visa de loggade värdena på din arbetsyta.

Följande Azure-resurser läggs automatiskt till din arbetsyta när de är regionalt tillgängliga:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

De resurser som du skapar kan användas som förutsättningar för andra självstudier och instruktionsartiklar om Machine Learning-tjänsten. Som med andra Azure-tjänster finns det begränsningar för vissa resurser som är associerade med Machine Learning. Ett exempel i Azure Batch AI-klusterstorlek. Information om standardgränser och hur du ökar kvoten finns i [den här artikeln](how-to-manage-quotas.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-a-workspace"></a>Skapa en arbetsyta 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Välj `Explore your Azure Machine Learning service workspace` på sidan för arbetsytan.

 ![Utforska arbetsyta](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Använda arbetsytan

Nu ska vi se hur en arbetsyta hjälper dig att hantera dina maskininlärningsskript. I det här avsnittet får du:

* Öppna en anteckningsbok i Azure Notebooks.
* Köra kod som skapar vissa loggade värden.
* Visa de loggade värdena på din arbetsyta.

Det här exemplet visar hur arbetsytan kan hjälpa dig att hålla reda på information som genereras i ett skript. 

### <a name="open-a-notebook"></a>Öppna en anteckningsbok 

Azure Notebooks tillhandahåller en kostnadsfri molnplattform för Jupyter-anteckningsböcker som är förkonfigurerad med allt du behöver för att köra Machine Learning.  

Välj `Open Azure Notebooks` för att testa ditt första experiment.

 ![Öppna Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

Din organisation kan kräva [administratörens godkännande](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) innan du kan logga in.

När du har loggat in öppnas en ny flik och en `Clone Library`-fråga visas. Välj `Clone`


### <a name="run-the-notebook"></a>Köra anteckningsboken

Tillsammans med två anteckningsböcker ser du en `config.json`-fil. Den här konfigurationsfilen innehåller information om arbetsytan som du skapade.  

Välj `01.run-experiment.ipynb` för att öppna anteckningsboken.

Om du vill köra cellerna en i taget använder du `Shift`+`Enter`. Eller välj `Cells` > `Run All` om du vill köra hela anteckningsboken. När du ser en asterisk [*] bredvid en cell körs den. När koden för den cellen är klar visas ett tal.

Du kan uppmanas att logga in. Kopiera koden i meddelandet. Välj sedan länken och klistra in koden i ett nytt fönster. Glöm inte att kopiera ett blanksteg före eller efter koden. Logga in med samma konto som du använde i Azure-portalen.

 ![Logga in](./media/quickstart-get-started/login.png)

I anteckningsboken läser den andra cellen från `config.json` för att ansluta till din arbetsyta.
```
ws = Workspace.from_config()
```

Den tredje cellen med kod startar ett experiment med namnet ”my-first-experiment”. Använd det här namnet för att söka efter information om körningen när du är tillbaka på din arbetsyta.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Observera i anteckningsbokens sista cell de värden som skrivs till en loggfil.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Du kan visa dessa värden på din arbetsyta när koden har körts.

## <a name="view-logged-values"></a>Visa loggade värden

När du har kört alla celler i anteckningsboken går du tillbaka till portalsidan.  

Välj `View Experiments`.

![Visa experiment](./media/quickstart-get-started/view_exp.png)

Stäng popup-fönstret `Reports`.

Välj `my-first-experiment`.

Visa information om körningen som du precis har utfört. Rulla ned på sidan för att hitta tabellen med körningar. Välj länken för att köra tal.

 ![Länk för körningshistorik](./media/quickstart-get-started/report.png)

Du ser linjer som skapades automatiskt för de loggade värdena.  

   ![Visa historik](./media/quickstart-get-started/plots.png)

Eftersom koden för att göra en uppskattning av pi använder slumpmässiga värden visar dina diagram olika värden.

## <a name="clean-up-resources"></a>Rensa resurser 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Du har skapat nödvändiga resurser för att experimentera med och distribuera modeller. Du har också kört lite kod i en anteckningsbok. Dessutom har du utforskat körningshistoriken från koden på arbetsytan i molnet.

För mer djupgående beskrivningar av arbetsflödet rekommenderar vi att du går självstudiekurserna om Machine Learning för att träna och distribuera en modell.  

> [!div class="nextstepaction"]
> [Självstudie: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)
