---
title: Skapa Azure Machine Learning-arbetsytor i portalen
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar, visar och tar bort Azure Machine Learning-arbetsytor i Azure-portalen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: f38b0895b0d6eddcf63c082d3df205f4d9de9d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297069"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Skapa och hantera Azure Machine Learning-arbetsytor i Azure-portalen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln ska du skapa, visa och ta bort [**Azure Machine Learning-arbetsytor**](concept-workspace.md) i Azure-portalen för [Azure Machine Learning](overview-what-is-azure-ml.md).  Portalen är det enklaste sättet att komma igång med arbetsytor, men när dina behov ändras eller kraven för automatisering ökar kan du också skapa och ta bort arbetsytor [med HJÄLP av CLI](reference-azure-machine-learning-cli.md), med [Python-kod](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) eller [via VS-kodtillägget](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du vill skapa en arbetsyta behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration. 

1. I det övre vänstra hörnet av Azure-portalen väljer du **+ Skapa en resurs**.

      ![Skapa en ny resurs](./media/how-to-manage-workspace/create-workspace.gif)

1. Använd sökfältet för att hitta **Machine Learning**.

1. Välj **Maskininlärning**.

1. Välj **Skapa** för att börja i fönstret **Maskininlärning.**

1. Ange följande information för att konfigurera den nya arbetsytan:

   Field|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar arbetsytan. I det här exemplet använder vi **docs-ws**. Namnen måste vara unika i resursgruppen. Använd ett namn som är lätt att komma ihåg och att skilja från arbetsytor som skapats av andra. Arbetsytans namn är skiftlägesokänsligt.
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **docs-aml**. 
   Location | Välj den plats som är närmast användarna och de dataresurser som ska skapas på arbetsytan.
   Arbetsyte utgåva | Välj **Basic** eller **Enterprise**.  Den här arbetsyteversionen avgör vilka funktioner du har åtkomst till och prissättning. Läs mer om [basic- och enterprise-erbjudanden](overview-what-is-azure-ml.md#sku). 

    ![Konfigurera arbetsytan](./media/how-to-manage-workspace/select-edition.png)

1. När du är klar med konfigurationen av arbetsytan väljer du **Granska + Skapa**.
2. Granska inställningarna och gör eventuella ytterligare ändringar eller korrigeringar. När du är nöjd med inställningarna väljer du **Skapa**.

   > [!Warning] 
   > Det kan ta flera minuter att skapa arbetsytan i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbetsytan väljer du **Gå till resurs**.

### <a name="download-a-configuration-file"></a>Ladda ned en konfigurationsfil

1. Om du ska skapa en [beräkningsinstans](tutorial-1st-experiment-sdk-setup.md#azure)hoppar du över det här steget.

1. Om du planerar att använda kod på den lokala miljön som refererar till den här arbetsytan väljer du **Hämta config.json** i avsnittet **Översikt** på arbetsytan.  

   ![Ladda ner config.json](./media/how-to-manage-workspace/configure.png)
   
   Placera filen i katalogstrukturen med dina Python-skript eller Jupyter-anteckningsböcker. Den kan finnas i samma katalog, en underkatalog med namnet *.azureml*eller i en överordnad katalog. När du skapar en beräkningsinstans läggs den här filen till i rätt katalog på den virtuella datorn åt dig.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Uppgradera till Enterprise-utgåvan

Du kan uppgradera arbetsytan från Basic edition till Enterprise Edition för att dra nytta av de förbättrade funktionerna, till exempel lågkodsupplevelser och förbättrade säkerhetsfunktioner.

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com).

1. Välj den arbetsyta som du vill uppgradera.

1. Välj **Läs mer** längst upp till höger på sidan.

   [![Uppgradera en](./media/how-to-manage-workspace/upgrade.png) arbetsyta](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Välj **Uppgradera** i fönstret som visas.


> [!IMPORTANT]
> Du kan inte nedgradera en enterprise edition-arbetsyta till en basic edition-arbetsyta. 

## <a name="find-a-workspace"></a><a name="view"></a>Hitta en arbetsyta

1. Skriv **Maskininlärning**i det övre sökfältet .  

1. Välj **Maskininlärning**.

   ![Sök efter Arbetsytan för Azure Machine Learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Titta igenom listan över arbetsytor som hittats. Du kan filtrera baserat på prenumeration, resursgrupper och platser.  

1. Välj en arbetsyta om du vill visa dess egenskaper.

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Använd knappen Ta bort högst upp på arbetsytan som du vill ta bort.

  ![Knappen Ta bort](./media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resursproviderfel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbetsytan

> [!WARNING]
> Det går inte att flytta din Azure Machine Learning-arbetsyta till en annan prenumeration, eller flytta den ägande prenumerationen till en ny klientorganisation. Om du gör det kan det orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Ta bort Azure-behållarregistret

Arbetsytan Azure Machine Learning använder Azure Container Registry (ACR) för vissa åtgärder. Det kommer automatiskt att skapa en ACR-instans när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

Följ självstudien för fullängds för att lära dig hur du använder en arbetsyta för att skapa, träna och distribuera modeller med Azure Machine Learning.

> [!div class="nextstepaction"]
> [Handledning: Träna modeller](tutorial-train-models-with-aml.md)
