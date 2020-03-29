---
title: Använda Azure-portalen för att konfigurera kundhanterade nycklar
titleSuffix: Cognitive Services
description: Lär dig hur du använder Azure-portalen för att konfigurera kundhanterade nycklar med Azure Key Vault. Med kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomstkontroller.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 22bd3afcf30b8b8ebce18b22d5419d49ec8c3b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053612"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurera kundhanterade nycklar med Azure Key Vault med hjälp av Azure-portalen

Du måste använda Azure Key Vault för att lagra dina kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Cognitive Services-resursen och nyckelvalvet måste finnas i samma region och i samma Azure Active Directory-klientorganisation (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Den här artikeln visar hur du konfigurerar ett Azure Key Vault med kundhanterade nycklar med Hjälp av [Azure-portalen](https://portal.azure.com/). Mer information om hur du skapar ett nyckelvalv med Azure-portalen finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurera Azure Key Vault

Med hjälp av kundhanterade nycklar krävs att två egenskaper anges i nyckelvalvet, **Mjuk borttagning** **och Rensa inte**. Dessa egenskaper är inte aktiverade som standard, men kan aktiveras med hjälp av antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckelvalv.

> [!IMPORTANT]
> Om du inte har aktiverat egenskaperna **Mjuk borttagning** och **Rensa inte** och du tar bort nyckeln kan du inte återställa data i cognitive service-resursen.

Mer information om hur du aktiverar dessa egenskaper i ett befintligt nyckelvalv finns i avsnitten **Aktivera mjuk borttagning** och **Aktivera rensningsskydd** i någon av följande artiklar:

- [Så här använder du mjuk borttagning med PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Så här använder du mjuk borttagning med CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Endast RSA-nycklar i storlek 2048 stöds med Azure Storage-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar

Så här aktiverar du kundhanterade nycklar i Azure-portalen:

1. Navigera till resursen Cognitive Services.
1. Klicka på **Kryptering**i bladet **Inställningar** för cognitive services. Välj alternativet **Hanterade kundnycklar,** som visas i följande bild.

    ![Skärmbild som visar hur du väljer Kundhanterade nycklar](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat kundhanterade nycklar har du möjlighet att ange en nyckel som ska associeras med cognitive services-resursen.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Så här anger du en nyckel som en URI:

1. Om du vill hitta nyckeln URI i Azure-portalen navigerar du till nyckelvalvet och väljer inställningen **Nycklar.** Markera önskad tangent och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckelversion om du vill visa inställningarna för den versionen.
1. Kopiera värdet i fältet **Nyckelidentifierare,** som tillhandahåller URI.Copy the value of the Key Identifier field, which provides the URI.

    ![Skärmbild som visar nyckelvalvsnyckeln URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. I **krypteringsinställningarna** för ditt lagringskonto väljer du alternativet **Ange nyckel-URI.**
1. Klistra in den URI som du kopierade till fältet **Nyckel-URI.**

   ![Skärmbild som visar hur du anger viktiga URI](../media/cognitive-services-encryption/ssecmk2.png)

1. Ange den prenumeration som innehåller nyckelvalvet.
1. Spara ändringarna.

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckelvalv

Om du vill ange en nyckel från ett nyckelvalv kontrollerar du först att du har ett nyckelvalv som innehåller en nyckel. Så här anger du en nyckel från ett nyckelvalv:

1. Välj alternativet **Välj från Nyckelvalv.**
1. Markera nyckelvalvet som innehåller den nyckel du vill använda.
1. Välj nyckeln i nyckelvalvet.

   ![Skärmbild som visar alternativet kundhanterad nyckel](../media/cognitive-services-encryption/ssecmk3.png)

1. Spara ändringarna.

## <a name="update-the-key-version"></a>Uppdatera nyckelversionen

När du skapar en ny version av en nyckel uppdaterar du Cognitive Services-resursen så att den nya versionen används. Följ de här stegen:

1. Navigera till cognitive services-resursen och visa **krypteringsinställningarna.**
1. Ange URI för den nya nyckelversionen. Alternativt kan du välja nyckelvalvet och nyckeln igen för att uppdatera versionen.
1. Spara ändringarna.

## <a name="use-a-different-key"></a>Använda en annan nyckel

Så här ändrar du nyckeln som används för kryptering:

1. Navigera till cognitive services-resursen och visa **krypteringsinställningarna.**
1. Ange URI för den nya nyckeln. Alternativt kan du välja nyckelvalvet och välja en ny nyckel.
1. Spara ändringarna.

## <a name="disable-customer-managed-keys"></a>Inaktivera kundhanterade nycklar

När du inaktiverar kundhanterade nycklar krypteras cognitive services-resursen med Microsoft-hanterade nycklar. Så här inaktiverar du kundhanterade nycklar:

1. Navigera till cognitive services-resursen och visa **krypteringsinställningarna.**
1. Avmarkera kryssrutan bredvid inställningen **Använd din egen nyckel.**

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulär för kundhanterad nyckelbegäran för kognitiva tjänster](https://aka.ms/cogsvc-cmk)
* [Face Services-kryptering av data i vila](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker kryptering av data i vila](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Språk understanding service kryptering av data i vila](../LUIS/luis-encryption-of-data-at-rest.md)
* [Kryptering av data i vila för innehållsmoderator](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Översättare kryptering av data i vila](../translator/translator-encryption-of-data-at-rest.md)
* [Personalizer kryptering av data i vila](../personalizer/personalizer-encryption-of-data-at-rest.md)
