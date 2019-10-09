---
title: Skapa och kryptera en virtuell Linux-dator med Azure Portal
description: I den här snabb starten får du lära dig hur du använder Azure Portal för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 0249867a6af16505a692e7090639807ed3d239c9
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031079"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Snabbstart: Skapa och kryptera en virtuell dator med Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure-portalen är ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. I den här snabb starten ska du använda Azure Portal för att distribuera en virtuell Linux-dator (VM) som kör Ubuntu 18,04 LTS, skapa ett nyckel valv för lagringen av krypterings nycklar och kryptera den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

1. Välj alternativet **Skapa en resurs** uppe till vänster i Azure Portal
1. Skriv **Key Vault** i sökrutan.
1. I listan resultat väljer du **Key Vault**.
1. I avsnittet Key Vault väljer du **skapa**.
1. På skärmen **skapa nyckel valv** väljer du ett unikt namn för ditt nya nyckel valv.

    > [!Important]
    > Varje Key Vault måste ha ett unikt namn. I följande exempel skapas en Key Vault med namnet *myADEKV*, men du måste namnge något annat.

1. Välj en **prenumeration**.
1.  Under **resurs grupp**väljer du **Skapa ny**. I popup-fönstret skriver du *myResourceGroup* för namnet på resursgruppen och väljer sedan **OK**. 

    ![Skärmen skapa resurs grupp](./media/disk-encryption/portal-qs-keyvaultcreation.png)

1. I list rutan **plats** väljer du **östra USA**.
1. Lämna standardvärdena för de andra alternativen.
1. Välj "åtkomst principer", som kommer att ta dig till en ny skärm.
1. Markera kryss rutan bredvid "Aktivera åtkomst till Azure Disk Encryption för volym kryptering.

    ![Skärmen skapa ResourceGroup](./media/disk-encryption/portal-qs-keyvault-enable-encryption.png)

1. Klicka på granska + skapa längst ned på skärmen åtkomst principer.
1. När du har granskat klickar du på "skapa".

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.

1. På sidan ny under populär väljer du **Ubuntu Server 18,04 LTS**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad.
1. För **resurs grupp**väljer du den resurs grupp som du skapade när du gjorde ditt nyckel valv ovan (t. ex. **myResourceGroup**)
1. Ange *MyVM*för den **virtuella datorns namn** och välj 
1. För **region**väljer du samma region som du använde när du gjorde ditt nyckel valv ovan (t. ex. **USA, östra**).
1. Kontrol lera att **storleken** är *standard D2s v3*.
1. Under **administratörs konto**väljer du **lösen ord**. Ange ett användar namn och ett lösen ord.
    @no__t 0ResourceGroup skapa skärmen @ no__t-1
1. Välj fliken "hantering" och kontrol lera att du har ett konto för diagnostik-lagring. Om du inte har några lagrings konton väljer du "Skapa ny", ger ditt nya konto ett namn och väljer "OK" @no__t 0ResourceGroup skapa skärm @ no__t-1
1. Klicka på granska + skapa.
1. På sidan **Skapa en virtuell dator** kan du se information om den virtuella datorn som du håller på att skapa. När du är klar väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

1. När den virtuella dator distributionen är klar väljer **du gå till resurs**.
1. På den vänstra sid panelen väljer du **diskar**.
1. På skärmen diskar väljer du **kryptering**. 

    ![Val av diskar och kryptering](./media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Välj **operativ system och data diskar**under **diskar som ska krypteras**på sidan kryptering.
1. Klicka på Välj ett nyckel valv och nyckel för kryptering under **krypterings inställningar**.
1. I den högra sid panelen väljer du namnet på nyckel valvet som du skapade tidigare som värde för * Key Vault * * och klickar på **Välj**.

    ![Val av diskar och kryptering](./media/disk-encryption/portal-qs-encrypt-vm-screen.png)
1. Klicka på "Spara" överst på krypterings skärmen. En popup varnar dig om att den virtuella datorn startas om. Klicka på **Ja**.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resurs gruppen för den virtuella datorn, väljer Ta bort och bekräftar sedan namnet på den resurs grupp som ska tas bort.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault som aktiverades för krypterings nycklar, skapat en virtuell dator och aktiverat den virtuella datorn för kryptering.  

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)