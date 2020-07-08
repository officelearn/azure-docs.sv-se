---
title: Skapa arbets ytor i portalen
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar, visar och tar bort Azure Machine Learning arbets ytor i Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 71bb85c5812a3e779570cdd87c5092e5b0de52f9
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045713"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Skapa och hantera Azure Machine Learning arbets ytor i Azure Portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln skapar du, visar och tar bort [**Azure Machine Learning arbets ytor**](concept-workspace.md) i Azure Portal för [Azure Machine Learning](overview-what-is-azure-ml.md).  Portalen är det enklaste sättet att komma igång med arbets ytor, men när du behöver ändra eller krav på Automation-höjning kan du också skapa och ta bort arbets ytor [med hjälp av CLI](reference-azure-machine-learning-cli.md), [med python-kod](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) eller [via vs Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Du behöver en Azure-prenumeration för att skapa en arbets yta. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration. 

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](./media/how-to-manage-workspace/create-workspace.gif)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Fält|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra. Namnet på arbets ytan är Skift läges okänsligt.
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Location | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.
   Arbetsyte version | Välj **Basic** eller **Enterprise**.  Den här arbets ytans utgåva avgör vilka funktioner du kommer att ha åtkomst till och prissättning på. Läs mer om [erbjudanden för Basic och Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Konfigurera din arbets yta](./media/how-to-manage-workspace/select-edition.png)

1. När du är klar med konfigurationen av arbets ytan väljer du **Granska + skapa**.
2. Granska inställningarna och gör eventuella ytterligare ändringar eller korrigeringar. När du är nöjd med inställningarna väljer du **skapa**.

   > [!Warning] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

### <a name="download-a-configuration-file"></a>Hämta en konfigurations fil

1. Om du kommer att skapa en [beräknings instans](tutorial-1st-experiment-sdk-setup.md#azure)hoppar du över det här steget.

1. Om du planerar att använda kod i din lokala miljö som hänvisar till den här arbets ytan väljer du **hämta config.jspå** i **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config.jspå](./media/how-to-manage-workspace/configure.png)
   
   Placera filen i katalog strukturen med dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog. När du skapar en beräknings instans läggs den här filen till i rätt katalog på den virtuella datorn åt dig.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Uppgradera till Enterprise Edition

Du kan uppgradera din arbets yta från Basic Edition till Enterprise Edition för att dra nytta av de förbättrade funktionerna, till exempel låg kod upplevelser och förbättrade säkerhetsfunktioner.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj den arbets yta som du vill uppgradera.

1. Välj **Läs mer** längst upp till höger på sidan.

   [![Uppgradera en arbets yta ](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Välj **uppgradering** i fönstret som visas.


> [!IMPORTANT]
> Du kan inte nedgradera en Enterprise Edition-arbetsyta till en Basic Edition-arbetsyta. 

## <a name="find-a-workspace"></a><a name="view"></a>Hitta en arbets yta

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I det övre Sök fältet skriver du **Machine Learning**.  

1. Välj **Machine Learning**.

   ![Sök efter Azure Machine Learning arbets yta](./media/how-to-manage-workspace/find-workspaces.png)

1. Titta igenom listan med arbets ytor som har hittats. Du kan filtrera baserat på prenumeration, resurs grupper och platser.  

1. Välj en arbets yta för att visa dess egenskaper.

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

I [Azure Portal](https://portal.azure.com/)väljer du **ta bort** överst i arbets ytan som du vill ta bort.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Ta bort arbets yta":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbets ytan

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Tar bort Azure Container Registry

I arbets ytan Azure Machine Learning används Azure Container Registry (ACR) för vissa åtgärder. En ACR-instans skapas automatiskt när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

I den fullständiga självstudien får du lära dig hur du använder en arbets yta för att skapa, träna och distribuera modeller med Azure Machine Learning.

> [!div class="nextstepaction"]
> [Självstudie: träna modeller](tutorial-train-models-with-aml.md)
