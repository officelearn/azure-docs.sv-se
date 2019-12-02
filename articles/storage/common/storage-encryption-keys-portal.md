---
title: Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure Portal-Azure Storage
description: Lär dig hur du använder Azure Portal för att konfigurera Kundhanterade nycklar med Azure Key Vault för Azure Storage kryptering. Med Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomst kontroller.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b72f30a636d0177edb8b3254d14be941b2d4e6bc
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665923"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-the-azure-portal"></a>Konfigurera Kundhanterade nycklar för Azure Storage med hjälp av Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av [Azure Portal](https://portal.azure.com/). Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Om du använder Kundhanterade nycklar med Azure Storage kryptering måste du ange två egenskaper för nyckel valvet, **mjuk borttagning** och **Rensa inte**. De här egenskaperna är inte aktiverade som standard. Om du vill aktivera dessa egenskaper använder du antingen PowerShell eller Azure CLI.
> Endast RSA-nycklar och nyckel storlek 2048 stöds.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till ditt lagringskonto.
1. På bladet **Inställningar** för lagrings kontot klickar du på **kryptering**. Välj alternativet **Använd en egen nyckel** , som du ser i följande bild.

    ![Portal skärm bild som visar krypterings alternativ](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat Kundhanterade nycklar har du möjlighet att ange en nyckel som ska associeras med lagrings kontot.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Följ dessa steg om du vill ange en nyckel som en URI:

1. Om du vill hitta nyckel-URI: n i Azure Portal navigerar du till nyckel valvet och väljer inställningen **nycklar** . Välj önskad nyckel och klicka sedan på nyckeln för att visa dess inställningar. Kopiera värdet för fältet **nyckel identifierare** , som innehåller URI.

    ![Skärm bild som visar Key Vault Key-URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. I **krypterings** inställningarna för ditt lagrings konto väljer du alternativet för att **Ange nyckel-URI** .
1. I fältet **nyckel-URI** anger du URI.

   ![Skärm bild som visar hur du anger nyckel-URI](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckel valv

Om du vill ange en nyckel från ett nyckel valv måste du först kontrol lera att du har ett nyckel valv som innehåller en nyckel. Följ dessa steg om du vill ange en nyckel från ett nyckel valv:

1. Välj alternativet **Välj från Key Vault** .
2. Välj det nyckel valv som innehåller den nyckel som du vill använda.
3. Välj nyckeln från nyckel valvet.

   ![Skärm bild som visar kundhanterad nyckel alternativ](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel måste du uppdatera lagrings kontot för att använda den nya versionen. Följ de här stegen:

1. Navigera till ditt lagrings konto och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckel versionen. Alternativt kan du välja nyckel valvet och nyckeln igen för att uppdatera versionen.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage kryptering för vilande data](storage-service-encryption.md)
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
