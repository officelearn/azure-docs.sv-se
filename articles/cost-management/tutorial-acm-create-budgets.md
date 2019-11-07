---
title: Självstudie – Skapa och hantera Azure-budgetar | Microsoft Docs
description: Den här självstudien hjälper dig att planera och ta hänsyn till kostnaderna för de Azure-tjänster som du använder.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8c3c0574389fc9808af3cd70c928ede82d375076
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720724"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Självstudie: skapa och hantera Azure-budgetar

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. Med budgetar kan du ta hänsyn till de Azure-tjänster du förbrukar eller prenumererar på under en viss period. De hjälper dig att informera andra om sina utgifter för att proaktivt hantera kostnader och för att övervaka hur utgifter fortskrider över tid. När de budget trösklar som du har skapat har överskridits utlöses bara meddelanden. Ingen av dina resurser påverkas och förbrukningen stoppas inte. Du kan använda budgetar för att jämföra och spåra utgifter när du analyserar kostnader.

Kostnader och användnings data är vanligt vis tillgängliga inom 12-16 timmar och budgetar utvärderas mot dessa kostnader var fjärde timme. E-postaviseringar tas normalt emot inom 12-16 timmar.

Budgetarna återställs automatiskt i slutet av en period (varje månad, kvartals vis eller varje år) för samma budget belopp när du väljer ett utgångs datum i framtiden. Eftersom de återställs med samma budget belopp måste du skapa separata budgetar när de budgeterade valuta beloppen skiljer sig åt i framtida perioder.

Exemplen i den här självstudien vägleder dig genom att skapa och redigera en budget för en Azure Enterprise-avtal-prenumeration (EA).

Se [hur du skapar en budget för att övervaka dina utgifter med Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video för att se hur du kan skapa budgetar i Azure för att övervaka utgifter.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure Portal
> * Redigera en budget

## <a name="prerequisites"></a>Nödvändiga komponenter

Budgetar stöds för olika typer av Azure-konton. Om du vill visa en fullständig lista över typer av stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Om du vill visa budgetar måste du ha minst Läs behörighet för ditt Azure-konto.

 För Azure EA-prenumerationer måste du ha Läs behörighet för att Visa budgetar. Du måste ha deltagar behörighet för att skapa och hantera budgetar. Du kan skapa enskilda budgetar för EA-prenumerationer och resurs grupper. Du kan dock inte skapa budgetar för EA-fakturerings konton.

Följande Azure-behörigheter, eller omfattningar, stöds per prenumeration för budgetar per användare och grupp. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Bidrags givare och Cost Management deltagare – kan skapa, ändra eller ta bort sina egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och Cost Management läsare – kan visa de budgetar som de har behörighet till.

Mer information om hur du tilldelar behörighet till Cost Management data finns i [tilldela åtkomst till Cost Management data](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Skapa en budget i Azure Portal

Du kan skapa en Azure-prenumerations budget för varje månad, kvartals vis eller årlig period. Ditt navigerings innehåll i Azure Portal avgör om du skapar en budget för en prenumeration eller för en hanterings grupp.

Om du vill skapa eller Visa en budget öppnar du önskad omfattning i Azure Portal och väljer **budgetar** i menyn. Du kan till exempel navigera till **prenumerationer**, välja en prenumeration i listan och sedan välja **budgetar** i menyn. Använd **omfånget** Pill för att växla till ett annat omfång, t. ex. en hanterings grupp, i budgetar. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

När du har skapat budgetar visas en enkel vy över dina aktuella utgifter.

Klicka på **Lägg till**.

![Exempel som visar en lista över budgetar som redan har skapats](./media/tutorial-acm-create-budgets/budgets01.png)

Kontrol lera att det angivna definitions området är korrekt i fönstret **skapa budget** . Välj de filter som du vill lägga till. Med filter kan du skapa budgetar för vissa kostnader, till exempel resurs grupper i en prenumeration eller tjänst som virtuella datorer. Alla filter som du kan använda i kostnads analyser kan också tillämpas på en budget.

När du har identifierat ditt omfång och filter anger du ett budget namn. Välj sedan en månatlig, kvartals Visa eller årlig budget återställnings period. Den här återställnings perioden bestämmer tids perioden som analyseras av budgeten. Kostnaden som utvärderas av budgeten börjar vid noll i början av varje ny period. När du skapar en kvartals budget fungerar den på samma sätt som en månatlig budget. Skillnaden är att budget beloppet för kvartalet delas jämnt mellan de tre månaderna av kvartalet. En årlig budget mängd delas jämnt mellan alla 12 månader från kalender året.

Om du har en prenumeration enligt principen betala per användning, MSDN eller Visual Studio kan fakturerings fakturerings perioden inte justeras till kalender månaden. För dessa prenumerations typer och resurs grupper kan du skapa en budget som är justerad till din faktura period eller kalender månader. Om du vill skapa en budget justerad till din faktura period väljer du en återställnings period för **fakturerings månad**, **fakturerings kvartal**eller **fakturerings år**. Om du vill skapa en budget justerad till kalender månaden väljer du en återställnings period på **varje månad**, **kvartals vis**eller **varje år**.

Sedan identifierar du förfallo datumet då budgeten blir ogiltig och slutar utvärdera dina kostnader.

Baserat på de fält som valts i budgeten så långt visas ett diagram som hjälper dig att välja ett tröskelvärde som ska användas för din budget. Den föreslagna budgeten baseras på den högsta prognostiserade kostnaden som du kan ådra dig i framtida perioder. Du kan ändra budget beloppet.

![Exempel som visar hur du skapar budget med månads kostnads data ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

När du har konfigurerat budget beloppet klickar du på **Nästa** för att konfigurera budget aviseringar. Budgetar kräver minst ett kostnads tröskelvärde (% av budget) och en motsvarande e-postadress. Du kan också använda upp till fem tröskelvärden och fem e-postadresser i en enda budget. När ett budget tröskelvärde är uppfyllt tas e-postaviseringar normalt emot i mindre än 20 timmar. Mer information om meddelanden finns i [använda kostnads aviseringar](cost-mgt-alerts-monitor-usage-spending.md). I exemplet nedan skapas en e-postavisering när 90% av budgeten nås. Om du skapar en budget med budget-API: et kan du även tilldela roller till personer som ska ta emot aviseringar. Det finns inte stöd för att tilldela roller till personer i Azure Portal. Mer information om API: er för Azure-budgetar finns i [budget-API](/rest/api/consumption/budgets).

![Exempel som visar aviserings villkor](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

När du har skapat en budget visas den i kostnads analys. Att visa din budget i förhållande till din utgifts trend är ett av de första stegen när du börjar [analysera dina kostnader och utgifter](quick-acm-cost-analysis.md).

![Exempel på budget och utgifter som visas i kostnads analys](./media/tutorial-acm-create-budgets/cost-analysis.png)

I föregående exempel skapade du en budget för en prenumeration. Men du kan också skapa en budget för en resurs grupp. Om du vill skapa en budget för en resurs grupp navigerar du till **Cost Management + fakturering** &gt; **prenumerationer** &gt; väljer en prenumeration > **resurs grupper** > väljer en resurs grupp > **budgetar** > och **Lägg till** en budget.

## <a name="trigger-an-action-group"></a>Utlös en åtgärds grupp

När du skapar eller redigerar en budget för en prenumeration eller resurs grupps omfång kan du konfigurera den så att den anropar en åtgärds grupp. Åtgärds gruppen kan utföra en rad olika åtgärder när ditt budget tröskelvärde är uppfyllt. Mer information om åtgärds grupper finns [i skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/platform/action-groups.md). Mer information om hur du använder budget baserad automatisering med åtgärds grupper finns i [hantera kostnader med Azure-budgetar](../billing/billing-cost-management-budget-scenario.md).

Om du vill skapa eller uppdatera åtgärds grupper klickar du på **Hantera åtgärds grupper** när du skapar eller redigerar en budget.

![Exempel på hur du skapar en budget för att Visa hantera åtgärds grupper](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Klicka sedan på **Lägg till åtgärds grupp** och skapa åtgärds gruppen.


![Bild av rutan Lägg till åtgärds grupp](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

När åtgärds gruppen har skapats stänger du rutan för att återgå till din budget.

Konfigurera din budget att använda din åtgärds grupp när ett enskilt tröskelvärde uppfylls. Upp till fem olika tröskelvärden stöds.

![Exempel som visar val av åtgärds grupp för ett varnings villkor](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

I följande exempel visas budget trösklarna inställt på 50%, 75% och 100%. Varje har kon figurer ATS för att utlösa de angivna åtgärderna i den angivna åtgärds gruppen.

![Exempel som visar aviserings villkor som kon figurer ATS med olika åtgärds grupper och typ av åtgärder](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Budget integrering med åtgärds grupper fungerar bara för åtgärds grupper som har det gemensamma varnings schemat inaktiverat. Mer information om hur du inaktiverar schemat finns [Hur gör jag för att aktivera det vanliga aviserings schemat?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure Portal
> * Redigera en budget

Gå vidare till nästa självstudie för att skapa en återkommande export för dina kostnads hanterings data.

> [!div class="nextstepaction"]
> [Skapa och hantera exporterade data](tutorial-export-acm-data.md)
