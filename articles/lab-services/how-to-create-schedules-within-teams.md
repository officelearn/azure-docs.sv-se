---
title: Skapa Azure Lab Services scheman inom team
description: Lär dig hur du skapar scheman för labb tjänster i Teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946853"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Skapa och hantera labb tjänst scheman i team

Med scheman kan du konfigurera ett klass rums labb så att virtuella datorer i labbet startar och stängs av automatiskt vid en viss tidpunkt. Du kan definiera ett eng ång slö schema eller ett återkommande schema. Följande procedurer ger dig anvisningar om hur du skapar och hanterar scheman för ett klass rums labb: 

Så här påverkar schemat virtuella labb datorer: 

- Den virtuella datorns mall ingår inte i scheman. 
- Endast tilldelade virtuella datorer har startats. Det innebär att om en dator inte begärs av en slutanvändare (student) kommer datorn inte att starta under de schemalagda timmarna. 
- Alla virtuella datorer (om de begärs av en användare eller inte) stoppas baserat på labb schema. 

> [!IMPORTANT]
> Den schemalagda körningen av virtuella datorer räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Skapa/redigera/ta bort ett schema för labbet

Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid vissa tidpunkter. Användar kvoten du angav tidigare är den ytterligare tid som tilldelats varje användare utanför den schemalagda tiden. 

1. Växla till sidan **scheman** och välj **Lägg till schemalagd händelse** i verktygsfältet (högst upp till vänster i fönstret). 
1. Ange följande parametrar för schemat:
    1. Bekräfta att **standard** är valt **händelse typ**. Du väljer **Start bara** för att ange start tiden för de virtuella datorerna. Du väljer **stoppa endast** om du bara vill ange stopp tiden för de virtuella datorerna. 
    1. Ange **start datum**.
    1. Ange **Start tiden** då du vill att de virtuella datorerna ska startas.
    1. Ange **stopp tiden** som de virtuella datorerna ska stängas av. 
    1. Ange **tids zonen** för start-och stopp tider som du har angett. 
    1. I avsnittet **Upprepa** väljer du **varje vecka** eller **aldrig**. 
    1. För **anteckningar (valfritt)** anger du en beskrivning eller information om schemat. 
1. Klicka på **Spara**. 

### <a name="view-schedules-in-calendar"></a>Visa scheman i kalendern

Du kan se schemalagda datum och tider som är markerade i kalendern. Välj knappen **idag** i det övre högra hörnet för att växla till det aktuella datumet i kalendern. Välj **vänsterpilen** för att växla till föregående **vecka och HÖGERPIL för att växla** till nästa vecka i kalendern. 

### <a name="edit-a-schedule"></a>Redigera ett schema

När du väljer ett markerat schema i kalendern visas knappar för att **Redigera** eller **ta bort** schemat. 

På sidan **Redigera schemalagd händelse** kan du uppdatera schemat och välja **Spara**. 

### <a name="delete-a-schedule"></a>Ta bort ett schema

1. Om du vill ta bort ett schema väljer du ett markerat schema i kalendern och väljer pappers korgs ikonen (ta bort):
1. I dialog rutan **ta bort schemalagd händelse** väljer du **Ja** för att bekräfta borttagningen. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Inställningar för automatisk avstängning och från koppling

Längst upp på sidan kommer du att märka en länk till inställningarna för automatisk **avstängning** .

Du kan aktivera flera kostnads kontroll funktioner för automatisk avstängning för att proaktivt förhindra ytterligare kostnader när de virtuella datorerna inte används aktivt. Kombinationen av följande tre funktioner för automatisk avstängning och från koppling fångar upp de flesta fall där användare av misstag lämnar sina virtuella datorer som kör:
 
- Koppla automatiskt bort användare från virtuella datorer som operativ systemet bedömer inaktivt.
- Stäng virtuella datorer automatiskt när användarna kopplar från.
- Stäng av virtuella datorer som har startats automatiskt, men som inte ansluter till dem.

Om du vill ha mer information klickar du på *informations* ikonen bredvid alternativen i inställningarna.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Kom igång och skapa ett labb tjänst labb från team](how-to-get-started-create-lab-within-teams.md)
- [Hantera användar listor i labb tjänster från team](how-to-manage-user-lists-within-teams.md)
- [Hantera en VM-pool i labb tjänster från team](how-to-manage-vm-pool-within-teams.md)
- [Åtkomst till en virtuell dator (elev visning) i labb tjänster från team](how-to-access-vm-for-students-within-teams.md)