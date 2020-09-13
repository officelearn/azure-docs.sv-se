---
title: Ta bort virtuella datorer från Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du tar bort datorer som hanteras med Uppdateringshantering.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648632"
---
# <a name="remove-vms-from-update-management"></a>Ta bort virtuella datorer från Uppdateringshantering

När du är klar med att hantera uppdateringar på dina virtuella datorer i din miljö kan du sluta hantera virtuella datorer med den [uppdateringshantering](update-mgmt-overview.md) funktionen. Om du vill sluta hantera dem redigerar du den sparade Sök frågan `MicrosoftDefaultComputerGroup` på arbets ytan Log Analytics som är länkad till ditt Automation-konto.

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Ta bort dina virtuella datorer

1. Starta **Cloud Shell** i Azure Portal i det övre navigerings fönstret för Azure Portal. Om du inte känner till Azure Cloud Shell kan du läsa [Översikt över Azure Cloud Shell](../../cloud-shell/overview.md).

2. Använd följande kommando för att identifiera UUID för en dator som du vill ta bort från hanteringen.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. I Azure Portal navigerar du till **Log Analytics arbets ytor**. Välj din arbets yta i listan.

4. I arbets ytan Log Analytics väljer du **loggar** och väljer sedan **query Explorer** på menyn med de vanligaste åtgärderna.

5. I **query Explorer** i rutan till höger expanderar du **sparade Queries\Updates** och väljer den sparade Sök frågan `MicrosoftDefaultComputerGroup` för att redigera den.

6. Granska frågan i Frågeredigeraren och hitta UUID för den virtuella datorn. Ta bort UUID för den virtuella datorn och upprepa stegen för alla andra virtuella datorer som du vill ta bort.

7. Spara den sparade sökningen när du är klar med att redigera den genom att välja **Spara** från översta raden.

>[!NOTE]
>Datorer visas fortfarande när du har avregistrerat dem eftersom vi rapporterar om alla datorer som utvärderats under de senaste 24 timmarna. När du har tagit bort datorn måste du vänta 24 timmar innan de visas längre.

## <a name="next-steps"></a>Nästa steg

Om du vill återaktivera hanteringen av den virtuella datorn läser [du aktivera uppdateringshantering genom att bläddra i Azure Portal](update-mgmt-enable-portal.md) eller [Aktivera uppdateringshantering från en virtuell Azure-dator](update-mgmt-enable-vm.md).