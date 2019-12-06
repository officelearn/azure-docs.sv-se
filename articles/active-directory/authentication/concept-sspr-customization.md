---
title: Anpassa lösen ords återställning via självbetjäning – Azure Active Directory
description: Anpassnings alternativ för lösen ords återställning via självbetjäning i Azure AD
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
ms.openlocfilehash: 039b514fe70da0e300e74bbc98a3a0f4e9ea342c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848603"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Anpassa Azure AD-funktionen för lösen ords återställning via självbetjäning

IT-proffs som vill distribuera självbetjäning för återställning av lösen ord (SSPR) i Azure Active Directory (Azure AD) kan anpassa upplevelsen så att den matchar användarnas behov.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassa länken "kontakta din administratör"

Användare av lösen ords återställning via självbetjäning har en "kontakta administratören"-länken som är tillgänglig för dem i portalen för återställning av lösen ord. Om en användare väljer den här länken görs en av två saker:

* Om det är kvar i standard läget:
   * E-postmeddelandet skickas till dina administratörer och ber dem ge hjälp att ändra användarens lösen ord. Se [exempel meddelandet](#sample-email) nedan.
* Om det är anpassat:
   * Skickar användaren till en webb sida eller e-postadress som angetts av administratören för att få hjälp.

> [!TIP]
> Om du anpassar detta rekommenderar vi att du ställer in det till något som användarna redan känner till för support

> [!WARNING]
> Om du anpassar den här inställningen med en e-postadress och ett konto som behöver återställa lösen ord kanske användaren inte behöver be om hjälp.

### <a name="sample-email"></a>Exempel på e-post

![Exempel förfrågan om att återställa e-post som skickas till administratör][Contact]

Kontaktens e-postadress skickas till följande mottagare i följande ordning:

1. Om rollen som **lösen ords administratör** tilldelas, meddelas administratörer med den här rollen.
2. Om inga lösen ords administratörer tilldelas, meddelas administratörer med rollen **användar administratör** .
3. Om ingen av de tidigare rollerna tilldelas, meddelas de **globala administratörerna** .

I samtliga fall meddelas högst 100 mottagare.

Läs mer om de olika administratörs rollerna och hur du tilldelar dem i [tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Inaktivera "kontakta administratören"-e-post

Om din organisation inte vill meddela administratörer om begär Anden om lösen ords återställning kan du aktivera följande konfiguration:

* Aktivera självbetjäning för återställning av lösen ord för alla slutanvändare. Det här alternativet är > **Egenskaper**för **lösen ords återställning** . Om du inte vill att användarna ska kunna återställa sina egna lösen ord kan du begränsa åtkomsten till en tom grupp. *Vi rekommenderar inte det här alternativet.*
* Anpassa supportavdelningen-länken för att tillhandahålla en webb-URL eller mailto:-adress som användarna kan använda för att få hjälp. Det här alternativet är under **lösen ords återställning** > **anpassning** > **e-post eller URL för anpassad helpdesk**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Anpassa AD FS inloggnings sida för SSPR

Active Directory Federation Services (AD FS) (AD FS) administratörer kan lägga till en länk till sina inloggnings sidor med hjälp av rikt linjerna i artikeln [Lägg till inloggnings sidan Beskrivning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) .

Om du vill lägga till en länk till AD FS inloggnings sidan använder du följande kommando på AD FS-servern. Användare kan använda den här sidan för att ange SSPR-arbetsflödet.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Anpassa inloggnings sidan och åtkomst panelens utseende och känsla

Du kan anpassa inloggnings sidan. Du kan lägga till en logo typ som visas tillsammans med den bild som passar ditt företags varumärke.

De bilder du väljer visas i följande fall:

* När en användare har angett sitt användar namn
* Om användaren har åtkomst till den anpassade URL: en:
   * Genom att överföra `whr`-parametern till sidan för lösen ords återställning, t. ex. `https://login.microsoftonline.com/?whr=contoso.com`
   * Genom att överföra `username`-parametern till sidan för lösen ords återställning, t. ex. `https://login.microsoftonline.com/?username=admin@contoso.com`

Hitta information om hur du konfigurerar företags anpassning i artikeln [Lägg till företags anpassning till din inloggnings sida i Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Katalog namn

Du kan ändra attributet katalog namn under **Azure Active Directory** > **Egenskaper**. Du kan visa ett eget organisations namn som visas i portalen och i den automatiserade kommunikationen. Det här alternativet är det som är synligt i automatiserade e-postmeddelanden i formulären som följer:

* Det egna namnet i e-postmeddelandet, till exempel "Microsoft på uppdrag av CONTOSO demo"
* Ämnes raden i e-postmeddelandet, till exempel "CONTOSO demonstrations konto e-postverifierings kod"

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
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Kontakta administratören om du vill ha hjälp med att återställa lösen ordets e-postexempel"
