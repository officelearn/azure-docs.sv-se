---
title: Använd Azure Portal för att konfigurera Kundhanterade nycklar
titleSuffix: Cognitive Services
description: Lär dig hur du använder Azure Portal för att konfigurera Kundhanterade nycklar med Azure Key Vault. Med Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomst kontroller.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: ad97bde447a7bfbddca480b8561403f2cee7e25a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372286"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure Portal

Du måste använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Den Cognitive Services resursen och nyckel valvet måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av [Azure Portal](https://portal.azure.com/). Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurera Azure Key Vault

Om du använder Kundhanterade nycklar måste du ange två egenskaper för nyckel valvet, **mjuk borttagning** och **Rensa inte**. Dessa egenskaper är inte aktiverade som standard, men kan aktive ras med antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckel valv.

> [!IMPORTANT]
> Om du inte har den **mjuka borttagningen** och inte **rensar** de egenskaper som är aktiverade och du tar bort din nyckel, kan du inte återställa data i din kognitiva tjänst resurs.

Information om hur du aktiverar de här egenskaperna för ett befintligt nyckel valv finns i avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i någon av följande artiklar:

- [Använda mjuk borttagning med PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Använda mjuk borttagning med CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Endast RSA-nycklar med storleken 2048 stöds med Azure Storage kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till din Cognitive Services-resurs.
1. På bladet **Inställningar** för Cognitive Services resurs klickar du på **kryptering**. Välj alternativet **Kundhanterade nycklar** , som du ser i följande bild.

    ![Skärm bild som visar hur du väljer Kundhanterade nycklar](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat Kundhanterade nycklar har du möjlighet att ange en nyckel som ska associeras med den Cognitive Services resursen.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Följ dessa steg om du vill ange en nyckel som en URI:

1. Om du vill hitta nyckel-URI: n i Azure Portal navigerar du till nyckel valvet och väljer inställningen **nycklar** . Välj önskad nyckel och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckel version för att visa inställningarna för den versionen.
1. Kopiera värdet för fältet **nyckel identifierare** , som innehåller URI.

    ![Skärm bild som visar Key Vault Key-URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. I **krypterings** inställningarna för ditt lagrings konto väljer du alternativet för att **Ange nyckel-URI** .
1. Klistra in den URI som du kopierade i fältet **nyckel-URI** .

   ![Skärm bild som visar hur du anger nyckel-URI](../media/cognitive-services-encryption/ssecmk2.png)

1. Ange den prenumeration som innehåller nyckel valvet.
1. Spara ändringarna.

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckel valv

Om du vill ange en nyckel från ett nyckel valv måste du först kontrol lera att du har ett nyckel valv som innehåller en nyckel. Följ dessa steg om du vill ange en nyckel från ett nyckel valv:

1. Välj alternativet **Välj från Key Vault** .
1. Välj det nyckel valv som innehåller den nyckel som du vill använda.
1. Välj nyckeln från nyckel valvet.

   ![Skärm bild som visar kundhanterad nyckel alternativ](../media/cognitive-services-encryption/ssecmk3.png)

1. Spara ändringarna.

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel uppdaterar du Cognitive Services resursen så att den använder den nya versionen. Följ de här stegen:

1. Navigera till Cognitive Services resurs och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckel versionen. Alternativt kan du välja nyckel valvet och nyckeln igen för att uppdatera versionen.
1. Spara ändringarna.

## <a name="use-a-different-key"></a>Använd en annan nyckel

Följ dessa steg om du vill ändra nyckeln som används för kryptering:

1. Navigera till Cognitive Services resurs och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckeln. Alternativt kan du välja nyckel valvet och välja en ny nyckel.
1. Spara ändringarna.

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar krypteras din Cognitive Services-resurs med Microsoft-hanterade nycklar. Följ dessa steg om du vill inaktivera Kundhanterade nycklar:

1. Navigera till Cognitive Services resurs och visa **krypterings** inställningarna.
1. Avmarkera kryss rutan bredvid inställningen **Använd din egen nyckel** .

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulär för Cognitive Services kundhanterad nyckel](https://aka.ms/cogsvc-cmk)
* [Kryptering av ansikts tjänster för vilande data](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker kryptering av data i vila](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Language Understanding tjänst kryptering av data i vila](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator kryptering av data i vila](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Translator-kryptering av data i vila](../translator/translator-encryption-of-data-at-rest.md)
