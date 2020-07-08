---
title: Ta bort virtuella datorer från Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du tar bort datorer som hanteras med Uppdateringshantering.
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610055"
---
# <a name="remove-vms-from-update-management"></a>Ta bort virtuella datorer från Uppdateringshantering

När du är klar med att distribuera uppdateringar av virtuella datorer i din miljö kan du ta bort dem från [uppdateringshantering](automation-update-management.md) -funktionen.

## <a name="to-remove-your-vms"></a>Ta bort dina virtuella datorer

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Använd följande kommando för att identifiera UUID för en dator som du vill ta bort från hanteringen.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. I arbets ytan Log Analytics under **Allmänt**går du till sparade sökningar efter omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates` .

4. För den sparade sökningen `MicrosoftDefaultComputerGroup` klickar du på ellipsen till höger och väljer **Redigera**.

5. Ta bort UUID för den virtuella datorn.

6. Upprepa stegen för alla andra virtuella datorer som ska tas bort.

7. Spara den sparade sökningen när du är klar med att redigera den.

>[!NOTE]
>Datorer visas fortfarande när du har avregistrerat dem eftersom vi rapporterar om alla datorer som utvärderats under de senaste 24 timmarna. När du har kopplat från datorn måste du vänta 24 timmar innan de inte längre visas.

## <a name="next-steps"></a>Nästa steg

* Information om hur du fortsätter att arbeta med Uppdateringshantering finns i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* Information om hur du löser allmänna funktions problem finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om problem med Linux Update-agenten finns i [Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).