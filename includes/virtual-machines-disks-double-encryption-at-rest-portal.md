---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177062"
---
## <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Komma igång

1. Logga in på [Azure-portalen](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Du måste använda den [angivna länken](https://aka.ms/diskencryptionupdates) för att få åtkomst till Azure Portal. Dubbel kryptering i vila är för närvarande inte synligt i den offentliga Azure Portal utan att använda länken.

1. Sök efter och välj **disk krypterings uppsättningar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Skärm bild av huvud Azure Portalen är disk krypterings uppsättningar markerat i Sök fältet.":::

1. Välj **+ Lägg till**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Skärm bild av bladet disk krypterings uppsättning, + Lägg till är markerat.":::

1. Välj en av de regioner som stöds.
1. För **krypterings typ**väljer du **Double Encryption med plattforms-hanterade och Kundhanterade nycklar**.

    > [!NOTE]
    > När du har skapat en disk krypterings uppsättning med en viss krypterings typ kan den inte ändras. Om du vill använda en annan krypterings typ måste du skapa en ny disk krypterings uppsättning.

1. Fyll i återstående information.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Skärm bild av bladet för att skapa disk krypterings uppsättning, regioner och Double Encryption med plattforms hanterade och Kundhanterade nycklar är markerade.":::

1. Välj en Azure Key Vault och nyckel eller skapa en ny, om det behövs.

    > [!NOTE]
    > Om du skapar en Key Vault-instans måste du aktivera mjuk borttagning och tömning av skydd. De här inställningarna är obligatoriska när du använder en Key Vault för kryptering av hanterade diskar och skyddar dig från att förlora data på grund av oavsiktlig borttagning.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Skärm bild av bladet Key Vault skapande.":::

1. Välj **Skapa**.
1. Navigera till den disk krypterings uppsättning som du skapade och välj det fel som visas. Då konfigureras disk krypterings inställningen så att den fungerar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Skärm bild av fel meddelandet disk krypterings uppsättning visas: om du vill associera en disk, avbildning eller ögonblicks bild med disk krypterings uppsättningen måste du bevilja behörighet till nyckel valvet.":::

    Ett meddelande bör visas och lyckas. På så sätt kan du använda disk krypterings uppsättningen med nyckel valvet.
    
    ![Skärm bild av lyckad behörighet och roll tilldelning för ditt nyckel valv.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Navigera till disken.
1. Välj **kryptering**.
1. För **krypterings typ**väljer du **Double Encryption med plattforms-hanterade och Kundhanterade nycklar**.
1. Välj disk krypterings uppsättning.
1. Välj **Spara**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Skärm bild av krypterings bladet för den hanterade disken, den ovan nämnda krypterings typen är markerad.":::

Du har nu aktiverat dubbel kryptering i vila på den hanterade disken.
