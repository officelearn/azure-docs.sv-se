---
title: Inte att logga in till åtkomstpanelens webbplats | Microsoft Docs
description: Vägledning för att felsöka problem som kan uppstå när du försöker logga in och Använd åtkomstpanelen
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviwer: japere,asteen
ms.openlocfilehash: 34a4da2c14b84b9e539fc2d3e755a523cf3574c9
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847245"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Inte att logga in till åtkomstpanelens webbplats

Åtkomstpanelen är en webbaserad portal som gör att en användare som har ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program som Azure AD-administratör har gett dem åtkomst till. En användare som har Azure AD-versioner kan också använda självbetjäning och apphanteringsfunktionerna via åtkomstpanelen. Åtkomstpanelen är separat från Azure-portalen och kräver inte användare har en Azure-prenumeration.

Användare kan logga in på åtkomstpanelen om de har ett arbets- eller skolkonto konto i Azure AD.

-   Användare kan autentiseras av Azure AD direkt.

-   Användare kan autentiseras med hjälp av Active Directory Federation Services (AD FS).

-   Användare kan autentiseras av Windows Server Active Directory.

Om en användare har en prenumeration på Azure eller Office 365 och har använt Azure-portalen eller ett Office 365-program, kommer de att kunna använda åtkomstpanelen sömlöst utan att behöva logga in igen. Användare som inte autentiseras uppmanas att logga in med användarnamn och lösenord för sitt konto i Azure AD. Om organisationen har konfigurerat federation, räcker att skriva användarnamnet.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först 

-   Kontrollera att användaren loggar in till den **korrigera URL: en**: <https://myapps.microsoft.com>

-   Kontrollera att användarens webbläsare har lagt till URL: en till dess **betrodda platser**

-   Kontrollera att användarkontot är **aktiverat** för inloggningar.

-   Kontrollera att användarkontot är **inte låst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller glömt.**

-   Se till att **Multifaktorautentisering** inte blockerar åtkomst.

-   Se till att en **princip för villkorlig åtkomst** eller **Identity Protection** principen blockerar åtkomst.

-   Se till att en användares **autentiseringskontakt** är uppdaterad så att Multifaktorautentisering eller villkorlig åtkomst-principer ska framtvingas.

-   Se till att även försök rensa cookies i webbläsaren och försök att logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Uppfyller Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomstpanelen, måste tillägget åtkomstpanelen installeras i användarens webbläsare. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Microsoft Edge i Windows 10 Anniversary Edition eller senare 

-   Chrome--På Windows 7 eller senare, och i Mac OS X eller senare

-   Firefox 26.0 eller senare, på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare


## <a name="problems-with-the-users-account"></a>Problem med användarkontot

Åtkomst till åtkomstpanelen blockeras på grund av ett problem med användarens konto. Följande är några sätt du kan felsöka och lösa problem med användare och deras kontoinställningar:

-   [Kontrollera om ett användarkonto i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrollera status för en användare](#check-a-users-account-status)

-   [Återställa en användares lösenord](#reset-a-users-password)

-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)

-   [Kontrollera status för en användares multifaktorautentisering](#check-a-users-multi-factor-authentication-status)

-   [Kontrollera en användares information](#check-a-users-authentication-contact-info)

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses)

-   [Tilldela en användare en licens](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrollera om ett användarkonto i Azure Active Directory

Följ dessa steg för att kontrollera om det finns en användares konto:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Kontrollera egenskaperna för objektet att se till att de ser ut som du förväntar dig och inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrollera status för en användare

Följ dessa steg om du vill kontrollera status för en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **profil**.

8.  Under **inställningar** kontrollerar du att **blockera inloggning** är inställd på **nr**.

### <a name="reset-a-users-password"></a>Återställa en användares lösenord

Följ dessa steg om du vill återställa en användares lösenord:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på den **Återställ lösenord** längst upp i fönstret användare.

8.  Klicka på den **Återställ lösenord** knappen på den **Återställ lösenord** fönstret som visas.

9.  Kopiera den **tillfälligt lösenord** eller **ange ett nytt lösenord** för användaren.

10. Kommunicera det nya lösenordet för användaren, de krävas för att ändra lösenordet under deras nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Om du vill aktivera lösenordsåterställning via självbetjäning, gör följande distribution:

-   [Användarna kan återställa sina Azure Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Användarna kan återställa eller ändra sina lokala Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrollera status för en användares multifaktorautentisering

Följ dessa steg om du vill kontrollera status för multifaktorautentisering för en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  Klicka på den **Multifaktorautentisering** längst upp i fönstret.

7.  När den **Multi-Factor Authentication-administrationsportalen** belastning, se till att du är på den **användare** fliken.

8.  Hitta användaren i listan över användare av sökning, filtrering och sortering.

9.  Välj användaren i listan över användare och **aktivera**, **inaktivera**, eller **tvinga** multifaktorautentisering efter behov.

   >[!NOTE]
   >Om en användare finns i en **tvingande** tillstånd, kan du ange dem till **inaktiverad** tillfälligt och låter dem tillbaka till sitt konto. När de är tillbaka i kan du ändra deras tillstånd att **aktiverad** igen för att kräva att Omregistrera kontaktinformationen under deras nästa inloggning. Du kan också följa stegen i den [Kontrollera en användares autentiseringskontakt](#check-a-users-authentication-contact-info) verifiera eller ange informationen för dessa.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Kontrollera en användares information

Följ dessa steg om du vill kontrollera en användares information används för multifaktorautentisering, villkorlig åtkomst, identitetsskydd och återställning av lösenord:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **profil**.

8.  Rulla ned till **autentiseringskontakt**.

9.  **Granska** data som registrerats för användaren och uppdatera efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Följ dessa steg om du vill kontrollera en användares gruppmedlemskap:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper** att se vilka grupper användaren är medlem i.

### <a name="check-a-users-assigned-licenses"></a>Kontrollera en användares tilldelade licenser

Följ dessa steg om du vill kontrollera en användares tilldelade licenser:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser du för närvarande har tilldelats.

### <a name="assign-a-user-a-license"></a>Tilldela en användare en licens 

Följ dessa steg om du vill tilldela en licens till en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser du för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfritt** klickar du på den **tilldelningsalternativ** objektet om du vill tilldela detaljerat produkter. Klicka på **Ok** när det är klart.

11. Klicka på den **tilldela** knappen för att tilldela dessa licenser till den här användaren.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om de här felsökningsstegen inte löser problemet

Öppna ett supportärende med följande information om de är tillgängliga:

-   Korrelations-ID för fel

-   UPN (användarens e-postadress)

-   Klient-ID:t

-   Typ av webbläsare

-   Tidszon och tid/tidsramen under fel inträffar

-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
