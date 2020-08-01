---
title: Ta bort virtuella datorer från Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du tar bort datorer som hanteras med Uppdateringshantering.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450440"
---
# <a name="remove-vms-from-update-management"></a>Ta bort virtuella datorer från Uppdateringshantering

När du är klar med att hantera uppdateringar på dina virtuella datorer i din miljö kan du sluta hantera virtuella datorer med den [uppdateringshantering](update-mgmt-overview.md) funktionen.

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

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

Om du vill återaktivera hanteringen av den virtuella datorn läser [du aktivera uppdateringshantering genom att bläddra i Azure Portal](update-mgmt-enable-portal.md) eller [Aktivera uppdateringshantering från en virtuell Azure-dator](update-mgmt-enable-vm.md).