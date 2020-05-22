---
title: Ta bort virtuella datorer från Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du tar bort virtuella datorer från Uppdateringshantering.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 29d6edd5463de78bba039e4ed6219575d924ac10
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749146"
---
# <a name="remove-vms-from-update-management"></a>Ta bort virtuella datorer från Uppdateringshantering

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

När du är klar med att distribuera uppdateringar av virtuella datorer i din miljö kan du ta bort dem från [uppdateringshantering](automation-update-management.md) -funktionen.

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

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

* [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md)
* [Ta bort länken mellan arbets ytan och automation-kontot för Uppdateringshantering](automation-unlink-workspace-update-management.md)
* [Aktivera Uppdateringshantering från ett Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Aktivera Uppdateringshantering från Azure Portal](automation-onboard-solutions-from-browse.md)
* [Aktivera Uppdateringshantering från en Runbook](automation-onboard-solutions.md)
* [Aktivera Uppdateringshantering från en virtuell Azure-dator](automation-onboard-solutions-from-vm.md)
* [Felsöka Uppdateringshantering problem](troubleshoot/update-management.md)
* [Felsök problem med Windows Update-agenten](troubleshoot/update-agent-issues.md)
* [Felsök problem med Linux-uppdaterings agent](troubleshoot/update-agent-issues-linux.md)
