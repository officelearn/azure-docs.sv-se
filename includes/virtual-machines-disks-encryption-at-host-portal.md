---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136336"
---
## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda kryptering på värden för dina virtuella datorer eller skalnings uppsättningar för virtuella datorer måste du få funktionen aktive rad i din prenumeration. Skicka ett e-postmeddelande till encryptionAtHost@microsoft. com med ditt prenumerations-ID för att få funktionen aktive rad för dina prenumerationer.

Logga in på Azure Portal med den [angivna länken](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Du måste använda den [angivna länken](https://aka.ms/diskencryptionupdates) för att få åtkomst till Azure Portal. Kryptering på värden är för närvarande inte synligt i den offentliga Azure Portal utan att använda länken.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Skapa en Azure Key Vault och disk krypterings uppsättning

När funktionen är aktive rad måste du konfigurera en Azure Key Vault och en disk krypterings uppsättning, om du inte redan gjort det.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Du måste distribuera en ny virtuell dator för att aktivera kryptering på värden, men den kan inte aktive ras på befintliga virtuella datorer.

1. Sök efter **Virtual Machines** och välj **+ Lägg** till för att skapa en virtuell dator.
1. Skapa en ny virtuell dator, Välj en lämplig region och en VM-storlek som stöds.
1. Fyll i de andra värdena på bladet **grundläggande** som du vill och fortsätt sedan till bladet **diskar** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="exempel text":::

1. På bladet **diskar** väljer du **Ja** för **kryptering på värden**.
1. Gör de återstående valen som du vill.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="exempel text":::

1. Slutför distributions processen för den virtuella datorn och gör de val som passar din miljö.

Nu har du distribuerat en virtuell dator med kryptering på värden aktive rad, men alla tillhör ande diskar krypteras med kryptering på värden.