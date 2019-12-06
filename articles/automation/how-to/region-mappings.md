---
title: Mappningar av Azure Automation-och Log Analytics-arbetsyta
description: I den här artikeln beskrivs de mappningar som tillåts mellan ett Automation-konto och en Log Analytics arbets yta som stöder lösningen
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849521"
---
# <a name="workspace-mappings"></a>Mappningar för arbets ytor

När du aktiverar lösningar som Uppdateringshantering, Ändringsspårning och inventering eller Starta/stoppa virtuella datorer när de inte används lösning, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. Den här mappningen gäller endast Automation-kontot och Log Analytics-arbetsytan. Resurserna som rapporterar till ditt Automation-konto eller Log Analytics arbets ytan kan finnas i andra regioner.

## <a name="supported-mappings"></a>Mappningar som stöds

Följande tabell visar de mappningar som stöds:

|**Region för Log Analytics arbets yta**|**Azure Automation Region**|
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

<sup>1</sup> "östasiatisk mappning för Log Analytics-arbetsytor till Automation-konton är inte en exakt region till regions mappning, men är rätt mappning.

<sup>2</sup> på grund av kapacitets begränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar Automation-konton och Log Analytics arbets ytor. Befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="unlink-workspace"></a>Ta bort länk till arbets yta

Om du inte längre vill integrera ditt Automation-konto med en Log Analytics arbets yta kan du ta bort länken till ditt konto direkt från Azure Portal. Innan du fortsätter måste du först ta bort Uppdateringshantering, Ändringsspårning och inventering eller Starta/stoppa virtuella datorer när de inte används lösningar om du använder dem. Om du inte tar bort dem kommer den här processen att förhindras från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar inklusive tidigare versioner av Azure SQL-övervaknings lösningen kan ha skapat Automation-till gångar och kan också behöva tas bort innan du tar bort länken till arbets ytan.

1. Öppna ditt Automation-konto från Azure Portal och välj **länkad arbets yta** under avsnittet **relaterade resurser** till vänster på sidan Automation-konto.

2. På sidan ta bort länk till arbets yta klickar du på **ta bort arbets ytan**. Du får ett meddelande som bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot Log Analytics arbets ytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använde Uppdateringshantering-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Uppdaterings scheman-varje kommer att ha namn som matchar de uppdaterings distributioner som du har skapat)

* Hybrid Worker-grupper som har skapats för lösningen – var och en får samma namn som `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Om du använde Starta/stoppa virtuella datorer när de inte används-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Starta och stoppa virtuella dator Runbook-scheman
* Starta och stoppa VM-Runbooks
* Variabler

Du kan också ta bort länken till arbets ytan från ditt Automation-konto från din Log Analytics-arbetsyta. På arbets ytan väljer du **Automation-konto** under **relaterade resurser**. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="next-steps"></a>Nästa steg

Lär dig att publicera följande lösningar:

Uppdateringshantering och Ändringsspårning och inventering:

* Från en [virtuell dator](../automation-onboard-solutions-from-vm.md)
* Från ditt [Automation-konto](../automation-onboard-solutions-from-automation-account.md)
* När du [bläddrar flera datorer](../automation-onboard-solutions-from-browse.md)
* Från en [Runbook](../automation-onboard-solutions.md)

Starta/stoppa virtuella datorer utanför arbetstid

* [Distribuera Starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md)
