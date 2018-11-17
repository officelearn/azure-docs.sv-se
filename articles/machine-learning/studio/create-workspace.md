---
title: Skapa en Machine Learning Studio-arbetsyta | Microsoft Docs
description: Skapa en arbetsyta för Azure Machine Learning Studio
services: machine-learning
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 606ee124d8e7a59fc653f11f2ad7542fc84af4e4
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823443"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Skapa och dela en Azure Machine Learning-arbetsyta

Du måste ha en Machine Learning Studio-arbetsyta om du vill använda Azure Machine Learning Studio. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Skapa en arbetsyta
1. Logga in på [Azure-portalen](https://portal.azure.com/)

    > [!NOTE]
    > Du måste vara administratör för Azure-prenumeration för att logga in och skapa en arbetsyta. 
    >
    > 

2. Klicka på **+ ny**

3. I sökrutan skriver **Machine Learning Studio-arbetsyta** och välj det matchande objektet. Klicka sedan på Välj **skapa** längst ned på sidan.

4. Ange informationen om arbetsytan:

    - Den *Arbetsytenamn* kan vara upp till 260 tecken, inte slutar med ett blanksteg. Namnet får inte innehålla följande tecken: `< > * % & : \ ? + /`
    - Den *webbtjänstplan* du väljer (eller skapa), tillsammans med den associerade *prisnivån* du väljer, används om du distribuerar webbtjänster från den här arbetsytan.

    ![Skapa en ny arbetsyta](./media/create-workspace/create-new-workspace.png)

5. Klicka på **Skapa**.

När arbetsytan har distribuerats kan öppna du den i Machine Learning Studio.

1. Bläddra på Machine Learning Studio på [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Välj din arbetsyta i det övre högra hörnet.

    ![Välj arbetsyta](./media/create-workspace/open-workspace.png)

3. Klicka på **Mina experiment**.

    ![Öppna experiment](./media/create-workspace/my-experiments.png)

Information om hur du hanterar din arbetsyta finns i [hantera en Azure Machine Learning-arbetsyta](manage-workspace.md).
Om du stöter på problem med att skapa din arbetsyta kan se [felsökningsguide: skapa och ansluta till en Machine Learning-arbetsyta](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Dela en Azure Machine Learning-arbetsyta
När en Machine Learning arbetsytan har skapats, du kan bjuda in användare till din arbetsyta dela åtkomst till din arbetsyta och alla dess experiment, datauppsättningar, anteckningsböcker, osv. Du kan lägga till användare i en av två roller:

* **Användaren** – en arbetsyta-användare kan skapa, öppna, ändra och ta bort experiment, datauppsättningar, etc. på arbetsytan.
* **Ägare** – en ägare kan bjuda in och ta bort användare i arbetsytan, utöver vad en användare kan göra.

> [!NOTE]
> Det administratörskonto som skapar arbetsytan läggs automatiskt till arbetsytan som arbetsyta ägare. Men andra administratörer eller användare i den aktuella prenumerationen beviljas automatiskt inte åtkomst till arbetsytan – du behöver att bjuda in dem explicit.
> 
> 

### <a name="to-share-a-workspace"></a>Dela en arbetsyta

1. Logga in på Machine Learning Studio på [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. I den vänstra rutan klickar du på **inställningar**

3. Klicka på den **användare** fliken

4. Klicka på **bjuda in fler användare** längst ned på sidan

    ![Studio-inställningar](./media/create-workspace/settings.png)

5. Ange en eller flera e-postadresser. Användarna behöver ett giltigt microsoftkonto eller ett organisationskonto (från Azure Active Directory).

6. Välj om du vill lägga till användarna som ägare eller användare.

7. Klicka på den **OK** markeringen knappen.

Varje användare som du lägger till får ett e-postmeddelande med anvisningar om hur du logga in på den delade arbetsytan.

> [!NOTE]
> För användare för att kunna distribuera och hantera webbtjänster på den här arbetsytan kan vara de deltagare / administratör i Azure-prenumeration. 



