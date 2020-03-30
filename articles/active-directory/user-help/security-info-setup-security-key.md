---
title: Konfigurera en säkerhetsnyckel som din verifieringsmetod - Azure AD
description: Så här konfigurerar du sidan Säkerhetsinformation (förhandsversion) för att verifiera din identitet så att en FIDO2-säkerhetsnyckel (Fast Identity Online) (FAST Identity Online) används som verifieringsmetod.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062344"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Konfigurera en säkerhetsnyckel som verifieringsmetod

Du kan använda säkerhetsnycklar som en lösenordslös inloggningsmetod i organisationen. En säkerhetsnyckel är en fysisk enhet som används med en unik PIN-kod för att logga in på ditt arbets- eller skolkonto. Eftersom säkerhetsnycklar kräver att du har den fysiska enheten och något som bara du vet, anses det vara en starkare verifieringsmetod än ett användarnamn och lösenord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Om du inte ser alternativet med säkerhetsnyckel är det möjligt att din organisation inte tillåter att du använder det här alternativet för verifiering. I det här fallet måste du välja en annan metod eller kontakta organisationens supportavdelning för mer hjälp.

## <a name="security-verification-versus-password-reset-authentication"></a>Säkerhetsverifiering kontra autentisering om återställning av lösenord

Metoder för säkerhetsinformation används för både tvåfaktorssäkerhetsverifiering och för återställning av lösenord. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhetsnyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="what-is-a-security-key"></a>Vad är en säkerhetsnyckel?

Vi stöder för närvarande flera designer och leverantörer av säkerhetsnycklar med hjälp av [FIDO(Fast Identity Online)](https://fidoalliance.org/fido2/) (FIDO2) lösenordslösa autentiseringsprotokoll. Med de här nycklarna kan du logga in på ditt arbets- eller skolkonto för att komma åt organisationens molnbaserade resurser när du använder en enhet och webbläsare som stöds.

Administratören eller organisationen ger dig en säkerhetsnyckel om de behöver den för ditt arbets- eller skolkonto. Det finns olika typer av säkerhetsnycklar som du kan använda, till exempel en USB-nyckel som du ansluter till enheten eller en NFC-nyckel som du trycker på en NFC-läsare. Du kan läsa mer om din säkerhetsnyckel, inklusive vilken typ den är, i tillverkarens dokumentation.

> [!Note]
> Om du inte kan använda en FIDO2-säkerhetsnyckel finns det andra lösenordslösa verifieringsmetoder som du kan använda, till exempel Microsoft Authenticator-appen eller Windows Hello. Mer information om Microsoft Authenticator-appen finns i [Vad är Microsoft Authenticator-appen?](user-help-auth-app-overview.md) Mer information om Windows Hello finns i [Översikt över Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan registrera din säkerhetsnyckel måste följande vara sant:

- Administratören har aktiverat den här funktionen för användning inom organisationen.

- Du använder en enhet som kör Windows 10 maj 2019-uppdateringen och använder en webbläsare som stöds.

- Du har godkänt en fysisk säkerhetsnyckel av administratören eller organisationen. Säkerhetsnyckeln måste vara både FIDO2- och Microsoft-kompatibel. Om du har några frågor om din säkerhetsnyckel och om den är kompatibel kontaktar du organisationens supportavdelning.

## <a name="register-your-security-key"></a>Registrera din säkerhetsnyckel

Du måste skapa din säkerhetsnyckel och ge den en unik PIN-kod innan du kan logga in på ditt arbets- eller skolkonto med hjälp av nyckeln. Du kan ha upp till 10 nycklar registrerade hos ditt konto. 

1. Gå till sidan Min https://myprofile.microsoft.com **profil** och logga in om du inte redan har gjort det.

2. Välj **Säkerhetsinformation**, välj **Lägg till metod**och välj sedan **Säkerhetsnyckel** i listrutan **Lägg till en metod.**

    ![Rutan Lägg till metod med säkerhetsnyckeln markerad](media/security-info/security-info-security-key-add-method.png)

3. Välj **Lägg till**och välj sedan vilken typ av säkerhetsnyckel du har, antingen **USB-enhet** eller **NFC-enhet**.

    ![Välj om du har en USB- eller NFC-typ av säkerhetsnyckel](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Om du är osäker på vilken typ av säkerhetsnyckel du har läser du tillverkarens dokumentation. Om du är osäker på tillverkaren kontaktar du organisationens supportavdelning för att få hjälp.

4. Ha din säkerhetsnyckel fysiskt tillgänglig och välj sedan **Nästa**i rutan **Säkerhetsnyckel** .

    ![Registreringsruta för säkerhetsnyckelstart](media/security-info/security-info-security-key-start-setup.png)

    En ny ruta visas som hjälper dig att konfigurera din nya inloggningsmetod.

5. I rutan **Konfigurera din nya inloggningsmetod** väljer du **Nästa**och sedan:

    - Om säkerhetsnyckeln är en USB-enhet sätter du in säkerhetsnyckeln i enhetens USB-port.

    - Om säkerhetsnyckeln är en NFC-enhet trycker du på säkerhetsnyckeln till läsaren.

6. Skriv in din unika säkerhetsnyckel-PIN-kod i **säkerhetsrutan i Windows** och välj sedan **OK**.

    Du återgår till rutan **Konfigurera din nya inloggningsmetod.**

7. Välj **Nästa**.

8. Gå tillbaka till sidan **Säkerhetsinformation,** skriv ett namn som du känner igen senare för den nya säkerhetsnyckeln och välj sedan **Nästa**.

    ![Sidan Säkerhetsinformation, namnge säkerhetsnyckeln](media/security-info/security-info-security-key-name.png)

    Säkerhetsnyckeln är registrerad och klar för dig att använda för att logga in på ditt arbets- eller skolkonto.

9. Välj **Klar** om du vill stänga rutan **Säkerhetsnyckel.**

    Sidan **Säkerhetsinformation** uppdateras med din säkerhetsnyckelinformation.

    ![Sidan Säkerhetsinformation, med alla registrerade metoder som visas](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Ta bort en säkerhetsnyckel från din säkerhetsinformation

Om du tappar bort eller inte längre vill använda en säkerhetsnyckel kan du ta bort nyckeln från säkerhetsinformationen. Även om detta hindrar säkerhetsnyckeln från att användas med ditt arbets- eller skolkonto fortsätter säkerhetsnyckeln att lagra data och autentiseringsuppgifter. Om du vill ta bort data- och autentiseringsuppgifter från själva säkerhetsnyckeln måste du följa instruktionerna i avsnittet [Återställ en Microsoft-kompatibel säkerhetsnyckel](#reset-your-security-key) i den här artikeln.

1. Välj länken **Ta bort** från säkerhetsnyckeln som du vill ta bort.

2. Välj **Ok** i rutan **Ta bort säkerhetsnyckel.**

    Säkerhetsnyckeln tas bort och du kan inte längre använda den för att logga in på ditt arbets- eller skolkonto.

>[!Important]
>Om du tar bort en säkerhetsnyckel av misstag kan du registrera den igen med hjälp av instruktionerna i avsnittet [Så här registrerar du säkerhetsnyckeln](#register-your-security-key) i den här artikeln.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Hantera inställningarna för säkerhetsnyckeln från Windows-inställningar

Du kan hantera inställningarna för säkerhetsnyckeln från appen **Windows-inställningar,** till exempel återställa säkerhetsnyckeln och skapa en ny PIN-kod för säkerhetsnyckel.

### <a name="reset-your-security-key"></a>Återställ säkerhetsnyckeln

Om du vill ta bort all kontoinformation som lagras på din fysiska säkerhetsnyckel måste du återställa nyckeln till fabriksinställningarna. Om du återställer säkerhetsnyckeln tas allt från nyckeln bort, så att du kan börja om från början.

>[!IMPORTANT]
>Om du återställer säkerhetsnyckeln tas allt från nyckeln bort och den återställs till fabriksinställningarna.
>
> **Alla data och autentiseringsuppgifter rensas.**

#### <a name="to-reset-your-security-key"></a>Så här återställer du säkerhetsnyckeln

1. Öppna appen Windows-inställningar, välj **Konton,** välj **Inloggningsalternativ,** välj **Säkerhetsnyckel**och välj sedan **Hantera**.

2. Sätt i säkerhetsnyckeln i USB-porten eller tryck på NFC-läsaren för att verifiera din identitet.

3. Följ anvisningarna på skärmen, baserat på din specifika säkerhetsnyckeltillverkare. Om nyckeltillverkaren inte finns med i anvisningarna på skärmen läser du tillverkarens webbplats för mer information.

4. Välj **Stäng** om du vill stänga skärmen **Hantera.**

### <a name="create-a-new-security-key-pin"></a>Skapa en ny PIN-kod för säkerhetsnyckel

Du kan skapa en ny PIN-kod för säkerhetsnyckeln för säkerhetsnyckeln.

#### <a name="to-create-a-new-security-key-pin"></a>Så här skapar du en ny PIN-kod för säkerhetsnyckel

1. Öppna appen Windows-inställningar, välj **Konton,** välj **Inloggningsalternativ,** välj **Säkerhetsnyckel**och välj sedan **Hantera**.

2. Sätt i säkerhetsnyckeln i USB-porten eller tryck på NFC-läsaren för att verifiera din identitet.
3. Välj **Lägg till** i **PIN-området för säkerhetsnyckel,** skriv och bekräfta den nya PIN-koden för säkerhetsnyckeln och välj sedan **OK**.

     Säkerhetsnyckeln uppdateras med den nya pinkoden för säkerhetsnyckeln som ska användas med ditt arbets- eller skolkonto. Om du bestämmer dig för att ändra PIN-koden igen kan du välja **knappen Ändra.**
4. Välj **Stäng** om du vill stänga skärmen **Hantera.**

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhetsinformation

För att registrera en säkerhetsnyckel måste du ha minst en ytterligare säkerhetsverifieringsmetod registrerad. Mer information finns i [avsnittet Översikt.](security-info-add-update-methods-overview.md) 

## <a name="next-steps"></a>Nästa steg

- Mer information om lösenordslösa verifieringsmetoder läser [Microsofts Azure AD offentlig förhandsversion av FIDO2-säkerhetsnycklar, aktiverar bloggen lösenordslösa inloggningar](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) eller läser [översiktsartiklarna Vad är Microsoft Authenticator?](user-help-auth-app-overview.md) [Windows Hello overview](https://www.microsoft.com/windows/windows-hello)

- Mer information om [Microsoft-kompatibla säkerhetsnycklar](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)finns.

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
