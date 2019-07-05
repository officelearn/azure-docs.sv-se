---
title: Baslinjen princip blockera äldre authentication (förhandsversion) – Azure Active Directory
description: Villkorlig åtkomst för att blockera äldre autentiseringsprotokoll
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
ms.openlocfilehash: b8e9ea8956d87e2ec47cc65495e81d8a0f0ad8cb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560928"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Baslinjeprincip: Blockera äldre authentication (förhandsversion)

Om du vill ge dina användare enkel åtkomst till dina appar i molnet, stöder Azure Active Directory (Azure AD) ett stort antal autentiseringsprotokoll, inklusive äldre autentisering. Äldre autentisering är en term som refererar till en autentiseringsbegäran som gjorts av:

* Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klient)
* Alla klienter som använder äldre e-postprotokoll, till exempel IMAP/SMTP/POP3

Idag kommer merparten av alla att kompromissa inloggningsförsök från äldre autentisering. Äldre autentisering har inte stöd för multifaktorautentisering (MFA). Även om du har en MFA-princip på din katalog kan en obehörig autentisera med ett äldre protokoll och kringgå MFA.

Det bästa sättet att skydda ditt konto från skadliga autentiseringsbegäranden av äldre protokoll är att blockera dessa försök allt på samma plats. Om du vill göra det enklare för dig att blockera alla inloggningsbegäranden som gjorts av äldre protokoll, skapat vi en baslinjeprincip som gör just detta.

**Blockera äldre autentisering** är [baslinjeprincip](concept-baseline-protection.md) som blockerar alla autentiseringsbegäranden från äldre protokoll. Modern autentisering måste användas för att logga in för alla användare. Alla begäranden som kommer från äldre protokoll kommer att blockeras används tillsammans med andra principer som baslinjen, och alla användare kommer att behöva MFA varje gång som krävs. Den här principen blockerar inte Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identifiera äldre användning

Innan du kan blockera äldre autentisering i din katalog, måste du först förstå om användarna har appar som använder äldre autentisering och hur den påverkar din övergripande katalog. Azure AD-inloggningen loggar kan användas för att förstå om du använder äldre autentisering.

1. Navigera till den **Azure-portalen** > **Azure Active Directory** > **inloggningar**.
1. Lägga till kolumnen Klientappen om den inte visas genom att klicka på **kolumner** > **Klientappen**.
1. Filtrera efter **Klientappen** > **andra klienter** och klicka på **tillämpa**.

Filtrering kommer bara visa du loggar in försök som har gjorts av äldre autentiseringsprotokoll. När du klickar på varje enskild inloggningsförsök visas ytterligare information. Den **Klientappen** fältet under den **grundläggande information** fliken visar vilket äldre autentiseringsprotokoll användes.

De här loggarna visar vilka användare fortfarande är beroende av äldre och vilka program som använder äldre protokoll att autentiseringsbegäranden. Implementera en princip för villkorlig åtkomst för användare som inte finns med i loggarna och bekräftas som inte använder äldre autentisering, eller aktivera den **baslinjeprincip: blockera äldre autentisering** endast dessa användare.

## <a name="moving-away-from-legacy-authentication"></a>Rör sig från äldre autentisering

När du har en bättre uppfattning av vem som använder äldre autentisering i din katalog och vilka program som är beroende av den uppgraderas dina användare att använda modern autentisering i nästa steg. Modern autentisering är en metod för Identitetshantering som ger säkrare användarautentisering och auktorisering. Om du har en MFA-principen på plats på din katalog, modern autentisering som garanterar att användaren uppmanas att ange MFA vid behov. Det är säkrare alternativ till äldre autentiseringsprotokoll.

Det här avsnittet ger stegvisa översikt på hur du uppdaterar din miljö till modern autentisering. Läs igenom stegen nedan innan du aktiverar en äldre autentisering blockerande princip i din organisation.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Steg 1: Aktivera modern autentisering i din katalog

Det första steget i att aktivera modern autentisering att se till att din katalog har stöd för modern autentisering. Modern autentisering är aktiverad som standard för kataloger som skapats på eller efter den 1 augusti 2017. Om din katalog har skapats före detta datum, måste du manuellt Aktivera modern autentisering för din katalog med följande steg:

1. Kontrollera om din katalog redan har stöd för modern autentisering genom att köra `Get-CsOAuthConfiguration` från den [Skype för företag Online PowerShell-modulen](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Om kommandot returnerar en tom `OAuthServers` egenskap och sedan Modern autentisering är inaktiverat. Uppdatera inställningen för att aktivera modern autentisering med hjälp av `Set-CsOAuthConfiguration`. Om din `OAuthServers` egenskapen innehåller en post, är du redo att börja.

Glöm inte att slutföra det här steget innan du går vidare. Det är viktigt att dina konfigurationer i katalogen ändras först eftersom de styr vilket protokoll som ska användas av alla Office-klienter. Även om du använder Office-klienter som har stöd för modern autentisering standard som att använda äldre protokoll om modern autentisering är inaktiverat på din katalog.

### <a name="step-2-office-applications"></a>Steg 2: Office-program

När du har aktiverat modern autentisering i din katalog kan börja du uppdatera program genom att aktivera modern autentisering för Office-klienter. Office 2016 eller senare klienter stöder modern autentisering som standard. Inga ytterligare åtgärder krävs.

Om du använder Office 2013 Windows-klienter eller äldre, vi rekommenderar att du uppgraderar till Office 2016 eller senare. Även efter att du har slutfört föregående steg för att aktivera modern autentisering i din katalog är fortsätter äldre Office-programmen att använda äldre autentiseringsprotokoll. Om du använder Office 2013-klienter och kan inte uppgradera direkt till Office 2016 eller senare, följer du stegen i följande artikel till [aktivera Modern autentisering för Office 2013 på Windows-enheter](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). För att skydda ditt konto när du använder äldre, bör du använda starka lösenord i din katalog. Kolla in [Azure AD-lösenordsskydd](../authentication/concept-password-ban-bad.md) att bannlysa svaga lösenord i din katalog.

Office 2010 har inte stöd för modern autentisering. Du måste uppgradera alla användare med Office 2010 till en nyare version av Office. Vi rekommenderar att uppgradera till Office 2016 eller senare, när den blockerar äldre autentisering som standard.

Om du använder MacOS, rekommenderar vi att uppgradera till Office för Mac 2016 eller senare. Om du använder den inbyggda e-postklienten, behöver du ha MacOS version 10.14 eller senare på alla enheter.

### <a name="step-3-exchange-and-sharepoint"></a>Steg 3: Exchange och SharePoint

För Windows-baserade Outlook-klienter att använda modern autentisering, måste Exchange Online vara modern autentisering aktiverad. Om modern autentisering är inaktiverat för Exchange Online, Windows-baserade Outlook-klienter som har stöd för modern autentisering (Outlook 2013 eller senare) använder grundläggande autentisering för att ansluta till Exchange Online-postlådor.

SharePoint Online är aktiverat för modern autentisering standard. Modern autentisering är aktiverat som standard i Exchange Online för kataloger som skapats efter den 1 augusti 2017. Men om du tidigare hade inaktiverats modern autentisering eller om du använder en katalog som skapades före detta datum, följer du stegen i följande artikel till [aktivera modern autentisering i Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Steg 4: Skype för företag

För att förhindra att äldre autentiseringsbegäranden av Skype för företag, är det nödvändigt att aktivera modern autentisering för Skype för Business Online. Modern autentisering för Skype för företag är aktiverat som standard för kataloger som skapats efter den 1 augusti 2017.

Om du vill aktivera modern autentisering i Skype för företag, föreslår vi att du övergången till Microsoft Teams, som har stöd för modern autentisering som standard. Om du inte kan tr just nu, kommer du måste aktivera modern autentisering för Skype för Business Online så att Skype för företag-klienter som startar med modern autentisering. Följ anvisningarna i artikeln [Skype för företag-topologier som stöds med Modern autentisering](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), steg att aktivera Modern autentisering för Skype för företag.

Förutom att aktivera modern autentisering för Skype för Business Online, rekommenderar vi modern authentication aktiveras för Exchange Online när du aktiverar modern autentisering för Skype för företag. Den här processen hjälper synkronisera tillståndet för modern autentisering i Exchange Online och Skype för företag – online och förhindrar flera inloggning anvisningarna för Skype för företag-klienter.

### <a name="step-5-using-mobile-devices"></a>Steg 5: Med hjälp av mobila enheter

Program på din mobila enhet måste blockera äldre autentisering samt. Vi rekommenderar att du använder Outlook för mobila enheter. Outlook Mobile stöder modern autentisering som standard och att uppfylla andra skyddsprinciper för MFA-baslinje.

För att kunna använda den interna iOS e-postklienten kommer du behöva köra iOS version 11.0 eller senare för att se till att e-postklient har uppdaterats för att blockera äldre autentisering.

### <a name="step-6-on-premises-clients"></a>Steg 6: Lokala klienter

Om du är en hybrid-kund som använder Exchange Server lokalt och Skype för företag lokalt kan måste båda tjänsterna uppdateras för att aktivera modern autentisering. När du använder modern autentisering i en hybridmiljö, du fortfarande autentisera användare på plats. Artikeln för att auktorisera deras åtkomst till resurser (filer eller e-postmeddelanden) ändringar.

Innan du kan börja med att aktivera modern autentisering på lokala måste du uppfylla theIf du uppfyller kraven, nu är du redo att aktivera modern autentisering på lokala.

Steg för att aktivera modern autentisering finns i följande artiklar:

* [Så här konfigurerar du Exchange Server lokalt för att använda Modern Hybridautentisering](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Hur du använder Modern autentisering (ADAL) med Skype för företag](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Aktivera baslinjeprincip för

Principen **baslinjeprincip: Blockera äldre authentication (förhandsversion)** är förkonfigurerad och visas högst upp när du navigerar till bladet för villkorlig åtkomst i Azure-portalen.

Aktivera den här principen och skydda din organisation:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj i listan med principer, **baslinjeprincip: Blockera äldre authentication (förhandsversion)** .
1. Ange **aktiverar principen** till **Använd principen omedelbart**.
1. Klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Protection principer för villkorlig åtkomst baslinje](concept-baseline-protection.md)
* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
