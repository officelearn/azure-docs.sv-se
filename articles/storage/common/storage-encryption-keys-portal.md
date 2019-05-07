---
title: Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från Azure portal
description: Lär dig hur du använder Azure-portalen för att konfigurera Kundhanterade nycklar för kryptering av Azure Storage. Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomstkontroller.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154159"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från Azure portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar ett nyckelvalv med Kundhanterade nycklar med hjälp av den [Azure-portalen](https://portal.azure.com/). Läs hur du skapar ett nyckelvalv med Azure portal i [snabbstarten: Ange och hämta en hemlighet från Azure Key Vault med Azure portal](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Använda Kundhanterade nycklar med Azure Storage kryptering kräver att nyckelvalvet har två nödvändiga egenskaper som har konfigurerats, **mjuk borttagning** och **gör inte rensa**. De här egenskaperna är aktiverade som standard när du skapar ett nytt nyckelvalv i Azure-portalen. Om du vill aktivera dessa egenskaper på ett befintligt nyckelvalv måste du använda PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure portal:

1. Navigera till ditt lagringskonto.
1. På den **inställningar** bladet för storage-konto klickar du på **kryptering**. Välj den **använda din egen nyckel** alternativ, enligt följande bild.

    ![Portalen skärmbild som visar krypteringsalternativet](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat Kundhanterade nycklar har möjlighet att ange en nyckel som ska associeras med lagringskontot.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Följ dessa steg om du vill ange en nyckel som en URI:

1. Om du vill leta upp nyckeln URI: N i Azure-portalen, navigera till ditt nyckelvalv och välj den **nycklar** inställningen. Välj önskad nyckel och sedan på nyckeln om du vill visa dess inställningar. Kopiera värdet för den **nyckelidentifierare** som anger URI: N.

    ![Skärmbild som visar key vault-nyckeln URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. I den **kryptering** inställningar för ditt lagringskonto, Välj den **RETUR-tangenten URI** alternativet.
1. I den **Key URI: N** fältet Ange URI: N.

   ![Skärmbild som visar hur du ange URI för nyckel](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från key vault

Om du vill ange en nyckel från key vault, se till att du har ett nyckelvalv som innehåller en nyckel. Följ dessa steg om du vill ange en nyckel från key vault:

1. Välj den **Välj från Key Vault** alternativet.
2. Välj det nyckelvalv som innehåller den nyckel som du vill använda.
3. Välj nyckeln från nyckelvalvet.

   ![Skärmbild som visar kundhanterad nyckel alternativet](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Uppdatera nyckelns version

När du skapar en ny version av en nyckel, måste du uppdatera lagringskontot för att använda den nya versionen. Följ de här stegen:

1. Gå till ditt lagringskonto och visa den **kryptering** inställningar.
1. Ange URI för den nya nyckelversionen. Alternativt kan välja du nyckelvalvet och nyckeln igen för att uppdatera versionen.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
