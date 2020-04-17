---
title: Använda Azure-portalen för att konfigurera kundhanterade nycklar
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure-portalen för att konfigurera kundhanterade nycklar med Azure Key Vault för Azure Storage-kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 192e58b101b824ca0cc0c732e02647838be6dc35
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456489"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurera kundhanterade nycklar med Azure Key Vault med hjälp av Azure-portalen

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar ett Azure Key Vault med kundhanterade nycklar med Hjälp av [Azure-portalen](https://portal.azure.com/). Mer information om hur du skapar ett nyckelvalv med Azure-portalen finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurera Azure Key Vault

Om du använder kundhanterade nycklar med Azure Storage-kryptering krävs att två egenskaper anges i nyckelvalvet, **Mjuk borttagning** **och Rensa inte**. Dessa egenskaper är inte aktiverade som standard, men kan aktiveras med hjälp av antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckelvalv.

Mer information om hur du aktiverar dessa egenskaper i ett befintligt nyckelvalv finns i avsnitten **Aktivera mjuk borttagning** och **Aktivera rensningsskydd** i någon av följande artiklar:

- [Så här använder du mjuk borttagning med PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Så här använder du mjuk borttagning med CLI](../../key-vault/general/soft-delete-cli.md).

Endast 2048-bitars RSA- och RSA-HSM-nycklar stöds med Azure Storage-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar

Så här aktiverar du kundhanterade nycklar i Azure-portalen:

1. Navigera till ditt lagringskonto.
1. Klicka på **Kryptering**på bladet **Inställningar** för lagringskontot . Välj alternativet **Hanterade kundnycklar** enligt följande bild.

    ![Portal skärmdump som visar krypteringsalternativ](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat kundhanterade nycklar har du möjlighet att ange en nyckel som ska associeras med lagringskontot.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Så här anger du en nyckel som en URI:

1. Om du vill hitta nyckeln URI i Azure-portalen navigerar du till nyckelvalvet och väljer inställningen **Nycklar.** Markera önskad tangent och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckelversion om du vill visa inställningarna för den versionen.
1. Kopiera värdet i fältet **Nyckelidentifierare,** som tillhandahåller URI.Copy the value of the Key Identifier field, which provides the URI.

    ![Skärmbild som visar nyckelvalvsnyckeln URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. I **krypteringsinställningarna** för ditt lagringskonto väljer du alternativet **Ange nyckel-URI.**
1. Klistra in den URI som du kopierade till fältet **Nyckel-URI.**

   ![Skärmbild som visar hur du anger viktiga URI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Ange den prenumeration som innehåller nyckelvalvet.
1. Spara ändringarna.

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckelvalv

Om du vill ange en nyckel från ett nyckelvalv kontrollerar du först att du har ett nyckelvalv som innehåller en nyckel. Så här anger du en nyckel från ett nyckelvalv:

1. Välj alternativet **Välj från Nyckelvalv.**
1. Markera nyckelvalvet som innehåller den nyckel du vill använda.
1. Välj nyckeln i nyckelvalvet.

   ![Skärmbild som visar alternativet kundhanterad nyckel](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Spara ändringarna.

## <a name="update-the-key-version"></a>Uppdatera nyckelversionen

När du skapar en ny version av en nyckel uppdaterar du lagringskontot så att det nya versionen används. Följ de här stegen:

1. Navigera till ditt lagringskonto och visa **krypteringsinställningarna.**
1. Ange URI för den nya nyckelversionen. Alternativt kan du välja nyckelvalvet och nyckeln igen för att uppdatera versionen.
1. Spara ändringarna.

## <a name="use-a-different-key"></a>Använda en annan nyckel

Så här ändrar du nyckeln som används för Azure Storage-kryptering:

1. Navigera till ditt lagringskonto och visa **krypteringsinställningarna.**
1. Ange URI för den nya nyckeln. Alternativt kan du välja nyckelvalvet och välja en ny nyckel.
1. Spara ändringarna.

## <a name="disable-customer-managed-keys"></a>Inaktivera kundhanterade nycklar

När du inaktiverar kundhanterade nycklar krypteras ditt lagringskonto återigen med Microsoft-hanterade nycklar. Så här inaktiverar du kundhanterade nycklar:

1. Navigera till ditt lagringskonto och visa **krypteringsinställningarna.**
1. Avmarkera kryssrutan bredvid inställningen **Använd din egen nyckel.**

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering för data i vila](storage-service-encryption.md)
- [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
