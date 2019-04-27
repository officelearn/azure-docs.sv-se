---
title: Skicka aviseringar från Azure Application Insights | Microsoft Docs
description: Självstudie om hur du skickar aviseringar som svar på fel i ditt program med hjälp av Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 05285a177827cd0dd1e0e39e779a395ccfdfc0cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541101"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Övervaka och skicka aviseringar om programmets hälsotillstånd med Azure Application Insights

Med Azure Application Insights kan du övervaka programmet och skicka aviseringar när det inte är tillgängligt, har drabbats av fel eller har prestandaproblem.  Den här självstudiekursen guidar dig genom processen att skapa tester för att kontinuerligt kontrollera tillgängligheten för ditt program.

Lär dig att:

> [!div class="checklist"]
> * Skapa tillgänglighetstest för att kontinuerligt kontrollera programmets respons
> * Skicka e-post till administratörer när ett problem uppstår

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

Skapa en [Application Insights-resurs](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Skapa tillgänglighetstest

Med tillgänglighetstester i Application Insights kan du automatiskt testa ditt program från olika platser runt om i världen.   I den här självstudien får utföra du en url-test för att säkerställa att ditt webbprogram är tillgänglig.  Du kan även skapa en fullständig genomgång för att testa driften detaljerat. 

1. Välj **Application Insights** och sedan din prenumeration.  

2. Välj **tillgänglighet** under den **Undersök** menyn och klicka sedan på **skapa test**.

    ![Lägga till tillgänglighetstest](media/tutorial-alert/add-test-001.png)

3. Skriv ett namn på testet och lämna de andra standardvärdena.  Det här alternativet utlöser begäranden för programmets url var femte minut från fem olika geografiska platser.

4. Välj **aviseringar** att öppna den **aviseringar** listrutan där du kan definiera information om hur du svarar om testet misslyckas. Välj **nära realtid** och ange status till **aktiverad.**

    Skriv en e-postadress att skicka när aviseringsvillkoren uppfylls.  Om du vill kan du ange adressen till en webhook att anropa när aviseringsvillkoren uppfylls.

    ![Skapa test](media/tutorial-alert/create-test-001.png)

5. Återgå till panelen test, väljer du ellipserna och redigera avisering om du vill ange konfigurationen för aviseringen i nära realtid.

    ![Redigera avisering](media/tutorial-alert/edit-alert-001.png)

6. Ange felaktiga platser till större än eller lika med 3. Skapa en [åtgärdsgrupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) konfigurera som hämtar ett meddelande när din tröskelvärdet överskrids.

    ![Spara aviseringen UI](media/tutorial-alert/save-alert-001.png)

7. När du har konfigurerat aviseringen, klickar du på test-namnet för att visa information från varje plats. Testerna kan visas i både rad graph och punktdiagram ritytans format för att visualisera lyckade/misslyckade för ett visst tidsintervall.

    ![Testinformation](media/tutorial-alert/test-details-001.png)

8. Du kan granska nedåt i detaljerna för valfritt test genom att klicka på dess punkt i punktdiagrammet. Detta startar den detaljerade vyn för slutpunkt till slutpunkt-transaktion. Exemplet nedan visar informationen för en misslyckad begäran.

    ![Testresultat](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du aviserar om problem går du vidare till nästa självstudie, där du lär dig att analyserar hur användare interagerar med ditt program.

> [!div class="nextstepaction"]
> [Förstå användare](../../azure-monitor/learn/tutorial-users.md)