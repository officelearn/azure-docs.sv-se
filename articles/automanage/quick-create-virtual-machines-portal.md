---
title: Snabb start – aktivera Azure automanage för virtuella datorer i Azure Portal
description: Lär dig hur du snabbt aktiverar autohantering för virtuella datorer på en ny eller befintlig virtuell dator i Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 43e0805a828a2ed32d23984b6ffef95a7e710953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947969"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Snabb start: Aktivera Azure automanage för virtuella datorer i Azure Portal

Kom igång med Azure automanage för virtuella datorer med hjälp av Azure Portal för att aktivera autohantering på en ny eller befintlig virtuell dator.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

> [!IMPORTANT]
> Följande RBAC-behörighet krävs för att aktivera autohantering: **ägar** rollen eller **deltagare** tillsammans med **Administratörs roller för användar åtkomst** .


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Aktivera autohantering för virtuella datorer på en befintlig virtuell dator

1. I Sök fältet söker du efter och väljer automatisk **hantering – metod tips för Azure virtuella datorer**.

2. Välj **Aktivera på den befintliga virtuella datorn**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Aktivera på en befintlig virtuell dator.":::

3. På bladet **Välj datorer** :
    1. Filtrera listan med virtuella datorer efter din **prenumeration** och **resurs grupp**.
    1. Markera kryss rutan för varje virtuell dator som du vill publicera.
    1. Klicka på knappen **Välj** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Välj befintlig virtuell dator i listan över tillgängliga virtuella datorer.":::

4. Under **konfigurations profil**klickar du på **Bläddra och ändrar profiler och inställningar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Bläddra och ändra profiler och inställningar.":::

5. På bladet **Välj konfigurations profil + inställningar** :
    1. Välj en profil till vänster: *utveckling/testning* för testning, *Prod* för produktion.
    1. Klicka på knappen **Välj** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Bläddra i produktions konfigurations profil.":::

6. Klicka på knappen **Aktivera**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Aktivera autohantering för virtuella datorer på en ny virtuell dator

1. Följ stegen för att skapa [en virtuell Windows-dator i Azure Portal](..\virtual-machines\windows\quick-create-portal.md).

2. När den virtuella datorn har distribuerats hamnar sidan distributions status som innehåller rekommenderade **Nästa steg** längst ned.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Nästa steg-avsnitt som finns längst ned på sidan distribution.":::

3. Under **Nästa steg**väljer du **Aktivera hantera virtuella datorer med bästa praxis**.

4. På sidan för automatisk **hantering – metod tips för virtuella Azure-datorer** fylls **datorer** i automatiskt av den virtuella datorn som du skapade.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Nyskapad virtuell dator visas som den valda datorn.":::

5. Under **konfigurations profil**klickar du på **Bläddra och ändrar profiler och inställningar**.

6. På bladet **Välj konfigurations profil + inställningar** :
    1. Välj en profil till vänster: *utveckling/testning* för testning, *Prod* för produktion.
    1. Klicka på knappen **Välj** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Bläddra i produktions konfigurations profil.":::

7. Klicka på knappen **Aktivera**.


## <a name="disable-automanage-for-vms"></a>Inaktivera autohantering för virtuella datorer

Sluta snabbt använda Azure automanagement för virtuella datorer genom att inaktivera automanagement.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Inaktiverar autohantering på en virtuell dator.":::

1. Gå till sidan för **metod tips för automatisk hantering – Azure virtuell dator** som visar en lista över alla dina automatiskt hanterade virtuella datorer.
1. Markera kryss rutan bredvid den virtuella dator som du vill inaktivera.
1. Klicka på knappen **inaktivera automanagent** .
1. Läs noggrant igenom meddelande tjänsten i det resulterande popup-meddelandet innan du godkänner **inaktive ring**.


## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resurs grupp för att testa Azure automanage för virtuella datorer och inte längre behöver den, kan du ta bort resurs gruppen. Om du tar bort gruppen raderas även den virtuella datorn och alla resurser i resurs gruppen.

Med Azure automanage skapas standard resurs grupper för lagring av resurser i. Kontrol lera resurs grupper som har namngivnings konventionen "DefaultResourceGroupRegionName" och "AzureBackupRGRegionName" för att rensa alla resurser.

1. Välj **resurs gruppen**.
1. Välj **ta bort**på sidan för resurs gruppen.
1. Bekräfta namnet på resurs gruppen när du uppmanas till det och välj sedan **ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabb starten aktiverade du Azure automanage för virtuella datorer. 

Upptäck hur du kan skapa och använda anpassade inställningar när du aktiverar automatisk hantering på den virtuella datorn. 

> [!div class="nextstepaction"]
> [Automatisk Azure-hantering för virtuella datorer – anpassad konfigurations profil](virtual-machines-custom-preferences.md)
