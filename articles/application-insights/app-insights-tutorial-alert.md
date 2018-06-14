---
title: Skicka aviseringar från Azure Application Insights | Microsoft Docs
description: Självstudiekurs om att skicka aviseringar som svar på fel i ditt program med hjälp av Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
ms.locfileid: "23947230"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Övervakaren och aviseringen på programmets hälsotillstånd med Azure Application Insights

Azure Application Insights hjälper dig att övervaka programmet och skicka aviseringar när den är antingen otillgänglig uppstått fel eller lider av prestandaproblem.  Den här kursen tar dig igenom processen att skapa tester för att kontrollera tillgängligheten för ditt program kontinuerligt och för att skicka olika typer av aviseringar som svar på identifierade problem.  Lär dig att:

> [!div class="checklist"]
> * Skapa tillgänglighetstest för att kontrollera kontinuerligt svaret för programmet
> * Skicka e-post till administratörer när ett problem uppstår
> * Skapa aviseringar baserat på prestandamått 
> * Använd en Logikapp för att skicka telemetri om sammanfattade enligt ett schema.


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
    - Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Skapa tillgänglighetstest
Tillgänglighetstester i Application Insights kan du automatiskt testa programmet från olika platser i världen.   I den här självstudiekursen kommer du utföra en enkel test för att säkerställa att programmet är tillgängligt.  Du kan också skapa en fullständig genomgång för att testa driften detaljerad. 

1. Välj **Programinsikter** och sedan välja din prenumeration.  
1. Välj **tillgänglighet** under den **Undersök** -menyn och klicka sedan på **Lägg till test**.
 
    ![Lägg till tillgänglighetstest](media/app-insights-tutorial-alert/add-test.png)

2. Skriv ett namn på testet och lämna övriga standardvärdena.  Detta begär startsidan för programmet var femte minut från 5 olika geografiska platser. 
3. Välj **aviseringar** att öppna den **aviseringar** panelen där du kan definiera information om hur du ska svara om testet misslyckas. Typ i en e-postadress för att skicka när aviseringsvillkoren uppfylls.  Du kan eventuellt skriva adressen för en webhook att anropa när aviseringsvillkoren uppfylls.

    ![Skapa test](media/app-insights-tutorial-alert/create-test.png)
 
4. Tillbaka till panelen test och om en stund bör du börja resultat från tillgänglighetstest.  Klicka på test-namn för att visa information från varje plats.  Punktdiagram diagrammet visar lyckad och varaktighet för varje test.

    ![Testa information](media/app-insights-tutorial-alert/test-details.png)

5.  Du kan detaljnivån i uppgifter om en viss test genom att klicka på dess punkt i diagrammet punktdiagram.  Exemplet nedan visar information för en misslyckad begäran.

    ![Testresultat](media/app-insights-tutorial-alert/test-result.png)
  
6. Om aviseringsvillkoren är uppfyllt, skickas ett e-postmeddelande liknar den nedan till den adress som du angav.

    ![Aviseringen e-post](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Skapa en avisering från mått
Förutom aviseringar skickas från ett tillgänglighetstest, kan du skapa en avisering från alla prestandamått som samlas in för ditt program.

2. Välj **aviseringar** från den **konfigurera** menyn.  Panelen Azure aviseringar öppnas.  Det kan finnas andra Varningsregler som konfigureras här för andra tjänster.
3. Klicka på **Lägg till mått avisering**.  Då öppnas på panelen om du vill skapa en ny avisering regel.

    ![Lägg till mått varning](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Ange en **namn** för aviseringen regel och markera programmet i den nedrullningsbara listrutan för **resurs**.
5. Välj en **mått** till exempel.  Ett diagram som visas för att ange värdet för denna begäran under de senaste 24 timmarna.  Du kan du ange villkor för måttet.

    ![Lägga till varningsregel](media/app-insights-tutorial-alert/add-alert-01.png)

6. Ange en **villkoret** och **tröskelvärdet** för aviseringen. Detta är antalet gånger som måtten som måste överskridas för en avisering ska skapas. 
6. Under **meddela** Kontrollera den **e-ägare, deltagare och läsare** om du vill skicka ett e-postmeddelande till användarna när aviseringen villkor uppfylls och lägga till fler mottagare e-postadress.  Du kan också ange en webhook eller en logikapp här som körs när villkoret uppfylls.  Dessa kan användas att minska det upptäckta problemet eller 

    ![Lägga till varningsregel](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Att skicka information
Aviseringar skapas i reaktion på en viss uppsättning problem som har identifierats i ditt program och du normalt reservera aviseringar för kritiska villkor som kräver omedelbara uppmärksamhet.  Du proaktivt att få information om ditt program med en Logikapp som körs automatiskt enligt ett schema.  Du kan till exempel ha en e-post till administratörer med information som kräver ytterligare utvärdering.

Mer information om hur du skapar en Logikapp med Application Insights finns [automatisera Application Insights bearbetar med hjälp av Logic Apps](automate-with-logic-apps.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du Avisera om problem, kan du gå vidare till nästa kurs att lära dig hur du analyserar hur användare interagerar med programmet.

> [!div class="nextstepaction"]
> [Förstå användare](app-insights-tutorial-users.md)