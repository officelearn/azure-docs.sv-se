---
title: Bas linje princip blockera äldre autentisering (för hands version) – Azure Active Directory
description: Princip för villkorlig åtkomst för att blockera bakåtkompatibla autentiseringsprotokoll
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 413df402d5acc878130a47821efd5fa3a512257d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931939"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Bas linje princip: Blockera äldre autentisering (för hands version)

Azure Active Directory (Azure AD) stöder en mängd olika autentiseringsprotokoll, inklusive äldre autentisering, för att ge dina användare enkel åtkomst till dina molnappar. Äldre autentisering är en term som hänvisar till en autentiseringsbegäran som görs av:

* Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klienten)
* Alla klienter som använder äldre e-postprotokoll som IMAP/SMTP/POP3

Idag kommer majoriteten av alla kompromissade inloggnings försök från äldre autentisering. Äldre autentisering stöder inte Multi-Factor Authentication (MFA). Även om du har en MFA-princip aktive rad i din katalog, kan en felaktig aktör autentisera med ett äldre protokoll och kringgå MFA.

Det bästa sättet att skydda ditt konto från skadliga autentiseringsbegäranden som görs av äldre protokoll är att blockera dessa försök helt och hållet. För att göra det enklare för dig att skydda din miljö har vi skapat den här bas linje principen för att blockera äldre autentisering.

**Blockera äldre autentisering** är en [bas linje princip](concept-baseline-protection.md) som blockerar alla autentiseringsbegäranden som görs från äldre protokoll. Modern autentisering måste användas för att kunna logga in för alla användare. Som används tillsammans med andra bas linje principer kommer alla begär Anden som kommer från äldre protokoll att blockeras och alla användare måste vara MFA när de behöver det. Den här principen blockerar inte Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identifiera användning av äldre autentisering

Innan du kan blockera äldre autentisering i din katalog måste du först förstå om dina användare har appar som använder äldre autentisering och hur de påverkar den övergripande katalogen. Inloggnings loggar för Azure AD kan användas för att förstå om du använder äldre autentisering.

1. Navigera till **Azure Portal** > **Azure Active Directory** > **inloggningar**.
1. Lägg till kolumnen klient program om den inte visas genom att klicka på **kolumner** > **klient program**.
1. **Lägg till filter** > **klient program** > Välj alla alternativ för **andra klienter** och klicka på **Använd**.

Vid filtrering visas bara inloggnings försök som gjorts av äldre autentiseringsprotokoll. Om du klickar på varje enskilt inloggnings försök visas ytterligare information. Fältet **klient app** på fliken **grundläggande information** visar vilket äldre autentiseringsprotokoll som användes.

I dessa loggar anges vilka användare som fortfarande är beroende av tidigare autentisering och vilka program som använder äldre protokoll för att göra autentiseringsbegäranden. För användare som inte visas i dessa loggar och som bekräftas att inte använda äldre autentisering, implementera en princip för villkorlig åtkomst eller aktivera **bas linje principen: blockera äldre autentisering** enbart för dessa användare.

## <a name="moving-away-from-legacy-authentication"></a>Flytta bort från äldre autentisering

När du har en bättre uppfattning om vem som använder äldre autentisering i din katalog och vilka program som är beroende av den, uppgraderar nästa steg dina användare till att använda modern autentisering. Modern autentisering är en metod för identitets hantering som ger säkrare användarautentisering och auktorisering. Om du har en MFA-princip på plats i din katalog, ser modern autentisering till att användaren uppmanas att använda MFA vid behov. Det är det säkrare alternativet för bakåtkompatibla autentiseringsprotokoll.

Det här avsnittet innehåller en steg-för-steg-översikt om hur du uppdaterar din miljö till modern autentisering. Läs igenom stegen nedan innan du aktiverar en äldre princip för att blockera autentisering i din organisation.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Steg 1: Aktivera modern autentisering i din katalog

Det första steget i att aktivera modern autentisering ser till att din katalog stöder modern autentisering. Modern autentisering är aktiverat som standard för kataloger som skapats den 1 augusti 2017. Om din katalog skapades före detta datum måste du aktivera modern autentisering för din katalog manuellt med hjälp av följande steg:

1. Kontrol lera om din katalog redan stöder modern autentisering genom att köra `Get-CsOAuthConfiguration` från [Skype för företag – Online PowerShell-modulen](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Om kommandot returnerar en tom `OAuthServers` egenskap är modern autentisering inaktive rad. Uppdatera inställningen för att aktivera modern autentisering med `Set-CsOAuthConfiguration`. Om din `OAuthServers` egenskap innehåller en post, är du redo att sätta igång.

Se till att slutföra det här steget innan du går vidare. Det är viktigt att dina webbplatskonfigurationer ändras först eftersom de avgör vilket protokoll som ska användas av alla Office-klienter. Även om du använder Office-klienter som har stöd för modern autentisering, kommer de att använda äldre protokoll om modern autentisering är inaktive rad i din katalog.

### <a name="step-2-office-applications"></a>Steg 2: Office-program

När du har aktiverat modern autentisering i din katalog kan du börja uppdatera program genom att aktivera modern autentisering för Office-klienter. Office 2016 eller senare klienter stöder modern autentisering som standard. Inga extra steg krävs.

Om du använder Office 2013 Windows-klienter eller äldre rekommenderar vi att du uppgraderar till Office 2016 eller senare. Även när du har slutfört det tidigare steget i att aktivera modern autentisering i din katalog, fortsätter äldre Office-program att använda äldre autentiseringsprotokoll. Om du använder Office 2013-klienter och inte kan uppgradera direkt till Office 2016 eller senare följer du stegen i följande artikel för att [Aktivera modern autentisering för Office 2013 på Windows-enheter](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). För att skydda ditt konto när du använder äldre autentisering rekommenderar vi att du använder starka lösen ord i din katalog. Kolla in [Azure AD Password Protection](../authentication/concept-password-ban-bad.md) för att förbjuda svaga lösen ord i din katalog.

Office 2010 stöder inte modern autentisering. Du måste uppgradera alla användare med Office 2010 till en nyare version av Office. Vi rekommenderar att du uppgraderar till Office 2016 eller senare, eftersom det blockerar äldre autentisering som standard.

Om du använder MacOS rekommenderar vi att du uppgraderar till Office för Mac 2016 eller senare. Om du använder den interna e-postklienten måste du ha MacOS version 10,14 eller senare på alla enheter.

### <a name="step-3-exchange-and-sharepoint"></a>Steg 3: Exchange och SharePoint

För Windows-baserade Outlook-klienter för att använda modern autentisering måste Exchange Online vara modern autentisering även aktive rad. Om modern autentisering är inaktive rad för Exchange Online kommer Windows-baserade Outlook-klienter som stöder modern autentisering (Outlook 2013 eller senare) att använda grundläggande autentisering för att ansluta till Exchange Online-postlådor.

SharePoint Online är aktiverat som standard för modern autentisering. För kataloger som skapats efter den 1 augusti 2017 aktive ras modern autentisering som standard i Exchange Online. Om du tidigare har inaktiverat modern autentisering eller använder en katalog som skapats före detta datum, följer du stegen i följande artikel för att [Aktivera modern autentisering i Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Steg 4: Skype för företag

För att förhindra att äldre autentiseringsbegäranden som görs av Skype för företag, är det nödvändigt att aktivera modern autentisering för Skype för företag – online. För kataloger som skapats efter den 1 augusti 2017 är modern autentisering för Skype för företag aktiverat som standard.

Vi rekommenderar att du går över till Microsoft Teams, som har stöd för modern autentisering som standard. Men om du inte kan migrera just nu måste du aktivera modern autentisering för Skype för företag – online så att Skype för företag-klienter börjar använda modern autentisering. Följ stegen i den här artikeln [Skype for Business-topologier som stöds med modern autentisering](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)för att aktivera modern autentisering för Skype för företag.

Förutom att aktivera modern autentisering för Skype för företag Online rekommenderar vi att du aktiverar modern autentisering för Exchange Online när du aktiverar modern autentisering för Skype för företag. Den här processen hjälper till att synkronisera statusen för modern autentisering i Exchange Online och Skype för företag – Online och förhindrar flera inloggnings meddelanden för Skype för företag-klienter.

### <a name="step-5-using-mobile-devices"></a>Steg 5: Använda mobila enheter

Program på din mobila enhet behöver även blockera äldre autentisering. Vi rekommenderar att du använder Outlook för mobil. Outlook för Mobile stöder modern autentisering som standard och uppfyller andra MFA-principer för skydd mot flera principer.

För att kunna använda den interna iOS-e-postklienten måste du köra iOS version 11,0 eller senare för att säkerställa att e-postklienten har uppdaterats för att blockera äldre autentisering.

### <a name="step-6-on-premises-clients"></a>Steg 6: Lokala klienter

Om du är en hybrid kund som använder Exchange Server lokalt och Skype för företag lokalt måste båda tjänsterna uppdateras för att aktivera modern autentisering. När du använder modern autentisering i en hybrid miljö autentiserar du fortfarande användare lokalt. Berättelsen om att auktorisera åtkomst till resurser (filer eller e-postmeddelanden) ändras.

Innan du kan börja aktivera modern autentisering lokalt måste du vara säker på att du uppfyller kraven.
Nu är du redo att aktivera modern autentisering lokalt.

Steg för att aktivera modern autentisering finns i följande artiklar:

* [Så här konfigurerar du Exchange Server lokalt för att använda hybrid modern autentisering](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Så här använder du modern Authentication (ADAL) med Skype för företag](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Aktivera bas linje principen

Princip **bas linje princip: Blockera äldre autentisering (för hands** version) är förkonfigurerad och visas överst när du navigerar till bladet för villkorlig åtkomst i Azure Portal. Den här inställningen börjar bara gälla efter en lyckad inloggning, så användarna kan fortfarande välja att försöka använda äldre autentisering.

Så här aktiverar du den här principen och skyddar din organisation:

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. I listan med principer väljer **du bas linje princip: Blockera äldre autentisering (för hands**version).
1. Ange principen för att **aktivera principen** **direkt**.
1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Principer för bas linje skydd för villkorlig åtkomst](concept-baseline-protection.md)
* [Fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
