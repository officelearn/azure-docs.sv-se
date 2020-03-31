---
title: Anpassa återställning av lösenord för självbetjäning – Azure Active Directory
description: Anpassningsalternativ för azure ad-återställning av lösenord för självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979462"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Anpassa Azure AD-funktionen för återställning av lösenord med självbetjäning

IT-tekniker som vill distribuera självbetjäningslösenordsåterställning (SSPR) i Azure Active directory (Azure AD) kan anpassa upplevelsen så att den matchar användarnas behov.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassa länken "Kontakta administratören"

Användare av återställning av lösenord med självbetjäning har en "Kontakta administratörslänken" som är tillgänglig för dem i portalen för återställning av lösenord. Om en användare väljer den här länken gör den en av två saker:

* Om den lämnas i standardtillståndet:
   * E-post skickas till administratörerna och ber dem att ge hjälp med att ändra användarens lösenord. Se [exempelmeddelandet](#sample-email) nedan.
* Om anpassad:
   * Skickar användaren till en webbsida eller e-postadress som administratören har angett för att få hjälp.

> [!TIP]
> Om du anpassar detta rekommenderar vi att du ställer in detta på något som användarna redan känner till för support

> [!WARNING]
> Om du anpassar den här inställningen med en e-postadress och ett konto som behöver en återställning av lösenord kanske användaren inte kan be om hjälp.

### <a name="sample-email"></a>Exempel på e-post

![Exempel på begäran om att återställa e-post som skickas till administratör][Contact]

Kontaktmeddelandet skickas till följande mottagare i följande ordning:

1. Om **administratörsrollen för helpdesk-administratör** eller **lösenordsadministratörsrollen** tilldelas meddelas administratörer med dessa roller.
1. Om inga helpdeskadministratörer eller lösenordsadministratörer **user administrator** har tilldelats meddelas administratörer med användaradministratörsrollen.
1. Om ingen av de tidigare rollerna har tilldelats meddelas de **globala administratörerna.**

I samtliga fall meddelas högst 100 mottagare.

Mer information om de olika administratörsrollerna och hur du tilldelar dem finns [i Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Inaktivera e-postmeddelanden om "Kontakta administratören"

Om din organisation inte vill meddela administratörer om begäranden om återställning av lösenord kan du aktivera följande konfiguration:

* Aktivera återställning av lösenord för självbetjäning för alla slutanvändare. Det här alternativet finns under Egenskaper **för Återställning av** > **lösenord**. Om du inte vill att användarna ska återställa sina egna lösenord kan du begränsa åtkomsten till en tom grupp. *Vi rekommenderar inte det här alternativet.*
* Anpassa hjälpdekens länk för att ange en webbadress eller e-postadress till: adress som användarna kan använda för att få hjälp. Det här alternativet är under Anpassad**e-post eller url för**anpassning av **lösenordsåterställning.** > **Customization** > 

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Anpassa AD FS-inloggningssidan för SSPR

AD FS-administratörer (Active Directory Federation Services) kan lägga till en länk på sin inloggningssida med hjälp av vägledningen i [beskrivningen av lägg till inloggningssida.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)

Om du vill lägga till en länk till AD FS-inloggningssidan använder du följande kommando på AD FS-servern. Användare kan använda den här sidan för att ange SSPR-arbetsflödet.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Anpassa inloggningssidan och åtkomstpanelens utseende och känsla

Du kan anpassa inloggningssidan. Du kan lägga till en logotyp som visas tillsammans med bilden som passar företagets varumärke.

Grafiken du väljer visas under följande omständigheter:

* När en användare har angett sitt användarnamn
* Om användaren kommer åt den anpassade webbadressen:
   * Genom att `whr` skicka parametern till sidan för återställning av lösenord, till exempel`https://login.microsoftonline.com/?whr=contoso.com`
   * Genom att `username` skicka parametern till sidan för återställning av lösenord, till exempel`https://login.microsoftonline.com/?username=admin@contoso.com`

Hitta information om hur du konfigurerar företagets varumärke i artikeln [Lägg till företagsprofilering på din inloggningssida i Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Katalognamn

Du kan ändra katalognamnsattributet under **Azure Active Directory** > **Properties**. Du kan visa ett eget organisationsnamn som visas i portalen och i den automatiska kommunikationen. Det här alternativet är det mest synliga i automatiska e-postmeddelanden i följande formulär:

* Det egna namnet i e-postmeddelandet, till exempel "Microsoft på uppdrag av CONTOSO demo"
* Ämnesraden i e-postmeddelandet, till exempel "CONTOSO demo konto e-verifieringskod"

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställa eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är trasigt. Hur felsöker jag SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Kontakta administratören för hjälp med att återställa e-postexemplet för lösenord"
