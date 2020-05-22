---
title: Ta bort virtuella datorer från Azure Automation Ändringsspårning och inventering
description: Den här artikeln beskriver hur du tar bort virtuella datorer från Ändringsspårning och inventering.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 1bd94beaa40f6ff793b50e261138cc31453f7016
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749167"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Ta bort virtuella datorer från Ändringsspårning och inventering

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

När du är klar med att distribuera ändringar av virtuella datorer i din miljö kan du ta bort dem från [ändringsspårning-och inventerings](change-tracking.md) funktionen.

1. Från ditt Automation-konto väljer du **ändrings spårning** eller **inventering** under **konfigurations hantering**.

2. Använd följande kommando för att identifiera UUID för en virtuell dator som du vill ta bort från hanteringen.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Öppna de sparade sökningarna i arbets ytan Log Analytics under **Allmänt**.

4. För den sparade sökningen `MicrosoftDefaultComputerGroup` klickar du på ellipsen till höger och väljer **Redigera**. 

5. Ta bort UUID för den virtuella datorn.

6. Upprepa stegen för alla andra virtuella datorer som ska tas bort.

7. Spara den sparade sökningen när du är klar med att redigera den. 

## <a name="next-steps"></a>Nästa steg

* [Översikt över Ändringsspårning och inventering](change-tracking.md)
* [Hantera Ändringsspårning och inventering](change-tracking-file-contents.md)
* [Ta bort länken mellan arbets ytan och automation-kontot för Ändringsspårning och inventering](automation-unlink-workspace-change-tracking.md)
* [Aktivera Ändringsspårning och inventering från ett Automation-konto](automation-enable-changes-from-auto-acct.md)
* [Aktivera Ändringsspårning och inventering från Azure Portal](automation-enable-changes-from-browse.md)
* [Aktivera Ändringsspårning och inventering från en Runbook](automation-enable-changes-from-runbook.md)
* [Aktivera Ändringsspårning och inventering från en virtuell Azure-dator](automation-enable-changes-from-vm.md)
* [Felsöka ändringar på en virtuell Azure-dator](automation-tutorial-troubleshoot-changes.md)
* [Felsöka Ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md)