---
title: Övervaka användning och utgifter i kostnad aviseringar | Microsoft Docs
description: Den här artikeln beskrivs hur kostnaden aviseringar hjälp du övervaka användning och utgifter i Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/28/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: d6f7a597ff5737bb5e9a3f07122ece313bd432ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337792"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Använda cost aviseringar för att övervaka användning och utgifter

Den här artikeln hjälper dig att förstå och använda Cost Management aviseringar för att övervaka dina Azure-användning och utgifter. Kostnad aviseringar genereras automatiskt baserat när Azure-resurser används. Aviseringar visar alla aktiva kostnadshantering och faktureringsvarningar tillsammans på samma ställe. När din förbrukning når ett visst tröskelvärde, genereras aviseringarna av Cost Management. Det finns tre typer av kostnaden aviseringar: budget aviseringar, kredit aviseringar och avdelning utgifter kvot aviseringar.

## <a name="budget-alerts"></a>Budget aviseringar

Budget aviseringar meddelar dig när utgifter baserat på användning eller kostnaden, når eller överstiger den mängd som definierats i den [Avisera villkor för budgeten](tutorial-acm-create-budgets.md). Cost Management budgetar skapas med hjälp av Azure portal eller [Azure Consumption](https://docs.microsoft.com/rest/api/consumption) API.

I Azure-portalen definieras budgetar av kostnaden. Med hjälp av Azure Consumption-API, definieras budgetar av kostnaden eller förbrukningen / användningen. Budget aviseringar har stöd för både kostnadsbaserad och användningsbaserad budgetar. Budget aviseringar genereras automatiskt när budget avisering villkor är uppfyllda. Du kan visa alla kostnader aviseringar i Azure-portalen. När en avisering har genererats visas den i kostnad aviseringar. En e-postavisering skickas även till dem i listan över signalmottagare av budgeten.

## <a name="credit-alerts"></a>Kredit aviseringar

Kredit aviseringar meddelar dig när din Azure-kredit monetära åtaganden förbrukas. Åtagandebelopp är för organisationer med Enterprise-avtal. Kredit aviseringar genereras automatiskt vid 90% och 100% av ditt saldo i Azure-kredit. När en avisering har genererats återspeglas i kostnad aviseringar och e-postmeddelandet som skickas till kontoinnehavare.

## <a name="department-spending-quota-alerts"></a>Avdelning utgiftsgränsen kvot aviseringar

Avdelning utgiftsgränsen kvot aviseringar meddelar dig när avdelning utgifter når ett fast tröskelvärde för kvoten. Utgiftsgränsen kvoter konfigureras i EA-portalen. När ett tröskelvärde uppfylls genererar ett e-postmeddelande till avdelningen ägare och visas i kostnad aviseringar. Till exempel 50% eller 75% av kvoten.

## <a name="supported-alert-features-by-offer-categories"></a>Aviseringen funktioner som stöds av erbjudandet kategorier

Stöd för aviseringstyper beror på vilken typ av Azure-konto att du har (Microsoft erbjuder). I följande tabell visas de aviseringarna funktioner som stöds av olika Microsoft-erbjudanden. Du kan visa en fullständig lista över Microsoft-erbjudanden på [förstå kostnadshantering data](understand-cost-mgt-data.md).

| Aviseringstyp | Enterprise Agreement | Microsofts kundavtal | Web direct/betala per användning-As-You-Go |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Avdelningens utgiftskvot | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Visa aviseringar

Klicka på **Kostnadshantering + fakturering** i listan med tjänster i Azure Portal. Sedan i listan under **kostnadshantering**väljer **kostnad aviseringar**.

![Exempelbild av aviseringar som visas i Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Det totala antalet aktiva och avvisade aviseringar visas på sidan kostnaden aviseringar.

Alla aviseringar visar typ av avisering. En budgetavisering visar orsaken till varför den genererats och namnet på budgeten som gäller för. Varje avisering visar det datum då den skapades, status och omfattning (prenumerations- eller grupp) som aviseringen gäller för.

Möjliga status innehåller **active** och **avvisade**. Aktiv status anger att aviseringen är fortfarande relevanta. Avvisade status anger att någon har markerat aviseringen om du vill ange det inte längre behövs.

Välj en avisering från listan för att visa dess egenskaper. Varningsinformationen visar mer information om aviseringen. Budget aviseringar innehåller en länk till budgeten. Om en rekommendation är tillgängligt för en budgetavisering om, visas också en länk till rekommendationen. Budget, kredit och avdelning utgiftsgränsen kvot aviseringar finns en länk för analys i kostnadsanalys där du kan utforska kostnader för aviseringens omfång. I följande exempel visas utgifter för en avdelning med detaljerad information om aviseringen.

![Exempelbild som visar utgifter för en avdelning med detaljerad information om aviseringen](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

När du visar information om en avisering om avvisade, kan du återaktivera den om manuell åtgärd krävs. I följande bild visas ett exempel.

![Exempel bild som visar stänga och återaktivera alternativ](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Se också

- Om du inte redan har skapat en budget eller ange aviseringsvillkor för en budget, fyller du i [skapa och hantera budgetar](tutorial-acm-create-budgets.md) självstudien.
