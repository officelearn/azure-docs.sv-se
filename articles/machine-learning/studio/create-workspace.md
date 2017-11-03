---
title: Skapa en arbetsyta i Machine Learning | Microsoft Docs
description: "Skapa en arbetsyta för Azure Machine Learning Studio"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye;bradsev;ahgyger
ms.openlocfilehash: 4e1fa0a9abd4721d15a94923263ff2f521bceee8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Skapa och dela en Azure Machine Learning-arbetsyta
Den här menyn innehåller länkar till avsnitt som beskriver hur du ställer in de olika datavetenskap miljöer som används av Cortana Analytics processen (CAP).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Om du vill använda Azure Machine Learning Studio, behöver du en Machine Learning-arbetsytan. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Att skapa en arbetsyta
1. Logga in på den [Azure-portalen](https://portal.azure.com/)

    > [!NOTE]
    > Du måste vara administratör för Azure-prenumeration för att logga in och skapa en arbetsyta. 
    >
    > 

2. Klicka på **+ nytt**

3. Välj **Intelligence + analys**, klickar du på **Machine Learning-arbetsytan**, klicka på **skapa**

4. Ange arbetsyteinformation om

    - Den *Arbetsytenamn* får vara högst 260 tecken som inte avslutas med ett blanksteg. Namnet får inte innehålla följande tecken:`< > * % & : \ ? + /`
    - Den *web service-plan* du välja (eller skapa), tillsammans med den associerade *prisnivån* du väljer, används om du distribuerar webbtjänster från den här arbetsytan.

    ![Skapa en ny arbetsyta](./media/create-workspace/create-new-workspace.png)

5. Klicka på **Skapa**

När arbetsytan har distribuerats kan öppna du den i Machine Learning Studio.

1. Bläddra till Maskininlärning Studio på [https://studio.azureml.net/](https://studio.azureml.net/).

2. Välj din arbetsyta i det övre högra hörnet.

    ![Välj arbetsyta](./media/create-workspace/open-workspace.png)

3. Klicka på **Mina experiment**.

    ![Öppna experiment](./media/create-workspace/my-experiments.png)

Information om hur du hanterar din arbetsyta finns [hantera en Azure Machine Learning-arbetsytan](manage-workspace.md).
Om det uppstår ett problem uppstod när din arbetsyta, se [felsökningsguide: skapa och ansluta till en Machine Learning-arbetsytan](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Dela en Azure Machine Learning-arbetsytan
När en Machine Learning arbetsytan har skapats, du kan erbjuda användare till din arbetsyta för att dela åtkomst till din arbetsyta och alla dess experiment, datauppsättningar, bärbara datorer och så vidare. Du kan lägga till användare i en av två roller:

* **Användaren** -arbetsytan användare kan skapa, öppna, ändra och ta bort experiment, datauppsättningar, etc. i arbetsytan.
* **Ägare** – bjuda in en ägare och ta bort användare i arbetsytan, förutom vad användaren kan göra.

> [!NOTE]
> Administratörskontot som skapar arbetsytan läggs automatiskt till arbetsytan som arbetsyta ägare. Men andra administratörer eller användare i den prenumerationen beviljas automatiskt inte åtkomst till arbetsytan, måste du be dem explicit.
> 
> 

### <a name="to-share-a-workspace"></a>Dela en arbetsyta

1. Logga in på datorn Learning Studio på [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. I den vänstra rutan klickar du på **inställningar**

3. Klicka på den **användare** fliken

4. Klicka på **bjuda in fler användare** längst ned på sidan

    ![Inställningar för Studio](./media/create-workspace/settings.png)

5. Ange en eller flera e-postadresser. De krävs ett giltigt microsoftkonto eller ett organisationskonto (från Azure Active Directory).

6. Välj om du vill lägga till användarna som ägare eller användare.

7. Klicka på den **OK** markering knappen.

Varje användare som du lägger till kommer att få ett e-postmeddelande med anvisningar om hur du loggar in på den delade arbetsytan.

> [!NOTE]
> De måste vara en deltagare eller en administratör i Azure-prenumeration för användare för att kunna distribuera eller hantera webbtjänster i den här arbetsytan. 



