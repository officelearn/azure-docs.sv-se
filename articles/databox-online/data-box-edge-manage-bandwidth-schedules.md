---
title: Azure Data Box Edge hantera scheman för bandbredd | Microsoft Docs
description: Beskriver hur du använder Azure-portalen kan du hantera scheman för bandbredd på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/12/2019
ms.author: alkohli
ms.openlocfilehash: 2c8d21a9494bace7d60b90637450cb10a5f3576e
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2019
ms.locfileid: "58109366"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Använd Azure-portalen kan du hantera scheman för bandbredd på din Azure Data Box Edge  

Den här artikeln beskriver hur du hanterar användare på din Azure Data Box Edge. Med bandbreddsscheman kan du konfigurera användningen av nätverksbandbredd mellan scheman under olika tider på dagen. Schemana kan användas för upp- och nedladdningar mellan din enhet och molnet.

Du kan lägga till, ändra eller ta bort schemana bandbredd för din Data Box Edge via Azure portal.

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till ett schema
> * Ändra schema
> * Ta bort ett schema


## <a name="add-a-schedule"></a>Lägga till ett schema

Utför följande steg i Azure portal för att lägga till ett schema.

1. I Azure-portalen för din Data Box Edge-resurs, går du till **bandbredd**.
2. I den högra rutan väljer **+ Lägg till schema**.

    ![Välj bandbredd](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. I **Lägg till schema**: 

   1. Ange **Startdag**, **Slutdag**, **Starttid** och **Sluttid** för schemat.
   2. Kontrollera den **hela dagen** om det här schemat ska köras hela dagen.
   3. **Bandbreddshastighet** är den bandbredd i megabit per sekund (Mbit/s) som används av din enhet i åtgärder som rör molnet (upp- och nedladdning). Ange ett tal mellan 20 och 1,000,000,007 för det här fältet.
   4. Markera **Obegränsad** bandbredd om du inte vill begränsa upp- och nedladdningen.
   5. Välj **Lägg till**.

      ![Lägg till schema](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Ett schema skapas med de angivna parametrarna. Schemat visas sedan i listan över bandbreddsscheman i portalen.

    ![Uppdaterade listan med bandbredd scheman](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Redigera schema

Gör följande om du vill redigera ett bandbreddsschema.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **bandbredd**. 
2. Välj från listan över scheman för bandbredd, och välj ett schema som du vill ändra.
    ![Välj bandbredd schema](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Gör önskade ändringar och spara ändringarna.

    ![Ändra användare](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. När schemat har ändrats uppdateras listan över scheman så att den återspeglar det ändrade schemat.

    ![Ändra användare](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ta bort ett schema

Utför följande steg för att ta bort ett schema för bandbredd som är associerade med din Data Box Edge-enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **bandbredd**.  

2. I listan över bandbreddsscheman väljer du det schema som du vill ta bort. I den **Redigera schema**väljer **ta bort**. När du uppmanas att bekräfta väljer **Ja**.

   ![Ta bort en användare](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. När schemat har tagits bort uppdateras listan över scheman.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hantera filresurser](data-box-edge-manage-shares.md).
