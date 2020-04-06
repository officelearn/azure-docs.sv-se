---
title: Skapa schema för klassrumslabb i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du skapar scheman för klassrumslabb i Azure Lab Services så att virtuella datorer i labben startar och stängs av vid en viss tidpunkt.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4887b4359451ca5ce85042b4de42d5376bf4a730
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667770"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Skapa och hantera scheman för klassrumslabb i Azure Lab Services 
Scheman kan du konfigurera ett klassrum lab så att virtuella datorer i labbet automatiskt starta och stängas av vid en viss tidpunkt. Du kan definiera ett engångsschema eller ett återkommande schema. Följande procedurer ger dig steg för att skapa och hantera scheman för ett klassrumslabb: 

> [!IMPORTANT]
> Den schemalagda körtiden för virtuella datorer räknas inte mot den [kvot som tilldelats en användare](how-to-configure-student-usage.md#set-quotas-for-users). Kvoten är för tiden utanför schematimmar som en deltagare tillbringar på virtuella datorer. 

## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid specifika tidpunkter. Den användarkvot som du angav tidigare är den extra tid som tilldelats varje användare utanför den här schemalagda tiden. 

> [!NOTE]
> Innan vi börjar, här är hur scheman påverkar lab virtuella datorer: 
>- Den virtuella mallens dator ingår inte i scheman. 
>- Endast tilldelade virtuella datorer startas. Detta innebär att om en dator inte begärs av en slutanvändare (deltagare), startar inte maskinen på de schemalagda timmarna. 
>- Alla virtuella datorer (oavsett om de begärs av en användare eller inte) stoppas baserat på labbschemat. 

1. Växla till sidan Scheman och välj **Lägg till schemalagd händelse** i **verktygsfältet.** 

    ![Knappen Lägg till schema på sidan Scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. Bekräfta att **Standard** har valt **händelsetypen**. Du väljer **Start bara** för att ange endast starttiden för de virtuella datorerna. Du väljer **Endast Stopp** om du bara vill ange stopptiden för de virtuella datorerna. 
7. Välj det aktuella schemat i avsnittet **Upprepa.** 

    ![Knappen Lägg till schema på sidan Scheman](../media/how-to-create-schedules/select-current-schedule.png)
5. Gör följande i dialogrutan **Upprepa:**
    1. Bekräfta att **varje vecka** är inställt för fältet **Upprepa.** 
    3. Ange **startdatum**.
    4. Ange den **starttid** då du vill att de virtuella datorerna ska startas.
    5. Ange den **stopptid** då de virtuella datorerna ska stängas av. 
    6. Ange **tidszonen** för de start- och stopptider som du har angett. 
    2. Välj de dagar då du vill att schemat ska börja gälla. I följande exempel väljs måndag-torsdag. 
    8. Välj **Spara**. 

        ![Ange upprepat schema](../media/how-to-create-schedules/set-repeat-schedule.png)

3. På sidan **Lägg till schemalagd händelse** för **Anteckningar (valfritt)** anger du nu en beskrivning eller några anteckningar för schemat. 
4. På sidan **Lägg till schemalagd händelse** väljer du **Spara**. 

    ![Veckoschema](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visa scheman i kalendern
Du kan se de schemalagda datum och tider som markerats i kalendervyn enligt följande bild:

![Schemalägg i kalendervyn](../media/how-to-create-schedules/schedules-calendar.png)

Välj knappen **Idag** i det övre högra hörnet om du vill växla till aktuellt datum i kalendern. Välj **vänsterpil** om du vill växla till föregående vecka och **högerpil** för att växla till nästa vecka i kalendern. 

## <a name="edit-a-schedule"></a>Redigera ett schema
När du väljer ett markerat schema i kalendern visas knappar för att **redigera** eller **ta bort** schemat. 

![Sidan Redigera schema](../media/how-to-create-schedules/schedule-edit-button.png)

På sidan **Redigera schemalagd händelse** kan du uppdatera schemat och välja **Spara**. 

## <a name="delete-a-schedule"></a>Ta bort ett schema

1. Om du vill ta bort ett schema markerar du ett markerat schema i kalendern och väljer knappen papperskorgen (ta bort):

    ![Knappen Ta bort i verktygsfältet](../media/how-to-create-schedules/schedule-delete-button.png)
2. I dialogrutan **Ta bort schemalagd händelse** väljer du **Ja** för att bekräfta borttagningen. 



## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labbanvändare får du tillgång till klassrumslabb](how-to-use-classroom-lab.md)
