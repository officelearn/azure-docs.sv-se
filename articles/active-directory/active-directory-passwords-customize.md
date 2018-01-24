---
title: "Självbetjäning för lösenordsåterställning anpassning - Azure Active Directory"
description: "Återställa anpassade alternativ för lösenordsåterställning för självbetjäning av Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 526286c7f6b62d165af43487ca63fe9055623d0c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Anpassa Azure AD-funktionerna för lösenordsåterställning via självbetjäning

IT-proffs som vill distribuera lösenordsåterställning via självbetjäning (SSPR) i Azure Active directory (Azure AD) kan anpassa upplevelsen för att matcha deras behov.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassa länken ”kontaktar du administratören”

Även om SSPR inte är aktiverad kan ha användare fortfarande en ”Kontakta administratören” länk på lösenordet återställa portalen. Om användaren väljer den här länken den antingen:
   * E-post dina administratörer och ber dem om hjälp med att ändra användarens lösenord. 
   * Skickar användarna till en URL som du anger för att få hjälp. 

Vi rekommenderar att du ställer in kontakten till något som en e-postadress eller en webbplats som användarna redan använder för supportfrågor.

![Kontakta][Contact]

Kontakta e-postmeddelandet skickas till följande mottagare i följande ordning:

1. Om den **lösenordsadministratör** roll har tilldelats, administratörer med den här rollen meddelas.
2. Om inga lösenordsadministratörer tilldelas, sedan administratörer med den **Användaradministratör** roll meddelas.
3. Om ingen av rollerna som tidigare tilldelats i **globala administratörer** meddelas.

I samtliga fall meddelas högst 100 mottagare.

Om du vill veta mer om olika administratörsroller och tilldela dem [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Inaktivera ”kontaktar du administratören” e-post

Om organisationen inte vill att meddela administratörer om lösenord återställa begäranden, kan du aktivera följande konfiguration:

* Aktivera Självbetjäning för återställning av lösenord för alla användare. Det här alternativet är **lösenordsåterställning** > **egenskaper**.
  
  Om du inte vill att användarna ska återställa sina lösenord, kan du omfattningen åtkomst till en tom grupp. *Det här alternativet rekommenderas inte.*
* Anpassa länken supportavdelningen om du vill ange en Webbadress eller mailto: adress som användarna kan använda för att få hjälp. Det här alternativet är **lösenordsåterställning** > **anpassning** > **anpassad supportavdelningen e-postadress eller URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Anpassa AD FS-inloggningssida för SSPR

Active Directory Federation Services (AD FS) administratörer kan lägga till en länk sina inloggningssidan genom att använda riktlinjerna i den [Lägg till inloggningssidan beskrivning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) artikel.

Om du vill lägga till en länk till sidan för AD FS, använder du följande kommando på AD FS-servern. Användare kan använda den här sidan för att ange SSPR-arbetsflöde.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Anpassa inloggning sida och åtkomst panelen Utseende

Du kan anpassa sidan logga in. Du kan lägga till en logotyp som visas tillsammans med den bild som passar din företagsanpassning.

Bilder som du väljer visas i följande fall:

* När en användare anger sitt lösenord
* Om användaren har åtkomst till den anpassade URL:
    * Genom att skicka den *wattimmar* parameter till lösenordet Återställ sida som ”https://login.microsoftonline.com/?whr=contoso.com”
    * Genom att skicka den *användarnamn* parametern till lösenordet som återställa sidan ”https://login.microsoftonline.com/?username=admin@contoso.com”

Hitta information om hur du konfigurerar företagsanpassning i artikeln [lägga till företagsprofilering för din inloggningssidan i Azure AD](customize-branding.md).

### <a name="directory-name"></a>Katalognamn

Du kan ändra directory namnattributet under **Azure Active Directory** > **egenskaper**. Du kan visa ett eget organisationsnamn som visas i portalen och automatiserade meddelanden. Det här alternativet är det mest synliga i automatisk e-postmeddelanden i formulär som följer:

* Eget namn i e-post, till exempel ”Microsoft uppdrag CONTOSO demo”
* Ämnesraden i e-post, till exempel ”CONTOSO demo konto e-Verifieringskod”

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Kontakta administratören för hjälp om du återställer ditt lösenord e-exempel"
