---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6077db0a09b09f7e4bfb859902da53b173845e55
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520768"
---
### <a name="portal"></a>Portalen

Om du konfigurerar kundhanterade nycklar för diskarna måste du skapa resurser i en viss ordning, om du gör det för första gången. Först måste du skapa och konfigurera ett Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Konfigurera ditt Azure Key Vault

1. Logga in på [Azure-portalen](https://portal.azure.com/) och sök efter Key Vault
1. Sök efter och välj **Nyckelvalv**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Ditt Azure-nyckelvalv, diskkrypteringsuppsättning, virtuell dator, diskar och ögonblicksbilder måste alla finnas i samma region och prenumeration för distribution för att lyckas.

1. Välj **+Lägg till** om du vill skapa ett nytt nyckelvalv.
1. Skapa en ny resursgrupp
1. Ange ett nyckelvalvsnamn, välj en region och välj en prisnivå.
1. Välj **Granska + Skapa**, kontrollera dina val och välj sedan **Skapa**.

    ![Skärmbild av skapandet av Azure Key Vault. Visa de specifika värden du skapar](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. När nyckelvalvet är klart väljer du det.
1. Välj **Tangenter** under **Inställningar**.
1. Välj **Generera/importera**

    ![Skärmbild av fönstret Resursinställningar för Key Vault. Visar knappen generera/importera inuti inställningarna.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Lämna både **nyckeltyp** inställd på **RSA** och **RSA nyckelstorlek** inställd på **2048**.
1. Fyll i de återstående valen som du vill och välj sedan **Skapa**.

    ![Skärmbild av knappen Skapa ett nyckelblad som visas när knappen generera/importera är markerad](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Konfigurera diskkrypteringsuppsättningen

Om du vill skapa och konfigurera diskkrypteringsuppsättningar måste du använda följande länk: https://aka.ms/diskencryptionsets. Om du befinner dig i Microsoft Azure Government-regionerna [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)måste du använda den här länken i stället: . Skapandet av diskkrypteringsuppsättning är ännu inte tillgängligt i den globala Azure-portalen.

1. Öppna länken diskkrypteringsuppsättningar som är lämplig för din region:

    Offentliga regioner:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government-regioner:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Välj **+Lägg till**.

    ![Skärmbild av huvudskärmen för diskkrypteringsportalen. Markera knappen Lägg till](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Välj din resursgrupp, namnge krypteringsuppsättningen och välj samma region som nyckelvalvet.
1. Välj **Nyckelvalv och nyckel**.
1. Välj det nyckelvalv och den nyckel som du skapade tidigare, samt versionen.
1. Tryck **på Markera**.
1. Välj **Granska + Skapa** och **skapa**sedan .

    ![Skärmbild av bladet för att skapa diskkryptering. Visar prenumeration, resursgrupp, diskkrypteringsuppsättningsnamn, region och nyckelvalv + nyckelväljare.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Öppna diskkrypteringsuppsättningen när den är klar med att skapa och välj den avisering som dyker upp.

    ![Skärmbild av varningspopup: "Om du vill associera en disk, bild eller ögonblicksbild med en diskkrypteringsuppsättning måste du bevilja behörigheter till nyckelvalvet". Välj den här aviseringen om du vill fortsätta](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Två meddelanden ska dyka upp och lyckas. Om du gör det kan du använda diskkrypteringsuppsättningen med nyckelvalvet.

![Skärmbild av lyckad behörighet och rolltilldelning för nyckelvalvet.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Nu när du har skapat och konfigurerat nyckelvalvet och diskkrypteringsuppsättningen kan du distribuera en virtuell dator med krypteringen.
Den virtuella distributionsprocessen liknar standarddistributionsprocessen, de enda skillnaderna är att du behöver distribuera den virtuella datorn i samma region som dina andra resurser och du väljer att använda en kundhanterad nyckel.

1. Öppna länken diskkrypteringsuppsättningar som är lämplig för din region:

    Offentliga regioner:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government-regioner:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Sök efter **virtuella datorer** och välj + **Lägg till** för att skapa en virtuell dator.
1. På fliken **Grundläggande** väljer du samma region som diskkrypteringsuppsättningen och Azure Key Vault.
1. Fyll i de andra värdena på fliken **Grundläggande** som du vill.

    ![Skärmbild av den virtuella datorns skapandeupplevelse, med regionvärdet markerat.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. På fliken **Diskar** väljer du **Kryptering i vila med en kundhanterad nyckel**.
1. Välj diskkrypteringsuppsättningen i listrutan **Diskkrypteringsuppsättning.**
1. Gör de återstående valen som du vill.

    ![Skärmbild av den virtuella datorns skapande av den virtuella hårddisken bladet. Med den nedrullningssna rullgardinsmenyn för diskkryptering markerad.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Aktivera på en befintlig disk

Om du vill hantera och konfigurera diskkryptering på dina https://aka.ms/diskencryptionsetsbefintliga diskar måste du använda följande länk: . Det finns ännu inte tillgängligt att aktivera kundhanterade nycklar på befintliga diskar i den globala Azure-portalen.

> [!CAUTION]
> Om du aktiverar diskkryptering på alla diskar som är anslutna till en virtuell dator måste du stoppa den virtuella datorn.

1. Öppna länken diskkrypteringsuppsättningar som är lämplig för din region:

    Offentliga regioner:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government-regioner:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Navigera till en virtuell dator som finns i samma region som en av diskkrypteringsuppsättningarna.
1. Öppna den virtuella datorn och välj **Stoppa**.

    ![Skärmbild av huvudöverlägget för din exempeld-vm. Med stoppknappen markerad](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. När den virtuella datorn har stoppats väljer du **Diskar** och väljer sedan den disk som du vill kryptera.

    ![Skärmbild av exempeldasss, med bladet Diskar öppet. OS-disken är markerad, som en exempeldisk som du kan välja.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Välj **Kryptering** och välj **Kryptering i vila med en kundhanterad nyckel** och välj sedan diskkrypteringsuppsättningen i listrutan.
1. Välj **Spara**.

    ![Skärmbild av exempeloperativsystemets disk. Krypteringsbladet är öppet, kryptering i vila med en kundhanterad nyckel väljs, liksom ditt exempel Azure Key Vault. När du har gjort dessa val markeras spara-knappen.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Upprepa den här processen för alla andra diskar som är anslutna till den virtuella datorn som du vill kryptera.
1. När diskarna är klara med att växla över till kundhanterade nycklar, om det inte finns några andra anslutna diskar som du vill kryptera, kan du starta den virtuella datorn.
