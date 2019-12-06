---
title: Kombinerad registrering för SSPR och MFA-Azure Active Directory
description: Azure AD-Multi-Factor Authentication och registrering av lösen ords återställning via självbetjäning (för hands version)
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
ms.openlocfilehash: 2b484acc0dc1a92a857f254ed37392ffb29eddb8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848637"
---
# <a name="combined-security-information-registration-preview"></a>Kombinerad säkerhets informations registrering (för hands version)

Innan en kombinerad registrering har användare registrerat autentiseringsmetoder för Azure Multi-Factor Authentication och lösen ords återställning via självbetjäning (SSPR) separat. Personer har förvirrat att liknande metoder användes för Multi-Factor Authentication och SSPR, men de var tvungna att registrera sig för båda funktionerna. Med kombinerad registrering kan användarna registrera sig en gång och få fördelarna med både Multi-Factor Authentication-och SSPR.

![Min profil visar registrerad säkerhets information för en användare](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Innan du aktiverar den nya upplevelsen kan du läsa den här administratörs säkra dokumentationen och den användar fokuserade dokumentationen så att du förstår funktionerna och effekterna av den här funktionen. Basera din utbildning på [användar dokumentationen](../user-help/user-help-security-info-overview.md) för att förbereda dina användare för den nya upplevelsen och hjälpa till att säkerställa en lyckad distribution.

Den kombinerade säkerhets informations registreringen i Azure AD är för närvarande inte tillgänglig för nationella moln som Azure amerikanska myndigheter, Azure Germany eller Azure Kina 21Vianet.

|     |
| --- |
| Kombinerad säkerhets informations registrering för Multi-Factor Authentication och Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord är en offentlig förhands gransknings funktion i Azure AD. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Användare som har Aktiver ATS för både den ursprungliga för hands versionen och den förbättrade kombinerade registreringen kommer att se det nya beteendet. Användare som har Aktiver ATS för båda erfarenheterna kan bara se den nya min profil upplevelsen. Den nya min profil överensstämmer med utseendet och känslan av kombinerad registrering och ger en sömlös upplevelse för användarna. Användarna kan se min profil genom att gå till [https://myprofile.microsoft.com](https://myprofile.microsoft.com).

> [!NOTE] 
> Du kan stöta på ett fel meddelande när du försöker få åtkomst till alternativet säkerhets information. Det går till exempel inte att logga in dig. I det här fallet kontrollerar du att du inte har någon konfigurations-eller grup princip objekt som blockerar cookies från tredje part i webbläsaren. 

Mina profil sidor är lokaliserade baserat på språk inställningarna för den dator som har åtkomst till sidan. Microsoft lagrar det senaste språket som används i webbläsarens cacheminne, så efterföljande försök att komma åt sidorna fortsätter att återges på det senast använda språket. Om du rensar cacheminnet åter renderas sidorna igen. Om du vill tvinga ett speciellt språk kan du lägga till `?lng=<language>` i slutet av URL: en, där `<language>` är koden för det språk som du vill återge.

![Konfigurera SSPR eller andra säkerhets verifierings metoder](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metoder som är tillgängliga i kombinerad registrering

Kombinerad registrering stöder följande autentiseringsmetoder och åtgärder:

|   | Registrera dig | Ändra | Ta bort |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (högst 5) | Nej | Ja |
| Annan Authenticator-app | Ja (högst 5) | Nej | Ja |
| Maskinvaru-token | Nej | Nej | Ja |
| Telefon | Ja | Ja | Ja |
| Alternativ telefon | Ja | Ja | Ja |
| Arbetstelefon | Nej | Nej | Nej |
| E-post | Ja | Ja | Ja |
| Säkerhetsfrågor | Ja | Nej | Ja |
| Applösenord | Ja | Nej | Ja |

> [!NOTE]
> Applösenord är bara tillgängliga för användare som har Aktiver ATS för Multi-Factor Authentication. Applösenord är inte tillgängliga för användare som har Aktiver ATS för Multi-Factor Authentication via en princip för villkorlig åtkomst.

Användare kan ange ett av följande alternativ som standard Multi-Factor Authentication metod:

- Microsoft Authenticator – meddelande.
- Authenticator-app eller maskinvaru-token – kod.
- Telefonsamtal.
- Textmeddelande.

När vi fortsätter att lägga till fler autentiseringsmetoder till Azure AD är dessa metoder tillgängliga i kombinerad registrering.

## <a name="combined-registration-modes"></a>Kombinerade registrerings lägen

Det finns två lägen för kombinerad registrering: avbryta och hantera.

- **Avbrotts läge** är en guide som liknar erfarenhet, som presenteras för användare när de registrerar eller uppdaterar sina säkerhets uppgifter vid inloggning.

- **Hanterings läget** är en del av användar profilen och gör att användarna kan hantera sina säkerhets uppgifter.

För båda lägena måste användare som tidigare har registrerat en metod som kan användas för Multi-Factor Authentication utföra Multi-Factor Authentication innan de kan komma åt sina säkerhets uppgifter.

### <a name="interrupt-mode"></a>Avbrotts läge

Kombinerad registrering respekterar både Multi-Factor Authentication-och SSPR-principer, om båda har Aktiver ATS för din klient. Dessa principer styr huruvida en användare avbryts för registrering under inloggning och vilka metoder som är tillgängliga för registrering.

Här följer flera scenarier där användare kan uppmanas att registrera eller uppdatera sin säkerhets information:

- Multi-Factor Authentication registrering tillämpas via identitets skydd: användarna uppmanas att registrera sig under inloggningen. De registrerar Multi-Factor Authentication metoder och SSPR-metoder (om användaren är aktive rad för SSPR).
- Multi-Factor Authentication registrering tillämpas via per användare-Multi-Factor Authentication: användarna uppmanas att registrera sig under inloggningen. De registrerar Multi-Factor Authentication metoder och SSPR-metoder (om användaren är aktive rad för SSPR).
- Multi-Factor Authentication registrering tillämpas via villkorlig åtkomst eller andra principer: användarna uppmanas att registrera sig när de använder en resurs som kräver Multi-Factor Authentication. De registrerar Multi-Factor Authentication metoder och SSPR-metoder (om användaren är aktive rad för SSPR).
- SSPR-registrering framtvingad: användarna uppmanas att registrera sig under inloggningen. De registrerar endast SSPR-metoder.
- Tvingande uppdatering av SSPR: användare måste granska sina säkerhets uppgifter med ett intervall som angetts av administratören. Användarna visar sin information och kan bekräfta den aktuella informationen eller göra ändringar om det behövs.

När registreringen är aktive rad visas det minsta antal metoder som krävs för att uppfylla både Multi-Factor Authentication-och SSPR-principer, från de flesta till minst säkra.

Exempel:

- En användare har Aktiver ATS för SSPR. SSPR-principen krävde två metoder för att återställa och har aktiverat mobil kod, e-post och telefon.
   - Den här användaren krävs för att registrera två metoder.
      - Användaren visas som standard autentiserare.
      - Användaren kan välja att registrera e-post i stället för Authenticator-appen eller telefon.

Det här flödesschemat beskriver vilka metoder som visas för en användare när du avbryter registreringen under inloggningen:

![Kombinerat flödes schema för säkerhets information](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Om du har både Multi-Factor Authentication-och SSPR aktiverat rekommenderar vi att du tillämpar Multi-Factor Authentication registrering.

Om SSPR-principen kräver att användare granskar sin säkerhets information med jämna mellanrum, avbryts användarna under inloggningen och alla registrerade metoder visas. De kan bekräfta den aktuella informationen om den är uppdaterad, eller så kan de göra ändringar om de behöver det.

### <a name="manage-mode"></a>Hantera läge

Användare kan komma åt hanterings läget genom att gå till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) eller genom att välja **säkerhets information** från min profil. Därifrån kan användarna lägga till metoder, ta bort eller ändra befintliga metoder, ändra standard metoden med mera.

## <a name="key-usage-scenarios"></a>Scenarier för nyckel användning

### <a name="set-up-security-info-during-sign-in"></a>Konfigurera säkerhets information under inloggning

En administratör har tvingat registrering.

En användare har inte konfigurerat all nödvändig säkerhets information och går till Azure Portal. När du har angett användar namn och lösen ord uppmanas användaren att ställa in säkerhets information. Användaren följer sedan stegen som visas i guiden för att konfigurera den säkerhets information som krävs. Om inställningarna tillåter det kan användaren välja att ställa in andra metoder än de som visas som standard. När du har slutfört guiden granskar användarna de metoder de har ställt in och deras standard metod för Multi-Factor Authentication. För att slutföra installationen bekräftar användaren informationen och fortsätter till Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Konfigurera säkerhets information från min profil

En administratör har inte tvingat registrering.

En användare som ännu inte har konfigurerat all nödvändig säkerhets information går till [https://myprofile.microsoft.com](https://myprofile.microsoft.com). Användaren väljer **säkerhets information** i den vänstra rutan. Därifrån väljer användaren att lägga till en metod, väljer någon av de tillgängliga metoderna och följer stegen för att ställa in den metoden. När du är färdig ser användaren den metod som precis har kon figurer ATS på sidan säkerhets information.

### <a name="delete-security-info-from-my-profile"></a>Ta bort säkerhets information från min profil

En användare som tidigare har konfigurerat minst en metod navigerar till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Användaren väljer att ta bort en av de tidigare registrerade metoderna. När du är färdig ser användaren inte längre metoden på sidan säkerhets information.

### <a name="change-the-default-method-from-my-profile"></a>Ändra standard metoden från min profil

En användare som tidigare har konfigurerat minst en metod som kan användas för Multi-Factor Authentication navigerar till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Användaren ändrar den aktuella standard metoden till en annan standard metod. När du är färdig ser användaren den nya standard metoden på sidan säkerhets information.

## <a name="next-steps"></a>Nästa steg

[Tvinga användare att registrera autentiseringsmetoder igen](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Aktivera kombinerad registrering i din klient organisation](howto-registration-mfa-sspr-combined.md)

[SSPR och MFA-användning och insikts rapportering](howto-authentication-methods-usage-insights.md)

[Tillgängliga metoder för Multi-Factor Authentication och SSPR](concept-authentication-methods.md)

[Konfigurera återställning av lösen ord för självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure-Multi-Factor Authentication](howto-mfa-getstarted.md)
