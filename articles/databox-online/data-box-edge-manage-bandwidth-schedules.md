---
title: Azure Data Box Edge hantera bandbreddsscheman | Microsoft-dokument
description: Beskriver hur du använder Azure-portalen för att hantera bandbreddsscheman på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756891"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Använda Azure-portalen för att hantera bandbreddsscheman på din Azure Data Box Edge  

I den här artikeln beskrivs hur du hanterar användare på din Azure Data Box Edge. Med bandbreddsscheman kan du konfigurera användningen av nätverksbandbredd mellan scheman under olika tider på dagen. Schemana kan användas för upp- och nedladdningar mellan din enhet och molnet.

Du kan lägga till, ändra eller ta bort bandbreddsscheman för din Data Box Edge via Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till ett schema
> * Ändra schema
> * Ta bort ett schema


## <a name="add-a-schedule"></a>Lägga till ett schema

Gör följande steg i Azure-portalen för att lägga till ett schema.

1. Gå till **Bandbredd**i Azure-portalen för din Data Box Edge-resurs .
2. Välj **+ Lägg till schema**i den högra rutan .

    ![Välj bandbredd](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. I **Lägg till schema**: 

   1. Ange **Startdag**, **Slutdag**, **Starttid** och **Sluttid** för schemat.
   2. Markera alternativet **Hela dagen** om det här schemat ska köras hela dagen.
   3. **Bandbreddshastighet** är den bandbredd i megabit per sekund (Mbit/s) som används av din enhet i åtgärder som rör molnet (upp- och nedladdning). Ange ett tal mellan 20 och 1 000 000 007 för det här fältet.
   4. Markera **Obegränsad** bandbredd om du inte vill begränsa upp- och nedladdningen.
   5. Välj **Lägg till**.

      ![Lägg till schema](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Ett schema skapas med de angivna parametrarna. Schemat visas sedan i listan över bandbreddsscheman i portalen.

    ![Uppdaterad lista över bandbreddsscheman](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Redigera schema

Gör följande om du vill redigera ett bandbreddsschema.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Bandbredd**. 
2. Välj och välj ett schema som du vill ändra i listan över bandbreddsscheman.
    ![Välj bandbreddsschema](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Gör önskade ändringar och spara ändringarna.

    ![Ändra användare](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. När schemat har ändrats uppdateras listan över scheman så att den återspeglar det ändrade schemat.

    ![Ändra användare](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ta bort ett schema

Gör följande för att ta bort ett bandbreddsschema som är associerat med databoxens edge-enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Bandbredd**.  

2. I listan över bandbreddsscheman väljer du det schema som du vill ta bort. Välj **Ta bort**i **redigeringsschemat**. När du uppmanas att bekräfta väljer du **Ja**.

   ![Ta bort en användare](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. När schemat har tagits bort uppdateras listan över scheman.


## <a name="next-steps"></a>Nästa steg

- Läs om hur du [hanterar resurser](data-box-edge-manage-shares.md).
