---
title: Azure Automation och Log Analytics arbetsytemappningar
description: I den här artikeln beskrivs de mappningar som tillåts mellan ett Automation-konto och en Log Analytics-arbetsyta för att stödja lösningen
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681842"
---
# <a name="workspace-mappings"></a>Mappningar av arbetsytor

När du aktiverar lösningar som uppdateringshantering, ändringsspårning och lager eller start-/stopp-virtuella datorer under lediga timmar stöds endast vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. Den här mappningen gäller endast för Automation-kontot och log analytics-arbetsytan. Resurserna som rapporterar till ditt Automation-konto eller Log Analytics-arbetsyta kan finnas i andra regioner.

## <a name="supported-mappings"></a>Mappningar som stöds

I följande tabell visas de mappningar som stöds:

|**Arbetsyta för logganalys**|**Azure Automation-region**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|ÖstraUS2|
|WestUS2 (På andra sätt)|WestUS2 (På andra sätt)|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asien och stillahavsområdet**||
|AustralienSyster|AustralienSyster|
|Sydöstran|Sydöstran|
|CentralIndia (centralindia)|CentralIndia (centralindia)|
|JapanEast|JapanEast|
|**Europa**||
|UkSouth (svenska)|UkSouth (svenska)|
|Västeuropa|Västeuropa|
|**US Gov**||
|USGovVirginia (olikartade)|USGovVirginia (olikartade)|

<sup>1</sup> EastUS-mappning för Log Analytics-arbetsytor till Automation-konton är inte en exakt region till regionmappning men är rätt mappning.

<sup>2</sup> På grund av kapacitetsbegränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar Automation-konton och Log Analytics-arbetsytor. Befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Om du bestämmer dig för att du inte längre vill integrera ditt Automation-konto med en Log Analytics-arbetsyta kan du ta bort länken till ditt konto direkt från Azure-portalen. Innan du fortsätter måste du först ta bort lösningarna för uppdateringshantering, ändringsspårning och lager och start/stopp-datorer under start-/stopp-lösningar om du använder dem. Om du inte tar bort dem kan du inte slutföra den ta bort länken. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösningen, kan ha skapat automatiseringsresurser och kan behöva tas bort innan arbetsytan tas bort.

1. Öppna ditt Automation-konto från Azure-portalen. På sidan Automation-konto väljer du **Länkad arbetsyta** under **Relaterade resurser**.

2. Klicka på **Ta bort länk arbetsyta**på sidan Ta bort länk. Du får en snabb verifiering om du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot din Log Analytics-arbetsyta kan du spåra förloppet under **Meddelanden** från menyn.

4. Om du använde lösningen Update Management kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

    * Uppdatera scheman - Var och en har ett namn som matchar en uppdateringsdistribution som du har skapat.
    * Hybridarbetsgrupp som skapats för lösningen - `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`Var och en har ett namn som liknar .

5. Om du har använt start-/stopp-start-/start-lösningen under av-timmar kan du också ta bort följande objekt som inte behövs när du har tagit bort lösningen.

    * Starta och stoppa VM-runbookscheman
    * Starta och stoppa VM-runbooks
    * Variabler

Du kan också ta bort länken till arbetsytan från ditt Automation-konto på arbetsytan. 

1. På arbetsytan väljer du **Automation-konto** under **Relaterade resurser**. 
2. På sidan Automation-konto väljer du **Ta bort länkkonto**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du 200860 uppdaterar och spårar och lager:

    * Från en [virtuell dator](../automation-onboard-solutions-from-vm.md)
    * Från ditt [Automation-konto](../automation-onboard-solutions-from-automation-account.md)
    * När [du surfar på flera datorer](../automation-onboard-solutions-from-browse.md)
    * Från en [runbook](../automation-onboard-solutions.md)

* Lär dig hur du lö ombord på start/stop-virtuella datorer under starttimmarslösning:

    * [Distribuera start-/stopp-virtuella datorer under lediga timmar](../automation-solution-vm-management.md)
