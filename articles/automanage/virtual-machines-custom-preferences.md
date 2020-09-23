---
title: Skapa en anpassad inställning i Azure automanage för virtuella datorer
description: Lär dig hur du justerar konfigurations profilen i Azure automanage för virtuella datorer och anger dina egna inställningar.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: cf8e7340ce3554bf27364335457a5b7eadcdc049
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937647"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Skapa en anpassad inställning i Azure automanage för virtuella datorer

Azure automanage för den virtuella datorns bästa praxis har standard konfigurations profiler som kan justeras vid behov. Den här artikeln förklarar hur du kan ställa in egna konfigurations profil inställningar när du aktiverar automatisk hantering på en ny eller befintlig virtuell dator.

Vi stöder för närvarande anpassningar på [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) och [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Du kan inte ändra konfigurations profilen eller-inställningen på den virtuella datorn när den automatiska hanteringen är aktive rad. Du måste inaktivera automatisk hantering för den virtuella datorn och sedan återaktivera automatisk hantering med önskad konfigurations profil och inställningar.


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

3. På bladet **Välj datorer** :
    1. Filtrera listan med virtuella datorer efter din **prenumeration** och **resurs grupp**.
    1. Markera kryss rutan för varje virtuell dator som du vill publicera.
    1. Klicka på knappen **Välj** .

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Välj befintlig virtuell dator i listan över tillgängliga virtuella datorer.":::

4. Under **konfigurations profil**klickar du på **Bläddra och ändrar profiler och inställningar**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Bläddra och ändra profiler och inställningar.":::

5. På bladet **Välj konfigurations profil + inställningar** väljer du en profil på vänster sida: *utveckling/testning* för testning, *Prod* för produktion.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Bläddra i produktions konfigurations profil.":::

6. Under **konfigurations inställningar** i den valda profilen finns en listruta där du kan justera för vissa tjänster.
    1. Klicka på **skapa nya inställningar**.
    1. På bladet **skapa en konfigurations inställning** fyller du i fliken grundläggande:
        1. Prenumeration
        1. Resursgrupp
        1. Preference-namn
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Fyll i konfigurations inställningar.":::

7. Gå till fliken Inställningar och justera de konfigurations inställningar som du vill använda.
        
    > [!NOTE]
    > Endast justeringar som fortfarande passar i våra bästa metoder övre och nedre gränser tillåts när du ändrar profil konfigurationerna.

8. Granska konfigurations profilen.
9. Klicka på knappen **Skapa**.

10. Klicka på knappen **Aktivera**.


## <a name="disable-automanage-for-vms"></a>Inaktivera autohantering för virtuella datorer

Sluta snabbt använda Azure automanagement för virtuella datorer genom att inaktivera automanagement.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Inaktiverar autohantering på en virtuell dator.":::

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

Få de vanligaste frågorna i vanliga frågor och svar. 

> [!div class="nextstepaction"]
> [Vanliga frågor och svar](faq.md)