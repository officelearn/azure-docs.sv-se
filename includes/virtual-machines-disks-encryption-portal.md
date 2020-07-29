---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177063"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Nu när du har skapat och konfigurerat nyckel valvet och disk krypterings uppsättningen kan du distribuera en virtuell dator med hjälp av krypteringen.
Processen för distribution av virtuella datorer liknar standard distributions processen, de enda skillnaderna är att du måste distribuera den virtuella datorn i samma region som dina andra resurser och du väljer att använda en kundhanterad nyckel.

1. Sök efter **Virtual Machines** och välj **+ Lägg** till för att skapa en virtuell dator.
1. På bladet **grundläggande** väljer du samma region som disk krypterings uppsättning och Azure Key Vault.
1. Fyll i de andra värdena på det **grundläggande** bladet som du vill.

    ![Skärm bild av upplevelsen för att skapa virtuella datorer med värdet region markerat.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. På bladet **diskar** väljer du **kryptering i vila med en kundhanterad nyckel**.
1. Välj disk krypterings uppsättning i list rutan **disk krypterings uppsättning** .
1. Gör de återstående valen som du vill.

    ![Skärm bild av bladet för att skapa virtuella datorer, bladet diskar. Med list rutan disk krypterings uppsättning markerat.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Aktivera på en befintlig disk

> [!CAUTION]
> Att aktivera disk kryptering på alla diskar som är anslutna till en virtuell dator kräver att du stoppar den virtuella datorn.
    
1. Navigera till en virtuell dator som finns i samma region som en av disk krypterings uppsättningarna.
1. Öppna den virtuella datorn och välj **stoppa**.

    ![Skärm bild av huvud överlägg för det virtuella dator exemplet med stopp-knappen markerad.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. När den virtuella datorn har stoppats väljer du **diskar** och väljer sedan den disk som du vill kryptera.

    ![Skärm bild av ditt exempel virtuella dator med bladet disk öppet. OS-disken är markerad som en exempel disk där du kan välja den.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Välj **kryptering** och välj **kryptering i vila med en kundhanterad nyckel** och välj sedan disk krypterings uppsättningen i list rutan.
1. Välj **Spara**.

    ![Skärm bild av exempel-OS-disken. Krypterings bladet är öppet, kryptering i vila med en kundhanterad nyckel har marker ATS, samt ditt exempel Azure Key Vault. När du har gjort dessa val markeras knappen Spara.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Upprepa processen för alla andra diskar som är anslutna till den virtuella dator som du vill kryptera.
1. När diskarna har växlat till Kundhanterade nycklar, och det inte finns några andra anslutna diskar som du vill kryptera, kan du starta den virtuella datorn.
