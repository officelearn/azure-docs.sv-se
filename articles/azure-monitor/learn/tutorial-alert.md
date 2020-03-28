---
title: Skicka aviseringar från Azure Application Insights | Microsoft Docs
description: Självstudie om hur du skickar aviseringar som svar på fel i ditt program med hjälp av Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77656270"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Övervaka och skicka aviseringar om programmets hälsotillstånd med Azure Application Insights

Med Azure Application Insights kan du övervaka programmet och skicka aviseringar när det inte är tillgängligt, har drabbats av fel eller har prestandaproblem.  Den här självstudien tar dig igenom processen att skapa tester för att kontinuerligt kontrollera tillgängligheten för ditt program.

Lär dig att:

> [!div class="checklist"]
> * Skapa tillgänglighetstest för att kontinuerligt kontrollera programmets respons
> * Skicka e-post till administratörer när ett problem uppstår

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

Skapa en [application insights-resurs](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Skapa tillgänglighetstest

Med tillgänglighetstester i Application Insights kan du automatiskt testa ditt program från olika platser runt om i världen.   I den här självstudien kommer du att utföra ett url-test för att säkerställa att webbprogrammet är tillgängligt.  Du kan även skapa en fullständig genomgång för att testa driften detaljerat. 

1. Välj **Application Insights** och sedan din prenumeration.  

2. Välj **Tillgänglighet** under menyn **Undersök** och klicka sedan på **Skapa test**.

    ![Lägga till tillgänglighetstest](media/tutorial-alert/add-test-001.png)

3. Skriv ett namn på testet och lämna de andra standardvärdena.  Det här valet utlöser begäranden om programadressen var femte minut från fem olika geografiska platser.

4. Välj **Aviseringar om** du vill öppna listrutan **Aviseringar** där du kan definiera information om hur du ska svara om testet misslyckas. Välj **Nästan realtid** och ange statusen till **Aktiverad.**

    Skriv en e-postadress att skicka när aviseringsvillkoren uppfylls.  Om du vill kan du ange adressen till en webhook att anropa när aviseringsvillkoren uppfylls.

    ![Skapa test](media/tutorial-alert/create-test-001.png)

5. Gå tillbaka till testpanelen, välj ellipserna och redigera aviseringen för att ange konfigurationen för din nästan realtidsavisering.

    ![Redigera avisering](media/tutorial-alert/edit-alert-001.png)

6. Ange att misslyckade platser ska vara större än eller lika med 3. Skapa en [åtgärdsgrupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) för att konfigurera vem som får ett meddelande när tröskelvärdet för aviseringar överskrids.

    ![Spara varningsgränssnitt](media/tutorial-alert/save-alert-001.png)

7. När du har konfigurerat aviseringen klickar du på testnamnet för att visa information från varje plats. Tester kan visas i både linjediagram och punktdiagramformat för att visualisera framgång/fel för ett visst tidsintervall.

    ![Testinformation](media/tutorial-alert/test-details-001.png)

8. Du kan öka detaljnivån i information om ett test genom att klicka på dess punkt i punktdiagrammet. Detta kommer att starta vyn heltäckande transaktionsinformation. Exemplet nedan visar informationen för en misslyckad begäran.

    ![Testresultat](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du aviserar om problem går du vidare till nästa självstudie, där du lär dig att analyserar hur användare interagerar med ditt program.

> [!div class="nextstepaction"]
> [Förstå användare](../../azure-monitor/learn/tutorial-users.md)
