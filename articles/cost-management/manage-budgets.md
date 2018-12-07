---
title: Hantera Cloudyn budgetar i Azure | Microsoft Docs
description: Den här artikeln hjälper dig att skapa och hantera budgetar i Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: ''
ms.openlocfilehash: ef2ccd1d1fb5583d1356969ca759e5fb58a81936
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997559"
---
# <a name="manage-budgets"></a>Hantera budgetar

Inställningen upp budgetar och budget-baserade aviseringar bidra till att förbättra din cloud styrning och ansvar. Den här artikeln hjälper dig att snabbt skapa budgetar och börja hantera dem i Cloudyn.

När du har ett Enterprise- eller MSP-konto, kan du använda dina hierarkiska kostnadsstrukturen för entiteten ska tilldelas olika affärsenheter, avdelningar eller andra kostnadsenhet månatliga budget kvoter. När du har ett Premium-konto, kan du använda funktionen budget för hantering, som sedan appliceras till hela cloud-utgifter. Alla budgetar tilldelas manuellt.

Baserat på tilldelade budgetar kan du ange tröskelvärde för aviseringar baserat på procentandelen av din budget som förbrukas och definiera allvarlighetsgraden för varje tröskelvärde.

Budgetrapporter visar tilldelade budgeten. Användare kan visa när deras utgifter är över, under eller översättningsminnen med deras användning över tid. När du väljer **Visa/dölj fält** högst upp på rapportsidan budget du kan visa kostnaden, budget, ackumulerade kostnaden eller total budget.

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Läs mer om budgetar i Cost Management [skapa och hantera budgetar](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Skapa budgetar

När du skapar en budget kan du ställa in den för räkenskapsåret och den gäller för en specifik enhet.

Skapa en budget och tilldela den till en entitet:

1. Gå till **kostnader** &gt; **kostnadshantering** &gt; **Budget**.
2. På sidan Budgetstyrning under **entiteter**, väljer du den entitet som där du vill skapa budgeten.
3. I året, väljer du det året där du vill skapa budgeten.
4. Ange ett Budgetvärde för varje månad. När du är klar klickar du på **spara**.
I det här exemplet anges månatliga budgeten för juni 2018 till $135,000. Den totala budgeten för året är $1,615,000.00.
![Skapa en budget](./media/manage-budgets/set-budget.png)


Importera en fil för årlig budget:

1. Under **åtgärder**väljer **exportera** att ladda ned en tom CSV-mall som ska användas som din bas för budgeten.
2. Fyll i CSV-filen med din budgetposter och spara den lokalt.
3. Under **åtgärder**väljer **Import**.
4. Välj din sparade fil och klicka sedan på **OK**.

Exportera din färdiga budget som en CSV-fil under **åtgärder**väljer **exportera** att hämta filen.

## <a name="view-budget-in-reports"></a>Visa budget i rapporter

När du är klar visas din budget i de flesta kostnadsrapporter under **kostnader** &gt; **kostnadsanalys** och i Cost vs. Rapport med budget över tid. Du kan även schemalägga rapporter baserade på budgetgränser med **åtgärder**.

Här är ett exempel på Cost Analysis-rapport. Den visar den totala budget och kostnad per arbetsbelastning och användning typer sedan i början av året.

![Exemplet Cost Analysis-rapporten med budget](./media/manage-budgets/cost-analysis-budget-example.png)

I det här exemplet antar vi att det aktuella datumet infaller den 22 juni. Kostnaden för juni 2018 är $71,611.28 jämfört med $135,000 månatliga budget. Kostnaden är mycket lägre än den månatliga budgeten eftersom det fortfarande finns åtta dagar ägna innan slutet av månaden.

Ett annat sätt att visa rapporten är att titta på ackumulerade kostnaden vs din budget. Se ackumulerade kostnaderna under **Visa/dölj fält**väljer **ackumulerade kostnaden** och **Total Budget**. Här är ett exempel som visar den ackumulerade kostnaden sedan i början av året.

![Ackumulerade budget](./media/manage-budgets/accumulated-budget.png)

Någon gång kanske i framtiden den ackumulerade kostnaden överskrider din budget. Du kan se att enklare om du ändrar diagramvy till den _rad_ typen.

![Budget visas i linjediagram](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Skapa budget aviseringar för ett filter

I exemplet ovan ser du att den ackumulerade kostnaden närmat sig budgeten. Du kan skapa automatiska budget aviseringar så att du meddelas när utgifter metoder eller överskrider din budget. Det är i princip en schemalagd rapport med ett tröskelvärde. Budget tröskelvärde för mått är:

- Återstående kostnaden jämfört med budget – ange ett värde tröskelvärde för valuta
- Kostnad procent jämfört med budget – ange ett värde tröskelvärdet i procent

Nu ska vi titta på ett exempel.

I Cost vs. Budget över tid rapporten, klickar du på **åtgärder** och välj sedan **Schemalägg rapport**. Välj ett tröskelvärde för mått på fliken tröskelvärdet. Till exempel **kostnadsbudget procent jämfört med**. Välj en typ av avisering och ange ett procentvärde av budgeten. Om du vill bli informerad bara en gång, väljer **antalet på varandra följande aviseringar** och skriv sedan _1_. Klicka på **Spara**.

![Budgetavisering](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutfört den första självstudiekursen för Cloudyn, kan du läsa den på [granska användning och kostnader](tutorial-review-usage.md).
- Läs mer om den [rapporter som är tillgängliga i Cloudyn](use-reports.md).
