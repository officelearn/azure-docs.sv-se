---
title: Anpassa lösenordsåterställning Azure AD via självbetjäning – Azure Active Directory
description: Återställa anpassade alternativ för lösenord för självbetjäning i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a77c6ce205c40b5814f9b26f9099d868d434d3ce
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316464"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Anpassa Azure AD-funktionerna för lösenordsåterställning via självbetjäning

IT-proffs som vill distribuera lösenordsåterställning via självbetjäning (SSPR) i Azure Active directory (Azure AD) kan anpassa upplevelsen för att matcha deras behov.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassa länken ”Kontakta administratören”

Även om SSPR inte är aktiverad kan användarna fortfarande att ha en ”Kontakta administratören” länk på lösenordet återställa portalen. Om användaren väljer den här länken det antingen:

   * E-post dina administratörer och ber dem om du behöver hjälp med att ändra användarens lösenord.
   * Skickar dina användare till en URL som du anger för att få hjälp.

Vi rekommenderar att du ställer in den här kontakten till något som en e-postadress eller en webbplats som användarna redan använder för supportförfrågningar.

![Kontakta][Contact]

Kontakta e-postmeddelandet skickas till följande mottagare i följande ordning:

1. Om den **lösenordsadministratör** tilldelas rollen, administratörer med den här rollen meddelas.
2. Om inga lösenordsadministratörer tilldelas, sedan administratörer med den **Användaradministratör** rollen meddelas.
3. Om inget av de föregående rollerna har tilldelats kommer **globala administratörer** meddelas.

I samtliga fall högst 100 mottagare får ett meddelande.

Du hittar mer information om de olika administratörsroller och tilldela dem [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Inaktivera ”Kontakta administratören” e-postmeddelanden

Om din organisation inte vill meddela administratörer om lösenord återställa begäranden kan du aktivera följande konfiguration:

* Aktivera Självbetjäning för återställning av lösenord för alla användare. Det här alternativet är **lösenordsåterställning** > **egenskaper**. Om du inte vill att användarna kan återställa sina lösenord, kan du begränsa åtkomst till en tom grupp. *Vi rekommenderar inte det här alternativet.*
* Anpassa helpdesk-länk om du vill ange en Webbadress eller en mailto: adress som användarna kan använda för att få hjälp. Det här alternativet är **lösenordsåterställning** > **anpassning** > **anpassad supportavdelningen e-postadress eller URL: en**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Anpassa AD FS-inloggningssida för SSPR

Active Directory Federation Services (AD FS) administratörer kan lägga till en länk deras inloggningssida med hjälp av informationen finns i den [Lägg till inloggningssidan beskrivning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) artikeln.

Om du vill lägga till en länk till sidan för AD FS, använder du följande kommando på AD FS-servern. Användare kan använda den här sidan för att ange SSPR-arbetsflöde.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Anpassa inloggning sidan och åtkomst till panelen utseendet och känslan

Du kan anpassa på inloggningssidan. Du kan lägga till en logotyp som visas tillsammans med den avbildning som passar din företagsanpassning.

Bilderna som du väljer visas i följande omständigheter:

* När en användare anger sina användarnamn
* Om användaren har åtkomst till den anpassade URL: en:
    * Genom att skicka den `whr` parametern till lösenordet för lösenordsåterställning, som `https://login.microsoftonline.com/?whr=contoso.com`
    * Genom att skicka den `username` parametern till lösenordet för lösenordsåterställning, som `https://login.microsoftonline.com/?username=admin@contoso.com`

Hitta information om hur du konfigurerar företagsanpassning i artikeln [lägga till företagsprofilering för din inloggningssidan i Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Katalognamn

Du kan ändra attributet directory namn under **Azure Active Directory** > **egenskaper**. Du kan visa ett eget organisationsnamn som visas i portalen och automatiserade meddelanden. Det här alternativet är det mest synliga i automatiserade e-postmeddelanden i de formulär som följer:

* Det egna namnet för e-postmeddelandet, till exempel ”Microsoft på uppdrag av CONTOSO demo”
* Ämnesraden för e-postmeddelandet, till exempel ”CONTOSO demo e-kontoverifieringskod”

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Kontakta administratören för hjälp med att återställa ditt lösenord för e-exempel"
