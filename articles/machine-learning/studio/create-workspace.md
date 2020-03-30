---
title: Skapa en arbetsyta
titleSuffix: ML Studio (classic) - Azure
description: Om du vill använda Azure Machine Learning Studio (klassisk) måste du ha en Machine Learning Studio -arbetsyta (klassisk). Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204468"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Skapa och dela en arbetsyta i Azure Machine Learning Studio (klassiskt)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Om du vill använda Azure Machine Learning Studio (klassisk) måste du ha en Machine Learning Studio -arbetsyta (klassisk). Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.

## <a name="create-a-studio-classic-workspace"></a>Skapa en studio -arbetsyta (klassisk)

1. Logga in på [Azure-portalen](https://portal.azure.com/)

    > [!NOTE]
    > Om du vill logga in och skapa en studioarbetsyta (klassisk) måste du vara administratör för Azure-prenumeration. 
    >
    > 

2. Klicka på **+Nytt**

3. Skriv Machine Learning **Studio (klassisk) Arbetsyta** i sökrutan och välj matchande objekt. Välj sedan **Klicka** sedan på Skapa längst ned på sidan.

4. Ange din information om arbetsytan:

   - *Arbetsytans namn* kan vara upp till 260 tecken, som inte slutar i ett blanksteg. Namnet får inte innehålla följande tecken:`< > * % & : \ ? + /`
   - Den *webbtjänstplan* du väljer (eller skapar), tillsammans med den associerade *prisnivå* du väljer, används om du distribuerar webbtjänster från den här arbetsytan.

     ![Skapa en ny studioarbetsyta](./media/create-workspace/create-new-workspace.png)

5. Klicka på **Skapa**.

> [!NOTE]
> Machine Learning Studio (klassisk) förlitar sig på ett Azure-lagringskonto som du tillhandahåller för att spara mellanliggande data när arbetsflödet körs. När arbetsytan har skapats, om lagringskontot tas bort, eller om åtkomstnycklarna ändras, slutar arbetsytan att fungera och alla experiment på arbetsytan misslyckas.
Om du av misstag tar bort lagringskontot återskapar du lagringskontot med samma namn i samma region som det borttagna lagringskontot och synkroniserar om åtkomstnyckeln. Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av Azure Portal.

När arbetsytan har distribuerats kan du öppna den i Machine Learning Studio (klassisk).

1. Bläddra till Machine Learning Studio [https://studio.azureml.net/](https://studio.azureml.net/)(klassisk) på .

2. Välj din arbetsyta i det övre högra hörnet.

    ![Välj arbetsyta](./media/create-workspace/open-workspace.png)

3. Klicka på **mina experiment**.

    ![Öppna experiment](./media/create-workspace/my-experiments.png)

Information om hur du hanterar din Studio-arbetsyta (klassisk) finns i [Hantera en klassisk arbetsyta (Azure Machine Learning Studio).](manage-workspace.md)
Om du stöter på problem med att skapa arbetsytan läser du [Felsökningsguide: Skapa och ansluta till en machine learning studio -arbetsyta (klassisk)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Dela en arbetsyta i Azure Machine Learning Studio (klassiskt)
När en Machine Learning Studio (klassisk) arbetsyta har skapats kan du bjuda in användare till din arbetsyta för att dela åtkomst till din arbetsyta och alla dess experiment, datauppsättningar, anteckningsböcker, etc. Du kan lägga till användare i en av två roller:

* **Användare** - En arbetsyteanvändare kan skapa, öppna, ändra och ta bort experiment, datauppsättningar etc. på arbetsytan.
* **Ägare** - En ägare kan bjuda in och ta bort användare på arbetsytan, utöver vad en användare kan göra.

> [!NOTE]
> Administratörskontot som skapar arbetsytan läggs automatiskt till på arbetsytan som arbetsytans ägare. Andra administratörer eller användare i den prenumerationen får dock inte automatiskt åtkomst till arbetsytan , du måste bjuda in dem uttryckligen.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Så här delar du en studioarbetsyta

1. Logga in på Machine Learning Studio (klassiskt) på[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Klicka på **INSTÄLLNINGAR** på den vänstra panelen

3. Klicka på fliken **ANVÄNDARE**

4. Klicka på **BJUD IN FLER ANVÄNDARE** LÄNGST NED PÅ sidan

    ![Studio-inställningar](./media/create-workspace/settings.png)

5. Ange en eller flera e-postadresser. Användarna behöver ett giltigt Microsoft-konto eller ett organisationskonto (från Azure Active Directory).

6. Välj om du vill lägga till användarna som ägare eller användare.

7. Klicka på knappen **OK-** bock.

Varje användare som du lägger till får ett e-postmeddelande med instruktioner om hur du loggar in på den delade arbetsytan.

> [!NOTE]
> För att användare ska kunna distribuera eller hantera webbtjänster på den här arbetsytan måste de vara en deltagare eller administratör i Azure-prenumerationen. 



