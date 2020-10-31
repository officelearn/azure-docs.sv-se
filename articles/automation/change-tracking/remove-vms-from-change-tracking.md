---
title: Ta bort virtuella datorer från Azure Automation Ändringsspårning och inventering
description: Den här artikeln beskriver hur du tar bort virtuella datorer från Ändringsspårning och inventering.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 61ced7eda78d3ce56d78dfd6cc54e0e92d376e68
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100685"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Ta bort virtuella datorer från Ändringsspårning och inventering

När du är klar med att spåra ändringar på de virtuella datorerna i din miljö kan du sluta hantera dem med funktionen [ändringsspårning och inventering](overview.md) . Om du vill sluta hantera dem redigerar du den sparade Sök frågan `MicrosoftDefaultComputerGroup` på arbets ytan Log Analytics som är länkad till ditt Automation-konto.

## <a name="sign-into-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Ta bort dina virtuella datorer

1. Starta **Cloud Shell** i Azure Portal i det övre navigerings fönstret för Azure Portal. Om du inte känner till Azure Cloud Shell kan du läsa [Översikt över Azure Cloud Shell](/azure/cloud-shell/overview).

2. Använd följande kommando för att identifiera UUID för en dator som du vill ta bort från hanteringen.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. I Azure Portal navigerar du till **Log Analytics arbets ytor** . Välj din arbets yta i listan.

4. I arbets ytan Log Analytics väljer du **loggar** och väljer sedan **query Explorer** på menyn med de vanligaste åtgärderna.

5. I **query Explorer** i rutan till höger expanderar du **sparade Queries\Updates** och väljer den sparade Sök frågan `MicrosoftDefaultComputerGroup` för att redigera den.

6. Granska frågan i Frågeredigeraren och hitta UUID för den virtuella datorn. Ta bort UUID för den virtuella datorn och upprepa stegen för alla andra virtuella datorer som du vill ta bort.

7. Spara den sparade sökningen när du är klar med att redigera den genom att välja **Spara** från översta raden.

>[!NOTE]
>Datorer visas fortfarande när du har avregistrerat dem eftersom vi rapporterar om alla datorer som utvärderats under de senaste 24 timmarna. När du har tagit bort datorn måste du vänta 24 timmar innan de visas längre.

## <a name="next-steps"></a>Nästa steg

Om du vill aktivera den här funktionen igen går du till [aktivera ändringsspårning och inventering från ett Automation-konto](enable-from-automation-account.md), [aktiverar ändringsspårning och inventering genom att bläddra i Azure Portal](enable-from-portal.md), [Aktivera ändringsspårning och inventering från en Runbook](enable-from-runbook.md)eller [Aktivera ändringsspårning och inventering från en virtuell Azure-dator](enable-from-vm.md).