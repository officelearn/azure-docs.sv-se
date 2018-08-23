---
title: Konvergerade registrering för Azure AD SSPR och MFA (offentlig förhandsversion)
description: Azure AD Multi-Factor Authentication så och självbetjäning lösenordsåterställning registrering (offentlig förhandsversion)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: af57faddcc1413747b4bb847e27287ba86562175
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055835"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Konvergerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication (förhandsversion)

Fram till nu var användare behöver registrera autentiseringsmetoder för Azure Multi-Factor Authentication (MFA) och lösenordsåterställning via självbetjäning (SSPR) i två olika portaler. Många användare har förvirrad av det faktum att liknande metoder har använts för både Azure MFA och SSPR och inte vill registrera i båda portalerna. Den här olikheterna ledde till vissa användare att det inte går att använda Azure MFA eller SSPR vid behov, vilket leder till ett helpdesk-anrop och eventuellt en upprörd användare. Användare kan nu registrera en gång och få fördelarna med både Azure MFA och SSPR, vilket eliminerar behovet av att registrera sina autentiseringsmetoder för dessa funktioner två gånger.  

Innan du aktiverar den här nya upplevelsen för din organisation rekommenderar vi att du läser den här artikeln samt våra [slutanvändardokumentation](https://aka.ms/securityinfoguide) ska förstå effekten upplevelsen kommer att ha på användarna. Du kan använda den [slutanvändardokumentation](https://aka.ms/securityinfoguide) att träna och förbereda dina användare för den nya upplevelse och se till att en lyckad distribution.

|     |
| --- |
| Konvergerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Om du vill låta dina användare registrera autentiseringsmetoder för både Azure Multi-Factor Authentication och lösenord för självbetjäning i enkel, gör du följande:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Bläddra till **Azure Active Directory**, **användarinställningar**, **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation**, du kan välja att aktivera för ett **valda** grupp av användare eller **alla** användare.

Användarna kan nu gå till [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) att registrera sina autentiseringsmetoder för både MFA och SSPR. Läs mer om vad användarna ser i den här nya upplevelsen i vår [slutanvändardokumentation](https://aka.ms/securityinfoguide).  

> [!NOTE]
> När du aktiverar den här upplevelsen användare registrera eller bekräfta sina telefonnummer eller mobilappar via den nya upplevelsen kommer att ha möjlighet att använda dem för MFA och SSPR, om dessa metoder är aktiverade i MFA och SSPR-principer. Om du inaktiverar sedan den här upplevelsen kan behöver användare navigerar till föregående SSPR-registreringssidan på aka.ms/ssprsetup utföra MFA innan de kan komma åt sidan.  

## <a name="how-it-works"></a>Hur det fungerar

Om en användare har redan registrerat autentiseringsmetoder via separata MFA och SSPR-registrering upplevelser behöver de inte att registrera informationen igen. Om dina inställningar kräver att användarna att registrera för MFA eller SSPR, kan de se en uppmaning om att granska deras säkerhetsinformation när du loggar in.

Om en användare har registrerat en metod som kan användas för MFA, uppmanas de att utföra MFA innan de kan komma åt den nya upplevelsen.

Om du har tvingande registrering för MFA eller SSPR och en användare har ännu inte har registrerats, uppmanas de att registrera när de loggar in.

Användare uppmanas att registrera när du loggar in visas följande upplevelse:

![Konvergerade registrering. Konfigurera metoder som en ny användare](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Den här endast visas när en användare uppmanas att registrera när du loggar in. Användare som går direkt till den nya upplevelsen på aka.ms/setupsecurityinfo visas en annan version av den upplevelsen, som beskrivs senare i den här artikeln.

De autentiseringsmetoder som visas kommer att ändras baserat på de metoder som är aktiverad i din MFA- eller SSPR-principer. Användaren uppmanas att registrera det minsta antalet autentiseringsmetoder som behövs för att vara kompatibel med principen för MFA, SSPR principen eller båda. Om det finns flexibilitet i vilka autentiseringsmetoder användaren kan registrera, kan de välja **väljer säkerhetsinformation** välja andra autentiseringsmetoder.  

> [!NOTE]
> Om du aktiverar användningen av både mobilapp och kod för mobilapp kan användare som registrerar Microsoft Authenticator-appen med hjälp av ett meddelande om du använder både meddelande och kod för att verifiera sin identitet.

Till skillnad från tidigare MFA-registrering används uppmanas användarna inte att registrera ett applösenord när du går via den nya upplevelsen för registrering. De bör i stället följa stegen i våra självstudier för lösenord att registrera applösenord i den nya upplevelsen.  

När en användare har slutfört registreringen, ställs automatiskt sina standardmetoden för MFA. Om användaren har registrerat en autentiseringsapp sätts standardmetoden till appen. Om användaren inte att registrera en autentiseringsapp och endast registrerade sina telefonnummer, sätts standardmetoden till telefonsamtal. Användarna kan ändra sina standard genom att gå till [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) och välja **ändra standard**.  

Om registreringen inte aktiv och användarna kan hantera sina egna autentiseringsmetoder på [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo). Om en användare har redan registrerat en metod som kan användas för att utföra MFA uppmanas de att utföra MFA innan de kan komma åt sidan.  

![Konvergerade registrering. Redigera metoder som registrerad användare](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Användarna ser på den här sidan tidigare registrerad autentiseringsmetoder och autentiseringsmetoder som registrerats för dem, till exempel Arbetstelefon. Användare kan också lägga till, redigera eller ta bort sina autentiseringsmetoder (exklusive Arbetstelefon).  

Granskningsloggarna för den här nya upplevelsen finns under kategorin autentiseringsmetoder i granskningsloggen.  

## <a name="known-issues"></a>Kända problem

**Standardmetoden för MFA har angetts till telefonsamtal när användare registrerar telefon med SMS**

   * Vissa användare kanske märker att deras standardmetoden för MFA har angetts till telefonsamtal när de registrerar sina telefonnummer med SMS: et. Användare kan lösa det här problemet genom att ändra deras standardmetoden genom att följa instruktionerna i artikeln [hantera din säkerhetsinformation (förhandsversion)](../user-help/security-info-manage-settings.md#change-your-info).

**Användaren kan inte komma åt den nya upplevelsen för registrering när administratör inaktiverar sina standardmetoden**

   * Vissa användare kanske inte kan komma åt den nya upplevelsen för registrering om sina tidigare registrerad standard MFA-metoden har inaktiverats av administratören. Här är ett exempelscenario:
      1. Användaren tidigare registrerad sitt telefonnummer och ange deras standardmetoden till telefonsamtal.
      2. Administratör har inaktiverat telefonsamtal som en MFA-metod för klienten.
      3. Användaren uppmanas att registrera under inloggning eftersom de behöver för att registrera ytterligare en metod för att uppfylla klienten SSPR-principen.
      4. Användaren försöker registrera men det går inte att komma åt sidan på grund av en standardmetod som man inte behöver och har fastnat i en loop.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du distribuerar Azure AD lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Lär dig att kräva multifaktorautentisering vid inloggning](howto-mfa-getstarted.md)

[Autentisering metoden configuration användardokumentation](https://aka.ms/securityinfoguide)