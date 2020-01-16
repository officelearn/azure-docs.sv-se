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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988505"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Använda kostnadsaviseringar för att övervaka användning och utgifter

Den här artikeln hjälper dig att förstå och använda Cost Management aviseringar för att övervaka din Azure-användning och dina utgifter. Kostnads varningar genereras automatiskt baserat på när Azure-resurser förbrukas. Aviseringar visar alla aktiva kostnads hanterings-och fakturerings aviseringar tillsammans på en plats. När förbrukningen når ett angivet tröskelvärde genereras aviseringar av Cost Management. Det finns tre typer av kostnadsaviseringar: budgetaviseringar, kreditaviseringar och aviseringar för avdelningens utgiftskvot.

## <a name="budget-alerts"></a>Budgetaviseringar

Budget varningar meddelar dig när utgifter, baserat på användning eller kostnad, når eller överskrider det belopp som definieras i [varnings villkoret för budgeten](tutorial-acm-create-budgets.md). Cost Management budgetar skapas med hjälp av Azure Portal eller [Azures förbruknings](https://docs.microsoft.com/rest/api/consumption) -API.

I Azure-portalen definieras budgetarna per kostnad. Om du använder API för Azure Consumption definieras budgetarna per kostnad eller förbrukning. Budgetaviseringarna stöder både kostnadsbaserade och förbrukningsbaserade budgetar. Budgetaviseringarna genereras automatiskt när aviseringsvillkoren uppfylls. Du kan visa alla kostnadsaviseringar i Azure-portalen. När en avisering genereras visas den i kostnadsaviseringarna. En e-postavisering skickas också till personerna i listan över aviseringsmottagare för budgeten.

## <a name="credit-alerts"></a>Kreditaviseringar

Kreditaviseringar meddelar dig när dina ekonomiska åtaganden i Azure-krediten har förbrukats. Ekonomiska åtaganden finns för organisationer som har Enterprise-avtal. Kreditaviseringar genereras automatiskt vid 90 % och 100 % av ditt Azure-kreditsaldo. När en avisering genereras återspeglas den i kostnadsaviseringar och i e-postmeddelandet som skickas till kontoinnehavarna.

## <a name="department-spending-quota-alerts"></a>Aviseringar för avdelningens utgiftskvot

Aviseringar för avdelningens utgiftskvot meddelar dig när avdelningens utgifter når ett fast tröskelvärde för kvoten. Utgiftskvoter konfigureras i EA-portalen. När ett tröskelvärde nås genereras ett e-postmeddelande till avdelningsägarna och visas i kostnadsaviseringarna. Till exempel 50 % eller 75 % av kvoten.

## <a name="supported-alert-features-by-offer-categories"></a>Aviseringsfunktioner som stöds av erbjudandekategorier

Stöd för aviseringstyper beror på vilken typ av Azure-konto du har (Microsoft-erbjudandet). I följande tabell visas de aviseringsfunktioner som stöds av de olika Microsoft-erbjudandena. Du kan visa en fullständig lista över Microsoft-erbjudanden för att [förstå Cost Management data](understand-cost-mgt-data.md).

| Aviseringstyp | Enterprise Agreement | Microsofts kundavtal | Webb direkt/betala per användning |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Avdelningens utgiftskvot | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Visa kostnadsaviseringar

Om du vill visa kostnadsaviseringar öppnar du önskat omfång i Azure-portalen och väljer **Budgetar** på menyn. Använd reglaget **Omfång** om du vill växla till ett annat omfång. Välj **Kostnadsaviseringar** på menyn. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

![Exempel på aviseringar som visas i Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Det totala antalet aktiva och avstängda aviseringar visas på sidan med kostnadsaviseringar.

Alla aviseringar visar aviseringstypen. En budgetavisering visar orsaken till varför den genererades och namnet på den budget som den gäller för. Varje avisering visar det datum då den genererades, dess status och omfånget (prenumeration eller hanterings grupp) som aviseringen gäller för.

Möjlig status inkluderar **aktiv** och **avvisad**. Statusen aktiv anger att aviseringen fortfarande är relevant. Statusen avvisad visar att någon har markerat aviseringen och angett att den inte längre är relevant.

Välj en avisering i listan om du vill visa information om den. Aviseringsinformationen visar mer information om aviseringen. Budgetaviseringar innehåller en länk till budgeten. Om det finns en rekommendation för en budgetavisering visas även en länk till rekommendationen. I aviseringarna för budget, kredit och avdelningens utgiftskvot finns en länk för att analysera i kostnadsanalys. Där kan du utforska kostnaderna för aviseringens omfång. I följande exempel visas utgifterna för en avdelning med aviserings information.

![Exempel bild som visar utgifter för en avdelning med aviserings information](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

När du visar information om en avvisad avisering kan du återaktivera den om du behöver göra en manuell åtgärd. I följande bild visas ett exempel.

![Exempel bild som visar alternativ för att stänga och återaktivera](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Se också

- Om du inte redan har skapat en budget eller angett aviserings villkor för en budget slutför du själv studie kursen [skapa och hantera budgetar](tutorial-acm-create-budgets.md) .
