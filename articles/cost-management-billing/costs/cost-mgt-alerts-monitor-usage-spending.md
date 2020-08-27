---
title: Övervaka användningen och utgifterna med kostnadsaviseringar
description: I den här artikeln beskrivs hur kostnadsaviseringar hjälper dig att övervaka användning och utgifter i Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: aeeb630cf15aedd62c085e2070e08fd223656094
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683444"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Använda kostnadsaviseringar för att övervaka användning och utgifter

Den här artikeln hjälper dig att förstå och använda Cost Management-aviseringar för att övervaka din användning och dina utgifter i Azure. Kostnadsaviseringar genereras automatiskt baserat på de tider då Azure-resurser förbrukas. Aviseringar visar alla aktiva kostnadshanterings- och faktureringsaviseringar på en och samma plats. När din förbrukning når ett angivet tröskelvärde genereras aviseringar av Cost Management. Det finns tre typer av kostnadsaviseringar: budgetaviseringar, kreditaviseringar och utgiftskvotaviseringar.

## <a name="budget-alerts"></a>Budgetaviseringar

Budgetaviseringar meddelar dig när utgifter, baserat på användning eller kostnad, når eller överskrider det belopp som definieras i [budgetens aviseringsvillkor](tutorial-acm-create-budgets.md). Cost Management-budgetar skapas med hjälp av Azure-portalen eller [Azure Consumption](https://docs.microsoft.com/rest/api/consumption)-API:et.

I Azure-portalen definieras budgetar efter kostnad. Med hjälp av Azure Consumption-API:et definieras budgetarna efter kostnad eller förbrukningsanvändning. Budgetaviseringar stöder både kostnadsbaserade och användningsbaserade budgetar. Budgetaviseringar genereras automatiskt när villkor för budgetavisering uppfylls. Du kan visa alla kostnadsaviseringar i Azure-portalen. När en avisering genereras visas den i kostnadsaviseringar. En e-postavisering skickas dessutom till personerna i budgetens lista över mottagare som ska meddelas.

## <a name="credit-alerts"></a>Kreditaviseringar

Kreditaviseringar meddelar dig när dina ekonomiska åtaganden för Azure-kredit förbrukas. Ekonomiska åtaganden är till för organisationer med Enterprise-avtal. Kreditaviseringar genereras automatiskt vid 90 % och vid 100 % av ditt Azure-kreditsaldo. När en avisering genereras avspeglas den i kostnadsaviseringar och i det e-postmeddelande som skickas till kontoinnehavarna.

## <a name="department-spending-quota-alerts"></a>Aviseringar om avdelningens utgiftskvot

Aviseringar om avdelningens utgiftskvot meddelar dig när avdelningsutgifter når ett fast tröskelvärde för kvoten. Utgiftskvoter konfigureras i EA-portalen. När ett tröskelvärde uppfylls genererar det ett e-postmeddelande till avdelningens ägare och visas i kostnadsaviseringar. Det kan till exempel gälla 50 % eller 75 % av kvoten.

## <a name="supported-alert-features-by-offer-categories"></a>Aviseringsfunktioner som stöds för erbjudandekategorier

Stöd för aviseringstyper beror på vilken typ av Azure-konto du har (Microsoft-erbjudande). I följande tabell visas de aviseringsfunktioner som stöds av olika Microsoft-erbjudanden. Du kan visa den fullständiga listan över Microsoft-erbjudanden på [Förstå Cost Management-data](understand-cost-mgt-data.md).

| Aviseringstyp | Enterprise-avtal | Microsoft-kundavtal | Web Direct/Betala per användning |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Avdelningens utgiftskvot | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Visa kostnadsaviseringar

Om du vill visa kostnadsaviseringar öppnar du önskat omfång i Azure-portalen och väljer **Budgetar** i menyn. Använd reglaget **Omfång** om du vill växla till ett annat omfång. Välj **Kostnadsaviseringar** i menyn. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

![Exempelbild med aviseringar som visas i Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Det totala antalet aktiva och stängda aviseringar visas på sidan för kostnadsaviseringar.

Alla aviseringar visar aviseringstypen. En budgetavisering visar orsaken till att den genererades samt namnet på den budget som den gäller för. Varje avisering visar det datum då den genererades, sin status samt det omfång (prenumeration eller hanteringsgrupp) som aviseringen gäller för.

Möjlig status inkluderar **aktiv** och **stängd**. Statusen aktiv anger att aviseringen fortfarande är relevant. Statusen stängd visar att någon har markerat aviseringen och angett att den inte längre är relevant.

Välj en avisering i listan om du vill visa information om den. Aviseringsinformation visar mer information om aviseringen. Budgetaviseringar innehåller en länk till budgeten. Om det finns en rekommendation för en budgetavisering visas även en länk till rekommendationen. Aviseringar för budget, kredit och avdelningens utgiftskvot har en länk för analys i kostnadsanalys, där du kan utforska kostnader för aviseringens omfång. I följande exempel visas utgifterna för en avdelning med aviseringsinformation.

![Exempelbild som visar utgifter för en avdelning med aviseringsinformation](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

När du visar information om en stängd avisering kan du återaktivera den om manuell åtgärd behövs. I följande bild visas ett exempel.

![Exempelbild som visar alternativ för att stänga och återaktivera](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Se även

- Om du inte redan har skapat en budget eller angett aviseringsvillkor för en budget bör du slutföra självstudien [Skapa och hantera budgetar](tutorial-acm-create-budgets.md).
