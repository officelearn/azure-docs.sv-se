---
title: Använd Azure Portal för att konfigurera Kundhanterade nycklar
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure Portal för att konfigurera Kundhanterade nycklar med Azure Key Vault för Azure Storage kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 4af70a4e2a698bd280c8c41018bc5aaa1bfa27f8
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85512546"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av [Azure Portal](https://portal.azure.com/). Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurera Azure Key Vault

Om du använder Kundhanterade nycklar med Azure Storage kryptering måste du ange två egenskaper för nyckel valvet, **mjuk borttagning** och **Rensa inte**. Dessa egenskaper är inte aktiverade som standard, men kan aktive ras med antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckel valv.

Information om hur du aktiverar de här egenskaperna för ett befintligt nyckel valv finns i avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i någon av följande artiklar:

- [Använda mjuk borttagning med PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Använda mjuk borttagning med CLI](../../key-vault/general/soft-delete-cli.md).

Azure Storage-kryptering stöder RSA-och RSA-HSM-nycklar i storlekarna 2048, 3072 och 4096. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till ditt lagringskonto.
1. På bladet **Inställningar** för lagrings kontot klickar du på **kryptering**. Välj alternativet **Kundhanterade nycklar** , som du ser i följande bild.

    ![Portal skärm bild som visar krypterings alternativ](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat Kundhanterade nycklar har du möjlighet att ange en nyckel som ska associeras med lagrings kontot.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Följ dessa steg om du vill ange en nyckel som en URI:

1. Om du vill hitta nyckel-URI: n i Azure Portal navigerar du till nyckel valvet och väljer inställningen **nycklar** . Välj önskad nyckel och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckel version för att visa inställningarna för den versionen.
1. Kopiera värdet för fältet **nyckel identifierare** , som innehåller URI.

    ![Skärm bild som visar Key Vault Key-URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. I **krypterings** inställningarna för ditt lagrings konto väljer du alternativet för att **Ange nyckel-URI** .
1. Klistra in den URI som du kopierade i fältet **nyckel-URI** .

   ![Skärm bild som visar hur du anger nyckel-URI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Ange den prenumeration som innehåller nyckel valvet.
1. Spara ändringarna.

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckel valv

Om du vill ange en nyckel från ett nyckel valv måste du först kontrol lera att du har ett nyckel valv som innehåller en nyckel. Följ dessa steg om du vill ange en nyckel från ett nyckel valv:

1. Välj alternativet **Välj från Key Vault** .
1. Välj det nyckel valv som innehåller den nyckel som du vill använda.
1. Välj nyckeln från nyckel valvet.

   ![Skärm bild som visar kundhanterad nyckel alternativ](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Spara ändringarna.

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

Uppdatera lagrings kontot för att använda den nya versionen när du skapar en ny version av en nyckel. Följ de här stegen:

1. Navigera till ditt lagrings konto och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckel versionen. Alternativt kan du välja nyckel valvet och nyckeln igen för att uppdatera versionen.
1. Spara ändringarna.

## <a name="use-a-different-key"></a>Använd en annan nyckel

Följ dessa steg om du vill ändra den nyckel som används för Azure Storage kryptering:

1. Navigera till ditt lagrings konto och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckeln. Alternativt kan du välja nyckel valvet och välja en ny nyckel.
1. Spara ändringarna.

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar är ditt lagrings konto återigen krypterat med Microsoft-hanterade nycklar. Följ dessa steg om du vill inaktivera Kundhanterade nycklar:

1. Navigera till ditt lagrings konto och visa **krypterings** inställningarna.
1. Avmarkera kryss rutan bredvid inställningen **Använd din egen nyckel** .

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
