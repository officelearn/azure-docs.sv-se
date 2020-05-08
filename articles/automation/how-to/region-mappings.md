---
title: Mappningar av Azure Automation-och Log Analytics-arbetsyta
description: I den här artikeln beskrivs de mappningar som tillåts mellan ett Automation-konto och en Log Analytics-arbetsyta.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901012"
---
# <a name="workspace-mappings"></a>Mappningar av arbetsytor

I Azure Automation kan du aktivera följande lösningar: "Uppdateringshantering," "Ändringsspårning och inventering" och "starta/stoppa virtuella datorer vid andra tider." När du gör det måste du dock vara medveten om att endast vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration. Den här mappningen gäller endast Automation-kontot och Log Analytics-arbetsytan. Log Analytics-arbetsytan och automation-kontot måste finnas i samma prenumeration, men kan finnas i olika resurs grupper som distribueras till samma region.

Mer information finns i [Log Analytics arbets yta och Automation-konto](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mappningar som stöds

Följande tabell visar de mappningar som stöds:

|**Region för Log Analytics arbets yta**|**Azure Automation region**|
|---|---|
|**USA**||
|Östra USA<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|Indiensödra|Indiensödra|
|**Asien och stillahavsområdet**||
|AustraliaSoutheast|AustraliaSoutheast|
|Sydostasien|Sydostasien|
|Kanada|Kanada|
|Japanöstra|Japanöstra|
|**Europa**||
|UKSouth|UKSouth|
|Västeuropa|Västeuropa|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> -mappning av öster för Log Analytics arbets ytor till Automation-konton är inte en exakt mappning mellan regioner och regioner, men är rätt mappning.

<sup>2</sup> på grund av kapacitets begränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar Automation-konton och Log Analytics arbets ytor. Befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="unlink-a-workspace"></a>Ta bort länk till en arbets yta

Om du bestämmer dig för att du inte längre vill integrera ditt Automation-konto med en Log Analytics arbets yta kan du ta bort länken för ditt konto direkt från Azure Portal. Innan du fortsätter måste du först ta bort "Uppdateringshantering", "Ändringsspårning och inventering" och "starta/stoppa virtuella datorer vid andra tider" om du använder dem. Om du inte tar bort dem kan du inte slutföra länknings åtgärden. Läs artikeln för varje lösning som du aktiverar för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dem kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL Monitoring-lösningen, kan ha skapat Automation-till gångar och kan behöva tas bort innan du tar bort länken till arbets ytan.

1. Öppna ditt Automation-konto från Azure Portal. På sidan **Automation-konto** väljer du **länkad arbets yta** under **relaterade resurser**.

2. På sidan **ta bort länk till arbets yta** väljer du **ta bort arbets ytan ta bort länk**. Du får ett meddelande om att du måste kontrol lera om du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot Log Analytics arbets ytan, kan du följa förloppet under **meddelanden** på menyn.

4. Om du har använt "Uppdateringshantering" kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort det.

    * Uppdaterings scheman: varje har ett namn som matchar en uppdaterings distribution som du har skapat.
    * Hybrid Worker-grupper som har skapats för lösningen: var och en `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`har ett namn som liknar.

5. Om du har använt "starta/stoppa virtuella datorer vid andra tider" kan du välja att ta bort följande objekt som inte behövs när du har tagit bort det.

    * Starta och stoppa virtuella dator Runbook-scheman
    * Starta och stoppa VM-Runbooks
    * Variabler

Du kan också ta bort länken för arbets ytan från ditt Automation-konto på arbets ytan.

1. I arbets ytan väljer du **Automation-konto** under **relaterade resurser**. 
2. På sidan **Automation-konto** väljer du **ta bort länk till konto**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du börjar använda "Uppdateringshantering" och "Ändringsspårning och inventering":

    * Från en [virtuell dator](../automation-onboard-solutions-from-vm.md).
    * Från ditt [Automation-konto](../automation-onboard-solutions-from-automation-account.md).
    * När du [bläddrar flera datorer](../automation-onboard-solutions-from-browse.md).
    * Från en [Runbook](../automation-onboard-solutions.md).

* Lär dig hur du börjar använda "starta/stoppa virtuella datorer vid andra tider":

    * [Översikt över starta/stoppa virtuella datorer under timmar](../automation-solution-vm-management.md).
