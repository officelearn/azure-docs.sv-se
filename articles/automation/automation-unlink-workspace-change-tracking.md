---
title: Ta bort länken mellan arbets ytan och automation-kontot för Ändringsspårning och inventering
description: Den här artikeln beskriver hur du avlänkar en Log Analytics arbets yta från Automation-kontot för Ändringsspårning och inventering
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83828261"
---
# <a name="unlink-workspace-from-automation-account"></a>Ta bort länk mellan arbetsyta och Automation-konto

Du kan välja att inte integrera ditt Automation-konto med en Log Analytics arbets yta när du aktiverar [ändringsspårning-och inventerings](change-tracking.md) åtgärder. I den här artikeln får du information om hur du tar bort länken till arbets ytan från ditt konto.

1. Logga in på Azure på https://portal.azure.com .

2. Ta bort Uppdateringshantering för dina virtuella datorer. Se [ta bort virtuella datorer från ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md).

3. Om Ändringsspårning och lager innehåller tidigare versioner av Azure SQL-övervakning, kan installationen av funktionen ha skapat Automation-resurser som du bör ta bort. Leta upp dessa till gångar och ta bort dem.

4. Öppna ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser** till vänster.

5. På sidan ta bort länk till arbets yta klickar du på **ta bort länk till arbets yta** och svarar på prompter.

   ![Sidan ta bort länk till arbets yta](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Medan Azure Automation försöker ta bort länken till arbets ytan Log Analytics kan du följa förloppet under **meddelanden** på menyn.

Du kan också ta bort länken till Log Analytics arbets ytan från ditt Automation-konto från arbets ytan:

1. På arbets ytan väljer du **Automation-konto** under **relaterade resurser**. 
2. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med Ändringsspårning och lager finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om hur du felsöker allmänna funktions problem finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).
