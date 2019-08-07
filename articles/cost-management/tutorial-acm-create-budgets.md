---
title: Självstudie – skapa och hantera Azure budgetar | Microsoft Docs
description: Den här självstudien hjälper plan och kontot för kostnaderna för Azure-tjänster som du förbrukar.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/31/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 7fd98c7bb2cd049da8f5f68a5d2401081822eb22
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779214"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Självstudier: Skapa och hantera Azure-budgetar

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. Med budgetar kan du ta hänsyn till de Azure-tjänster du förbrukar eller prenumererar på under en viss period. De hjälper dig att informera andra om sina utgifter att proaktivt hantera kostnader och övervaka hur utgifter fortskrider över tid. När budgetgränser som du har skapat överskrids utlöses endast meddelanden. Ingen av dina resurser som påverkas och förbrukning stoppats inte. Du kan använda budgetar för att jämföra och spåra utgifter som du analysera kostnaderna.

Månads budgetar utvärderas mot utgifter var fjärde timme. Data och meddelanden för förbrukade resurser är dock tillgängliga inom åtta timmar.  

Budgetar återställa automatiskt i slutet av en viss (månadsvis, Kvartalsvis eller årligen) för samma budgetbelopp när du väljer ett förfallodatum i framtiden. Eftersom de återställa med samma budgetbelopp, måste du skapa separata budgetar när budgeterade valutabelopp skiljer sig åt för framtida perioder.

Exemplen i den här självstudiekursen beskriver hur du skapar och redigerar en budget för en prenumeration på Azure Enterprise Agreement (EA).

Se [hur du skapar en budget för att övervaka dina utgifter med Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video för att se hur du kan skapa budgetar i Azure för att övervaka utgifter.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure portal
> * Redigera en budget

## <a name="prerequisites"></a>Förutsättningar

Budgetar stöds för olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Om du vill visa budgetar måste du ha minst Läs behörighet för ditt Azure-konto.

 För Azure EA-prenumerationer måste du ha Läs behörighet för att Visa budgetar. För att skapa och hantera budgetar, måste du ha behörigheten deltagare. Du kan skapa enskilda budgetar för EA-prenumerationer och resursgrupper. Du kan dock skapa budgetar för EA fakturering konton.

Följande Azure-behörigheter, eller omfattningar, stöds per prenumeration för budgetar per användare och grupp. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Deltagare och kostnadshantering deltagare – kan skapa, ändra eller ta bort egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och kostnadshantering läsare – kan visa dig som de har behörighet att.

Läs mer om att tilldela behörighet till Cost Management-data, [tilldela åtkomst till Cost Management data](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Skapa en budget i Azure portal

Du kan skapa en Azure-prenumeration budget under en månad, kvartal eller årlig. Ditt navigerings innehåll i Azure Portal avgör om du skapar en budget för en prenumeration eller för en hanterings grupp.

Om du vill skapa eller Visa en budget öppnar du önskad omfattning i Azure Portal och väljer **budgetar** i menyn. Du kan till exempel navigeratill prenumerationer, välja en prenumeration i listan och sedan välja **budgetar** i menyn. Använd omfånget Pill för att växla till ett annat omfång, t. ex. en hanterings grupp, i budgetar. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

När du har skapat din budget visar de en enkel vy över dina aktuella utgifter mot dessa.

Klicka på **Lägg till**.

![Exempel som visar en lista över budgetar som redan har skapats](./media/tutorial-acm-create-budgets/budgets01.png)

Kontrol lera att det angivna definitions området är korrekt i fönstret **skapa budget** . Välj de filter som du vill lägga till. Med filter kan du skapa budgetar för vissa kostnader, till exempel resurs grupper i en prenumeration eller tjänst som virtuella datorer. Alla filter som du kan använda i kostnads analyser kan också tillämpas på en budget.

När du har identifierat ditt omfång och filter anger du ett budget namn. Välj sedan en månatlig, kvartals Visa eller årlig budget återställnings period. Den här återställnings perioden bestämmer tids perioden som analyseras av budgeten. Kostnaden som utvärderas av budgeten börjar vid noll i början av varje ny period. När du skapar en kvartalsvis budget, fungerar den på samma sätt som en månatlig budget. Skillnaden är budgetbeloppet för kvartalet som fördelas jämnt mellan de tre månaderna av kvartalet. En årlig budget mängd delas jämnt mellan alla 12 månader från kalender året.

Om du har en prenumeration enligt principen betala per användning, MSDN eller Visual Studio kan fakturerings fakturerings perioden inte justeras till kalender månaden. För dessa prenumerations typer och resurs grupper kan du skapa en budget som är justerad till din faktura period eller kalender månader. Om du vill skapa en budget justerad till din faktura period väljer du en återställnings period för **fakturerings månad**, **fakturerings kvartal**eller **fakturerings år**. Om du vill skapa en budget justerad till kalender månaden väljer du en återställnings period på **varje månad**, **kvartals vis**eller **varje år**.

Sedan identifierar du förfallo datumet då budgeten blir ogiltig och slutar utvärdera dina kostnader.

Baserat på de fält som valts i budgeten så långt visas ett diagram som hjälper dig att välja ett tröskelvärde som ska användas för din budget. Den föreslagna budgeten baseras på den högsta prognostiserade kostnaden som du kan ådra dig i framtida perioder. Du kan ändra budget beloppet.

![Exempel som visar hur du skapar budget med månads kostnads data ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

När du har konfigurerat budget beloppet klickar du på **Nästa** för att konfigurera budget aviseringar. Budgetar kräver minst en kostnad tröskelvärdet (% av budgeten) och en motsvarande e-postadress. Du kan också ta upp till fem tröskelvärden och fem e-postadresser i en enda budget. När ett tröskelvärde för budget är uppfyllt, tas e-postmeddelanden emot på normalt inom mindre än åtta timmar. Mer information om meddelanden finns i [använda kostnads aviseringar](cost-mgt-alerts-monitor-usage-spending.md). I exemplet nedan skapas en e-postavisering när 90% av budgeten nås.

![Exempel som visar aviserings villkor](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

När du har skapat en budget, visas den i kostnadsanalys. Visa din budget i förhållande till din kostnadstrend är en av de första stegen när du börjar [analysera dina kostnader och utgifter](quick-acm-cost-analysis.md).

![Exempel budget och utgifter visas i kostnadsanalys](./media/tutorial-acm-create-budgets/cost-analysis.png)

I exemplet ovan skapade du en budget för en prenumeration. Du kan också skapa en budget för en resursgrupp. Om du vill skapa en budget för en resursgrupp, navigerar du till **kostnadshantering + fakturering** &gt; **prenumerationer** &gt; Välj en prenumeration > **resurs grupper** > Välj en resursgrupp > **budgetar** > och sedan **Lägg till** en budget.

## <a name="trigger-an-action-group"></a>Utlös en åtgärds grupp

När du skapar eller redigerar en budget för en prenumeration eller resurs grupps omfång kan du konfigurera den så att den anropar en åtgärds grupp. Åtgärds gruppen kan utföra en rad olika åtgärder när ditt budget tröskelvärde är uppfyllt. Mer information om åtgärds grupper finns [i skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/platform/action-groups.md). Mer information om hur du använder budget baserad automatisering med åtgärds grupper finns i [hantera kostnader med Azure](../billing/billing-cost-management-budget-scenario.md)-budgetar.

Om du vill skapa eller uppdatera åtgärds grupper klickar du på **Hantera åtgärds grupper** när du skapar eller redigerar en budget.

![Exempel på hur du skapar en budget för att Visa hantera åtgärds grupper](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Klicka sedan på **Lägg till åtgärds grupp** och skapa åtgärds gruppen.


![Bild av rutan Lägg till åtgärds grupp](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

När åtgärds gruppen har skapats stänger du rutan för att återgå till din budget.

Konfigurera din budget att använda din åtgärds grupp när ett enskilt tröskelvärde uppfylls. Upp till fem olika tröskelvärden stöds.

![Exempel som visar val av åtgärds grupp för ett varnings villkor](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

I följande exempel visas budget trösklarna inställt på 50%, 75% och 100%. Varje har kon figurer ATS för att utlösa de angivna åtgärderna i den angivna åtgärds gruppen.

![Exempel som visar aviserings villkor som kon figurer ATS med olika åtgärds grupper och typ av åtgärder](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure portal
> * Redigera en budget

Gå vidare till nästa självstudie för att skapa en återkommande exporten av cost management-data.

> [!div class="nextstepaction"]
> [Skapa och hantera exporterade data](tutorial-export-acm-data.md)
