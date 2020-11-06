---
title: Använd Azure Portal för att konfigurera Kundhanterade nycklar för Azure Data Box
description: Lär dig hur du använder Azure Portal för att konfigurera Kundhanterade nycklar med Azure Key Vault för Azure Data Box. Med Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomst kontroller.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 40b777342c2c565efc5b40d361a259c98eae693c
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337726"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Använda Kundhanterade nycklar i Azure Key Vault för Azure Data Box

Azure Data Box skyddar enhetens upplåsnings nyckel (även kallat enhets lösen ord) som används för att låsa enheten via en krypterings nyckel. Som standard krypteras enhetens upplåsnings nyckel för en Data Box-enhet ordning med en hanterad Microsoft-nyckel. Du kan även ange en kundhanterad nyckel för ytterligare kontroll över enhetens upplåsnings nyckel. 

Kundhanterade nycklar måste skapas och lagras i en Azure Key Vault. Mer information om Azure Key Vault finns i [Azure Key Vault?](../key-vault/general/overview.md).

Den här artikeln visar hur du använder Kundhanterade nycklar med Azure Data Box i [Azure Portal](https://portal.azure.com/). Den här artikeln gäller för både Azure Data Box enheter och Azure Data Box Heavy enheter.

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har skapat en Azure Data Box order enligt anvisningarna i [Självstudier: order Azure Data Box](data-box-deploy-ordered.md).

2. Du har en befintlig Azure Key Vault med en nyckel som du kan använda för att skydda din enhets upplåsnings nyckel. Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../key-vault/secrets/quick-create-portal.md).

    - **Mjuk borttagning** och **Rensa inte** är inställda på ditt befintliga nyckel valv. De här egenskaperna är inte aktiverade som standard. Om du vill aktivera dessa egenskaper kan du läsa avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i någon av följande artiklar:

        - [Använda mjuk borttagning med PowerShell](../key-vault/general/soft-delete-powershell.md).
        - [Använda mjuk borttagning med CLI](../key-vault/general/soft-delete-cli.md).
    - Det befintliga nyckel valvet ska ha en RSA-nyckel på 2048 storlek eller mer. Mer information om nycklar finns i [om Azure Key Vault nycklar](../key-vault/keys/about-keys.md).
    - Nyckel valvet måste finnas i samma region som de lagrings konton som används för dina data. Flera lagrings konton kan länkas till din Azure Data Box-resurs.
    - Om du inte har ett befintligt nyckel valv kan du också skapa det på det sätt som beskrivs i följande avsnitt.

## <a name="enable-keys"></a>Aktivera nycklar

Det är valfritt att konfigurera kundhanterad nyckel för Azure Data Box. Som standard använder Data Box-enhet en Microsoft-hanterad nyckel för att skydda din BitLocker-nyckel. Följ dessa steg om du vill aktivera en kundhanterad nyckel i Azure Portal:

1. Gå till bladet **Översikt** för din data Box-enhets ordning.

    ![Översikts bladet för Data Box-enhet order](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Gå till **inställningar > kryptering**. Under **krypterings typ** kan du välja hur du vill skydda din enhets upplåsnings nyckel. Som standard används en hanterad Microsoft-nyckel för att skydda din enhets lösen ord för att låsa upp enheten. 

    ![Välj krypterings alternativ](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Välj krypterings typ som **kund hanterad nyckel**. När du har valt den Kundhanterade nyckeln **väljer du ett nyckel valv och nyckel**.

    ![Välj kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

4. I bladet **Välj nyckel från Azure Key Vault** fylls prenumerationen i automatiskt. För **Key Vault** kan du välja ett befintligt nyckel valv i list rutan.

    ![Välj befintlig Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

    Du kan också välja **Skapa nytt** för att skapa ett nytt nyckel valv. På **bladet skapa nyckel valv** anger du resurs gruppen och namnet på nyckel valvet. Se till att det **mjuka borttagnings** -och **rensnings skyddet** har Aktiver ATS. Acceptera alla andra standardvärden. Välj **Granska + skapa**.

    ![Granska och skapa Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

5. Granska informationen som är kopplad till ditt nyckel valv och välj **skapa**. Vänta några minuter tills nyckel valvet har skapats.

    ![Skapa Azure Key Vault med dina inställningar](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

6. I **Välj nyckel från Azure Key Vault** kan du välja en nyckel i det befintliga nyckel valvet.

    ![Välj nyckel från Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

7. Om du vill skapa en ny nyckel väljer du **Skapa ny** för att skapa en nyckel. RSA-nyckelns storlek kan vara 2048 eller högre.

    ![Skapa ny nyckel i Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

8. Ange namnet på nyckeln, acceptera de andra standardinställningarna och välj **skapa**.

    ![Namn ny nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


9. Du får ett meddelande om att en nyckel har skapats i ditt nyckel valv. Välj **version** och välj sedan **Välj**.

    ![Välj version för ny nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

10. I fönstret **krypterings typ** ser du nyckel valvet och nyckeln som du har valt för din Kundhanterade nyckel.

    ![Nyckel-och nyckel valv för kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

11. Spara nyckeln. 

    ![Spara kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Nyckel-URL: en visas under **krypterings typ**.

    ![Kund hanterad nyckel-URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Du kan inaktivera Microsofts hanterade nyckel och flytta till kundhanterad nyckel i vilken fas som helst av den Data Box-enhet ordningen. När du har skapat den Kundhanterade nyckeln kan du dock inte växla tillbaka till den Microsoft-hanterade nyckeln.

## <a name="troubleshoot-errors"></a>Felsöka fel

Om du får fel som rör din Kundhanterade nyckel kan du använda följande tabell för att felsöka.

| Felkod| Felinformation| Återställnings bara?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Det gick inte att hämta nyckeln eftersom den hanterade nyckeln för kund är inaktive rad.| Ja, genom att aktivera nyckel versionen.|
| SsemUserErrorEncryptionKeyExpired| Det gick inte att hämta nyckeln eftersom den hanterade nyckeln för kund har upphört att gälla.| Ja, genom att aktivera nyckel versionen.|
| SsemUserErrorKeyDetailsNotFound| Det gick inte att hämta nyckeln eftersom kundens hanterade nyckel inte hittades.| Om du har tagit bort nyckel valvet kan du inte återställa den Kundhanterade nyckeln.  Om du har migrerat nyckel valvet till en annan klient organisation läser du [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../key-vault/general/move-subscription.md). Om du har tagit bort nyckel valvet:<ol><li>Ja, om det är i rensnings skydds tiden, med hjälp av stegen i [återställa ett nyckel valv](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nej, om det ligger utanför rensnings skyddets varaktighet.</li></ol><br>Om nyckel valvet har genomgått en klient migrering, ja, kan den återställas med hjälp av något av stegen nedan: <ol><li>Återställ Key Vault tillbaka till den gamla klienten.</li><li>Ange `Identity = None` och ange värdet tillbaka till `Identity = SystemAssigned` . Detta tar bort och återskapar identiteten när den nya identiteten har skapats. Aktivera `Get` , `Wrap` och `Unwrap` behörighet till den nya identiteten i nyckel valvets åtkomst princip.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Det gick inte att hämta nyckeln eftersom åtkomsten till kund Managed Key har återkallats.| Ja, kontrol lera om: <ol><li>Key Vault har fortfarande MSI i åtkomst principen.</li><li>Åtkomst principen ger behörighet att hämta, figursätta och packa upp.</li><li>Om nyckel valvet finns i ett vNet bakom brand väggen kontrollerar du om **Tillåt Microsoft-betrodda tjänster** är aktiverat.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Det gick inte att hämta nyckeln eftersom det inte gick att hitta det associerade nyckel valvet för kundens hanterade nyckel. | Om du har tagit bort nyckel valvet kan du inte återställa den Kundhanterade nyckeln.  Om du har migrerat nyckel valvet till en annan klient organisation läser du [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../key-vault/general/move-subscription.md). Om du har tagit bort nyckel valvet:<ol><li>Ja, om det är i rensnings skydds tiden, med hjälp av stegen i [återställa ett nyckel valv](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nej, om det ligger utanför rensnings skyddets varaktighet.</li></ol><br>Om nyckel valvet har genomgått en klient migrering, ja, kan den återställas med hjälp av något av stegen nedan: <ol><li>Återställ Key Vault tillbaka till den gamla klienten.</li><li>Ange `Identity = None` och ange värdet tillbaka till `Identity = SystemAssigned` . Detta tar bort och återskapar identiteten när den nya identiteten har skapats. Aktivera `Get` , `Wrap` och `Unwrap` behörighet till den nya identiteten i nyckel valvets åtkomst princip.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Det gick inte att hämta nyckeln eftersom kundens hanterade nyckel inte hittades.| Ja, kontrol lera om: <ol><li>Key Vault har fortfarande MSI i åtkomst principen.</li><li>Identiteten är av typen System tilldelad.</li><li>Aktivera get-, wrap-och unwrap-behörigheter till identiteten i nyckel valvets åtkomst princip.</li></ol>|
| Allmänt fel  | Det gick inte att hämta nyckeln.| Detta är ett allmänt fel. Kontakta Microsoft Support för att felsöka felet och fastställa nästa steg.|


## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault](../key-vault/general/overview.md)?
