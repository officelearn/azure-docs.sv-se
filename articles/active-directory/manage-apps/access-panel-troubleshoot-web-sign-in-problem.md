---
title: Problem med att logga in på webbplatsen för åtkomstpanelen | Microsoft-dokument
description: Vägledning för felsökning av problem som kan uppstå när du försöker logga in för att använda åtkomstpanelen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784313"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem med att logga in på åtkomstpanelens webbplats

Åtkomstpanelen är en webbaserad portal som gör det möjligt för en användare som har ett arbets- eller skolkonto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har gett dem åtkomst till. En användare som har Azure AD-versioner kan också använda funktioner för självbetjäningsgrupp och apphantering via åtkomstpanelen. Åtkomstpanelen är skild från Azure-portalen och kräver inte att användare har en Azure-prenumeration.

Användare kan logga in på åtkomstpanelen om de har ett arbets- eller skolkonto i Azure AD.

-   Användare kan autentiseras av Azure AD direkt.

-   Användare kan autentiseras med hjälp av AD FS (Active Directory Federation Services).

-   Användare kan autentiseras av Active Directory för Windows Server.

Om en användare har en prenumeration för Azure eller Office 365 och har använt Azure-portalen eller ett Office 365-program kan de använda åtkomstpanelen sömlöst utan att behöva logga in igen. Användare som inte autentiseras uppmanas att logga in med användarnamn och lösenord för sitt konto i Azure AD. Om organisationen har konfigurerat federationen är det tillräckligt att skriva användarnamnet.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först 

-   Kontrollera att användaren loggar in på **rätt webbadress:**<https://myapps.microsoft.com>

-   Kontrollera att användarens webbläsare har lagt till webbadressen på sina **betrodda webbplatser**

-   Kontrollera att användarens konto är **aktiverat** för inloggningar.

-   Kontrollera att användarens konto inte är **utelåst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller glömts bort.**

-   Kontrollera att **multifaktorautentisering** inte blockerar användaråtkomst.

-   Kontrollera att en **princip för villkorlig åtkomst** eller **identitetsskydd** inte blockerar användaråtkomst.

-   Kontrollera att en användares **autentiseringskontaktinformation** är uppdaterad så att principer för multifaktorautentisering eller villkorlig åtkomst kan tillämpas.

-   Se till att också försöka rensa webbläsarens cookies och försöka logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Uppfyller webbläsarkraven för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och har CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) på åtkomstpanelen måste tillägget för åtkomstpanelen installeras i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som är konfigurerat för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Microsoft Edge på Windows 10 Anniversary Edition eller senare 

-   Chrome – på Windows 7 eller senare och på MacOS X eller senare

-   Firefox 26.0 eller senare – på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare


## <a name="problems-with-the-users-account"></a>Problem med användarens konto

Åtkomst till åtkomstpanelen kan blockeras på grund av ett problem med användarens konto. Här följer några sätt att felsöka och lösa problem med användare och deras kontoinställningar:

-   [Kontrollera om det finns ett användarkonto i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrollera en användares kontostatus](#check-a-users-account-status)

-   [Återställa en användares lösenord](#reset-a-users-password)

-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)

-   [Kontrollera en användares multifaktorautentiseringsstatus](#check-a-users-multi-factor-authentication-status)

-   [Kontrollera en användares autentiseringskontaktinformation](#check-a-users-authentication-contact-info)

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses)

-   [Tilldela en användare en licens](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrollera om det finns ett användarkonto i Azure Active Directory

Så här kontrollerar du om det finns en användares konto:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  Kontrollera egenskaperna för användarobjektet för att vara säker på att de ser ut som förväntat och inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrollera en användares kontostatus

Så här kontrollerar du en användares kontostatus:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  klicka på **Profil**.

8.  Under **Inställningar** kontrollerar du att **Blockera inloggning** är inställt på **Nr**.

### <a name="reset-a-users-password"></a>Återställa en användares lösenord

Så här återställer du en användares lösenord:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på knappen **Återställ lösenord** högst upp i användarfönstret.

8.  Klicka på knappen **Återställ lösenord** i fönstret **Återställ lösenord** som visas.

9.  Kopiera det **tillfälliga lösenordet** eller ange ett **nytt lösenord** för användaren.

10. Kommunicera det här nya lösenordet till användaren, de måste ändra det här lösenordet under sin nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Så här aktiverar du återställning av lösenord för självbetjäning:

-   [Gör det möjligt för användare att återställa sina Azure Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Gör det möjligt för användare att återställa eller ändra sina lokala active directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrollera en användares multifaktorautentiseringsstatus

Så här kontrollerar du en användares multifaktorautentiseringsstatus:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. Klicka på knappen **Multifaktorautentisering** högst upp i fönstret.

7. När **Multifaktorautentiseringsadministrationsportalen** har läses in kontrollerar du att du är på fliken **Användare.**

8. Leta reda på användaren i listan över användare genom att söka, filtrera eller sortera.

9. Välj användaren i listan över användare och **Aktivera**, **Inaktivera**eller **Framtvinga** multifaktorautentisering som du vill.

   >[!NOTE]
   >Om en användare är i ett **verkställt** tillstånd kan du ställa in dem på **Inaktiverat** tillfälligt så att de kan komma tillbaka till sitt konto. När de är tillbaka i, kan du sedan ändra deras tillstånd till **Aktiverad** igen för att kräva att de ska registrera sina kontaktuppgifter under sin nästa inloggning. Du kan också följa stegen i [autentiseringsinformationen för kontrollera en användares autentiseringsinformation](#check-a-users-authentication-contact-info) för att verifiera eller ange dessa data.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Kontrollera en användares autentiseringskontaktinformation

Så här kontrollerar du en användares autentiseringsinformation som används för multifaktorautentisering, villkorlig åtkomst, identitetsskydd och återställning av lösenord:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  klicka på **Profil**.

8.  Bläddra ned till **Autentiseringskontaktinformation**.

9.  **Granska** data som registrerats för användaren och uppdatera efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Så här kontrollerar du en användares gruppmedlemskap:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Grupper** för att se vilka grupper användaren är medlem i.

### <a name="check-a-users-assigned-licenses"></a>Kontrollera en användares tilldelade licenser

Så här kontrollerar du en användares tilldelade licenser:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

### <a name="assign-a-user-a-license"></a>Tilldela en användare en licens 

Så här tilldelar du en licens till en användare:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

8.  klicka på knappen **Tilldela.**

9.  Välj **en eller flera produkter** i listan över tillgängliga produkter.

10. **Valfritt** klicka på **tilldelningsalternativsartikeln** om du vill tilldela produkter på ett detaljerat sätt. Klicka på **Ok** när detta är klart.

11. Klicka på knappen **Tilldela** om du vill tilldela dessa licenser till den här användaren.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om dessa felsökningssteg inte löser problemet

öppna en supportbiljett med följande information om sådan finns:

-   Korrelationsfel-ID

-   UPN (användarens e-postadress)

-   Klient-ID:t

-   Typ av webbläsare

-   Tidszon och tid/tidsram under fel inträffar

-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
