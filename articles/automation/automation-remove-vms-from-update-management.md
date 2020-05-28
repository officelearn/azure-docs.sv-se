---
title: Ta bort virtuella datorer från Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du tar bort virtuella datorer från Uppdateringshantering.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 9bb9ee93be4e045b52355255ecb86e54e48e5c5d
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117419"
---
# <a name="remove-vms-from-update-management"></a>Ta bort virtuella datorer från Uppdateringshantering

När du är klar med att distribuera uppdateringar av virtuella datorer i din miljö kan du ta bort dem från [uppdateringshantering](automation-update-management.md) -funktionen.

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Använd följande kommando för att identifiera UUID för en virtuell dator som du vill ta bort från hanteringen.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. I arbets ytan Log Analytics under **Allmänt**går du till sparade sökningar efter omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates` .

4. För den sparade sökningen `MicrosoftDefaultComputerGroup` klickar du på ellipsen till höger och väljer **Redigera**. 

5. Ta bort UUID för den virtuella datorn.

6. Upprepa stegen för alla andra virtuella datorer som ska tas bort.

7. Spara den sparade sökningen när du är klar med att redigera den. 

## <a name="next-steps"></a>Nästa steg

* Information om hur du fortsätter att arbeta med Uppdateringshantering finns i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* Information om hur du löser allmänna funktions problem finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om problem med Linux Update-agenten finns i [Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).