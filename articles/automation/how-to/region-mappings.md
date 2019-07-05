---
title: Azure Automation och Log Analytics arbetsyta-mappningar
description: Den här artikeln beskriver de mappningar som tillåts mellan ett Automation-konto och en Log Analytics-arbetsyta för lösningen
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9faa261f4b6293d778c5da685f630a21fd88f600
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478581"
---
# <a name="workspace-mappings"></a>Mappningar av arbetsyta

När du aktiverar lösningar som uppdateringshantering, ändringsspårning och inventering eller starta/stoppa virtuella datorer vid låg belastning på nätverket lösning stöds endast i vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. Den här mappningen gäller endast för Automation-kontot och Log Analytics-arbetsytan. De resurser som rapporterar till Automation-konto eller Log Analytics-arbetsytan kan finnas i andra regioner.

## <a name="supported-mappings"></a>Mappningar som stöds

I följande tabell visas mappningarna som stöds:

|**Log Analytics arbetsytans Region**|**Azure Automation Region**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asien och stillahavsområdet**||
|Sydöstra Australien|Sydöstra Australien|
|SoutheastAsia|SoutheastAsia|
|Indiencentrala|Indiencentrala|
|JapanEast|JapanEast|
|**Europa**||
|Södrastorbritannien|Södrastorbritannien|
|Västeuropa|Västeuropa|
|**USA-förvaltad region**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS mappning för Log Analytics-arbetsytor till Automation-konton är inte en exakt mappning för olika regioner, men är korrekt mappning.

<sup>2</sup> på grund av begränsningar i kapaciteten regionen som inte är tillgänglig när du skapar nya resurser. Detta inkluderar Automation-konton och Log Analytics-arbetsytor. Redan befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Om du inte längre vill integrera ditt Automation-konto med en arbetsyta för Log Analytics kan du kan ta bort länken till ditt konto direkt från Azure-portalen. Innan du fortsätter måste du först ta bort den uppdateringshantering, ändringsspårning och inventering eller starta/stoppa virtuella datorer vid låg belastning på nätverket lösningar om du använder dem. Om du inte tar bort dem, den här processen kommer inte att kunna fortsätta. Läsa artikeln för den lösning du har importerat för att förstå de steg som krävs för att ta bort den.

När du tar bort dessa lösningar kan utföra du följande steg om du vill ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösning kan ha skapat automation-tillgångar och kan också behöva tas bort innan du arbetsytans länk.

1. Öppna ditt Automation-konto från Azure-portalen och på Automation-konto väljer du sidan **länkade arbetsytan** under avsnittet **relaterade resurser** till vänster.

2. På sidan Avlänka från arbetsytan **ta bort arbetsytans länk**. Du får en uppmaning som bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till konton som Log Analytics-arbetsytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använder lösningen för uppdateringshantering, kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdatera schemalägger, var och en har namn som matchar de uppdateringsdistributioner du skapat)

* Hybrid worker-grupper som skapats för lösningen – var och en namnges på samma sätt till av typen machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Om du har använt Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa scheman för VM-runbook
* Starta och stoppa Virtuella runbooks
* Variabler

Du kan också Avlänka din arbetsyta från ditt Automation-konto från Log Analytics-arbetsytan. På arbetsytan och välj **Automatiseringskontot** under **relaterade resurser**. På sidan Automation-konto väljer **Avlänka konto**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur att publicera på följande sätt:

Uppdatera hantering och ändringsspårning och inventering:

* Från en [virtuell dator](../automation-onboard-solutions-from-vm.md)
* Från din [Automation-konto](../automation-onboard-solutions-from-automation-account.md)
* När [surfning flera datorer](../automation-onboard-solutions-from-browse.md)
* Från en [runbook](../automation-onboard-solutions.md)

Starta/stoppa virtuella datorer utanför arbetstid

* [Distribuera Starta/Stoppa VM under kontorstid](../automation-solution-vm-management.md)