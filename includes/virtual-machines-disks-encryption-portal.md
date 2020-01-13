---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a799339f2780c2bc372c39120a6e20b34d907326
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912761"
---
### <a name="portal"></a>Portalen

Om du ställer in Kundhanterade nycklar för dina diskar måste du skapa resurser i en viss ordning, om du gör det för första gången. Först måste du skapa och konfigurera en Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Konfigurera din Azure Key Vault

1. Logga in på [Azure Portal](https://portal.azure.com/) och sök efter Key Vault
1. Sök efter och välj **nyckel valv**.

    [![SSE-Key-Vault-Portal-search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Ditt Azure Key Vault, disk krypterings uppsättning, virtuell dator, diskar och ögonblicks bilder måste vara i samma region och prenumeration för att distributionen ska lyckas.

1. Välj **+ Lägg** till för att skapa en ny Key Vault.
1. Skapa en ny resursgrupp
1. Ange ett nyckel valv namn, Välj en region och välj en pris nivå.
1. Välj **Granska + skapa**, verifiera dina val och välj sedan **skapa**.

    ![SSE-Create-a-Key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. När du har slutfört distributionen av nyckel valvet väljer du det.
1. Välj **nycklar** under **Inställningar**.
1. Välj **generera/importera**

    ![SSE-Key-Vault-generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Lämna båda **nyckel typerna** inställd på **RSA** och **RSA-nyckel storleken** inställd på **2080**.
1. Fyll i de återstående valen som du vill och välj sedan **skapa**.

    ![SSE-Create-a-Key-generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Konfigurera din disk krypterings uppsättning

Om du vill skapa och konfigurera disk krypterings uppsättningar måste du använda följande länk: https://aka.ms/diskencryptionsets. Det går inte att skapa disk krypterings uppsättning ännu i den globala Azure Portal.

1. Öppna [länken disk krypterings uppsättningar](https://aka.ms/diskencryptionsets).
1. Välj **+ Lägg till**.

    ![SSE-Create-Disk-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Välj din resurs grupp, namnge krypterings uppsättningen och välj samma region som nyckel valvet.
1. Välj **nyckel valv och nyckel**.
1. Välj nyckel valvet och nyckeln som du skapade tidigare, samt versionen.
1. Tryck på **Välj**.
1. Välj **Granska + skapa** och sedan **skapa**.

    ![SSE-disk-ENC-set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Öppna disk krypterings uppsättningen när den har skapats och välj den avisering som visas.

    ![SSE-disk-ENC-Alert-Fix. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Två meddelanden bör visas och lyckas. På så sätt kan du använda disk krypterings uppsättningen med nyckel valvet.

![disk-ENC-Notification-Success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Nu när du har skapat och konfigurerat nyckel valvet och disk krypterings uppsättningen kan du distribuera en virtuell dator med hjälp av krypteringen.
Processen för distribution av virtuella datorer liknar standard distributions processen, de enda skillnaderna är att du måste distribuera den virtuella datorn i samma region som dina andra resurser och du väljer att använda en kundhanterad nyckel.

1. Sök efter **Virtual Machines** och välj **+ Lägg** till för att skapa en virtuell dator.
1. På fliken **grundläggande** väljer du samma region som disk krypterings uppsättning och Azure Key Vault.
1. Fyll i de andra värdena på fliken **grundläggande** som du vill.

    ![SSE-Create-a-VM-region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. På fliken **diskar** väljer du **kryptering i vila med en kundhanterad nyckel**.
1. Välj disk krypterings uppsättning i list rutan **disk krypterings uppsättning** .
1. Gör de återstående valen som du vill.

    ![SSE-Create-VM-Select-CMK-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)