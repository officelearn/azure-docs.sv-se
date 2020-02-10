---
title: Hantera Cloudyn-budgetar i Azure
description: Den här artikeln hjälper dig att snabbt skapa budgetar och börja hantera dem i Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: vitavor
ms.custom: seodec18
ms.openlocfilehash: 91a1027add615c71784b6be1261fab97aadd9f3a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769992"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Hantera Azure-budgetar med Cloudyn

Genom att ange budgetar och budgetbaserade aviseringar kan du förbättra din molnstyrning och ditt ansvar. Den här artikeln hjälper dig att snabbt skapa budgetar och börja hantera dem i Cloudyn.

Om du har ett Enterprise- eller MSP-konto kan du använda din hierarkiska struktur för kostnadsentiteten och tilldela månatliga budgetkvoter till olika affärsenheter, avdelningar eller andra kostnadsentiteter. Om du har ett Premium-konto kan du använda funktionen för budgethantering, som sedan tillämpas på alla molnutgifter. Alla budgetar tilldelas manuellt.

Baserat på tilldelade budgetar kan du ange tröskelvärdesaviseringar utifrån den procentandel av din budget som används, samt definiera allvarlighetsgraden för varje tröskelvärde.

Budgetrapporter visar den tilldelade budgeten. Användarna kan se när deras utgifter överstiger, understiger eller är i nivå med förbrukningen över tid. När du väljer **Visa/dölj fält** längst upp i en budgetrapport, kan du se kostnad, budget, ackumulerad kostnad eller total budget.

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information om budgetar i Cost Management finns i [Skapa och hantera budgetar](../costs/tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Skapa budgetar

När du skapar en budget anger du den för räkenskapsåret och för en specifik entitet.

Så här skapar du en budget och tilldelar den till en entitet:

1. Gå till **Kostnader** &gt; **Cost Management** &gt; **Budget**.
2. På sidan Budgethantering under **Entiteter** väljer du den entitet där du vill skapa budgeten.
3. Som budgetår väljer du det år som du vill skapa budgeten för.
4. Ange ett budgetvärde för varje månad. Klicka på **Spara** när du är klar.
I det här exemplet är månadsbudgeten för juni 2018 inställd på 135 000 USD. Den totala budgeten för året är 1 615 000 USD.
![Skapa en budgetsida där du anger en budget för varje månad](./media/manage-budgets/set-budget.png)


Så här importerar du en fil för årsbudgeten:

1. Under **Åtgärder** väljer du **Exportera** för att ladda ned en tom CSV-mall som ska användas som grund för budgeten.
2. Fyll i CSV-filen med dina budgetposter och spara den lokalt.
3. Gå till **Åtgärder** väljer du **Importera**.
4. Välj den sparade filen och klicka sedan på **OK**.

Om du vill exportera din slutförda budget som en CSV-fil, väljer du **Exportera** under **Åtgärder** för att ladda ned filen.

## <a name="view-budget-in-reports"></a>Visa budgeten i rapporter

När du är klar visas budgeten i de flesta kostnadsrapporter under **Kostnader** &gt; **Kostnadsanalys** och i rapporten Kostnad jämfört med Budget över tid. Du kan också schemalägga rapporter baserat på budgettrösklar med hjälp av **Åtgärder**.

Här är ett exempel på kostnadsanalysrapporten. Den visar den totala budgeten och kostnaden per arbetsbelastning och användningstyper sedan årets början.

![Exempel på kostnadsanalysrapport med budget](./media/manage-budgets/cost-analysis-budget-example.png)

I det här exemplet antar vi att dagens datum är den 22 juni. Kostnaden för juni 2018 är 71 611,28 USD jämfört med månadsbudgeten på 135 000 USD. Kostnaden är mycket lägre än månadsbudgeten eftersom det kvarstår åtta dagars utgifter innan månaden är slut.

Ett annat sätt att visa rapporten på är att titta på ackumulerade kostnader jämfört med din budget. Om du vill se ackumulerade kostnader går du till **Visa/dölj fält** och väljer **Ackumulerad kostnad** och **Total budget**. Här är ett exempel som visar den ackumulerade kostnaden sedan årets början.

![Exempel på ackumulerad kostnad och total budget i rapporten Kostnad jämfört med Budget över tid](./media/manage-budgets/accumulated-budget.png)

Någon gång i framtiden kanske din ackumulerade kostnad överskrider din budget. Du kan vara enklare att se om du ändrar diagramvyn till _linje_typ.

![Budget visas i ett linjediagram i rapporten Kostnad per månad](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Skapa budgetaviseringar för ett filter

I föregående exempel såg du att den ackumulerade kostnaden närmade sig budgeten. Du kan skapa automatiska budgetaviseringar så att du får ett meddelande när utgifterna närmar sig eller överskrider din budget. Aviseringen är i princip en schemalagd rapport med ett tröskelvärde. Tröskelmåttet för budgetaviseringar innefattar:

- Återstående kostnad jämfört med budget – för att ange ett tröskelvärde för valuta
- Kostnadsprocent jämfört med budget – för att ange ett tröskelvärde för procent

Nu ska vi titta på ett exempel.

I rapporten Kostnad jämfört med Budget över tid, klickar du på **Åtgärder** och väljer **Schemalägg rapport**. På fliken Tröskel väljer du ett tröskelvärdesmått. Till exempel **Kostnadsprocent jämfört med budget**. Välj en aviseringstyp och ange ett procentvärde för budgeten. Om du bara vill bli meddelad en gång väljer du **Antal på varandra följande aviseringar** och skriver sedan _1_. Klicka på **Spara**.

![Skapa en budgetavisering i rutan Spara eller schemalägg rapporten](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första självstudien för Cloudyn, finns den i [Granska användning och kostnader](tutorial-review-usage.md).
- Läs mer om vilka [rapporter som är tillgängliga i Cloudyn](use-reports.md).
