---
title: Skapa schema för klassrum labb i Azure Lab Services | Microsoft Docs
description: Lär dig mer om att skapa scheman för klassrum labb i Azure Lab Services så att virtuella datorer i labs starta och stänga av en viss tid.
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
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55501363"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Skapa och hantera scheman för klassrum labb i Azure Lab Services 
Scheman kan du konfigurera ett klassrumslabb så att virtuella datorer i labbet automatiskt starta och stänga av en viss tid. Du kan definiera en engångsschema eller ett återkommande schema. Följande procedurer ger dig steg för att skapa och hantera scheman för ett klassrumslabb: 

> [!IMPORTANT]
> Schemalagda körningstiden för virtuella datorer räknas inte mot den [kvot som har tilldelats till en användare](how-to-configure-student-usage.md#set-quotas-per-user). Kvoten är tiden utanför Schemalägg timmar som en student tillbringar på virtuella datorer. 

## <a name="add-a-schedule-once"></a>Lägg till ett schema (en gång)

1. Växla till den **scheman** och välj **Lägg till schema** i verktygsfältet. 

    ![Lägg till schemaknapp på sidan scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. På den **Lägg till schema** bekräftar som **när** alternativet är markerat längst upp. Om du inte väljer **när**. 
3. För **schemalägga datum (krävs)**, ange ett datum eller välja kalenderikonen för att välja ett datum. 
4. För **starttid**, väljer du den tid när du vill att de virtuella datorerna startas. Starttiden är obligatorisk om sluttid har angetts. Välj **Remove Starthändelse** om du vill ange endast sluttid. Om den **starttid** är inaktiverat, Välj **Lägg till Starthändelse** bredvid listrutan så att den. 
5. För **stopptid**, väljer du den tid när du vill att de virtuella datorerna ska stängas. Sluttid krävs om starttiden inte anges. Välj **händelsen för ta bort stoppa** om du vill ange endast starttid. Om den **stopptid** är inaktiverat, Välj **Lägg till Stoppa händelse** bredvid listrutan så att den.
6. För **tidszon (krävs)**, Välj tidszon för start och stopptider som du har angett. 
7. För **anteckningar**, ange en beskrivning av eller anteckningar för schemat. 
8. Välj **Spara**. 

    ![Onetime schema](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Lägg till ett återkommande schema (varje vecka)

1. Växla till den **scheman** och välj **Lägg till schema** i verktygsfältet. 

    ![Lägg till schemaknapp på sidan scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. På den **Lägg till schema** växlar du till **veckovisa** högst upp. 
3. För **schemalägga dagar (obligatoriskt)**, Välj de dagar som du vill att schemat ska börja gälla. I följande exempel, har måndag – fredag valts. 
4. För den **från** fältet, anger du den **schemalägga startdatum** eller välj ett datum genom att välja den **kalender** knappen. Det här fältet är obligatoriskt. 
5. För **det schemalagda slutdatumet**anger eller väljer ett slutdatum där de virtuella datorerna är stängs av. 
6. För **starttid**, väljer du den tid då du vill att de virtuella datorerna startas. Starttiden är obligatorisk om sluttid har angetts. Välj **Remove Starthändelse** om du vill ange endast sluttid. Om den **starttid** är inaktiverat, Välj **Lägg till Starthändelse** bredvid listrutan så att den. 
7. För **stopptid**, väljer du den tid då du vill att de virtuella datorerna ska stängas. Sluttid krävs om starttiden inte anges. Välj **händelsen för ta bort stoppa** om du vill ange endast starttid. Om den **stopptid** är inaktiverat, Välj **Lägg till Stoppa händelse** bredvid listrutan så att den.
8. För **tidszon (krävs)**, Välj tidszon för start och stopptider som du har angett.  
9. För **anteckningar**, ange en beskrivning av eller anteckningar för schemat. 
10. Välj **Spara**. 

    ![Schema för veckovis](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visa scheman i kalender
Du kan se schemalagda datum och tider som markerats i kalendervyn, enligt följande bild:

![Scheman i kalendervyn](../media/how-to-create-schedules/schedules-in-calendar.png)

Välj den **idag** i det övre högra hörnet att växla till aktuellt datum i kalendern. Välj **VÄNSTERPIL** att växla till föregående vecka och **högerpilen** att växla till nästa vecka i kalendern. 

## <a name="edit-a-schedule"></a>Redigera ett schema
När du dubbelklickar på enligt ett schema som är markerade i kalendern eller Välj den **penna** knappen i verktygsfältet, visas den **Redigera schema** sidan. Uppdaterar inställningarna på den här sidan är samma som uppdaterar inställningarna i den **Lägg till schema** sidan enligt beskrivningen i den [lägga till ett återkommande schema](#add-a-recurring-schedule-weekly) avsnittet. 

![Redigera schemasidan](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Ta bort ett schema

1. Välj Papperskorgen om du vill ta bort ett schema (ta bort) i verktygsfältet, enligt följande bild:

    ![Ta bort i verktygsfältet](../media/how-to-create-schedules/delete-schedule-button.png)

    Du kan använda knappen Ta bort för alla schemalagda datum och tider i kalendern och välj **ta bort**. 
2. På den **ta bort scheman** väljer **Ja**.

    ![Ta bort scheman bekräftelse](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)
