---
title: Använd Azure-portalen för att konfigurera kundhanterade nycklar för tjänsten Import/Export
description: Lär dig hur du använder Azure-portalen för att konfigurera kundhanterade nycklar med Azure Key Vault för Azure Import/Export-tjänsten. Med kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomstkontroller.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a7077b5e94800d93833f259fefd0cd4c168ec867
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811438"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Använda kundhanterade nycklar i Azure Key Vault for Import/Export-tjänsten

Azure Import/Export skyddar BitLocker-nycklarna som används för att låsa enheterna via en krypteringsnyckel. Som standard krypteras BitLocker-nycklar med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du också tillhandahålla kundhanterade nycklar.

Kundhanterade nycklar måste skapas och lagras i ett Azure Key Vault. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)

Den här artikeln visar hur du använder kundhanterade nycklar med tjänsten Import/Export i [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har skapat ett import- eller exportjobb enligt instruktionerna i:

    - [Skapa ett importjobb för blobbar](storage-import-export-data-to-blobs.md).
    - [Skapa ett importjobb för filer](storage-import-export-data-to-files.md).
    - [Skapa ett exportjobb för blobbar](storage-import-export-data-from-blobs.md)

2. Du har ett befintligt Azure Key Vault med en nyckel i det som du kan använda för att skydda din BitLocker-nyckel. Mer information om hur du skapar ett nyckelvalv med Azure-portalen finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../../key-vault/quick-create-portal.md).

    - **Mjuk borttagning** **och Rensa inte** är inställda på ditt befintliga Key Vault. Dessa egenskaper är inte aktiverade som standard. Om du vill aktivera dessa egenskaper finns i avsnitten **Aktivera mjuk borttagning** och **Aktivera rensningsskydd** i någon av följande artiklar:

        - [Så här använder du mjuk borttagning med PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
        - [Så här använder du mjuk borttagning med CLI](../../key-vault/key-vault-soft-delete-cli.md).
    - Det befintliga nyckelvalvet bör ha en RSA-nyckel på 2048 storlek eller mer. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - Nyckelvalvet måste finnas i samma region som lagringskontot för dina data.  
    - Om du inte har ett befintligt Azure Key Vault kan du också skapa det infogat enligt beskrivningen i följande avsnitt.

## <a name="enable-keys"></a>Aktivera nycklar

Det är valfritt att konfigurera kundhanterad nyckel för tjänsten Importera/Exportera. Som standard använder tjänsten Import/Export en Microsoft-hanterad nyckel för att skydda bitlockernyckeln. Så här aktiverar du kundhanterade nycklar i Azure-portalen:

1. Gå till **bladet Översikt** för ditt importjobb.
2. Välj **hur BitLocker-tangenterna ska krypteras**i den högra rutan .

    ![Välj krypteringsalternativ](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. I **krypteringsbladet** kan du visa och kopiera enhetens BitLocker-nyckel. Under **Krypteringstyp**kan du välja hur du vill skydda BitLocker-tangenten. Som standard används en Hanterad Microsoft-nyckel.

    ![Visa BitLocker-tangent](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Du har möjlighet att ange en kundhanterad nyckel. När du har valt den hanterade nyckeln för kunden **väljer du nyckelvalv och en nyckel**.

    ![Välj kundhanterad nyckel](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. I **bladet Välj nyckel från Azure Key Vault** fylls prenumerationen i automatiskt. För **Key vault**kan du välja ett befintligt nyckelvalv i listrutan.

    ![Markera eller skapa Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Du kan också välja **Skapa ny** för att skapa ett nytt nyckelvalv. I **bladet Skapa nyckelvalv**anger du resursgruppen och nyckelvalvets namn. Acceptera alla andra standardvärden. Välj **Granska + Skapa**.

    ![Skapa nytt Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Granska informationen som är associerad med nyckelvalvet och välj **Skapa**. Vänta ett par minuter innan nyckelvalvet har skapats.

    ![Skapa Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. I **Välj-tangenten från Azure Key Vault**kan du välja en nyckel i det befintliga nyckelvalvet.

9. Om du har skapat ett nytt nyckelvalv väljer du **Skapa nytt** för att skapa en nyckel. RSA-nyckelstorlek kan vara 2048 eller högre.

    ![Skapa ny nyckel i Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Om skyddet för mjuk borttagning och rensning inte är aktiverat när du skapar nyckelvalvet uppdateras nyckelvalvet så att det har aktiverat mjukt borttagnings- och rensningsskydd.

10. Ange namnet på nyckeln, acceptera de andra standardinställningarna och välj **Skapa**.

    ![Skapa ny nyckel](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Markera **versionen** och välj sedan **Välj**. Du får ett meddelande om att en nyckel har skapats i nyckelvalvet.

    ![Ny nyckel skapad i nyckelvalvet](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

I **krypteringsbladet** kan du se nyckelvalvet och nyckeln som valts för kundens hanterade nyckel.

## <a name="disable-keys"></a>Inaktivera nycklar

Du kan bara inaktivera Hanterade Microsoft-nycklar och gå till kundhanterade nycklar när som helst i import-/exportjobbet. Du kan dock inte inaktivera den hanterade nyckeln för kunden när du har skapat den.

## <a name="troubleshoot-customer-managed-key-errors"></a>Felsöka kundhanterade nyckelfel

Om du får några fel relaterade till kundens hanterade nyckel använder du följande tabell för att felsöka:

| Felkod     |Information     | Ersättningsgilla?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Tillämpade en kundhanterad nyckel men nyckelåtkomsten har återkallats. Mer information finns i aktivera [nyckelåtkomsten](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Ja, kontrollera om: <ol><li>Nyckelvalvet har fortfarande MSI i åtkomstprincipen.</li><li>Åtkomstprincipen ger behörighet till Hämta, Radbryt, Packa upp.</li><li>Om nyckelvalvet finns i ett vNet bakom brandväggen kontrollerar du om **Tillåt Microsoft Trusted Services** är aktiverat.</li></ol>                                                                                            |
| CmkErrorDisabled      | Använde en kundhanterad nyckel men nyckeln är inaktiverad. Mer information finns i aktivera [nyckeln](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Ja, genom att aktivera nyckelversionen     |
| CmkErrorNotFound      | Tillämpade en kundhanterad nyckel men kan inte hitta nyckeln. <br>Om nyckeln tas bort och rensas efter kvarhållningsperioden kan du inte återställa nyckeln. Om du säkerhetskopierade nyckeln kan du återställa nyckeln för att lösa problemet. | Nej, nyckeln har tagits bort och har även rensats efter kvarhållningsperioden. <br>Ja, bara om kunden har nyckeln säkerhetskopierad och återställer den.  |
| CmkErrorVaultNotFound | Tillämpade en kundhanterad nyckel men kan inte hitta nyckelvalvet som är associerat med nyckeln.<br>Om du har tagit bort nyckelvalvet kan du inte återställa den hanterade nyckeln för kunden.  Om du har migrerat nyckelvalvet till en annan klient kan du [se Ändra ett klient-ID för nyckelvalv efter ett prenumerationssteg](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). |   Nej, om kunden har tagit bort nyckelvalvet.<br> Ja, om nyckelvalvet genomgick en klientmigrering gör du en av: <ol><li>flytta tillbaka nyckelvalvet till den gamla klienten.</li><li>ange Identitet = Ingen och sedan tillbaka till Identity = SystemAssigned, tas bort och återskapar identiteten</li></ol>|

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
