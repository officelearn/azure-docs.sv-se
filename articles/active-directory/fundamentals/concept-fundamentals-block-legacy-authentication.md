---
title: Blockera äldre autentiseringsprotokoll i Azure AD
description: Lär dig hur och varför organisationer bör blockera äldre autentiseringsprotokoll
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ce0233fdefb8360376e94c0baafabe4c62ced7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309197"
---
# <a name="blocking-legacy-authentication"></a>Blockera äldre autentisering
 
För att ge användarna enkel åtkomst till dina molnappar stöder Azure Active Directory (Azure AD) ett brett utbud av autentiseringsprotokoll, inklusive äldre autentisering. Äldre autentisering är en term som refererar till en autentiseringsbegäran som gjorts av:

- Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klienten)
- Alla klienter som använder äldre e-postprotokoll som IMAP/SMTP/POP3

Idag kommer majoriteten av alla komprometterande inloggningsförsök från äldre autentisering. Äldre autentisering stöder inte MFA (Multifaktorautentisering). Även om du har en MFA-princip aktiverad på din katalog kan en dålig aktör autentisera med ett äldre protokoll och kringgå MFA. Det bästa sättet att skydda ditt konto från begäranden om skadlig autentisering som görs av äldre protokoll är att blockera dessa försök helt och hållet.

## <a name="identify-legacy-authentication-use"></a>Identifiera äldre autentiseringsanvändning

Innan du kan blockera äldre autentisering i katalogen måste du först förstå om användarna har appar som använder äldre autentisering och hur det påverkar din övergripande katalog. Azure AD-inloggningsloggar kan användas för att förstå om du använder äldre autentisering.

1. Navigera till **Azure Portal** >Azure Active **Directory-inloggningar** **Azure Active Directory** >.
1. Lägg till kolumnen **Klientapp** om den inte visas genom att klicka på **Columns** > **Client App**.
1. Filtrera efter **klientapp**  > kontrollera alla äldre **autentiseringsklienter** som presenteras.
1. Filtrera efter > **statuslyckades**. **Status** 
1. Expandera datumintervallet om det behövs med hjälp av **filtret Datum.**

Filtrering visar bara lyckade inloggningsförsök som har gjorts av de valda äldre autentiseringsprotokollen. Om du klickar på varje enskilt inloggningsförsök visas ytterligare information. Kolumnen Klientapp eller fältet Klientapp under fliken Grundläggande information efter att ha valt en enskild datarad anger vilket äldre autentiseringsprotokoll som användes. Dessa loggar anger vilka användare som fortfarande är beroende av äldre autentisering och vilka program som använder äldre protokoll för att göra autentiseringsbegäranden. För användare som inte visas i dessa loggar och som inte har äldre autentisering implementerar du en princip för villkorlig åtkomst eller aktiverar baslinjeprincipen: blockera äldre autentisering endast för dessa användare.

## <a name="moving-away-from-legacy-authentication"></a>Gå bort från äldre autentisering 

När du har en bättre uppfattning om vem som använder äldre autentisering i din katalog och vilka program som är beroende av den, är nästa steg att uppgradera användarna för att använda modern autentisering. Modern autentisering är en metod för identitetshantering som erbjuder säkrare användarautentisering och auktorisering. Om du har en MFA-princip på plats i katalogen säkerställer modern autentisering att användaren uppmanas till MFA när det behövs. Det är det säkrare alternativet till äldre autentiseringsprotokoll.

Det här avsnittet innehåller en steg-för-steg-översikt över hur du uppdaterar din miljö till modern autentisering. Läs igenom stegen nedan innan du aktiverar en äldre autentiseringsblockeringsprincip i organisationen.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Steg 1: Aktivera modern autentisering i katalogen

Det första steget för att aktivera modern autentisering är att se till att din katalog stöder modern autentisering. Modern autentisering aktiveras som standard för kataloger som skapats den 1 augusti 2017 eller senare. Om katalogen skapades före det här datumet måste du manuellt aktivera modern autentisering för katalogen med hjälp av följande steg:

1. Kontrollera om katalogen redan stöder modern `Get-CsOAuthConfiguration` autentisering genom att köras från [PowerShell-modulen Skype för företag – Online](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Om kommandot returnerar `OAuthServers` en tom egenskap inaktiveras modern autentisering. Uppdatera inställningen för att `Set-CsOAuthConfiguration`aktivera modern autentisering med . Om `OAuthServers` din egendom innehåller en post är det bra att gå.

Var noga med att slutföra det här steget innan du går vidare. Det är viktigt att katalogkonfigurationerna ändras först eftersom de dikterar vilket protokoll som ska användas av alla Office-klienter. Även om du använder Office-klienter som stöder modern autentisering används som standard äldre protokoll om modern autentisering är inaktiverad i katalogen.

### <a name="step-2-office-applications"></a>Steg 2: Office-program

När du har aktiverat modern autentisering i katalogen kan du börja uppdatera program genom att aktivera modern autentisering för Office-klienter. Office 2016 eller senare klienter stöder modern autentisering som standard. Inga extra steg krävs.

Om du använder Office 2013 Windows-klienter eller äldre rekommenderar vi att du uppgraderar till Office 2016 eller senare. Även efter att ha slutfört föregående steg för att aktivera modern autentisering i katalogen fortsätter de äldre Office-programmen att använda äldre autentiseringsprotokoll. Om du använder Office 2013-klienter och inte omedelbart kan uppgradera till Office 2016 eller senare följer du stegen i följande artikel för [Att aktivera modern autentisering för Office 2013 på Windows-enheter](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). För att skydda ditt konto medan du använder äldre autentisering rekommenderar vi att du använder starka lösenord i katalogen. Kolla in [Azure AD lösenordsskydd](../authentication/concept-password-ban-bad.md) för att förbjuda svaga lösenord i hela katalogen.

Office 2010 stöder inte modern autentisering. Du måste uppgradera alla användare med Office 2010 till en nyare version av Office. Vi rekommenderar att du uppgraderar till Office 2016 eller senare, eftersom den blockerar äldre autentisering som standard.

Om du använder macOS rekommenderar vi att du uppgraderar till Office för Mac 2016 eller senare. Om du använder den inbyggda e-postklienten måste du ha macOS version 10.14 eller senare på alla enheter.

### <a name="step-3-exchange-and-sharepoint"></a>Steg 3: Exchange och SharePoint

För att Windows-baserade Outlook-klienter ska kunna använda modern autentisering måste Exchange Online också vara modern autentisering aktiverat. Om modern autentisering är inaktiverad för Exchange Online använder Windows-baserade Outlook-klienter som stöder modern autentisering (Outlook 2013 eller senare) grundläggande autentisering för att ansluta till Exchange Online-postlådor.

SharePoint Online är aktiverat för modern autentiseringsstandard. För kataloger som skapats efter den 1 augusti 2017 aktiveras modern autentisering som standard i Exchange Online. Om du tidigare har inaktiverat modern autentisering eller använder en katalog som skapats före det här datumet följer du stegen i följande artikel för att [aktivera modern autentisering i Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Steg 4: Skype för företag

För att förhindra äldre autentiseringsbegäranden från Skype för företag är det nödvändigt att aktivera modern autentisering för Skype för företag – Online. För kataloger som skapats efter den 1 augusti 2017 aktiveras modern autentisering för Skype för företag som standard.

Vi föreslår att du övergår till Microsoft Teams, som stöder modern autentisering som standard. Men om du inte kan migrera just nu måste du aktivera modern autentisering för Skype för företag – Online så att Skype för företag-klienter börjar använda modern autentisering. Följ stegen i den här artikeln [Skype för företag-topologier som stöds med modern autentisering](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)för att aktivera modern autentisering för Skype för företag.

Förutom att aktivera modern autentisering för Skype för företag – Online rekommenderar vi att du aktiverar modern autentisering för Exchange Online när du aktiverar modern autentisering för Skype för företag. Den här processen hjälper till att synkronisera tillståndet för modern autentisering i Exchange Online och Skype för företag online och förhindrar flera inloggningsan följande för Skype för företag-klienter.

### <a name="step-5-using-mobile-devices"></a>Steg 5: Använda mobila enheter

Program på din mobila enhet måste också blockera äldre autentisering. Vi rekommenderar att du använder Outlook för mobilen. Outlook för mobil stöder modern autentisering som standard och uppfyller andra principer för grundläggande skydd för MFA.Outlook for Mobile supports modern authentication by default and will satisfy other MFA baseline protection policies.

För att kunna använda den inbyggda iOS-e-postklienten måste du köra iOS version 11.0 eller senare för att säkerställa att e-postklienten har uppdaterats för att blockera äldre autentisering.

### <a name="step-6-on-premises-clients"></a>Steg 6: Lokala kunder

Om du är en hybridkund som använder Exchange Server lokalt och skype för företag lokalt måste båda tjänsterna uppdateras för att aktivera modern autentisering. När du använder modern autentisering i en hybridmiljö autentiserar du fortfarande användare lokalt. Historien om att tillåta deras åtkomst till resurser (filer eller e-post) ändras.

Innan du kan börja aktivera modern autentisering lokalt ska du vara säker på att du har uppfyllt förutsättningarna. Du är nu redo att aktivera modern autentisering lokalt.

Steg för att aktivera modern autentisering finns i följande artiklar:

* [Konfigurera Exchange Server lokalt för att använda hybridmodern autentisering](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Så här använder du modern autentisering (ADAL) med Skype för företag](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Exchange Server lokalt för att använda hybridmodern autentisering](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Så här använder du modern autentisering (ADAL) med Skype för företag](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Blockera äldre autentisering](../conditional-access/block-legacy-authentication.md)
