---
title: Skapa ett schema för klass rums labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar scheman för klass rums labb i Azure Lab Services så att virtuella datorer i labbet startar och stängs ned vid en viss tidpunkt.
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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385597"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Skapa och hantera scheman för klass rums labb i Azure Lab Services 
Med scheman kan du konfigurera ett klass rums labb så att virtuella datorer i labbet startar och stängs av automatiskt vid en viss tidpunkt. Du kan definiera ett eng ång slö schema eller ett återkommande schema. Följande procedurer ger dig anvisningar om hur du skapar och hanterar scheman för ett klass rums labb: 

> [!IMPORTANT]
> Den schemalagda körningen av virtuella datorer räknas inte mot den [kvot som tilldelats till en användare](how-to-configure-student-usage.md#set-quotas-for-users). Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

## <a name="add-a-schedule-once"></a>Lägg till ett schema (en gång)

1. Växla till sidan **scheman** och välj **Lägg till schema** i verktygsfältet. 

    ![Knappen Lägg till schema på sidan scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. På sidan **Lägg till schema** bekräftar du att alternativet **när** är markerat längst upp. Om den inte är det väljer du **en gång**. 
3. För **schema datum (obligatoriskt)** anger du datumet eller väljer Kalender ikonen för att välja ett datum. 
4. För **Start tid**väljer du den tid då du vill att de virtuella datorerna ska starta. Start tiden måste anges om stopp tiden inte har angetts. Välj **ta bort start händelse** om du bara vill ange stopp tiden. Om **Start tiden** är inaktive rad väljer du **Lägg till Start händelse** bredvid den nedrullningsbara listan för att aktivera det. 
5. För **stopp tid**väljer du den tid då du vill att de virtuella datorerna ska stängas av. Stopp tiden måste anges om start tiden inte har angetts. Välj **ta bort stopp händelse** om du bara vill ange start tiden. Om **stopp tiden** är inaktive rad väljer du **Lägg till stopp händelse** bredvid List rutan för att aktivera det.
6. För **tidszon (krävs)** väljer du tids zonen för start-och stopp tider som du har angett. 
7. För **anteckningar**anger du en beskrivning eller information om schemat. 
8. Välj **Spara**. 

    ![Databasmigrering-schema](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Lägg till ett återkommande schema (veckovis)

1. Växla till sidan **scheman** och välj **Lägg till schema** i verktygsfältet. 

    ![Knappen Lägg till schema på sidan scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. På sidan **Lägg till schema** växlar du till **veckovis** överst. 
3. I **schema dagar (obligatoriskt)** väljer du de dagar som du vill att schemat ska börja gälla. I följande exempel är måndag-fredag markerat. 
4. I fältet **från** anger du schemats **start datum** eller väljer ett datum genom att välja knappen **kalender** . Det här fältet är obligatoriskt. 
5. För **schema slutdatum**anger eller väljer du ett slutdatum som de virtuella datorerna ska stängas av. 
6. För **Start tid**väljer du den tid då du vill att de virtuella datorerna ska startas. Start tiden måste anges om stopp tiden inte har angetts. Välj **ta bort start händelse** om du bara vill ange stopp tiden. Om **Start tiden** är inaktive rad väljer du **Lägg till Start händelse** bredvid den nedrullningsbara listan för att aktivera det. 
7. För **stopp tid**väljer du den tid då du vill att de virtuella datorerna ska stängas av. Stopp tiden måste anges om start tiden inte har angetts. Välj **ta bort stopp händelse** om du bara vill ange start tiden. Om **stopp tiden** är inaktive rad väljer du **Lägg till stopp händelse** bredvid List rutan för att aktivera det.
8. För **tidszon (krävs)** väljer du tids zonen för start-och stopp tider som du har angett.  
9. För **anteckningar**anger du en beskrivning eller information om schemat. 
10. Välj **Spara**. 

    ![Vecko schema](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visa scheman i kalendern
Du kan se schemalagda datum och tider som marker ATS i vyn Kalender enligt följande bild:

![Scheman i vyn Kalender](../media/how-to-create-schedules/schedules-in-calendar.png)

Välj knappen **idag** i det övre högra hörnet för att växla till det aktuella datumet i kalendern. Välj **vänsterpilen** för att växla till föregående **vecka och HÖGERPIL** för att växla till nästa vecka i kalendern. 

## <a name="edit-a-schedule"></a>Redigera ett schema
När du dubbelklickar på ett markerat schema i kalendern eller väljer **Penn** knappen i verktygsfältet visas sidan **Redigera schema** . Att uppdatera inställningarna på den här sidan är samma som att uppdatera inställningarna på sidan **Lägg till schema** enligt beskrivningen i avsnittet [Lägg till ett återkommande schema](#add-a-recurring-schedule-weekly) . 

![Sidan Redigera schema](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Ta bort ett schema

1. Om du vill ta bort ett schema väljer du knappen pappers korg (ta bort) i verktygsfältet, som du ser i följande bild:

    ![Knappen Ta bort i verktygsfältet](../media/how-to-create-schedules/delete-schedule-button.png)

    Du kan använda knappen Ta bort för schemalagda datum och tider i kalendern och välja **ta bort**. 
2. På sidan **ta bort scheman** väljer du **Ja**.

    ![Bekräftelse av borttagnings scheman](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)
