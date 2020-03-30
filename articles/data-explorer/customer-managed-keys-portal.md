---
title: Konfigurera kundhanterade nycklar med Azure-portalen
description: I den här artikeln beskrivs hur du konfigurerar kryptering med kundhanterade nycklar på dina data i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301049"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Konfigurera kundhanterade nycklar med Azure-portalen

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-mall](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Aktivera kryptering med kundhanterade nycklar i Azure-portalen

Den här artikeln visar hur du aktiverar kryptering med kundhanterade nycklar med Azure-portalen. Som standard använder Azure Data Explorer-kryptering Microsoft-hanterade nycklar. Konfigurera azure data explorer-klustret för att använda kundhanterade nycklar och ange nyckeln som ska associeras med klustret.

1. Gå till din Azure [Data Explorer-klusterresurs](create-cluster-database-portal.md#create-a-cluster) i [Azure-portalen.](https://portal.azure.com/) 
1. Välj **Inställningar** > **Kryptering** i vänstra rutan i portalen.
1. Välj **På** för inställningen **Kundhanterad nyckel** i **krypteringsfönstret.**
1. Klicka på **Välj nyckel**.

    ![Konfigurera kundhanterade nycklar](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. I fönstret **Välj nyckel från Azure Key Vault** väljer du ett befintligt **nyckelvalv** i listrutan. Om du väljer **Skapa ny** för att skapa ett [nytt Nyckelvalv](/azure/key-vault/quick-create-portal#create-a-vault)dirigeras du till skärmen **Skapa nyckelvalv.**

1. Välj **Nyckel**.
1. Välj **Version**.
1. Klicka på **Markera**.

    ![Välj nyckel från Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. Välj **Spara**i **krypteringsfönstret** som nu innehåller nyckeln . När CMK skapas visas ett meddelande om lyckade försök i **Aviseringar**.

    ![Spara kundhanterad nyckel](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Genom att aktivera kundhanterade nycklar för ditt Azure Data Explorer-kluster skapar du en systemtilldelad identitet för klustret om det inte finns någon. Dessutom tillhandahåller du de vybehörigheter som krävs för ditt Azure Data Explorer-kluster i det valda Nyckelvalvet och får egenskaperna Nyckelvalv. 

> [!NOTE]
> Välj **Av** om du vill ta bort den kundhanterade nyckeln när den har skapats.

## <a name="next-steps"></a>Nästa steg

* [Säkra Azure Data Explorer-kluster i Azure](security.md)
* [Skydda klustret i Azure Data Explorer - Azure-portalen](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera kundhanterade nycklar med azure Resource Manager-mallen](customer-managed-keys-resource-manager.md)
* [Konfigurera kundhanterade nycklar med C #](customer-managed-keys-csharp.md)



