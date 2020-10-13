---
title: Automatisering av arbets flöde i Azure Security Center | Microsoft Docs
description: Lär dig hur du skapar och automatiserar arbets flöden i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b713977d811411ea2ccd7dfa22c7757321ecd7aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712297"
---
# <a name="create-automatic-responses-to-alerts-and-recommendations-with-workflow-automation"></a>Skapa automatiska svar på aviseringar och rekommendationer med automatisering av arbets flöden

Varje säkerhets program innehåller flera arbets flöden för incident svar. De här processerna kan omfatta att meddela relevanta intressenter, starta en ändrings hanterings process och tillämpa vissa åtgärder för reparation. Säkerhets experter rekommenderar att du automatiserar så många steg som beskrivs i de här procedurerna. Automation minskar kostnaderna. Det kan också förbättra säkerheten genom att se till att process stegen utförs snabbt, konsekvent och enligt dina fördefinierade krav.

I den här artikeln beskrivs funktionen för arbets flödes automatisering i Azure Security Center. Den här funktionen kan utlösa Logic Apps om säkerhets aviseringar och rekommendationer. Till exempel kanske du vill Security Center att e-posta en speciell användare när en avisering inträffar. Du lär dig också hur du skapar Logic Apps att använda [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Om du tidigare använde vyn spel böcker (förhands granskning) på sid panelen, hittar du samma funktioner tillsammans med de utökade funktionerna på automatiserings sidan för nya arbets flöden.



## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|**Rollen säkerhets administratör** eller **ägare** av resurs gruppen<br>Måste också ha Skriv behörighet för mål resursen<br><br>Om du vill arbeta med Azure Logic Apps arbets flöden måste du också ha följande Logic Apps roller/behörigheter:<br> - [Logic app operatörs](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) behörigheter krävs eller Läs-/utlösare för Logic app (den här rollen kan inte skapa eller redigera Logi Kap par). *Kör* bara befintliga)<br> - [Logic app Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) -behörigheter krävs för att skapa och ändra Logic app<br>Om du vill använda Logic app-kopplingar kan du behöva ytterligare autentiseringsuppgifter för att logga in på deras respektive tjänster (till exempel dina Outlook/team/slack-instanser)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Skapa en Logic-app och definiera när den ska köras automatiskt 

1. Från Security Centers sid panelen väljer du **arbets flödes automatisering**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lista över automatiseringar av arbets flöden&quot;:::

    Från den här sidan kan du skapa nya Automation-regler, samt aktivera, inaktivera eller ta bort befintliga.

1. Om du vill definiera ett nytt arbets flöde klickar du på **Lägg till arbets flödes automatisering**. 

    Ett fönster visas med alternativen för din nya automatisering. Här kan du ange:
    1. Ett namn och en beskrivning av Automation.
    1. Utlösare som kommer att initiera det här automatiska arbets flödet. Du kanske exempelvis vill att din Logic app ska köras när en säkerhets avisering som innehåller &quot;SQL" genereras.
    1. Den Logic-app som ska köras när utlösarens villkor uppfylls. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Lista över automatiseringar av arbets flöden&quot;:::

    Från den här sidan kan du skapa nya Automation-regler, samt aktivera, inaktivera eller ta bort befintliga.

1. Om du vill definiera ett nytt arbets flöde klickar du på **Lägg till arbets flödes automatisering**. 

    Ett fönster visas med alternativen för din nya automatisering. Här kan du ange:
    1. Ett namn och en beskrivning av Automation.
    1. Utlösare som kommer att initiera det här automatiska arbets flödet. Du kanske exempelvis vill att din Logic app ska köras när en säkerhets avisering som innehåller &quot;SQL":::

1. I avsnittet åtgärder klickar du på **skapa en ny** för att starta processen för att skapa logiska appar.

    Du kommer att Azure Logic Apps.

    [![Skapa en ny Logic app](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Ange ett namn, en resurs grupp och en plats och klicka på **skapa**.

1. I den nya Logic-appen kan du välja mellan inbyggda och fördefinierade mallar i kategorin säkerhet. Eller så kan du definiera ett anpassat flöde av händelser som inträffar när den här processen utlöses.

    I Logic Apps designer stöds följande utlösare från Security Center-anslutningar:

    * **När en Azure Security Center rekommendation skapas eller utlöses** – om din Logic-app är beroende av en rekommendation som inaktive ras eller ersätts, slutar automatiseringen att fungera och du måste uppdatera utlösaren. Information om hur du spårar ändringar i rekommendationer finns i [Azure Security Center viktig information](release-notes.md).

    * **När en Azure Security Center-avisering skapas eller utlöses** , kan du anpassa utlösaren så att den bara relaterar till aviseringar med de allvarlighets grader som intresserar dig.
    
    > [!NOTE]
    > Om du använder den äldre utlösaren "när ett svar på en Azure Security Center avisering utlöses", kommer dina Logi Kap par inte att startas av funktionen för arbets flödes automatisering. Använd i stället någon av de utlösare som anges ovan. 

    [![Exempel på Logic-appen](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. När du har definierat din Logic app går du tillbaka till fönstret definition för arbets flödes automatisering ("Lägg till arbets flödes automatisering"). Klicka på **Uppdatera** för att se till att den nya Logic-appen kan väljas.

    ![Uppdatera](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Välj din Logic app och spara Automation. Observera att List rutan Logic app endast visar Logic Apps med stöd för Security Center kopplingar som nämns ovan.


## <a name="manually-trigger-a-logic-app"></a>Aktivera en Logic-app manuellt

Du kan också köra Logic Apps manuellt vid visning av säkerhets aviseringar eller rekommendationer.

Om du vill köra en Logic-app manuellt öppnar du en avisering eller en rekommendation och klickar på **Utlös Logic app**:

[![Aktivera en Logic-app manuellt](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Data typs scheman

Om du vill visa obehandlade händelse scheman för de säkerhets aviseringar eller rekommendationer som skickas till Logic App-instansen går du till [data typerna för arbets flödets Automation-scheman](https://aka.ms/ASCAutomationSchemas). Detta kan vara användbart i de fall där du inte använder Security Center inbyggda Logic app-kopplingar som nämns ovan, men använder i stället Logic Apps generiska HTTP-koppling – du kan använda Event JSON-schemat för att manuellt parsa det när du ser anpassa.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att skapa Logic Apps, automatisera körningen i Security Center och köra dem manuellt. 

För relaterat material, se: 

- [Microsoft Learn-modulen för att automatisera ett säkerhets svar med hjälp av arbets flödes automatisering](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md)
- [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
- [Om Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps-anslutningsappar](https://docs.microsoft.com/connectors/)
- [Data typs scheman för arbets flödes automatisering](https://aka.ms/ASCAutomationSchemas)
