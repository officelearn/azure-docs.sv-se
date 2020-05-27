---
title: Ta bort länken mellan arbets ytan och automation-kontot för Uppdateringshantering
description: Den här artikeln beskriver hur du avlänkar en Log Analytics arbets yta från Automation-kontot för Uppdateringshantering
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835792"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Ta bort länken mellan arbets ytan och automation-kontot för Uppdateringshantering

Du kan välja att inte integrera ditt Automation-konto med en Log Analytics arbets yta under [uppdateringshantering](automation-update-management.md) åtgärder. I den här artikeln får du information om hur du tar bort länken till arbets ytan från ditt konto.

1. Logga in på Azure på https://portal.azure.com .

2. Ta bort Uppdateringshantering för dina virtuella datorer. Se [ta bort virtuella datorer från uppdateringshantering](automation-remove-vms-from-update-management.md).

3. Om Uppdateringshantering innehåller tidigare versioner av Azure SQL-övervakning kan installationen av funktionen ha skapat Automation-resurser som du bör ta bort. För Uppdateringshantering kanske du vill ta bort följande objekt som inte längre behövs:

   * Uppdaterings scheman – varje har ett namn som matchar den uppdaterings distribution som du har skapat.
   * Hybrid Worker-grupper som har skapats för Uppdateringshantering – var och en får samma namn som machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Öppna ditt Automation-konto och välj **länkad arbets yta** under **relaterade resurser** till vänster.

5. På sidan ta bort länk till arbets yta klickar du på **ta bort länk till arbets yta** och svarar på prompter.

   ![Sidan ta bort länk till arbets yta](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Medan Azure Automation försöker ta bort länken till arbets ytan Log Analytics kan du följa förloppet under **meddelanden** på menyn.

Du kan också ta bort länken till Log Analytics arbets ytan från ditt Automation-konto från arbets ytan:

1. På arbets ytan väljer du **Automation-konto** under **relaterade resurser**. 
2. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med funktionen finns i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* Information om hur du felsöker funktions fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Fel sökning av problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux-uppdaterings agent finns i [Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).
