---
title: Skapa en arbetsyta
titleSuffix: ML Studio (classic) - Azure
description: Om du vill använda Azure Machine Learning Studio (klassisk) måste du ha en Machine Learning Studio (klassisk)-arbets yta. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 91ba4d1f7d32071cce0de1de528abf02982ce7be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427622"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Skapa och dela en Azure Machine Learning Studio (klassisk)-arbets yta

Om du vill använda Azure Machine Learning Studio (klassisk) måste du ha en Machine Learning Studio (klassisk)-arbets yta. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.

## <a name="create-a-studio-classic-workspace"></a>Skapa en Studio-arbetsyta (klassisk)

1. Logga in på [Azure-portalen](https://portal.azure.com/)

    > [!NOTE]
    > Du måste vara administratör för Azure-prenumeration för att kunna logga in och skapa en Studio-arbetsyta (klassisk). 
    >
    > 

2. Klicka på **+ ny**

3. I rutan Sök skriver du **Machine Learning Studio (klassisk) arbets yta** och väljer det matchande objektet. Klicka sedan på Välj **skapa** längst ned på sidan.

4. Ange informationen om arbetsytan:

   - Den *Arbetsytenamn* kan vara upp till 260 tecken, inte slutar med ett blanksteg. Namnet får inte innehålla följande tecken: `< > * % & : \ ? + /`
   - Den *webbtjänstplan* du väljer (eller skapa), tillsammans med den associerade *prisnivån* du väljer, används om du distribuerar webbtjänster från den här arbetsytan.

     ![Skapa en ny Studio-arbetsyta (klassisk)](./media/create-workspace/create-new-workspace.png)

5. Klicka på **Skapa**.

> [!NOTE]
> Machine Learning Studio (klassisk) förlitar sig på ett Azure Storage-konto som du anger för att spara mellanliggande data när arbets flödet körs. Om lagrings kontot tas bort när arbets ytan har skapats, eller om åtkomst nycklarna ändras, slutar arbets ytan att fungera och alla experiment på arbets ytan kommer att Miss förväntas.
Om du råkar ta bort lagrings kontot, återskapar du lagrings kontot med samma namn i samma region som det borttagna lagrings kontot och synkroniserar om åtkomst nyckeln. Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av Azure Portal.

När arbets ytan har distribuerats kan du öppna den i Machine Learning Studio (klassisk).

1. Bläddra till Machine Learning Studio (klassisk) på [https://studio.azureml.net/](https://studio.azureml.net/).

2. Välj din arbetsyta i det övre högra hörnet.

    ![Välj arbetsyta](./media/create-workspace/open-workspace.png)

3. Klicka på **Mina experiment**.

    ![Öppna experiment](./media/create-workspace/my-experiments.png)

Information om hur du hanterar din Studio (klassisk) arbets yta finns i [hantera en Azure Machine Learning Studio (klassisk)-arbets yta](manage-workspace.md).
Om du stöter på problem med att skapa din arbets yta, se [fel söknings guide: skapa och ansluta till en Machine Learning Studio (klassisk)-arbets yta](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Dela en Azure Machine Learning Studio (klassisk)-arbets yta
När en Machine Learning Studio (klassisk)-arbets yta har skapats kan du bjuda in användare till din arbets yta att dela åtkomst till din arbets yta och alla experiment, data uppsättningar, antecknings böcker osv. Du kan lägga till användare i en av två roller:

* **Användaren** – en arbetsyta-användare kan skapa, öppna, ändra och ta bort experiment, datauppsättningar, etc. på arbetsytan.
* **Ägare** – en ägare kan bjuda in och ta bort användare i arbetsytan, utöver vad en användare kan göra.

> [!NOTE]
> Det administratörskonto som skapar arbetsytan läggs automatiskt till arbetsytan som arbetsyta ägare. Men andra administratörer eller användare i den aktuella prenumerationen beviljas automatiskt inte åtkomst till arbetsytan – du behöver att bjuda in dem explicit.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Dela en Studio-arbetsyta (klassisk)

1. Logga in på Machine Learning Studio (klassisk) på [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

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



