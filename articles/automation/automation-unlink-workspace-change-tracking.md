---
title: Ta bort länken mellan arbets ytan och automation-kontot för Ändringsspårning och inventering
description: Den här artikeln beskriver hur du avlänkar en Log Analytics arbets yta från Automation-kontot för Ändringsspårning och inventering
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: df2de44c2c8831fa4319b80484a119052434f8fb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749111"
---
# <a name="unlink-workspace-from-automation-account-for-change-tracking-and-inventory"></a>Ta bort länken mellan arbets ytan och automation-kontot för Ändringsspårning och inventering

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

* [Hantera Ändringsspårning och inventering](change-tracking-file-contents.md)
* [Ta bort virtuella datorer från Ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md)
* [Felsöka ändringar på en virtuell Azure-dator](automation-tutorial-troubleshoot-changes.md)
* [Felsöka Ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md)
