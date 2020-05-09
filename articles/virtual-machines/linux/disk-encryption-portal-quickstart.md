---
title: Skapa och kryptera en virtuell Linux-dator med Azure-portalen
description: I den här snabb starten får du lära dig hur du använder Azure Portal för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 6e32bfdf8c4b2dd7ce61393ab545770cafc73cf5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792538"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Snabb start: skapa och kryptera en virtuell dator med Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure-portalen är ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. I den här snabb starten ska du använda Azure Portal för att distribuera en virtuell Linux-dator (VM) som kör Ubuntu 18,04 LTS, skapa ett nyckel valv för lagringen av krypterings nycklar och kryptera den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. På sidan ny under populär väljer du **Ubuntu Server 18,04 LTS**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad.
1. För **resurs grupp**väljer du den resurs grupp som du skapade när du gjorde ditt nyckel valv ovan (t. ex. **myResourceGroup**).
1. Ange *MyVM*som **namn på virtuell dator**.
1. För **region**väljer du samma region som du använde när du gjorde ditt nyckel valv ovan (t. ex. **USA, östra**).
1. Kontrol lera att **storleken** är *standard D2s v3*.
1. Under **administratörs konto**väljer du **lösen ord**. Ange ett användar namn och ett lösen ord.

    :::image type="content" source="./media/disk-encryption/portal-qs-vm-creation.png" alt-text="Skärmen skapa ResourceGroup":::


    > [!WARNING]
    > Fliken "diskar" innehåller fältet "krypterings typ" under **disk alternativ**. Det här fältet används för att ange krypterings alternativ för [Managed disks](managed-disks-overview.md) + CMK, inte för Azure Disk Encryption. 
    >
    > För att undvika förvirring rekommenderar vi att du hoppar över fliken *diskar* helt när du har slutfört den här självstudien. 

1. Välj fliken "hantering" och kontrol lera att du har ett konto för diagnostik-lagring. Om du inte har några lagrings konton väljer du "Skapa ny", ger ditt nya konto ett namn och väljer OK

    :::image type="content" source="./media/disk-encryption/portal-qs-vm-creation-storage.png" alt-text="Skärmen skapa ResourceGroup":::

1. Klicka på granska + skapa.
1. På sidan **Skapa en virtuell dator** kan du se information om den virtuella datorn som du håller på att skapa. När du är klar väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

1. När den virtuella dator distributionen är klar väljer **du gå till resurs**.
1. På den vänstra sid panelen väljer du **diskar**.
1. På skärmen diskar väljer du **kryptering**. 

    :::image type="content" source="../media/disk-encryption/portal-qs-disks-to-encryption.png" alt-text="Val av diskar och kryptering":::

1. Välj **operativ system och data diskar**under **diskar som ska krypteras**på sidan kryptering.
1. Under **krypterings inställningar**väljer du **Välj ett nyckel valv och nyckel för kryptering**.
1. På skärmen **Välj nyckel från Azure Key Vault** väljer du **Skapa ny**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Val av diskar och kryptering":::

1. På skärmen **skapa nyckel valv** kontrollerar du att resurs gruppen är samma som den som du använde för att skapa den virtuella datorn.
1. Ge ditt nyckel valv ett namn.  Varje nyckel valv i Azure måste ha ett unikt namn.
1. På fliken **åtkomst principer** markerar du rutan **Azure Disk Encryption för volym kryptering** .

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-enable.png" alt-text="Val av diskar och kryptering":::

1. Välj **Granska + skapa**.  
1. När nyckel valvet har klarat valideringen väljer du **skapa**. Du kommer tillbaka till skärmen **Välj nyckel från Azure Key Vault** .
1. Lämna fältet **nyckel** tomt och välj **Välj**.
1. Klicka på **Spara**överst på krypterings skärmen. En popup varnar dig om att den virtuella datorn startas om. Klicka på **Ja**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan Ta bort och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault som har Aktiver ATS för krypterings nycklar, skapat en virtuell dator och aktiverat den virtuella datorn för kryptering.  

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
