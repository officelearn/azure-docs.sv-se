---
title: Anpassa återställning av lösenord för självbetjäning – Azure Active Directory
description: Lär dig hur du anpassar alternativ för användarvisning och upplevelse för Azure AD-återställning av lösenord för självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394259"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Anpassa användarupplevelsen för azure Active Directory självbetjäningslösenordsåterställning

Självbetjäningslösenordsåterställning (SSPR) ger användare i Azure Active Directory (Azure AD) möjlighet att ändra eller återställa sitt lösenord, utan administratör eller helpdesk-inblandning. Om en användares konto är låst eller om de glömmer sitt lösenord kan de följa uppmaningar om att häva blockeringen och återgå till arbetet. Den här möjligheten minskar antalet supportsamtal och produktivitetsförluster när en användare inte kan logga in på sin enhet eller ett program.

Om du vill förbättra SSPR-upplevelsen för användarna kan du anpassa utseendet och känslan på sidan för återställning av lösenord, e-postmeddelanden eller inloggningssidor. Med de här anpassningsalternativen kan du göra det klart för användaren att de är på rätt plats och ge dem förtroende för att de får åtkomst till företagets resurser.
    
I den här artikeln beskrivs hur du anpassar sspr-e-postlänken för användare, företagsprofilering och AD FS-inloggningssidan.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassa länken "Kontakta administratören"

För att hjälpa användare att nå ut för att få hjälp med återställning av lösenord med självbetjäning visas länken "Kontakta administratören" i portalen för återställning av lösenord. Om en användare väljer den här länken gör den en av två saker:

* Om den här kontaktlänken lämnas i standardtillståndet skickas ett e-postmeddelande till administratörerna och de uppmanas att ge hjälp med att ändra användarens lösenord. Följande exempelmeddelande visar det här standardmeddelandet för e-post:

    ![Exempel på begäran om att återställa e-post som skickas till administratören](./media/howto-sspr-customization/sspr-contact-admin.png)

* Om den anpassas skickar användaren till en webbsida eller e-postadress som angetts av administratören för hjälp.
    * Om du anpassar detta rekommenderar vi att du ställer in detta på något som användarna redan känner till för support.

    > [!WARNING]
    > Om du anpassar den här inställningen med en e-postadress och ett konto som behöver en återställning av lösenord kanske användaren inte kan be om hjälp.

### <a name="default-email-behavior"></a>Standardbeteende för e-post

Standardmeddelandet för kontakt skickas till mottagarna i följande ordning:

1. Om *administratörsrollen för helpdesk-administratör* eller *lösenordsadministratörsrollen* tilldelas meddelas administratörer med dessa roller.
1. Om ingen administratör eller lösenordsadministratör har tilldelats meddelas administratörer med *användaradministratörsrollen.*
1. Om ingen av de tidigare rollerna har tilldelats meddelas de *globala administratörerna.*

I samtliga fall meddelas högst 100 mottagare.

Mer information om de olika administratörsrollerna och hur du tilldelar dem finns [i Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Inaktivera e-postmeddelanden om "Kontakta administratören"

Om din organisation inte vill meddela administratörer om begäranden om återställning av lösenord kan följande konfigurationsalternativ användas:

* Anpassa hjälpdekens länk för att ange en webbadress eller e-postadress till: adress som användarna kan använda för att få hjälp. Det här alternativet är under Anpassad**e-post eller url för**anpassning av **lösenordsåterställning.** > **Customization** > 
* Aktivera återställning av lösenord för självbetjäning för alla användare. Det här alternativet finns under Egenskaper **för Återställning av** > **lösenord**. Om du inte vill att användarna ska återställa sina egna lösenord kan du begränsa åtkomsten till en tom grupp. *Vi rekommenderar inte det här alternativet.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Anpassa inloggningssidan och åtkomstpanelen

Du kan anpassa inloggningssidan, till exempel för att lägga till en logotyp som visas tillsammans med den bild som passar företagets varumärke. Mer information om hur du konfigurerar företagets varumärke finns [i Lägga till företagsprofilering på din inloggningssida i Azure AD](../fundamentals/customize-branding.md).

Grafiken du väljer visas under följande omständigheter:

* När en användare har angett sitt användarnamn
* Om användaren kommer åt den anpassade webbadressen:
   * Genom att `whr` skicka parametern till sidan för återställning av lösenord, till exempel`https://login.microsoftonline.com/?whr=contoso.com`
   * Genom att `username` skicka parametern till sidan för återställning av lösenord, till exempel`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Katalognamn

Om du vill att saker och ting ska se mer användarvänliga ut kan du ändra organisationsnamnet i portalen och i den automatiska kommunikationen. Om du vill ändra katalognamnsattributet i Azure-portalen bläddrar du till **Azure Active Directory** > **Properties**. Det här alternativet med eget organisationsnamn är det mest synliga i automatiska e-postmeddelanden, som i följande exempel:

* Det egna namnet i e-postmeddelandet, till exempel "*Microsoft på uppdrag av CONTOSO demo*"
* Ämnesraden i e-postmeddelandet, till exempel "*CONTOSO demo konto e-verifieringskod*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Anpassa AD FS-inloggningssidan

Om du använder AD FS (Active Directory Federation Services) för användarloggningshändelser kan du lägga till en länk till inloggningssidan med hjälp av vägledningen i artikeln om Lägg till [beskrivning av inloggningssidan](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Ge användarna en länk till sidan så att de kan *https://passwordreset.microsoftonline.com*komma in i SSPR-arbetsflödet, till exempel . Om du vill lägga till en länk till AD FS-inloggningssidan använder du följande kommando på AD FS-servern:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Nästa steg

Information om hur du förstår användningen av SSPR i din miljö finns i [Rapporteringsalternativ för Azure AD-lösenordshantering](howto-sspr-reporting.md).

Om du eller användarna har problem med SSPR läser [du Felsöka återställning av lösenord för självbetjäning](active-directory-passwords-troubleshoot.md)
