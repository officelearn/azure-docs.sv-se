---
title: Kombinerat registreringen för Azure AD SSPR och Multi-Factor Authentication (förhandsversion) – Azure Active Directory
description: Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning återställa registrering (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f3eec1f846f1b74ab3e19bca022d4e009540d1a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280017"
---
# <a name="combined-security-information-registration-preview"></a>Kombinerade security information registrering (förhandsversion)

Innan du kombinerade registrerings registrerad användare autentiseringsmetoder för Azure Multi-Factor Authentication och lösenordsåterställning via självbetjäning (SSPR) separat. Personer har förväxlas att liknande metoder har använts för Multifaktorautentisering och SSPR, men de tvungna att registrera dig för båda funktionerna. Nu med kombinerade registration kan användare registrera en gång och få fördelarna med både Multifaktorautentisering och SSPR.

![Min profil som visar registrerade säkerhetsinformation för en användare](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Granska den här administratören designmiljöer dokumentationen och fokus på användaren dokumentationen så att du förstår funktioner och effekten av den här funktionen innan du aktiverar den nya upplevelsen. Basera din utbildning om användardokumentation för att förbereda dina användare för den nya upplevelsen och bidra till att säkerställa en lyckad distribution.

|     |
| --- |
| Kombinerade security information registreringen för Multifaktorautentisering och återställning av lösenord för Azure Active Directory (Azure AD) är en funktion i offentliga förhandsversionen av Azure AD. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Användare som är aktiverade för både den ursprungliga förhandsversionen och förbättrad kombinerade registrerings-upplevelsen ser det nya beteendet. Användare som har aktiverats för båda upplevelser visas endast den nya upplevelsen min profil. Den nya Mina profilen överensstämmer med utseendet och känslan kombinerade registrerings och ger en sömlös upplevelse för användare. Användare kan se min profil genom att gå till [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Min profilsidorna är lokaliserade baserat på språkinställningar på den dator som kommer åt sidan. Microsoft lagrar det senaste språk som används i webbläsarens cacheminne, så att efterföljande försök att komma åt sidorna kommer fortsätta att visas i det senaste språk som används. Om du har rensat, renderas igen sidorna. Om du vill tvinga ett visst språk kan du lägga till `?lng=<language>` i slutet av URL: en, där `<language>` är koden för det språk du vill rendera.

![Konfigurera SSPR eller andra säkerhetsmetoder för verifiering](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metoder som finns tillgängliga i kombinerade registreringen

Kombineras registrering har stöd för följande autentiseringsmetoder och åtgärder:

|   | Registrera dig | Ändra | Ta bort |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (högst 5) | Nej | Ja |
| Andra authenticator-appen | Ja (högst 5) | Nej | Ja |
| Maskinvarutoken | Nej | Nej | Ja |
| Telefon | Ja | Ja | Ja |
| Alternativ telefon | Ja | Ja | Ja |
| Arbetstelefon | Nej | Nej | Nej |
| E-post | Ja | Ja | Ja |
| Säkerhetsfrågor | Ja | Nej | Ja |
| Applösenord | Ja | Nej | Ja |

> [!NOTE]
> Applösenord är endast tillgängliga för användare som har utförts för Multifaktorautentisering. Applösenord är inte tillgängliga för användare som har aktiverats för multi-Factor Authentication via en princip för villkorlig åtkomst.

Användare kan ange något av följande alternativ som standardmetoden för autentisering med flera faktorer:

- Microsoft Authenticator – meddelande.
- Authenticator-appen eller maskinvara token – code.
- Telefonsamtal.
- Textmeddelande.

När vi fortsätter att lägga till flera autentiseringsmetoder i Azure AD, blir metoderna tillgänglig i kombinerade registreringen.

## <a name="combined-registration-modes"></a>Kombinerade registrerings-lägen

Det finns två lägen kombinerade registrerings: Avbryt och hantera.

- **Avbryter läge** är en guiden-liknande upplevelse som visas för användare när de registrera eller uppdatera deras säkerhetsinformation vid inloggning.

- **Hantera läge** är en del av användarens profil och tillåter användare att hantera deras säkerhetsinformation.

För båda lägena behöver användare som tidigare har registrerat en metod som kan användas för multi-Factor Authentication att utföra Multifaktorautentisering innan de kan komma åt deras säkerhetsinformation.

### <a name="interrupt-mode"></a>Avbryt läge

Kombinerade registrerings värnar om både Multifaktorautentisering och SSPR-principer om båda är aktiverade för din klient. Dessa principer styr om en användare avbryts för registrering under inloggning och vilka metoder är tillgängliga för registrering.

Här följer flera scenarier där användarna uppmanas att registrera eller uppdatera deras säkerhetsinformation:

* Registrering för Multifaktorautentisering aktiveras via Identity Protection: Användarna uppmanas att registrera under inloggning. De registrerar Multifaktorautentisering metoder och SSPR-metoder (om användaren har aktiverats för SSPR).
* Registrering för Multifaktorautentisering aktiveras via per användare för Multifaktorautentisering: Användarna uppmanas att registrera under inloggning. De registrerar Multifaktorautentisering metoder och SSPR-metoder (om användaren har aktiverats för SSPR).
* Registrering för Multifaktorautentisering aktiveras via villkorlig åtkomst eller andra principer: Användarna uppmanas att registrera när de använder en resurs som kräver Multifaktorautentisering. De registrerar Multifaktorautentisering metoder och SSPR-metoder (om användaren har aktiverats för SSPR).
* SSPR-registrering tillämpas: Användarna uppmanas att registrera under inloggning. De registrerar sig för SSPR-metoder.
* SSPR-uppdatering tillämpas: Användarna uppmanas att granska deras säkerhetsinformation vid ett intervall som angetts av administratören. Användare kan visas deras information och kontrollera den aktuella informationen eller gör ändringar om det behövs.

När registreringen tillämpas visas användare det minsta antalet metoder som krävs för att vara kompatibel med både Multifaktorautentisering och SSPR-principer, från mest till minst säkert.

Exempel:

* En användare har aktiverats för SSPR. SSPR-princip krävs två metoder för att återställa och har aktiverat mobilappkoden, e-post och telefon.
   * Den här användaren krävs för att registrera på två sätt.
      * Användaren visas authenticator-appen och phone som standard.
      * Användaren kan välja att registrera e-post i stället för authenticator-appen eller telefon.

Det här flödesschemat beskrivs vilka metoder visas för en användare avbruten registrerar vid inloggning:

![Flödesschema för kombinerade säkerhet info](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Om du har både Multifaktorautentisering och SSPR aktiverat, rekommenderar vi att du framtvinga registrering för Multifaktorautentisering.

Om SSPR-principen kräver att användare att granska deras säkerhetsinformation med jämna mellanrum, avbryts under inloggning användare och visas alla sina registrerade metoder. De kan kontrollera den aktuella informationen om den är uppdaterad, eller de kan göra ändringar om det behövs.

### <a name="manage-mode"></a>Hantera läge

Användare kan komma åt hantera läge genom att gå till [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) eller genom att välja **säkerhetsinformation** från min profil. Därifrån kan användare kan lägga till metoder, ta bort eller ändra befintliga metoder, ändra standardmetoden och mycket mer.

## <a name="key-usage-scenarios"></a>Scenarier för nyckelanvändning

### <a name="set-up-security-info-during-sign-in"></a>Konfigurera säkerhetsinformation under inloggning

En administratör har tvingande registrering.

En användare inte har ställt in alla nödvändiga säkerhetsinformation och går till Azure-portalen. När du har angett det användarnamn och lösenord, uppmanas användaren att ställa in säkerhetsinformation. Användaren följer sedan anvisningarna som visas i guiden för att ställa in säkerhetsinformationen som krävs. Om dina inställningar tillåter det, kan användaren välja att konfigurera andra metoder än de som visas som standard. När guiden har slutförts, granska användare de metoder som de har konfigurerat och deras standardmetoden för Multifaktorautentisering. För att slutföra installationen måste användaren bekräftar informationen och fortsätter att Azure-portalen.

### <a name="set-up-security-info-from-my-profile"></a>Konfigurera säkerhetsinformation från min profil

En administratör har inte tvingande registrering.

En användare som ännu inte har ställts in all nödvändig säkerhetsinformation går till [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). Användaren väljer **säkerhetsinformation** i den vänstra rutan. Därifrån kan användaren väljer att lägga till en metod, väljer någon av metoderna som är tillgängliga och följer stegen för att ställa in den metoden. När du är klar ser användaren den metod som precis har ställts in på sidan säkerhetsinformation.

### <a name="delete-security-info-from-my-profile"></a>Ta bort säkerhetsinformation från min profil

En användare som redan har konfigurerat minst en metod som navigerar till [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Användaren väljer att ta bort ett av de tidigare registrerade metoderna. När du är klar ser användaren inte längre den metoden på sidan säkerhetsinformation.

### <a name="change-the-default-method-from-my-profile"></a>Ändra standardmetoden från min profil

En användare som redan har konfigurerat minst en metod som kan användas för multi-Factor Authentication navigerar till [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Användaren ändrar aktuella standardmetoden till en annan standard-metod. När du är klar ser användaren den nya standardmetoden på sidan säkerhetsinformation.

## <a name="next-steps"></a>Nästa steg

[Aktivera kombinerade registrering i din klient](howto-registration-mfa-sspr-combined.md)

[Tillgängliga metoder för Multifaktorautentisering och SSPR](concept-authentication-methods.md)

[Konfigurera lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
