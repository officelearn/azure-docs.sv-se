---
title: Övervaka användning och utgifter med kostnads aviseringar | Microsoft Docs
description: I den här artikeln beskrivs hur kostnads aviseringar hjälper dig att övervaka användning och utgifter i Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: alavital
ms.custom: ''
ms.openlocfilehash: 4be484cdff2014f11c872da9a246ef8406447712
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230108"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Använda kostnadsaviseringar för att övervaka användning och utgifter

Den här artikeln hjälper dig att förstå och använda Cost Management aviseringar för att övervaka din Azure-användning och dina utgifter. Kostnads varningar genereras automatiskt baserat på när Azure-resurser förbrukas. Aviseringar visar alla aktiva kostnads hanterings-och fakturerings aviseringar tillsammans på en plats. När förbrukningen når ett angivet tröskelvärde genereras aviseringar av Cost Management. Det finns tre typer av kostnads aviseringar: budget aviseringar, kredit aviseringar och utgifts kvot varningar.

## <a name="budget-alerts"></a>Budget aviseringar

Budget varningar meddelar dig när utgifter, baserat på användning eller kostnad, når eller överskrider det belopp som definieras i [varnings villkoret för budgeten](tutorial-acm-create-budgets.md). Cost Management budgetar skapas med hjälp av Azure Portal eller [Azures förbruknings](https://docs.microsoft.com/rest/api/consumption) -API.

I Azure Portal definieras budgetarna per kostnad. Med hjälp av Azures förbruknings-API definieras budgetarna per kostnad eller förbruknings användning. Budget aviseringar stöder både kostnadsbaserade och användnings budgetar. Budget aviseringar genereras automatiskt när budget aviserings villkoren uppfylls. Du kan visa alla kostnads aviseringar i Azure Portal. När en avisering genereras visas den i kostnads aviseringar. En e-postavisering skickas också till personerna i listan över aviserings mottagare för budgeten.

## <a name="credit-alerts"></a>Kredit varningar

Kredit varningar meddelar dig när dina ekonomiska kredit krediter för Azure förbrukas. Monetära åtaganden är för organisationer med företags avtal. Kredit varningar genereras automatiskt vid 90% och vid 100% av ditt Azure-saldo. När en avisering genereras avspeglas den i kostnads aviseringar och i e-postmeddelandet som skickas till konto ägare.

## <a name="department-spending-quota-alerts"></a>Kvot aviseringar för avdelnings utgifter

Aviseringar för avdelnings utgifts kvot meddelar dig när avdelnings utgifter når ett fast tröskelvärde för kvoten. Utgifts kvoter konfigureras i EA-portalen. När ett tröskelvärde uppfylls genererar det ett e-postmeddelande till avdelningens ägare och visas i kostnads aviseringar. Till exempel 50% eller 75% av kvoten.

## <a name="supported-alert-features-by-offer-categories"></a>Aviserings funktioner som stöds av erbjudande kategorier

Stöd för aviserings typer beror på vilken typ av Azure-konto du har (Microsoft-erbjudandet). I följande tabell visas de aviserings funktioner som stöds av de olika Microsoft-erbjudandena. Du kan visa en fullständig lista över Microsoft-erbjudanden för att [förstå Cost Management data](understand-cost-mgt-data.md).

| Aviseringstyp | Enterprise-avtal | Microsoft-kundavtal | Webb direkt/betala per användning |
|---|---|---|---|
| Finans | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Avdelnings utgifts kvot | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Visa kostnads aviseringar

Om du vill visa kostnads aviseringar öppnar du önskad omfattning i Azure Portal och väljer **budgetar** i menyn. Använd **omfånget** Pill för att växla till ett annat omfång. Välj **kostnads aviseringar** i menyn. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

![Exempel på en bild av aviseringar som visas i Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Det totala antalet aktiva och avstängda aviseringar visas på sidan kostnads aviseringar.

Alla aviseringar visar aviserings typen. En budget avisering visar orsaken till varför den genererades och namnet på den budget som den gäller för. Varje avisering visar det datum då den genererades, dess status och omfånget (prenumeration eller hanterings grupp) som aviseringen gäller för.

Möjlig status är **aktiv** och **avstängd**. Aktiv status anger att aviseringen fortfarande är relevant. Statusen avvisad visar att någon har markerat aviseringen och anger att den inte längre är relevant.

Välj en avisering i listan om du vill visa information om den. Aviserings information visar mer information om aviseringen. Budget aviseringar innehåller en länk till budgeten. Om det finns en rekommendation för en budget avisering visas även en länk till rekommendationen. Budget-, kredit-och avdelnings kvot aviseringar har en länk för att analysera i kostnads analys där du kan utforska kostnader för aviseringens omfattning. I följande exempel visas utgifterna för en avdelning med aviserings information.

![Exempel bild som visar utgifter för en avdelning med aviserings information](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

När du visar information om en avvisad avisering kan du återaktivera den om du behöver göra en manuell åtgärd. I följande bild visas ett exempel.

![Exempel bild som visar alternativ för att stänga och återaktivera](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Se även

- Om du inte redan har skapat en budget eller angett aviserings villkor för en budget slutför du själv studie kursen [skapa och hantera budgetar](tutorial-acm-create-budgets.md) .
