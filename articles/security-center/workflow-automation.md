---
title: Automatisering av arbetsflöden i Azure Security Center | Microsoft-dokument
description: Lär dig hur du skapar och automatiserar arbetsflöden i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 972b5415b85e82a5afdaf7d85d3a3bcb9e144d4d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384950"
---
# <a name="workflow-automation"></a>Arbetsflödesautomation

Varje säkerhetsprogram innehåller flera arbetsflöden för incidentsvar. Dessa processer kan omfatta att meddela relevanta intressenter, starta en ändringshanteringsprocess och tillämpa specifika åtgärder för att åtgärda. Säkerhetsexperter rekommenderar att du automatiserar så många steg av dessa procedurer som du kan. Automatisering minskar kostnaderna. Det kan också förbättra din säkerhet genom att se till att processstegen görs snabbt, konsekvent och enligt dina fördefinierade krav.

I den här artikeln beskrivs funktionen för automatisering av arbetsflöden i Azure Security Center. Den här funktionen kan utlösa Logic Apps på säkerhetsaviseringar och rekommendationer. Du kanske till exempel vill att Security Center ska skicka e-post till en viss användare när en avisering inträffar. Du får också lära dig hur du skapar Logic Apps med [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Om du tidigare har använt vyn Playbooks (Preview) i sidofältet hittar du samma funktioner tillsammans med de utökade funktionerna på den nya automatiseringssidan för arbetsflödet.


## <a name="requirements"></a>Krav

* Om du vill arbeta med Azure Logic Apps-arbetsflöden måste du ha följande Logic Apps-roller/-behörigheter:

    * [Logic App](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) Operator-behörigheter krävs eller Logic App läs-/triggeråtkomst (den här rollen kan inte skapa eller redigera logikappar, bara *köra* befintliga)

    * [Logic App](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) Contributor-behörigheter krävs för att skapa och ändra Logic App

* Om du vill använda Logic App-kopplingar kan du behöva ytterligare autentiseringsuppgifter för att logga in på deras respektive tjänster (till exempel dina Outlook/Teams/Slack-instanser)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Skapa en logikapp och definiera när den ska köras automatiskt 

1. Välj **Automatisering av arbetsflöde i**Säkerhetscenters sidofält .

    [![Lista över automatiseringar av arbetsflöden](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Från den här sidan kan du skapa nya automatiseringsregler samt aktivera, inaktivera eller ta bort befintliga.  
1. Om du vill definiera ett nytt arbetsflöde klickar du på **Lägg till automatisering av arbetsflöden**. 

    En ruta visas med alternativen för den nya automatiseringen. Här kan du skriva in:
    1. Ett namn och en beskrivning för automatiseringen.
    1. De utlösare som initierar det här automatiska arbetsflödet. Du kanske till exempel vill att logikappen ska köras när en säkerhetsavisering som innehåller "SQL" genereras.
    1. Logikappen som körs när dina utlösarvillkor är uppfyllda. 

        [![Lista över automatiseringar av arbetsflöden](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. I avsnittet Åtgärder klickar du på **Skapa en ny** för att starta processen för att skapa logikapp.

    Du kommer till Azure Logic Apps.

    [![Skapa en ny logikapp](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Ange ett namn, en resursgrupp och en plats och klicka på **Skapa**.

1. I den nya Logikappen kan du välja mellan inbyggda, fördefinierade mallar från säkerhetskategorin. Du kan också definiera ett anpassat flöde av händelser som ska inträffa när den här processen utlöses.

    I Logic App-designern stöds följande utlösare från Security Center-kopplingarna:

    * **När en Rekommendation för Azure Security Center skapas eller utlöses**
    * **När en Azure Security Center-avisering skapas eller utlöses** 
    
    > [!TIP]
    > Du kan anpassa utlösaren så att den bara relaterar till aviseringar med de allvarlighetsgrader som intresserar dig.
    
    > [!NOTE]
    > Om du använder den äldre utlösaren "När ett svar på en Azure Security Center-avisering utlöses" startas inte logikapparna av funktionen Arbetsflödesautomatisering. Använd i stället någon av de utlösare som nämns ovan. 

    [![Exempel på logikapp](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. När du har definierat logikappen går du tillbaka till definitionsfönstret för automatisering av arbetsflöden ("Lägg till automatisering av arbetsflödet"). Klicka på **Uppdatera** om du vill vara säkra på att den nya Logikappen är tillgänglig för val.

    ![Uppdatera](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Välj logikapp och spara automatiseringen. Observera att listrutan Logic App endast visar Logic Apps med stöd för Security Center-kopplingar som nämns ovan.


## <a name="manually-trigger-a-logic-app"></a>Utlösa en logikapp manuellt

Du kan också köra Logic Apps manuellt när du visar en säkerhetsavisering eller någon rekommendation som erbjuder [Quick Fix-reparation](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Om du vill köra en Logic App manuellt öppnar du en avisering eller en rekommendation som stöder snabbkorrigering och klickar på **Trigger Logic App:**

[![Utlösa en logikapp manuellt](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Scheman för datatyper

Om du vill visa råa händelsescheman för säkerhetsaviseringar eller rekommendationer som skickas till Logic App-instansen besöker du [schemana för datatyper för arbetsflödesautomatisering](https://aka.ms/ASCAutomationSchemas). Detta kan vara användbart i de fall där du inte använder Security Centers inbyggda Logic App-kopplingar som nämns ovan, utan i stället använder Logic Apps allmänna HTTP-anslutning - du kan använda händelsen JSON-schemat för att manuellt tolka det som du tycker passar.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig om hur du skapar Logic Apps, kör dem manuellt i Security Center och automatiserar körningen. 

För annat relaterat material, se följande artiklar: 

- [Säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md)
- [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
- [Om Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps-anslutningsappar](https://docs.microsoft.com/connectors/)
- [Scheman för datatyper för arbetsflödesautomatisering](https://aka.ms/ASCAutomationSchemas)
