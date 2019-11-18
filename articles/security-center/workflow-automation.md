---
title: Automatisering av arbets flöde (för hands version) i Azure Security Center | Microsoft Docs
description: Lär dig hur du skapar och automatiserar arbets flöden i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 1abfd8af7e0ef18d4e7bcf05b4726c5d0bcb0a84
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151704"
---
# <a name="workflow-automation-preview"></a>Automatisering av arbets flöde (för hands version)

Varje säkerhets program innehåller flera arbets flöden för incident svar. De här processerna kan omfatta att meddela relevanta intressenter, starta en ändrings hanterings process och tillämpa vissa åtgärder för reparation. Säkerhets experter rekommenderar att du automatiserar så många steg som beskrivs i de här procedurerna. Automation minskar kostnaderna. Det kan också förbättra säkerheten genom att se till att process stegen utförs snabbt, konsekvent och enligt dina fördefinierade krav.

Den här artikeln beskriver arbets flödets automatiserings funktion (för hands version) av Azure Security Center. Den här förhands gransknings funktionen kan utlösa Logic Apps om säkerhets aviseringar och rekommendationer. Till exempel kanske du vill Security Center att e-posta en speciell användare när en avisering inträffar. Du lär dig också hur du skapar Logic Apps att använda [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Om du tidigare använde vyn spel böcker (för hands version) på sid panelen hittar du samma funktioner tillsammans med de utökade funktionerna på sidan ny arbets flödes automatisering (för hands version).


## <a name="requirements"></a>Krav

* Om du vill arbeta med Azure Logic Apps arbets flöden måste du ha följande Logic Apps roller/behörigheter:

    * [Logic app operatörs](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) behörigheter krävs eller Läs-/utlösare för Logic app (den här rollen kan inte skapa eller redigera Logi Kap par). *Kör* bara befintliga)

    * [Logic app Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) -behörigheter krävs för att skapa och ändra Logic app

* Om du vill använda Logic app-kopplingar kan du behöva ytterligare autentiseringsuppgifter för att logga in på deras respektive tjänster (till exempel dina Outlook/team/slack-instanser)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Skapa en Logic-app och definiera när den ska köras automatiskt 

1. Från Security Centers sid panelen väljer du **arbets flödes automatisering (för hands version)** .

    [![lista över arbets flödes automatiseringar](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Från den här sidan kan du skapa nya Automation-regler, samt aktivera, inaktivera eller ta bort befintliga.  
1. Om du vill definiera ett nytt arbets flöde klickar du på **Lägg till arbets flödes automatisering**. 

    Ett fönster visas med alternativen för din nya automatisering. Här kan du ange:
    1. Ett namn och en beskrivning av Automation.
    1. Utlösare som kommer att initiera det här automatiska arbets flödet. Du kanske exempelvis vill att din Logic app ska köras när en säkerhets avisering som innehåller "SQL" genereras.
    1. Den Logic-app som ska köras när utlösarens villkor uppfylls. 

        [![lista över arbets flödes automatiseringar](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. I avsnittet åtgärder klickar du på **skapa en ny** för att starta processen för att skapa logiska appar.

    Du kommer att Azure Logic Apps.

    [![att skapa en ny Logic app](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Ange ett namn, en resurs grupp och en plats och klicka på **skapa**.

1. I den nya Logic-appen kan du välja mellan inbyggda och fördefinierade mallar i kategorin säkerhet. Eller så kan du definiera ett anpassat flöde av händelser som inträffar när den här processen utlöses.

    I Logic Apps designer stöds följande utlösare från Security Center-anslutningar:

    * **När en Azure Security Center rekommendation skapas eller utlösas (för hands version)**
    * **När en Azure Security Center-avisering skapas eller utlösas (för hands version)**
    
    > [!NOTE]
    > Om du använder den äldre utlösaren "när ett svar på en Azure Security Center-avisering utlöses", kommer Logic Apps inte att startas av funktionen för automatisering av arbets flöde. Använd i stället någon av de utlösare som anges ovan. 

    [![exempel på Logic-appen](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. När du har definierat din Logic app går du tillbaka till fönstret definition för arbets flödes automatisering ("Lägg till arbets flödes automatisering"). Klicka på **Uppdatera** för att se till att den nya Logic-appen kan väljas.

    ![Uppdatera](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Välj din Logic app och spara Automation. Observera att List rutan Logic app endast visar Logic Apps med stöd för Security Center kopplingar som nämns ovan.


## <a name="manually-trigger-a-logic-app"></a>Aktivera en Logic-app manuellt

Du kan också köra Logic Apps manuellt när du visar en säkerhets rekommendation.

Om du vill köra en Logic-app manuellt öppnar du en rekommendation och klickar på Utlös Logic app (för hands version):

[![utlösa en logisk app manuellt](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att skapa Logic Apps, köra dem manuellt i Security Center och automatisera körningen. 

Information om annat relaterat material finns i följande artiklar: 

- [Säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md)
- [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
- [Om Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps anslutningar](https://docs.microsoft.com/connectors/)
