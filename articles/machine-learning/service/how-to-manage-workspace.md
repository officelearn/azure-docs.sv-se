---
title: Skapa Azure ML-arbetsytor i portalen
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att skapa, visa och ta bort Azure Machine Learning-tjänstens arbetsytor i Azure-portalen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: df015df3c20cdb99512167e3dc3e455da2762c52
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744793"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces-in-the-azure-portal"></a>Skapa och hantera Azure Machine Learning service-arbetsytor i Azure Portal

I den här artikeln får du skapa, visa och ta bort [ **Azure Machine Learning-tjänstens arbetsytor** ](concept-workspace.md) i Azure-portalen för [Azure Machine Learning-tjänsten](overview-what-is-azure-ml.md).  Portalen är det enklaste sättet att komma igång med arbets ytor, men när du behöver ändra eller krav på Automation-höjning kan du också skapa och ta bort arbets ytor [med hjälp av CLI](reference-azure-machine-learning-cli.md), [med python-kod](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) eller [via vs Code-tillägget](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Du behöver en Azure-prenumeration för att skapa en arbetsyta. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Hämta en konfigurations fil

1. Hoppa över det här steget om du vill skapa en virtuell dator i [Notebook](tutorial-1st-experiment-sdk-setup.md#azure).

1. Om du planerar att använda kod i din lokala miljö som hänvisar till den här arbets ytan väljer du **Hämta config. JSON** från **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Placera filen i katalog strukturen med dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog. När du skapar en VM-anteckningsbok läggs den här filen till i rätt katalog på den virtuella datorn åt dig.


## <a name="view"></a>Visa en arbets yta

1. I det övre vänstra hörnet i portalen, väljer **alla tjänster**.

1. I fältet **alla tjänster** filter skriver du **Machine Learning-tjänsten**.  

1. Välj **Machine Learning tjänst arbets ytor**.

   ![Sök efter Azure Machine Learning service-arbetsyta](media/how-to-manage-workspace/all-services.png)

1. Titta igenom listan över arbetsytor hittades. Du kan filtrera baserat på prenumerationen, resursgrupper och platser.  

1. Välj en arbets yta för att visa dess egenskaper.
   ![Egenskaper för arbets yta](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Använd knappen Ta bort överst på arbetsytan som du vill ta bort.

  ![Ta bort knapp](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

Följ den kompletta självstudiekursen lär du dig hur du använder en arbetsyta för att skapa, träna och distribuera modeller med Azure Machine Learning-tjänsten.

> [!div class="nextstepaction"]
> [Självstudier: Träna modeller](tutorial-train-models-with-aml.md)
