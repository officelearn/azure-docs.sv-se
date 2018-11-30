---
title: Självstudie – skapa och hantera Azure budgetar | Microsoft Docs
description: Den här självstudien hjälper plan och kontot för kostnaderna för Azure-tjänster som du förbrukar.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: bb8eb4eae192cf0dd3a3555a3b1209705dd83aaf
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583699"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Självstudie: Skapa och hantera budgetar för Azure

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. Med budgetar kan du ta hänsyn till de Azure-tjänster du förbrukar eller prenumererar på under en viss period. De hjälper dig att informera andra om sina utgifter att proaktivt hantera kostnader och övervaka hur utgifter fortskrider över tid. När budgetgränser som du har skapat överskrids utlöses endast meddelanden. Ingen av dina resurser som påverkas och förbrukning stoppats inte. Du kan använda budgetar för att jämföra och spåra utgifter som du analysera kostnaderna.

Budgetar återställa automatiskt i slutet av en viss (månadsvis, Kvartalsvis eller årligen) för samma budgetbelopp när du väljer ett förfallodatum i framtiden. Eftersom de återställa med samma budgetbelopp, måste du skapa separata budgetar när budgeterade valutabelopp skiljer sig åt för framtida perioder.

Exemplen i den här självstudiekursen beskriver hur du skapar och redigerar en budget för en prenumeration på Azure Enterprise Agreement (EA).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure portal
> * Redigera en budget

## <a name="prerequisites"></a>Förutsättningar

Budgetar är tillgängliga för alla Azure EA-kunder. Du måste ha läsbehörighet till en Azure EA-prenumeration visa budgetar. För att skapa och hantera budgetar, måste du ha behörigheten deltagare. Du kan skapa enskilda budgetar för EA-prenumerationer och resursgrupper. Du kan dock skapa budgetar för EA fakturering konton.

Följande Azure-behörigheter stöds per prenumeration för budgetar för användare och grupp:

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Deltagare och kostnadshantering deltagare – kan skapa, ändra eller ta bort egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och kostnadshantering läsare – kan visa dig som de har behörighet att.

Läs mer om att tilldela behörighet till Cost Management-data, [tilldela åtkomst till Cost Management data](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Skapa en budget i Azure portal

Du kan skapa en Azure-prenumeration budget under en månad, kvartal eller årlig. Styra innehållet i Azure portal anger om du skapar en budget för en prenumeration eller för en resursgrupp.

I Azure-portalen går du till **kostnadshantering + fakturering** &gt; **prenumerationer** &gt; Välj en prenumeration &gt; **budgetar**. I det här exemplet är budget som du skapar för den prenumeration du valde.

När du har skapat din budget visar de en enkel vy över dina aktuella utgifter mot dessa.

Klicka på **Lägg till**.

![Cost Management budgetar](./media/tutorial-acm-create-budgets/budgets01.png)

I den **skapa budget** fönstret, ange ett budgetnamn och budgetbelopp. Välj antingen en månadsvis, kvartalsvis, eller en årlig varaktigheten. Välj sedan ett slutdatum. Budgetar kräver minst en kostnad tröskelvärdet (% av budgeten) och en motsvarande e-postadress. Du kan också ta upp till fem tröskelvärden och fem e-postadresser i en enda budget. När ett tröskelvärde för budget är uppfyllt, tas e-postmeddelanden emot på normalt inom mindre än åtta timmar.

Här är ett exempel på hur du skapar en månatlig budget för $ 4 500. En e-postavisering hämtar genereras när 90% av budgeten har uppnåtts.

![Månatliga budget-exempel](./media/tutorial-acm-create-budgets/monthly-budget01.png)

När du skapar en kvartalsvis budget, fungerar den på samma sätt som en månatlig budget. Skillnaden är budgetbeloppet för kvartalet som fördelas jämnt mellan de tre månaderna av kvartalet. Som förväntat, delat budgetbelopp årliga jämnt mellan alla 12 månaders kalenderåret.

Aktuella kostnader mot budget uppdateras när Cost Management får uppdaterade faktureringsinformation. Vanligtvis, varje dag.

![Aktuella kostnader mot budget](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

När du har skapat en budget, visas den i kostnadsanalys. Visa din budget i förhållande till din kostnadstrend är en av de första stegen när du börjar [analysera dina kostnader och utgifter](quick-acm-cost-analysis.md).

![Budget visas i kostnadsanalys](./media/tutorial-acm-create-budgets/cost-analysis.png)

I exemplet ovan skapade du en budget för en prenumeration. Du kan också skapa en budget för en resursgrupp. Om du vill skapa en budget för en resursgrupp, navigerar du till **kostnadshantering + fakturering** &gt; **prenumerationer** &gt; Välj en prenumeration > **resurs grupper** > Välj en resursgrupp > **budgetar** > och sedan **Lägg till** en budget.

## <a name="edit-a-budget"></a>Redigera en budget

Beroende på vilken åtkomstnivå som du har, kan du redigera en budget för att ändra dess egenskaper. I följande exempel visas är några av egenskaperna skrivskyddade eftersom användaren har deltagarbehörighet för prenumerationen. För närvarande den **förfallodatum** är inaktiverad och kan inte ändras när.

![Redigera budget – behörigheten deltagare](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure portal
> * Redigera en budget

Gå vidare till nästa självstudie för att skapa en återkommande exporten av cost management-data.

> [!div class="nextstepaction"]
> [Skapa och hantera exporterade data](tutorial-export-acm-data.md)
