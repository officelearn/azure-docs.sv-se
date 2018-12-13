---
title: Konvergerade registrering för Azure AD SSPR och MFA (offentlig förhandsversion)
description: Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning återställa registrering (offentlig förhandsversion)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: dbced5cfa2a47dc2fdcf630d62104bb7ba8e7bc0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186658"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Konvergerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication (förhandsversion)

Fram till nu var användare behöver registrera autentiseringsmetoder för Azure Multi-Factor Authentication (MFA) och lösenordsåterställning via självbetjäning (SSPR) i två olika portaler. Många användare har ihop att liknande metoder har använts för både Azure MFA och SSPR och inte vill registrera i båda portalerna. Vissa användare gick sedan inte att använda Azure MFA- eller SSPR när de behövs, ledande till samtal till supportavdelningen. 

Användare kan nu registrera en gång och få fördelarna med både Azure MFA och SSPR. Användarna behöver inte registrera sina autentiseringsmetoder för dessa funktioner två gånger.  

Innan du aktiverar den här nya upplevelsen för din organisation rekommenderar vi att du läser den här artikeln och [användardokumentation](https://aka.ms/securityinfoguide) ska förstå effekten som upplevelsen har på dina användare. Du kan använda användardokumentationen för att träna och förbereda dina användare för den nya upplevelsen och bidrar till att säkerställa en lyckad distribution.

|     |
| --- |
| Konvergerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication är en offentlig förhandsversion av funktionen i Azure Active Directory (AD Azure). Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Om du vill låta dina användare registrera autentiseringsmetoder för både Azure Multi-Factor Authentication och lösenord för självbetjäning i enkel, gör du följande:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Bläddra till **Azure Active Directory** > **användarinställningar** > **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation**, du kan välja att aktivera för ett **valda** grupp av användare eller **alla** användare.

Användarna kan nu gå till [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) att registrera sina autentiseringsmetoder för både MFA och SSPR. Läs mer om vad användarna ser i den här nya upplevelsen i den [användardokumentation](https://aka.ms/securityinfoguide).  

> [!NOTE]
> När du aktiverar den här upplevelsen kan kan användare registrera eller bekräfta sina telefonnummer eller mobilappar via den nya upplevelsen använda dem för MFA och SSPR, om dessa metoder är aktiverade i MFA och SSPR-principer. Om du inaktiverar sedan den här upplevelsen kan kommer användare går till föregående SSPR-registreringssidan på https:/aka.ms/ssprsetup att behöva utföra multifaktorautentisering innan de kan komma åt sidan.  

## <a name="how-it-works"></a>Hur det fungerar

Om en användare har redan registrerat autentiseringsmetoder via separata MFA och SSPR-registrering upplevelser behöver de inte registrera informationen igen. Men om inställningarna kräver att användarna att registrera för MFA eller SSPR, kan de se en uppmaning om att granska deras säkerhetsinformation när du loggar in.

Om en användare har registrerat en metod som kan användas för MFA, uppmanas de att utföra multifaktorautentisering innan de kan komma åt den nya upplevelsen.

Om du har tvingande registrering för MFA eller SSPR och en användare har ännu inte har registrerats, uppmanas de att registrera när de loggar in.

Användare uppmanas att registrera när du loggar in finns i följande upplevelse:

![Konvergerade registrering. Konfigurera metoder som en ny användare.](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Detta visas endast när en användare uppmanas att registrera när du loggar in. Användare som går direkt till mångåriga erfarenhet https://aka.ms/setupsecurityinfo finns i en annan version av den upplevelsen, som beskrivs senare i den här artikeln.

Metoderna visade autentisering ändras baserat på de metoder som är aktiverad i din MFA och SSPR-principer. Användaren uppmanas att registrera det minsta antalet autentiseringsmetoder som behövs för att vara kompatibel med principen för MFA, SSPR principen eller båda. Om det finns flexibilitet i vilka autentiseringsmetoder användaren kan registrera, kan de välja **väljer säkerhetsinformation** välja andra autentiseringsmetoder.  

> [!NOTE]
> Om du aktiverar användningen av både mobilapp och kod för mobilapp kan kan användare som registrerar Microsoft Authenticator-appen via ett meddelande använda både meddelande och kod ska verifiera sin identitet.

Till skillnad från tidigare MFA-registrering används uppmanas inte användare att registrera ett applösenord när du går via den nya upplevelsen för registrering. De bör i stället följa stegen i appar lösenord självstudiekursen och registrera applösenord i den nya upplevelsen.  

När en användare har slutfört registreringen, anges automatiskt sina standardmetoden för MFA. Om användaren har registrerat en autentiseringsapp anges standardmetoden till appen. Om användaren inte att registrera en autentiseringsapp och endast registrerade sina telefonnummer, sätts standardmetoden till telefonsamtal. Användarna kan ändra sina standard genom att gå till https://aka.ms/setupsecurityinfo och välja **ändra standard**.  

Om registreringen inte aktiv och användarna kan hantera sina egna autentiseringsmetoder på https://aka.ms/setupsecurityinfo. Om en användare har redan registrerat en metod som kan användas för MFA, ombeds de att utföra multifaktorautentisering innan de kan komma åt sidan.  

![Konvergerade registrering. Redigera metoder som registrerad användare.](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Användarna ser tidigare registrerad autentiseringsmetoder och autentiseringsmetoder som registrerats för dem, till exempel Arbetstelefon på den här sidan. Användare kan också lägga till, redigera eller ta bort sina autentiseringsmetoder (exklusive Arbetstelefon).  

Granskningsloggarna för den här nya upplevelsen finns under kategorin autentiseringsmetoder i granskningsloggen.  

## <a name="known-issues"></a>Kända problem

Vi har identifierat en bugg med konvergerade registration där B2B-gästanvändare som är aktiverade för konvergerade registrering inte kan registrera för MFA. När de är riktade till registreringssidan sidfel ut. Våra utvecklare är medvetna om problemet och arbetar för att tillhandahålla en lösning. Rekommendationen är att skapa en grupp och utesluta alla B2B-användare i gruppen för tillfället.

**Standardmetoden för MFA har angetts till telefonsamtal när en användare registrerar en telefon med SMS**

   * Vissa användare märker att deras standardmetoden för MFA har angetts till telefonsamtal när de registrerar sina telefonnummer genom att använda SMS: et. Användare kan lösa det här problemet genom att ändra deras standardmetoden genom att följa anvisningarna i artikeln [hantera din säkerhetsinformation (förhandsversion)](../user-help/security-info-manage-settings.md#change-your-info).

**Användaren kan inte komma åt den nya upplevelsen för registrering när en administratör har inaktiverat sina standardmetoden**

   * Vissa användare kanske inte kan komma åt den nya upplevelsen för registrering om administratören har inaktiverat sina tidigare registrerad standardmetoden för MFA. Här är ett exempelscenario:
      1. Användaren tidigare registrerad sitt telefonnummer och ange deras standardmetoden till telefonsamtal.
      2. Administratör har inaktiverat telefonsamtal som en MFA-metod för klienten.
      3. Användaren uppmanas att registrera under inloggning eftersom de behöver för att registrera ytterligare en metod för att uppfylla klienten SSPR-principen.
      4. Användaren försöker registrera men det går inte att komma åt sidan och har fastnat i en loop eftersom en standardmetod har inte angetts.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du distribuerar Azure AD lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Lär dig att kräva multifaktorautentisering för att logga in](howto-mfa-getstarted.md)

[Lär dig hur du konfigurerar användarautentiseringsmetoder](https://aka.ms/securityinfoguide)