---
title: Ta bort virtuella datorer från Azure Automation Ändringsspårning och inventering
description: Den här artikeln beskriver hur du tar bort virtuella datorer från Ändringsspårning och inventering.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836676"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Ta bort virtuella datorer från Ändringsspårning och inventering

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

* Information om hur du fortsätter att arbeta med Ändringsspårning och inventering finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om hur du löser allmänna funktions problem finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).