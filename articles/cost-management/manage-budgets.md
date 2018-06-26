---
title: Hantera budgetar i Azure kostnaden Management | Microsoft Docs
description: Den här artikeln hjälper dig att skapa och hantera budgetar i hantering av kostnaden.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/25/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9d6bf29909393846ec17a1bcc210fb989efd7f99
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938440"
---
# <a name="manage-budgets"></a>Hantera budgetar

Ställa in budgetar och budget-baserade aviseringar bidra till att förbättra din moln-styrning och ansvar. Den här artikeln hjälper dig att snabbt skapa budgetar och börja hantera dem i hantering av kostnaden.

När du har ett Enterprise- eller MSP-konto, kan du använda din hierarkiska kostnaden entitet struktur för att tilldela olika affärsenheter eller avdelningar kostnaden entiteter månatliga budget kvoter. Du kan använda budget hanteringsfunktioner, som sedan tillämpas på dina utgifter i hela moln när du har ett Premium-konto. Alla budgetar tilldelas manuellt.

Baserat på tilldelade budgetar, kan du ange tröskelvärdet aviseringar baserat på procentandelen av din budget som används och definiera allvarlighetsgrad för varje tröskelvärde.

Budget rapporterna visar tilldelade budget. Användare kan visa när deras utgifter är över, under eller i par med deras förbrukning över tid. När du väljer **Visa/dölj fält** längst upp i en Budgetrapport, kan du visa kostnad, budget, ackumulerade kostnaden eller totala budget.

## <a name="create-budgets"></a>Skapa budgetar

När du skapar en budget du anger för räkenskapsåret och den gäller för en specifik enhet.

Skapa en budget och tilldela den till en enhet:

1. Gå till **kostnader** &gt; **Kostnadshanteringen** &gt; **Budget**.
2. På sidan Budget hantering under **entiteter**, Välj den entitet som du vill skapa budget.
3. Välj år där du vill skapa budget under budget.
4. Ange ett Budgetvärde för för varje månad. När du är klar klickar du på **spara**.
I det här exemplet anges månatliga budgeten för juni 2018 till $135,000. Den totala budgeten för år är $1,615,000.00.
![Skapa en budget](./media/manage-budgets/set-budget.png)


Importera en fil för den årliga budgeten:

1. Under **åtgärder**väljer **exportera** att ladda ned en tom CSV-mall för att användas som en bas för budget.
2. Fyll i CSV-filen med din budgetposter och spara den lokalt.
3. Under **åtgärder**väljer **importera**.
4. Välj den sparade filen och klicka sedan på **OK**.

Exportera din färdiga budget som en CSV-fil under **åtgärder**väljer **exportera** att hämta filen.

## <a name="view-budget-in-reports"></a>Visa budget i rapporter

När du är klar, visas din budget i de flesta kostnadsrapporter under **kostnader** &gt; **kostnaden Analysis** och kostnaden jämfört. Rapporten budget över tid. Du kan även schemalägga rapporter utifrån budget tröskelvärden med **åtgärder**.

Här är ett exempel på rapporten kostnaden. Den visar den totala budget och kostnaden av arbetsbelastning och användning typer sedan början på året.

![Exempel kostnaden analysrapporten med budget](./media/manage-budgets/cost-analysis-budget-example.png)

I det här exemplet förutsätter att det aktuella datumet infaller 22 juni. Kostnaden för juni 2018 är $71,611.28 jämfört med $135,000 månatliga budget. Kostnaden är mycket lägre än månatliga budget eftersom det fortfarande åtta dagar i utgifter före utgången av månaden.

Ett annat sätt att visa rapporten är att titta på ackumulerade kostnaden jämfört din budget. Se ackumulerade kostnaderna under **Visa/dölj fält**väljer **ackumulerade kostnaden** och **totalbudget**. Här är ett exempel som visar den ackumulerade kostnaden sedan början på året.

![Ackumulerade budget](./media/manage-budgets/accumulated-budget.png)

Senare kanske i framtiden ackumulerade kostnaden överskrider din budget. Kan du enkelt se att om du ändrar diagramvy till den _rad_ typen.

![Budget som visas i linjediagram](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Skapa budget aviseringar för ett filter

I det förra exemplet ser du att den ackumulerade kostnaden närmat sig budget. Du kan skapa automatiska budget aviseringar så att du får ett meddelande när utgifter metoder eller överskrider din budget. Det är i princip en schemalagd rapport med ett tröskelvärde. Budget tröskelvärde mått är:

- Återstående kostnad kontra budget – ange ett tröskelvärde för valuta värde
- Kostnad procentandel kontra budget – ange ett värde för procenttröskel

Nu ska vi titta på ett exempel.

I kostnaden vs. Budget över tid rapporten klickar du på **åtgärder** och välj sedan **schemalägga rapport**. Välj ett tröskelvärde mått på fliken tröskelvärdet. Till exempel **kostnaden procentandel kontra budget**. Välj en typ av avisering och ange ett procentvärde budget. Om du vill få meddelanden bara en gång, väljer **antal på varandra följande aviseringar** och skriv sedan _1_. Klicka på **Spara**.

![Budget avisering](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutförts första självstudierna för hantering av kostnader, läsa den på [granska användning och kostnader](https://docs.microsoft.com/en-us/azure/cost-management/tutorial-review-usage).
- Lär dig mer om den [rapporterna i kostnaden Management](use-reports.md).
