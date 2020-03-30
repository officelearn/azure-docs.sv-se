---
title: Skapa och kryptera en virtuell Linux-dator med Azure-portalen
description: I den här snabbstarten får du lära dig hur du använder Azure-portalen för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 9f09f28dc63e7f061946a66beb59bd4c62be70aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970564"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Snabbstart: Skapa och kryptera en virtuell dator med Azure-portalen

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure-portalen är ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. I den här snabbstarten använder du Azure-portalen för att distribuera en virtuell Linux-dator (VM) som kör Ubuntu 18.04 LTS, skapar ett nyckelvalv för lagring av krypteringsnycklar och krypterar den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Ubuntu Server 18.04 LTS**under Populär på sidan Nytt .
1. Kontrollera att rätt prenumeration är markerad under **Projektinformation**på fliken **Grunderna.**
1. För **resursgrupp**väljer du den resursgrupp som du skapade när du skapade nyckelvalvet ovan (t.ex. **myResourceGroup**).
1. För **namn på virtuell dator**anger du *MyVM*.
1. För **Region**väljer du samma region som du använde när du gjorde nyckelvalvet ovan (t.ex. **östra USA).**
1. Kontrollera att **storleken** är *Standard D2s v3*.
1. Under **Administratörskonto**väljer du **Lösenord**. Ange ett användarnamn och ett lösenord.
    ![Skapa resursgruppsskärm](./media/disk-encryption/portal-qs-vm-creation.png)
1. Välj fliken "Hantering" och kontrollera att du har ett diagnostiklagringskonto. Om du inte har några lagringskonton väljer du "Skapa ny", ![ger ditt nya konto ett namn och väljer skärmen "Ok" Skapa Resursgrupp](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Klicka på "Granska + Skapa".
1. På sidan **Skapa en virtuell dator** kan du se information om den virtuella datorn som du håller på att skapa. När du är klar väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

1. När vm-distributionen är klar väljer du **Gå till resurs**.
1. Välj **Diskar**i det vänstra sidofältet .
1. Välj **Kryptering**på diskskärmen . 

    ![diskar och krypteringsval](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Välj **OPERATIVSYSTEM och datadiskar**under **Diskar för att kryptera**.
1. Under **Krypteringsinställningar**väljer du **Välj ett nyckelvalv och nyckel för kryptering**.
1. På skärmen Välj på skärmen Välj på skärmen Välj i **Azure Key Vault** väljer du Skapa **nytt**.

    ![diskar och krypteringsval](../media/disk-encryption/portal-qs-keyvault-create.png)

1. På skärmen **Skapa nyckelvalv** kontrollerar du att resursgruppen är samma som den du använde för att skapa den virtuella datorn.
1. Ge ditt nyckelvalv ett namn.  Varje nyckelvalv i Azure måste ha ett unikt namn.
1. Kontrollera azure **diskkryptering för volymkryptering** på fliken **Åtkomstprinciper.**

    ![diskar och krypteringsval](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Välj **Granska + skapa**.  
1. När nyckelvalvet har passerat valideringen väljer du **Skapa**. Då kommer du till **skärmen Välj från Azure Key Vault.**
1. Lämna fältet **Nyckel** tomt och välj **Välj**.
1. Klicka på **Spara**högst upp på krypteringsskärmen . En popup kommer att varna dig att den virtuella datorn kommer att starta om. Klicka på **Ja**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan Ta bort och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Nyckelvalv som har aktiverats för krypteringsnycklar, skapade en virtuell dator och aktiverade den virtuella datorn för kryptering.  

> [!div class="nextstepaction"]
> [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)
