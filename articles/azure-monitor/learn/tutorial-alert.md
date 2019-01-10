---
title: Skicka aviseringar från Azure Application Insights | Microsoft Docs
description: Självstudie om hur du skickar aviseringar som svar på fel i ditt program med hjälp av Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 300f0ddc8b738b5fd8578ed0b33cc15000c1098a
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101793"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Övervaka och skicka aviseringar om programmets hälsotillstånd med Azure Application Insights

Med Azure Application Insights kan du övervaka programmet och skicka aviseringar när det inte är tillgängligt, har drabbats av fel eller har prestandaproblem.  Den här självstudien går igenom processen med att skapa tester för att kontinuerligt kontrollera tillgängligheten för ditt program och för att skicka olika typer av aviseringar som svar på identifierade problem.  Lär dig att:

> [!div class="checklist"]
> * Skapa tillgänglighetstest för att kontinuerligt kontrollera programmets respons
> * Skicka e-post till administratörer när ett problem uppstår
> * Skapa aviseringar utifrån prestandamått 
> * Använda en logikapp för att skicka sammanfattade telemetridata enligt ett schema.


## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
    - Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](../../azure-monitor/app/asp-net.md). 


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Skapa tillgänglighetstest
Med tillgänglighetstester i Application Insights kan du automatiskt testa ditt program från olika platser runt om i världen.   I den här självstudien får du utföra ett enkelt test för att säkerställa att programmet är tillgängligt.  Du kan även skapa en fullständig genomgång för att testa driften detaljerat. 

1. Välj **Application Insights** och sedan din prenumeration.  
1. Välj **Tillgänglighet** under menyn **Undersök** och klicka sedan på **Lägg till test**.
 
    ![Lägga till tillgänglighetstest](media/tutorial-alert/add-test.png)

2. Skriv ett namn på testet och lämna de andra standardvärdena.  Detta begär startsidan för programmet var 5:e minut från 5 olika geografiska platser. 
3. Välj **Aviseringar** för att öppna panelen **Aviseringar** där du kan definiera information om hur du svarar om testet misslyckas. Skriv en e-postadress att skicka när aviseringsvillkoren uppfylls.  Om du vill kan du ange adressen till en webhook att anropa när aviseringsvillkoren uppfylls.

    ![Skapa test](media/tutorial-alert/create-test.png)
 
4. Gå tillbaka till testpanelen. Efter några minuter bör du börja se resultat från tillgänglighetstestet.  Klicka på testnamnet för att visa information för varje plats.  Punktdiagrammet visar status och varaktighet för varje test.

    ![Testinformation](media/tutorial-alert/test-details.png)

5.  Du kan detaljgranska ett visst test genom att klicka på dess punkt i punktdiagrammet.  Exemplet nedan visar informationen för en misslyckad begäran.

    ![Testresultat](media/tutorial-alert/test-result.png)
  
6. Om aviseringsvillkoren uppfylls skickas ett e-postmeddelande liknande det nedan till den adress du har angett.

    ![E-postavisering](media/tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Skapa en avisering utifrån mått
Utöver att skicka aviseringar från ett tillgänglighetstest kan du skapa en avisering utifrån valfritt prestandamått som samlas in för programmet.

2. Välj **Aviseringar** på menyn **Konfigurera**.  Då öppnas panelen Azure-aviseringar.  Det kan finnas andra aviseringsregler här för andra tjänster.
3. Klicka på **Lägg till måttavisering**.  Då öppnas panelen för att skapa en ny aviseringsregel.

    ![Lägga till måttavisering](media/tutorial-alert/add-metric-alert.png)

4. Skriv ett **namn** på aviseringsregeln och välj ditt program i listrutan för **Resurs**.
5. Välj det **mått** som ska användas.  En graf visas med värdet för denna begäran under de senaste 24 timmarna.  Detta hjälper dig att ange villkoren för måttet.

    ![Lägga till aviseringsregel](media/tutorial-alert/add-alert-01.png)

6. Ange ett **villkor** och **tröskelvärde** för aviseringen. Detta är antalet gånger som måttet måste överskridas för att en avisering ska skapas. 
6. Under **Meddela via** markerar du rutan **E-postägare, deltagare och läsare** för att skicka ett e-postmeddelande till dessa användare när aviseringsvillkoret uppfylls. Lägg till e-postadressen för eventuella ytterligare mottagare.  Du kan även ange en webhook eller en logikapp här som körs när villkoret uppfylls.  Dessa kan användas för att försöka åtgärda ett identifierat problem eller 

    ![Lägga till aviseringsregel](media/tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proaktivt skicka information
Aviseringar skapas som en reaktion på en viss uppsättning problem som identifierats i ditt program, och du reserverar normalt aviseringar för kritiska villkor som kräver omedelbar uppmärksamhet.  Du kan proaktivt ta emot information om ditt program med en logikapp som körs automatiskt enligt ett schema.  Du kan till exempel ha skickat e-post till administratörer dagligen med sammanfattad information som kräver ytterligare utvärdering.

Information om hur du skapar en logikapp med Application Insights finns i [Automatisera Application Insights-processer med hjälp av logikappar](../../azure-monitor/app/automate-with-logic-apps.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du aviserar om problem går du vidare till nästa självstudie, där du lär dig att analyserar hur användare interagerar med ditt program.

> [!div class="nextstepaction"]
> [Förstå användare](../../azure-monitor/learn/tutorial-users.md)