---
title: "Problem som loggar in på webbplatsen åtkomst panelen | Microsoft Docs"
description: "Vägledning för att felsöka problem som kan uppstå när du försöker logga in att använda åtkomstpanelen"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviwer: japere
ms.openlocfilehash: 83c3a893fe18f2de2acc4fdbc9b048c92cc71d15
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Logga in på webbplatsen åtkomst panelen problem

Åtkomstpanelen är en webbaserad portal som gör att en användare som har ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program som Azure AD-administratör har gett dem åtkomst till. En användare med Azure AD-versioner kan också använda självbetjäning och funktioner för hantering av appen via åtkomstpanelen. Åtkomstpanelen är separat från Azure portal och kräver inte att användare ska ha en Azure-prenumeration.

Användare kan logga in på åtkomstpanelen om de har ett arbets- eller skolkonto konto i Azure AD.

-   Användare kan autentiseras av Azure AD direkt.

-   Användare kan autentiseras med hjälp av Active Directory Federation Services (AD FS).

-   Användare kan autentiseras av Windows Server Active Directory.

Om en användare har en prenumeration på Azure eller Office 365 och har använt Azure-portalen eller ett Office 365-program, kommer de att kunna använda åtkomstpanelen sömlöst utan att behöva logga in igen. Användare som inte autentiseras uppmanas att logga in med användarnamn och lösenord för sitt konto i Azure AD. Om organisationen har konfigurerat federation, räcker att ange användarnamnet.

## <a name="general-issues-to-check-first"></a>Allmänna problem med att kontrollera först 

-   Kontrollera att användaren loggar in på den **korrigera URL**: <https://myapps.microsoft.com>

-   Kontrollera att användarens webbläsare har lagt till URL: en till dess **tillförlitliga platser**

-   Kontrollera att användarkontot är **aktiverat** för inloggningar.

-   Kontrollera att användarkontot är **inte låst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller har glömt.**

-   Kontrollera att **Multifaktorautentisering** inte blockerar åtkomst.

-   Kontrollera att en **principen för villkorlig åtkomst** eller **identitetsskydd** principen inte blockerar åtkomst.

-   Se till att en användares **autentisering kontaktinformation** är uppdaterad så att Multi-Factor Authentication eller villkorlig åtkomst principer som ska framtvingas.

-   Se till att även försök att rensa cookies i webbläsaren och försök att logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Möte Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du vill använda lösenordsbaserade enkel inloggning (SSO) på åtkomstpanelen måste åtkomstpanelen tillägget installeras i webbläsaren. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

Användarens webbläsare kan vara för lösenordsbaserad enkel inloggning:

-   Internet Explorer 8, 9, 10, 11--på Windows 7 eller senare

-   Kanten på Windows 10 årsdagar Edition eller senare 

-   Chrome--På Windows 7 eller senare, och i MacOS X eller senare

-   Firefox 26.0 eller senare--på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare


## <a name="problems-with-the-users-account"></a>Problem med användarkontot

Åtkomst till åtkomstpanelen blockeras på grund av ett problem med användarens konto. Följande är några metoder som du kan felsöka och lösa problem med användare och deras kontoinställningar:

-   [Kontrollera om det finns ett konto i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrollera status för en användare](#check-a-users-account-status)

-   [Återställa en användares lösenord](#reset-a-users-password)

-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)

-   [Kontrollera status för multifaktorautentisering för en användare](#check-a-users-multi-factor-authentication-status)

-   [Kontrollera en användares kontaktinformation för autentisering](#check-a-users-authentication-contact-info)

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses)

-   [Tilldela en användare en licens](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrollera om det finns ett konto i Azure Active Directory

Följ dessa steg för att kontrollera om det finns ett användarkonto:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Kontrollera egenskaperna för användarobjektet för att säkerställa att de ser ut som förväntat och inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrollera status för en användare

Följ dessa steg för att kontrollera status för en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **profil**.

8.  Under **inställningar** se till att **Block logga in** är inställd på **nr**.

### <a name="reset-a-users-password"></a>Återställa en användares lösenord

Följ dessa steg om du vill återställa en användares lösenord:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på den **Återställ lösenord** längst upp i fönstret för användaren.

8.  Klicka på den **Återställ lösenord** knappen på den **Återställ lösenord** fönstret som visas.

9.  Kopiera den **tillfälligt lösenord** eller **ange ett nytt lösenord** för användaren.

10. Meddela detta nya lösenord för användaren, de krävas för att ändra lösenordet under deras nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Om du vill aktivera lösenordsåterställning via självbetjäning, gör följande distribution:

-   [Användarna kan återställa sina Azure Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Användarna kan återställa eller ändra sina lokala Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrollera status för multifaktorautentisering för en användare

Följ dessa steg om du vill kontrollera status för multifaktorautentisering för en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  Klicka på den **Multifaktorautentisering** längst upp i fönstret.

7.  En gång i **Multi-Factor Authentication-administrationsportalen** belastning, kontrollera att du är på den **användare** fliken.

8.  Hitta användaren i listan över användare genom att söka, filtrera eller sortera.

9.  Välj användaren i listan över användare och **aktivera**, **inaktivera**, eller **framtvinga** multifaktorautentisering efter behov.

   >[!NOTE]
   >Om en användare finns i en **tvingande** tillstånd, så kan du ange dem **inaktiverad** tillfälligt och låter dem tillbaka till deras konto. När de är tillbaka i sedan kan du ändra tillståndet till **aktiverad** igen för att kräva att registrera kontaktuppgifter under deras nästa inloggning. Alternativt, följer du stegen i den [Kontrollera en användares autentisering kontaktinformation](#check-a-users-authentication-contact-info) att kontrollera eller ange informationen för dessa.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Kontrollera en användares kontaktinformation för autentisering

Följ dessa steg om du vill kontrollera en användares autentisering kontaktinformation som användes för multifaktorautentisering, villkorlig åtkomst, identitetsskydd och återställning av lösenord:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **profil**.

8.  Rulla ned till **autentisering kontaktinformation**.

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

7.  Klicka på **licenser** finns som licenser användaren för närvarande har tilldelats.

### <a name="assign-a-user-a-license"></a>Tilldela en användare en licens 

Följ dessa steg om du vill tilldela en licens till en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser användaren för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfria** klickar du på den **tilldelning alternativ** objektet om du vill tilldela granularly produkter. Klicka på **Ok** när detta har slutförts.

11. Klicka på den **tilldela** för att tilldela licenserna till den här användaren.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om felsökningen inte löser problemet

Öppna ett supportärende med följande information om de är tillgängliga:

-   Fel-ID för korrelation

-   UPN (användarens e-postadress)

-   Klient-ID:t

-   Typ av webbläsare

-   Tidszon och tid/tidsperioden under fel inträffar

-   Fiddler spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
