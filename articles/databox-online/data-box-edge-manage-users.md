---
title: Azure Data Box Edge hantera användare | Microsoft-dokument
description: Beskriver hur du använder Azure-portalen för att hantera användare på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946130"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Använda Azure-portalen för att hantera användare på din Azure Data Box Edge

I den här artikeln beskrivs hur du hanterar användare på din Azure Data Box Edge. Du kan hantera Azure Data Box Edge via Azure-portalen eller via det lokala webbgränssnittet. Använd Azure-portalen om du vill lägga till, ändra eller ta bort användare.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till en användare
> * Ändra användare
> * Ta bort en användare

## <a name="about-users"></a>Om användare

Användare kan ha skrivskyddad åtkomst eller fullständig behörighet. Som namnet antyder kan användare med skrivskyddad åtkomst endast visa data som delas. Användare med fullständig behörighet kan läsa data som delas, skriva till dessa resurser och ändra eller ta bort delade data.

 - **Användare med fullständig behörighet** – En lokal användare med fullständig åtkomst.
 - **Skrivskyddad användare** – En lokal användare med skrivskyddad åtkomst. Dessa användare är kopplade till resurser som tillåter skrivskyddade åtgärder.

Användarbehörigheterna definieras när användaren skapas i samband med att resurserna skapas. Ändring av behörigheter på resursnivå stöds för närvarande inte.

## <a name="add-a-user"></a>Lägga till en användare

Lägg till en användare genom att följa de här instruktionerna på Azure-portalen.

1. Gå till databoxkantsresursen i Azure-portalen och gå sedan till **Översikt > Användare**. Välj **+ Lägg till användare** i kommandofältet.

    ![Välj lägg till användare](media/data-box-edge-manage-users/add-user-1.png)

2. Ange användarnamnet och lösenordet för den användare som du vill lägga till. Bekräfta lösenordet och välj **Lägg till**.

    ![Ange användarnamn och lösenord](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Dessa användare är reserverade i systemet och bör inte användas: Administratör, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount och Gäst.  

3. Ett meddelande visas när användarskapandet startar och slutförs. När användaren har skapats väljer du **Uppdatera** i kommandofältet om du vill visa den uppdaterade användarlistan.


## <a name="modify-user"></a>Ändra användare

Du kan ändra lösenordet som associeras med en användare när användaren har skapats. Välj i listan över användare. Ange och bekräfta det nya lösenordet. Spara ändringarna.
 
![Ändra användare](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Ta bort en användare

Utför följande steg på Azure-portalen om du vill ta bort en användare.


1. Gå till databoxkantsresursen i Azure-portalen och gå sedan till **Översikt > Användare**.

    ![Välj användare att ta bort](media/data-box-edge-manage-users/delete-user-1.png)

2. Välj en användare i listan över användare och välj sedan **Ta bort**.  

   ![Välja Ta bort](media/data-box-edge-manage-users/delete-user-2.png)

3. Bekräfta borttagningen när du uppmanas att göra det. 

   ![Bekräfta borttagning](media/data-box-edge-manage-users/delete-user-3.png)

Listan över användare uppdateras för att återspegla den borttagna användaren.

![Uppdaterad lista över användare](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig att [hantera bandbredd](data-box-edge-manage-bandwidth-schedules.md).
