---
title: Självstudie – skapa och hantera Azure budgetar | Microsoft Docs
description: Den här självstudien hjälper plan och kontot för kostnaderna för Azure-tjänster som du förbrukar.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 704aefd68f35ca20f72a2a0c46bf11912c139e65
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490724"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Självstudier: Skapa och hantera budgetar för Azure

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. Med budgetar kan du ta hänsyn till de Azure-tjänster du förbrukar eller prenumererar på under en viss period. De hjälper dig att informera andra om sina utgifter att proaktivt hantera kostnader och övervaka hur utgifter fortskrider över tid. När budgetgränser som du har skapat överskrids utlöses endast meddelanden. Ingen av dina resurser som påverkas och förbrukning stoppats inte. Du kan använda budgetar för att jämföra och spåra utgifter som du analysera kostnaderna.

Månatliga budgetar utvärderas mot utgifter var fjärde timme. Men data och meddelanden för förbrukade resurser är tillgängliga inom åtta timmar.  

Budgetar återställa automatiskt i slutet av en viss (månadsvis, Kvartalsvis eller årligen) för samma budgetbelopp när du väljer ett förfallodatum i framtiden. Eftersom de återställa med samma budgetbelopp, måste du skapa separata budgetar när budgeterade valutabelopp skiljer sig åt för framtida perioder.

Exemplen i den här självstudiekursen beskriver hur du skapar och redigerar en budget för en prenumeration på Azure Enterprise Agreement (EA).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure portal
> * Redigera en budget

## <a name="prerequisites"></a>Nödvändiga komponenter

Budgetar har stöd för en mängd olika typer av Azure-konto. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Om du vill visa budgetar, behöver du minst skrivskyddad åtkomst för dina Azure-konto.

 Du måste ha läsbehörighet till Visa budgetar för Azure EA-prenumerationer. För att skapa och hantera budgetar, måste du ha behörigheten deltagare. Du kan skapa enskilda budgetar för EA-prenumerationer och resursgrupper. Du kan dock skapa budgetar för EA fakturering konton.

Följande Azure-behörigheter eller scope, stöds per prenumeration för budgetar av användare och grupper. Mer information om scope finns [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Deltagare och kostnadshantering deltagare – kan skapa, ändra eller ta bort egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och kostnadshantering läsare – kan visa dig som de har behörighet att.

Läs mer om att tilldela behörighet till Cost Management-data, [tilldela åtkomst till Cost Management data](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Skapa en budget i Azure portal

Du kan skapa en Azure-prenumeration budget under en månad, kvartal eller årlig. Styra innehållet i Azure portal anger om du skapar en budget för en prenumeration eller för en hanteringsgrupp.

Om du vill skapa eller visa en budget, öppna det önskade omfånget i Azure portal och väljer **budgetar** på menyn. Till exempel Gå till **prenumerationer**, Välj en prenumeration i listan och välj sedan **budgetar** på menyn. Använd den **omfång** pill att växla till ett annat omfång, t.ex. en hanteringsgrupp i din budget. Mer information om scope finns [förstå och arbeta med omfattningar](understand-work-scopes.md).

När du har skapat din budget visar de en enkel vy över dina aktuella utgifter mot dessa.

Klicka på **Lägg till**.

![Cost Management budgetar visas i Azure portal](./media/tutorial-acm-create-budgets/budgets01.png)

I den **skapa budget** fönstret, ange ett budgetnamn och budgetbelopp. Välj antingen en månadsvis, kvartalsvis, eller en årlig varaktigheten. Välj sedan ett slutdatum. Budgetar kräver minst en kostnad tröskelvärdet (% av budgeten) och en motsvarande e-postadress. Du kan också ta upp till fem tröskelvärden och fem e-postadresser i en enda budget. När ett tröskelvärde för budget är uppfyllt, tas e-postmeddelanden emot på normalt inom mindre än åtta timmar. Mer information om meddelanden finns i [Använd kostnad aviseringar](cost-mgt-alerts-monitor-usage-spending.md).

Om du har en betala per användning, MSDN eller Visual Studio-prenumeration, kanske inte din faktura faktureringsperiod justeras per kalendermånad. Du kan skapa en budget som justeras till din faktureringsperiod eller kalendermånaderna för dessa typer av prenumerationer och resursgrupper. Välj en återställning viss Faktureringsmånad, fakturering kvartal eller år för fakturering om du vill skapa en budget som justeras till dina fakturaperioden. Om du vill skapa en budget som justerad per kalendermånad, väljer du en återställning tidsperiod månads-, Kvartalsvis eller årligen.

Här är ett exempel på hur du skapar en månatlig budget för $ 4 500. En e-postavisering hämtar genereras när 90% av budgeten har uppnåtts.

![Exempel-informationen som visas i rutan skapa budget](./media/tutorial-acm-create-budgets/monthly-budget01.png)

När du skapar en kvartalsvis budget, fungerar den på samma sätt som en månatlig budget. Skillnaden är budgetbeloppet för kvartalet som fördelas jämnt mellan de tre månaderna av kvartalet. Som förväntat, delat budgetbelopp årliga jämnt mellan alla 12 månaders kalenderåret.

Aktuella kostnader mot budget uppdateras när Cost Management får uppdaterade faktureringsinformation. Vanligtvis, varje dag.

![Exempelinformation som visar aktuella kostnader mot budget](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

När du har skapat en budget, visas den i kostnadsanalys. Visa din budget i förhållande till din kostnadstrend är en av de första stegen när du börjar [analysera dina kostnader och utgifter](quick-acm-cost-analysis.md).

![Exempel budget och utgifter visas i kostnadsanalys](./media/tutorial-acm-create-budgets/cost-analysis.png)

I exemplet ovan skapade du en budget för en prenumeration. Du kan också skapa en budget för en resursgrupp. Om du vill skapa en budget för en resursgrupp, navigerar du till **kostnadshantering + fakturering** &gt; **prenumerationer** &gt; Välj en prenumeration > **resurs grupper** > Välj en resursgrupp > **budgetar** > och sedan **Lägg till** en budget.

## <a name="edit-a-budget"></a>Redigera en budget

Beroende på vilken åtkomstnivå som du har, kan du redigera en budget för att ändra dess egenskaper. I följande exempel visas är några av egenskaperna skrivskyddade eftersom användaren har deltagarbehörighet för prenumerationen. För närvarande den **förfallodatum** är inaktiverad och kan inte ändras när.

![Exempel på att redigera en budget om du vill ändra olika egenskaper](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Utlösa en åtgärdsgrupp

När du skapar eller redigerar en budget för en prenumeration eller resource Gruppomfång, kan du konfigurera den för att anropa en åtgärdsgrupp. Åtgärdsgruppen kan utföra en mängd olika åtgärder när budgettröskeln för ditt uppfylls. Läs mer om åtgärdsgrupper [skapa och hantera åtgärdsgrupper i Azure-portalen](../azure-monitor/platform/action-groups.md). Läs mer om hur du använder baserat på budget automation med åtgärdsgrupper [hantera kostnader med Azure budgetar](../billing/billing-cost-management-budget-scenario.md).

Om du vill skapa eller uppdatera åtgärdsgrupper, klickar du på **hantera åtgärdsgrupper** medan du skapar eller redigerar en budget.

![Exempel på att skapa en budget för att visa hantera åtgärdsgrupper](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Klicka sedan på **Lägg till åtgärdsgrupp** och skapa åtgärdsgruppen.


![Bild av rutan Lägg till åtgärd grupp](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Gruppen har skapats efter åtgärden, Stäng rutan för att återgå till din budget.

Konfigurera din budget för att använda din grupp när ett tröskelvärde för enskilda uppfylls. Upp till fem olika trösklar stöds.

![Exempel som visar val av åtgärd för en aviseringstillståndet](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

I följande exempel visas budgetgränser inställd på 50%, 75% och 100%. Var och en har konfigurerats för att utlösa de angivna åtgärderna inom avsedda åtgärdsgruppen.

![Exempel på aviseringsvillkor som konfigurerats med olika åtgärdsgrupper och typ av åtgärder](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure portal
> * Redigera en budget

Gå vidare till nästa självstudie för att skapa en återkommande exporten av cost management-data.

> [!div class="nextstepaction"]
> [Skapa och hantera exporterade data](tutorial-export-acm-data.md)
