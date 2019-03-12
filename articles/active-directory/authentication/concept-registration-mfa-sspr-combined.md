---
title: Kombinerade registrering för Azure AD SSPR och MFA (förhandsversion)
description: Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning återställa registrering (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9872ef76264956430bb69856a197042c196dfd
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550198"
---
# <a name="combined-security-information-registration-preview"></a>Kombinerade security information registrering (förhandsversion)

Innan kombinerade registrerades registrerad användare autentiseringsmetoder för Azure Multi-Factor Authentication (MFA) och lösenordsåterställning via självbetjäning (SSPR) via två olika kanaler. Personer har förväxlas att liknande metoder har använts för både Azure MFA och SSPR, men de tvungna att registrera separat för varje funktion. Nu med kombinerade registration kan användare registrera en gång och få fördelarna med både Azure MFA och SSPR.

![Kombineras säkerhetsinformation - min profil som visar registrerade säkerhetsinformation för en användare, inklusive Microsoft Authenticator och Phone för en exempelanvändare i katalogen.](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Granska den här administratören designmiljöer dokumentationen och fokus på användaren dokumentationen så att du förstår funktioner och effekten av den här funktionen innan du aktiverar den nya upplevelsen. Basera din utbildning om användardokumentation för att förbereda dina användare för den nya upplevelsen och bidra till att säkerställa en lyckad distribution.

|     |
| --- |
| Kombinerade security information registreringen för Azure Multi-Factor Authentication och Azure AD lösenordsåterställning via självbetjäning är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Om en användare har aktiverats för både den ursprungliga förhandsversionen och förbättrad kombinerade registrerings-upplevelse, visas så den nya upplevelsen. Användare som har aktiverats för båda upplevelser visas endast den nya upplevelsen för min profil. Den nya Mina profilen överensstämmer med utseendet och känslan kombinerade registrerings och ger en sömlös upplevelse för användare. Användare kan se min profil genom att gå till [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Min profil sidorna är lokaliserade baserat på de aktuella språkinställningarna på datorn som kommer åt sidan. Microsoft lagrar det senaste språk som används i webbläsarens cacheminne så efterföljande försök att komma åt kommer fortsätta att visas i det senaste språk som används. Rensa cacheminnet kommer sidorna att visas igen. Om du vill tvinga fram ett visst språk att lägga till en `?lng=de-DE` i slutet av URL: en där `de-DE` har angetts till rätt språk kod tvingar sidorna att visas på det språket.

![Min profil gränssnitt som visar säkerhetsinformation och möjlighet för användaren att konfigurera SSPR eller andra verifieringsmetoder för ytterligare säkerhet.](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Metoder som finns tillgängliga i konvergerade registreringen

För närvarande stöder kombinerade registrering av följande metoder och åtgärder för dessa metoder:

|   | Registrera dig | Ändra | Ta bort |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (max 5) | Nej | Ja |
| Andra authenticator-appen | Ja (max 5) | Nej | Ja |
| Maskinvarutoken | Nej | Nej | Ja |
| Telefon | Ja | Ja | Ja |
| Alternativ telefon | Ja | Ja | Ja |
| Arbetstelefon | Nej | Nej | Nej |
| E-post | Ja | Ja | Ja |
| Säkerhetsfrågor | Ja | Nej | Ja |
| Applösenord | Ja | Nej | Ja |

> [!NOTE]
> Applösenord är bara tillgängliga för användare som har utförts för MFA. Applösenord är inte tillgängliga för användare som är aktiverade för MFA via principer för villkorlig åtkomst.

Användare kan ange följande alternativ som deras standardmetoden för MFA:

- Microsoft Authenticator – meddelande
- Authenticator-appen eller maskinvara token – code
- Telefonsamtal
- Textmeddelande

När vi fortsätter att lägga till flera autentiseringsmetoder som sådana i Azure AD, blir metoderna tillgänglig i kombinerade registreringen.

## <a name="combined-registration-modes"></a>Kombinerade registrerings lägen

Det finns två ”lägen” kombinerade registrerings: Avbryt och hantera. 

Avbryter läge, är en guiden-liknande upplevelse som visas för en användare när de registrera eller uppdatera deras säkerhetsinformation vid inloggning. 

Hantera läge är en del av användarens profil och låter dem att hantera deras säkerhetsinformation. 

För båda lägena, om en användare har redan registrerat en metod som kan användas för MFA, behöver de utföra MFA innan de kan komma åt deras säkerhetsinformation.

### <a name="interrupt-mode"></a>Avbryt läge

Kombinerade registrerings värnar om både MFA och SSPR-principer om båda är aktiverade för din klient. Dessa principer styr om en användare avbryts för att registrera sig när de loggar in och vilka metoder är tillgängliga för att registrera.

Följande lista över flera scenarier där en användare kan uppmanas att registrera eller uppdatera deras säkerhetsinformation:

* MFA-registrering som aktiveras via Identity Protection: Användare uppmanas att registrera när de loggar in. De registrera MFA-metoder och SSPR-metoder (om användaren är aktiverad för SSPR).
* MFA-registrering som aktiveras via MFA per användare: Användare uppmanas att registrera när de loggar in. De registrera MFA-metoder och SSPR-metoder (om användaren är aktiverad för SSPR).
* MFA-registrering som aktiveras via villkorlig åtkomst eller andra principer: Användarna uppmanas att registrera vid åtkomst till en resurs som kräver MFA. Användare ska registrera MFA-metoder och SSPR-metoder (om användaren har aktiverats för SSPR).
* SSPR-registrering tillämpas: Användarna uppmanas att registrera när de loggar in. De endast registrera SSPR-metoder
* SSPR-uppdatering tillämpas: Användarna uppmanas att granska deras säkerhetsinformation vid ett intervall som angetts av administratören. Användarna kan visas deras information och välja ”ser bra” eller gör ändringar om det behövs.

När registreringen tillämpas visas användare det minsta antalet metoder som krävs för att vara kompatibel med både MFA och SSPR-principer från de flesta minst säkert.

Exempel:

* En användare har aktiverats för SSPR. SSPR-princip krävs två metoder för att återställa och har aktiverat mobilappkoden, e-post och telefon.
   * Den här användaren krävs för att registrera på två sätt.
      * Som standard visas de authenticator-appen och telefon.
      * Användaren kan välja att registrera e-post i stället för authenticator-appen eller telefon.

I följande flödesschema beskrivs vilka metoder visas för en användare avbruten att registrera när de loggar in:

![Flödesschema för säkerhet info förklarar antalet metoder som krävs när det krävs mer information när du loggar in i kombination. Detta kan ändras om endast MFA eller bara SSPR krävs](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Om du har både MFA och SSPR aktiverat, rekommenderar vi att använda MFA-registrering.

Om SSPR-principen kräver att användare att granska deras säkerhetsinformation med jämna mellanrum, avbryts när de loggar in användare och visas alla sina registrerade metoder. De kan välja ”ser bra” om informationen är uppdaterad eller de kan välja ”Redigera information” att göra ändringar.

### <a name="manage-mode"></a>Hantera läge

Användare kan komma åt hantera läge genom att gå till [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) eller genom att välja ”säkerhetsinformation” från min profil. Därifrån kan användare kan lägga till metoder, ta bort eller ändra befintliga metoder, ändra deras standardmetoden och mycket mer.

## <a name="key-usage-scenarios"></a>Scenarier för nyckelanvändning

### <a name="set-up-security-info-during-sign-in"></a>Konfigurera säkerhetsinformation när de loggar in

En administratör har tvingande registrering.

En användare inte har ställt in alla nödvändiga säkerhetsinformation och navigerar till Azure-portalen. När du har angett sitt användarnamn och lösenord, uppmanas användaren att ställa in säkerhetsinformation. Användaren följer sedan anvisningarna som visas i guiden för att ställa in säkerhetsinformationen som krävs. Användaren kan välja att konfigurera andra metoder än vad som anges som standard om dina inställningar tillåter. I slutet av guiden granskar du de metoder som de har konfigurerat och deras standardmetoden för MFA. För att slutföra installationen måste användaren bekräftar informationen och fortsätter att Azure-portalen.

### <a name="set-up-security-info-from-my-profile"></a>Konfigurera säkerhetsinformation från min profil

En administratör har inte tvingande registrering.

En användare som ännu inte har ställts in all nödvändig säkerhetsinformation navigerar till [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). Användaren väljer sedan **säkerhetsinformation** från det vänstra navigeringsfältet. Därifrån kan användaren väljer att lägga till en metod, väljer någon av metoderna som är tillgängliga för dem och följer stegen för att ställa in den metoden. När du är klar ser användaren den metod som de som precis har konfigurerat på sidan säkerhetsinformation.

### <a name="delete-security-info-from-my-profile"></a>Ta bort säkerhetsinformation från min profil

En användare som redan har konfigurerat minst en metod som navigerar till [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Användaren väljer att ta bort ett av de tidigare registrerade metoderna. När du är klar ser användaren inte längre den metoden på sidan säkerhetsinformation.

### <a name="change-default-method-from-my-profile"></a>Ändra standardmetoden från min profil

En användare som redan har konfigurerat minst en metod som kan användas för MFA navigerar till [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Användaren ändrar sitt aktuella standardmetoden till en annan standard-metod. När du är klar ser användaren sina nya standardmetoden på sidan säkerhetsinformation.

## <a name="next-steps"></a>Nästa steg

[Aktivera kombinerade registrering i din klient](howto-registration-mfa-sspr-combined.md)

[Tillgängliga metoder för MFA och SSPR](concept-authentication-methods.md)

[Konfigurera lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
