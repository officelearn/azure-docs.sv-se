---
title: "Skapa aktivitet loggen aviseringar och hantera dem med hjälp av den nya aviseringar (förhandsgranskning)-miljön i Azure-Monitor | Microsoft Docs"
description: "Den här artikeln innehåller information om hur du skapar aktiviteten loggen aviseringar från fliken aviseringar (förhandsgranskning) under Azure-Monitor. Den här artikeln beskrivs den nya användarupplevelsen för den här funktionen."
author: JYOTHIRMAISURI
manager: vvithal
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: 
ms.openlocfilehash: 44e1a7fe17515e23fcfd32c37c1ef4837ea8140c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>Skapa aktivitetsloggen får aviseringar via de nya aviseringarna (förhandsgranskning)

Aktiviteten loggen aviseringar är de aviseringar som hämta aktiveras när en ny aktivitet logga en händelse inträffar som matchar de villkor som anges i aviseringen.

Dessa aviseringar är för Azure-resurser kan skapas med hjälp av en Azure Resource Manager-mall. De kan också skapa, uppdatera, eller tas bort i Azure-portalen. Den här artikeln beskriver begrepp bakom aktivitet loggen aviseringar. Den sedan visar hur du använder Azure-portalen för att konfigurera en avisering på aktiviteten logghändelser med hjälp av den nya upplevelsen i [aviseringar i Azure (förhandsversion)](monitoring-overview-unified-alerts.md).

Normalt skapar du aktiviteten loggen aviseringar för att ta emot meddelanden när specifika ändringar sker för resurser i din Azure-prenumeration som ofta är begränsade till viss resursgrupp eller resurs. Du kanske exempelvis vill meddelas när någon virtuell dator i (exempel resursgrupp) **myProductionResourceGroup** har tagits bort kan du kanske vill bli meddelad om några nya roller har tilldelats en användare i din prenumeration.

Du kan konfigurera en aktivitet loggen avisering baserat på någon översta egenskap i JSON-objekt för en aktivitet händelselogg. Portalen visar dock de vanligaste alternativen som anges i följande avsnitt:

>[!NOTE]

> När kategorin som är ”administrativa” måste du ange minst en av de föregående villkoren i aviseringen. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.
>

När en aktivitet loggen avisering aktiveras använder en grupp för att generera meddelanden eller åtgärder. En grupp är en återanvändbar uppsättning notification mottagare, till exempel e-postadresser, webhook URL: er eller SMS telefonnummer. Mottagarna kan refereras från flera aviseringar centralisera och gruppera dina aviseringskanaler. När du definierar aviseringen aktivitet loggen har du två alternativ. Du kan:

* Använd en befintlig grupp i din logg varning.
* Skapa en ny grupp.

Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper i Azure portal](monitoring-action-groups.md).

Läs mer om meddelanden om hälsostatus i [varningar aktivitet loggen på meddelanden om hälsostatus](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>What's new in aviseringar förhandsgranskning för aktivitetsloggar?

[Azure-aviseringar (förhandsgranskning)](monitoring-overview-unified-alerts.md) nu ger förbättrad användarupplevelse för aktiviteten loggen aviseringar. Med den [förbättrad användarupplevelse för aviseringar](monitoring-overview-unified-alerts.md), kan du nu:

- [Skapa](#create-an-alert-rule-for-an-activity-log) och [hantera](#view-and-manage-activity-log-alert-rules) aktiviteten logga Varningsregler, från **övervakaren** > **aviseringar (förhandsgranskning)** bladet. Lär dig mer om [aktivitetsloggar](monitoring-overview-activity-logs.md).

- **Nya alternativ för aviseringar mål**: när du skapar en ny aktivitet loggen varningsregeln nu kan du välja en målresurs eller en resursgrupp eller prenumeration.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Skapa en aviseringsregel för en aktivitetsloggen

> [!NOTE]

>  När du skapar Varningsregler måste du kontrollera följande:

> - Prenumerationen i omfånget skiljer sig inte från prenumerationen där aviseringen har skapats.
- Villkor måste vara nivå/status/anroparen / resursgruppen / resurs-id / resurstyp / händelsekategori som aviseringen konfigureras.
- Det finns ingen ”anyOf” villkor eller kapslade villkor i varningskonfigurationen JSON (i princip bara en allOf tillåts med inga ytterligare allOf/anyOf).


Följ dessa steg:

1. Välj Azure-portalen **övervakaren** > **aviseringar (förhandsversion).**
2. Klicka på **nya Varningsregeln** överst i den **aviseringar (förhandsgranskning)** fönster.

     ![ny varningsregel](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     Den **Skapa regel** visas.

      ![nya alternativ för regel för varning](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **Definiera avisering villkor** anger följande information och klickar på **klar**.

    - **Aviseringen mål:** om du vill visa och välja mål för den nya aviseringen, Använd **filtrera efter prenumeration** / **filtrera efter resurstyp** och välj en resurs eller en resursgrupp från den listan som visas.

    > [!NOTE]

    > Du kan välja en resurs, resursgrupp eller en hela prenumerationen.

    **Exempelvy för avisering mål**

     ![Välj mål](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - Under **mål kriterier**, klickar du på **lägga till villkor** ange signal som **aktivitetsloggen**.

    - Välj signalen från listan som visas.

    Du kan välja loggen historik tidslinjen och motsvarande avisering logik för signalen mål:

    **Lägga till kriterier skärm**

    ![Lägg till villkor](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Historik tid**: under de senaste 6/12/24 timmarna under den senaste veckan.

    **Varna logik**:

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. Under **definiera Varningsregler,** ange följande information:

    - **Varningsregelns namn** – namn för den nya varningsregeln
    - **Beskrivning** – beskrivning för den nya varningsregeln
    - **Spara aviseringen till resursgruppen** – Välj resursgrupp, där du vill spara den nya regeln.

5. Under **grupp**, ange den grupp som du vill tilldela till den här nya varningsregeln i den nedrullningsbara menyn. Du kan också [skapa en ny åtgärdsgrupp](monitoring-action-groups.md) och tilldela den nya regeln. Klicka för att skapa en ny grupp **+ ny grupp**.

6. Om du vill aktivera regler när du har skapat, klicka på **Ja** för **Aktivera regel när du skapar** alternativet.
7. Klicka på **skapa varningsregeln**.

    Skapa en ny avisering regel för aktivitetsloggen och ett meddelande visas överst höger i fönstret.

    ![ varningsregeln har skapats](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    Du kan aktivera, inaktivera, redigera eller ta bort en regel. [Lär dig mer](#view-and-manage-activity-log-alert-rules) om hur du hanterar aktivitet loggen regler.

## <a name="view-and-manage-activity-log-alert-rules"></a>Visa och hantera aktivitet loggen Varningsregler

1. Från Azure-portalen klickar du på **övervakaren** > **aviseringar (förhandsgranskning)** och på **hantera regler** på upp till vänster i fönstret.

    ![ Hantera aviseringsregler](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Lista över tillgängliga regler visas.

2. Sök aktivitet loggen regeln för att ändra.

    ![ Sök aktivitet loggen Varningsregler](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Du kan använda de tillgängliga filtren - **prenumeration**, **resursgruppen**, eller **resurs** att hitta aktiviteten regeln som du vill redigera.

    > [!NOTE]

    > Du kan bara redigera **beskrivning** , **mål kriterier** och **åtgärdsgrupper**.

3.  Välj en regel och dubbelklicka för att redigera alternativ för regel. Gör nödvändiga ändringar och klicka sedan på **spara**.

    ![ Hantera aviseringsregler](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Du kan aktivera, inaktivera eller ta bort en regel. Välj lämpligt alternativ överst i fönstret när du har valt regeln enligt anvisningarna i steg 2.


## <a name="next-steps"></a>Nästa steg

- [Arkivera aktivitet loggen aviseringar](monitoring-archive-activity-log.md)
- [Strömma aktivitetsloggar till händelsehubbar](monitoring-stream-activity-logs-event-hubs.md)
- [Migrera till aktivitetsloggar](monitoring-migrate-management-alerts.md)
