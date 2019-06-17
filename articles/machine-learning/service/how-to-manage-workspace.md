---
title: Skapa och hantera arbetsytor
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
ms.openlocfilehash: 7f0806a1d68cd2cede1ae51f0a50a8125c1e7c8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016541"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Skapa och hantera Azure Machine Learning-tjänstens arbetsytor

I den här artikeln får du skapa, visa och ta bort [ **Azure Machine Learning-tjänstens arbetsytor** ](concept-workspace.md) i Azure-portalen för [Azure Machine Learning-tjänsten](overview-what-is-azure-ml.md).  Du kan också skapa och ta bort arbetsytor [med hjälp av CLI](reference-azure-machine-learning-cli.md), [med Python-kod](https://aka.ms/aml-sdk) eller [via VS Code-tillägg](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Du behöver en Azure-prenumeration för att skapa en arbetsyta. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Visa en arbetsyta

1. I det övre vänstra hörnet i portalen, väljer **alla tjänster**.

1. I den **alla tjänster** filter och skriver **machine learning-tjänsten**.  

1. Välj **Machine Learning-tjänstens arbetsytor**.

   ![Sök efter Azure Machine Learning-tjänstens arbetsyta](media/how-to-manage-workspace/all-services.png)

1. Titta igenom listan över arbetsytor hittades. Du kan filtrera baserat på prenumerationen, resursgrupper och platser.  

1. Välj en arbetsyta för att visa dess egenskaper.
   ![Egenskaper för arbetsyta](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Använd knappen Ta bort överst på arbetsytan som du vill ta bort.

  ![Ta bort knapp](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

Följ den kompletta självstudiekursen lär du dig hur du använder en arbetsyta för att skapa, träna och distribuera modeller med Azure Machine Learning-tjänsten.

> [!div class="nextstepaction"]
> [Självstudie: Skapa modeller](tutorial-train-models-with-aml.md)
