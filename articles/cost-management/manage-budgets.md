---
title: Hantera Cloudyn-budgetar i Azure
description: Den här artikeln hjälper dig att snabbt skapa budgetar och börja hantera dem i Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9d7d0e049d3c35aab56145beb94c8e41e56c5785
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219099"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Hantera Azure budgetar med Cloudyn

Inställningen upp budgetar och budget-baserade aviseringar bidra till att förbättra din cloud styrning och ansvar. Den här artikeln hjälper dig att snabbt skapa budgetar och börja hantera dem i Cloudyn.

När du har ett Enterprise- eller MSP-konto, kan du använda dina hierarkiska kostnadsstrukturen för entiteten ska tilldelas olika affärsenheter, avdelningar eller andra kostnadsenhet månatliga budget kvoter. När du har ett Premium-konto, kan du använda funktionen budget för hantering, som sedan appliceras till hela cloud-utgifter. Alla budgetar tilldelas manuellt.

Baserat på tilldelade budgetar kan du ange tröskelvärde för aviseringar baserat på procentandelen av din budget som förbrukas och definiera allvarlighetsgraden för varje tröskelvärde.

Budgetrapporter visar tilldelade budgeten. Användare kan visa när deras utgifter är över, under eller översättningsminnen med deras användning över tid. När du väljer **Visa/Dölj fält** överst i en budget rapport kan du Visa kostnad, budget, ackumulerad kostnad eller total budget.

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information om budgetar i Cost Management finns i [skapa och hantera budgetar](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Skapa budgetar

När du skapar en budget kan du ställa in den för räkenskapsåret och den gäller för en specifik enhet.

Skapa en budget och tilldela den till en entitet:

1. Gå till **kostnader** &gt; **Cost Management** &gt; **budget**.
2. På sidan budget hantering under **entiteter**väljer du den entitet där du vill skapa budgeten.
3. I året, väljer du det året där du vill skapa budgeten.
4. Ange ett Budgetvärde för varje månad. När du är klar klickar du på **Spara**.
I det här exemplet anges månatliga budgeten för juni 2018 till $135,000. Den totala budgeten för året är $1,615,000.00.
![skapa en budget sida där du ställer in en budget för varje månad](./media/manage-budgets/set-budget.png)


Importera en fil för årlig budget:

1. Under **åtgärder**väljer du **Exportera** för att ladda ned en tom CSV-mall som ska användas som grund för budgeten.
2. Fyll i CSV-filen med din budgetposter och spara den lokalt.
3. Under **åtgärder**väljer du **Importera**.
4. Välj den sparade filen och klicka sedan på **OK**.

Om du vill exportera din slutförda budget som en CSV-fil väljer du **Exportera** i **åtgärder**för att ladda ned filen.

## <a name="view-budget-in-reports"></a>Visa budget i rapporter

När du är klar visas din budget i de flesta kostnads rapporter under **kostnader** &gt; **kostnads analys** och i rapporten kostnad kontra budget över tid. Du kan också schemalägga rapporter baserat på budget tröskelvärden med hjälp av **åtgärder**.

Här är ett exempel på Cost Analysis-rapport. Den visar den totala budget och kostnad per arbetsbelastning och användning typer sedan i början av året.

![Exemplet Cost Analysis-rapporten med budget](./media/manage-budgets/cost-analysis-budget-example.png)

I det här exemplet antar vi att det aktuella datumet infaller den 22 juni. Kostnaden för juni 2018 är $71,611.28 jämfört med $135,000 månatliga budget. Kostnaden är mycket lägre än den månatliga budgeten eftersom det fortfarande finns åtta dagar ägna innan slutet av månaden.

Ett annat sätt att visa rapporten är att titta på ackumulerade kostnaden vs din budget. Om du vill se ackumulerade kostnader under **Visa/Dölj fält**väljer du **Ackumulerad kostnad** och **total budget**. Här är ett exempel som visar den ackumulerade kostnaden sedan i början av året.

![Exempel på Ackumulerad kostnad och total budget som visas i rapporten kostnad kontra budget över tid](./media/manage-budgets/accumulated-budget.png)

Någon gång kanske i framtiden den ackumulerade kostnaden överskrider din budget. Du kan enkelt se att om du ändrar diagramvy till typ av _linje_ .

![Budget visas i ett linjediagram i kostnaden av månader rapporten](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Skapa budget aviseringar för ett filter

I exemplet ovan ser du att den ackumulerade kostnaden närmat sig budgeten. Du kan skapa automatiska budget aviseringar så att du meddelas när utgifter metoder eller överskrider din budget. Det är i princip en schemalagd rapport med ett tröskelvärde. Budget tröskelvärde för mått är:

- Återstående kostnaden jämfört med budget – ange ett värde tröskelvärde för valuta
- Kostnad procent jämfört med budget – ange ett värde tröskelvärdet i procent

Nu ska vi titta på ett exempel.

I rapporten kostnad kontra budget över tid klickar du på **åtgärder** och väljer sedan **Schemalägg rapport**. Välj ett tröskelvärde för mått på fliken tröskelvärdet. Till exempel **kostnads procent vs budget**. Välj en typ av avisering och ange ett procentvärde av budgeten. Om du bara vill bli meddelad en gång väljer du **antal aviseringar i följd** och skriver _1_. Klicka på **Save** (Spara).

![Skapa en budgetavisering om på Spara eller schema rapporten](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första självstudien för Cloudyn kan du läsa den vid [granskning av användning och kostnader](tutorial-review-usage.md).
- Lär dig mer om de [rapporter som är tillgängliga i Cloudyn](use-reports.md).
