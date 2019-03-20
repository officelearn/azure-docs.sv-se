---
title: Azure Data Box Edge hantera användare | Microsoft Docs
description: Beskriver hur du använder Azure-portalen för att hantera användare på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 5642f0c1bcb7d7d8dcb17885aa6d3e9197889685
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57888443"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Använda Azure portal för att hantera användare på din Azure Data Box-Edge

Den här artikeln beskriver hur du hanterar användare på din Azure Data Box Edge. Du kan hantera Azure Data Box Edge via Azure portal eller via lokalt webbgränssnitt. Använd Azure-portalen om du vill lägga till, ändra eller ta bort användare.

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till en användare
> * Ändra användare
> * Ta bort en användare

## <a name="about-users"></a>Om användare

Användare kan ha skrivskyddad åtkomst eller fullständig behörighet. Som namnet antyder kan användare med skrivskyddad åtkomst endast visa data som delas. Användare med fullständig behörighet kan läsa data som delas, skriva till dessa resurser och ändra eller ta bort delade data.

 - **Användare med fullständig behörighet** – En lokal användare med fullständig åtkomst.
 - **Skrivskyddad användare** – En lokal användare med skrivskyddad åtkomst. Dessa användare är kopplade till resurser som tillåter skrivskyddade åtgärder.

Användarbehörigheterna definieras när användaren skapas i samband med att resurserna skapas. När behörigheterna som associeras med en användare har definierats kan dessa ändras med hjälp av Utforskaren. 


## <a name="add-a-user"></a>Lägga till en användare

Lägg till en användare genom att följa de här instruktionerna på Azure-portalen.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **översikt > användare**. Välj **+ Lägg till användare** i kommandofältet.

    ![Välj Lägg till användare](media/data-box-edge-manage-users/add-user-1.png)

2. Ange användarnamnet och lösenordet för den användare som du vill lägga till. Bekräfta lösenordet och välj **Lägg till**.

    ![Ange användarnamn och lösenord](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Dessa användare har reserverats av systemet och bör inte användas: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Ett meddelande visas när användare skapas startar och är klar. När användaren har skapats från kommandofältet väljer **uppdatera** att visa den uppdaterade listan med användare.


## <a name="modify-user"></a>Ändra användare

Du kan ändra lösenordet som associeras med en användare när användaren har skapats. Välj i listan med användare. Ange och bekräfta det nya lösenordet. Spara ändringarna.
 
![Ändra användare](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Ta bort en användare

Utför följande steg på Azure-portalen om du vill ta bort en användare.


1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **översikt > användare**.

    ![Välj användare för att ta bort](media/data-box-edge-manage-users/delete-user-1.png)

2. Välj en användare i listan över användare och välj sedan **ta bort**.  

   ![Välja Ta bort](media/data-box-edge-manage-users/delete-user-2.png)

3. Bekräfta borttagningen när du uppmanas att göra det. 

   ![Bekräfta borttagning](media/data-box-edge-manage-users/delete-user-3.png)

Listan över användare uppdateras för att återspegla den borttagna användaren.

![Uppdaterade listan med användare](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar bandbredd](data-box-edge-manage-bandwidth-schedules.md).
