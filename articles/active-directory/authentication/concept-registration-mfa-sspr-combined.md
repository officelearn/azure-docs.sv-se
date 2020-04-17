---
title: Kombinerad registrering för SSPR och MFA – Azure Active Directory
description: Registrering av azure AD multifaktorautentisering och registrering av återställning av lösenord med självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48350bf8f0ffb8681d95f6f42f9aa93256395f9a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534694"
---
# <a name="combined-security-information-registration-overview"></a>Översikt över kombinerad registrering av säkerhetsinformation

Före kombinerad registrering registrerade användare autentiseringsmetoder för Azure Multi-Factor Authentication och self-service password reset (SSPR) separat. Människor var förvirrade över att liknande metoder användes för Multi-Factor Authentication och SSPR men de var tvungna att registrera sig för båda funktionerna. Nu, med kombinerad registrering, användare kan registrera sig en gång och få fördelarna med både Multi-Factor Authentication och SSPR.

I den här artikeln beskrivs vilken kombinerad säkerhetsregistrering som finns. Information om hur du kommer igång med kombinerad säkerhetsregistrering finns i följande artikel:

> [!div class="nextstepaction"]
> [Aktivera kombinerad säkerhetsregistrering](howto-registration-mfa-sspr-combined.md)

![Min profil som visar registrerad säkerhetsinformation för en användare](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Innan du aktiverar den nya upplevelsen bör du läsa den här administratörsfokuserade dokumentationen och den användarfokuserade dokumentationen så att du förstår funktionens funktionalitet och effekt. Basera din utbildning på [användardokumentationen](../user-help/user-help-security-info-overview.md) för att förbereda användarna för den nya upplevelsen och bidra till att säkerställa en lyckad distribution.

Azure AD kombinerad registrering av säkerhetsinformation är för närvarande inte tillgänglig för nationella moln som Azure US Government, Azure Germany eller Azure China 21Vianet.

> [!IMPORTANT]
> Användare som är aktiverade för både den ursprungliga förhandsversionen och den förbättrade kombinerade registreringsupplevelsen ser det nya beteendet. Användare som är aktiverade för båda upplevelserna ser bara den nya upplevelsen Min profil. Den nya Min profil överensstämmer med utseendet och känslan av kombinerad registrering och ger en sömlös upplevelse för användarna. Användare kan se Min [https://myprofile.microsoft.com](https://myprofile.microsoft.com)profil genom att gå till .
>
> Du kan stöta på ett felmeddelande när du försöker komma åt alternativet Säkerhetsinformation. Till exempel "Tyvärr, vi kan inte logga in dig". I det här fallet bekräftar du att du inte har någon konfigurations- eller grupprincipobjekt som blockerar cookies från tredje part i webbläsaren.

Mina profilsidor är lokaliserade baserat på språkinställningarna för den dator som använder sidan. Microsoft lagrar det senaste språket som används i webbläsarens cacheminne, så efterföljande försök att komma åt sidorna fortsätter att återges på det sista språk som användes. Om du rensar cacheminnet återges sidorna igen. Om du vill tvinga fram ett `?lng=<language>` visst språk kan du `<language>` lägga till i slutet av webbadressen, var är koden för det språk du vill återge.

![Ställ in SSPR eller andra metoder för säkerhetsverifiering](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metoder som finns i kombinerad registrering

Kombinerad registrering stöder följande autentiseringsmetoder och åtgärder:

|   | Registrera dig | Ändra | Ta bort |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (högst 5) | Inga | Ja |
| Annan autentiseringsapp | Ja (högst 5) | Inga | Ja |
| Maskinvarutoken | Inga | Inga | Ja |
| Telefon | Ja | Ja | Ja |
| Alternativ telefon | Ja | Ja | Ja |
| Arbetstelefon | Inga | Inga | Inga |
| E-post | Ja | Ja | Ja |
| Säkerhetsfrågor | Ja | Inga | Ja |
| Applösenord | Ja | Inga | Ja |
| FIDO2-säkerhetsnycklar<br />*Hanterat läge endast från sidan [Säkerhetsinformation](https://mysignins.microsoft.com/security-info)*| Ja | Ja | Ja |

> [!NOTE]
> Applösenord är endast tillgängliga för användare som har tillämpats för multifaktorautentisering. Applösenord är inte tillgängliga för användare som är aktiverade för multifaktorautentisering via en princip för villkorlig åtkomst.

Användare kan ange ett av följande alternativ som standardmetod för multifaktorautentisering:

- Microsoft Authenticator – meddelande.
- Autentiseringsapp eller maskinvarutoken – kod.
- Telefonsamtal.
- Textmeddelande.

När vi fortsätter att lägga till fler autentiseringsmetoder i Azure AD är dessa metoder tillgängliga i kombinerad registrering.

## <a name="combined-registration-modes"></a>Kombinerade registreringslägen

Det finns två former av kombinerad registrering: avbryta och hantera.

- **Avbrottsläge** är en guideliknande upplevelse som presenteras för användare när de registrerar eller uppdaterar sin säkerhetsinformation vid inloggning.
- **Hanteringsläge** är en del av användarprofilen och tillåter användare att hantera sin säkerhetsinformation.

För båda lägena måste användare som tidigare har registrerat en metod som kan användas för multifaktorautentisering utföra multifaktorautentisering innan de kan komma åt sin säkerhetsinformation.

### <a name="interrupt-mode"></a>Avbrottsläge

Kombinerad registrering respekterar både Multifaktorautentisering och SSPR-principer, om båda är aktiverade för din klient. Dessa principer styr om en användare avbryts för registrering under inloggningen och vilka metoder som är tillgängliga för registrering.

Här är flera scenarier där användare kan uppmanas att registrera eller uppdatera sin säkerhetsinformation:

- Registrering av multifaktorautentisering som tillämpas via Identitetsskydd: Användare uppmanas att registrera sig under inloggningen. De registrerar multifaktorautentiseringsmetoder och SSPR-metoder (om användaren är aktiverad för SSPR).
- Registrering av multifaktorautentisering som tillämpas via multifaktorautentisering per användare: Användare uppmanas att registrera sig under inloggningen. De registrerar multifaktorautentiseringsmetoder och SSPR-metoder (om användaren är aktiverad för SSPR).
- Registrering av multifaktorautentisering som tillämpas via villkorlig åtkomst eller andra principer: Användare uppmanas att registrera sig när de använder en resurs som kräver multifaktorautentisering. De registrerar multifaktorautentiseringsmetoder och SSPR-metoder (om användaren är aktiverad för SSPR).
- SSPR-registreringen tillämpas: Användare uppmanas att registrera sig under inloggningen. De registrerar endast SSPR-metoder.
- SSPR refreshforced: Användare måste granska sin säkerhetsinformation med ett intervall som anges av administratören. Användarna visas sin information och kan bekräfta aktuell information eller göra ändringar om det behövs.

När registreringen tillämpas visas det minsta antalet metoder som krävs för att vara kompatibla med både MultiFaktorautentisering och SSPR-principer, från de flesta till minst säkra.

Ett exempel:

- En användare är aktiverad för SSPR. SSPR-principen krävde två metoder för att återställa och har aktiverat mobilappkod, e-post och telefon.
   - Den här användaren måste registrera två metoder.
      - Användaren visas som standard autentiseringsapp och telefon.
      - Användaren kan välja att registrera e-post i stället för autentiseringsapp eller telefon.

Detta flödesschema beskriver vilka metoder som visas för en användare när den avbryts för att registrera sig under inloggningen:

![Flödesschema för samlad säkerhetsinformation](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Om du har aktiverat både multifaktorautentisering och SSPR rekommenderar vi att du tillämpar registrering av multifaktorautentisering.

Om SSPR-principen kräver att användarna granskar sin säkerhetsinformation med jämna mellanrum, avbryts användarna under inloggningen och visar alla sina registrerade metoder. De kan bekräfta aktuell information om den är uppdaterad eller så kan de göra ändringar om de behöver det. Användare måste utföra multifaktorautentisering när de öppnar den här sidan.

### <a name="manage-mode"></a>Hantera läge

Användare kan komma åt [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) hanteringsläge genom att gå till eller genom att välja **Säkerhetsinformation** från Min profil. Därifrån kan användare lägga till metoder, ta bort eller ändra befintliga metoder, ändra standardmetoden med mera.

## <a name="key-usage-scenarios"></a>Scenarier för nyckelanvändning

### <a name="set-up-security-info-during-sign-in"></a>Konfigurera säkerhetsinformation under inloggning

En administratör har verkställt registreringen.

En användare har inte konfigurerat all nödvändig säkerhetsinformation och går till Azure-portalen. När du har angett användarnamn och lösenord uppmanas användaren att ställa in säkerhetsinformation. Användaren följer sedan stegen som visas i guiden för att ställa in den säkerhetsinformation som krävs. Om inställningarna tillåter det kan användaren välja att ställa in andra metoder än de som visas som standard. När du har slutfört guiden granskar användarna de metoder som de har ställt in och deras standardmetod för multifaktorautentisering. För att slutföra installationsprocessen bekräftar användaren informationen och fortsätter till Azure-portalen.

### <a name="set-up-security-info-from-my-profile"></a>Konfigurera säkerhetsinformation från Min profil

En administratör har inte verkställt registreringen.

En användare som ännu inte har ställt in [https://myprofile.microsoft.com](https://myprofile.microsoft.com)all nödvändig säkerhetsinformation går till . Användaren väljer **Säkerhetsinformation** i den vänstra rutan. Därifrån väljer användaren att lägga till en metod, väljer någon av de tillgängliga metoderna och följer stegen för att ställa in den metoden. När du är klar ser användaren den metod som just har ställts in på sidan Säkerhetsinformation.

### <a name="delete-security-info-from-my-profile"></a>Ta bort säkerhetsinformation från Min profil

En användare som tidigare har ställt in [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)minst en metod navigerar till . Användaren väljer att ta bort en av de tidigare registrerade metoderna. När du är klar ser användaren inte längre den metoden på sidan Säkerhetsinformation.

### <a name="change-the-default-method-from-my-profile"></a>Ändra standardmetoden från Min profil

En användare som tidigare har ställt in minst en metod som kan [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)användas för multifaktorautentisering navigerar till . Användaren ändrar den aktuella standardmetoden till en annan standardmetod. När du är klar ser användaren den nya standardmetoden på sidan Säkerhetsinformation.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i självstudierna för att [aktivera återställning av lösenord för självbetjäning](tutorial-enable-sspr.md) och aktivera Azure [Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Lär dig hur du [aktiverar kombinerad registrering i din klientorganisation](howto-registration-mfa-sspr-combined.md) eller [tvingar användare att registrera om autentiseringsmetoder](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Du kan också granska [tillgängliga metoder för Azure Multi-Factor Authentication och SSPR](concept-authentication-methods.md).
