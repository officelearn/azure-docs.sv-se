---
title: Problem med att logga in på åtkomst panelens webbplats | Microsoft Docs
description: Vägledning för att felsöka problem som du kan stöta på när du försöker logga in för att använda åtkomst panelen
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760821"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem med att logga in på åtkomst panelens webbplats

Åtkomst panelen är en webbaserad portal som gör det möjligt för en användare som har ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. En användare som har Azure AD-versioner kan också använda funktioner för självbetjänings grupp och program hantering via åtkomst panelen. Åtkomst panelen är separat från Azure Portal och kräver inte att användare har en Azure-prenumeration.

Användare kan logga in på åtkomst panelen om de har ett arbets-eller skol konto i Azure AD.

-   Användare kan autentiseras av Azure AD direkt.

-   Användare kan autentiseras med hjälp av Active Directory Federation Services (AD FS) (AD FS).

-   Användare kan autentiseras av Windows Server Active Directory.

Om en användare har en prenumeration på Azure eller Office 365 och har använt Azure Portal eller ett Office 365-program, kan de använda åtkomst panelen sömlöst utan att behöva logga in igen. Användare som inte är autentiserade uppmanas att logga in med användar namnet och lösen ordet för sitt konto i Azure AD. Om organisationen har konfigurerat Federation räcker det att ange användar namnet.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först 

-   Kontrol lera att användaren loggar in på **rätt URL**:<https://myapps.microsoft.com>

-   Kontrol lera att användarens webbläsare har lagt till URL: en till dess **Betrodda platser**

-   Kontrol lera att användar kontot är **aktiverat** för inloggningar.

-   Kontrol lera att användarens konto **inte är utelåst.**

-   Kontrol lera att användarens **lösen ord inte har upphört att gälla eller är bortglömt.**

-   Kontrol lera att **Multi-Factor Authentication** inte blockerar användar åtkomsten.

-   Se till att en **princip för villkorlig åtkomst** eller **identitets skydds** princip inte blockerar användar åtkomst.

-   Se till att en användares **kontakt information för autentisering** är uppdaterad för att tillåta att Multi-Factor Authentication eller villkorliga åtkomst principer tillämpas.

-   Se till att du också försöker rensa webbläsarens cookies och försöka logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Mötes webbläsarens krav på åtkomst panelen

Åtkomst panelen kräver en webbläsare som stöder Java Script och har CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomst panelen måste åtkomst panels tillägget vara installerat i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som har kon figurer ATS för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan användarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Microsoft Edge på Windows 10-jubileums version eller senare 

-   Chrome – på Windows 7 eller senare och på MacOS X eller senare

-   Firefox 26,0 eller senare –-på Windows XP SP2 eller senare och på Mac OS X 10,6 eller senare


## <a name="problems-with-the-users-account"></a>Problem med användarens konto

Åtkomst till åtkomst panelen kan blockeras på grund av ett problem med användarens konto. Här följer några exempel på hur du kan felsöka och lösa problem med användare och deras konto inställningar:

-   [Kontrol lera om ett användar konto finns i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrol lera en användares konto status](#check-a-users-account-status)

-   [Återställa en användares lösen ord](#reset-a-users-password)

-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)

-   [Kontrol lera en användares Multi-Factor Authentication-status](#check-a-users-multi-factor-authentication-status)

-   [Kontrol lera en användares kontakt information för autentisering](#check-a-users-authentication-contact-info)

-   [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)

-   [Kontrol lera en användares tilldelade licenser](#check-a-users-assigned-licenses)

-   [Tilldela en licens för en användare](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrol lera om ett användar konto finns i Azure Active Directory

Följ dessa steg om du vill kontrol lera om det finns ett användar konto:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Kontrol lera egenskaperna för användarobjektet och se till att de ser ut som du förväntar dig och att inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrol lera en användares konto status

Följ dessa steg om du vill kontrol lera en användares konto status:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **profil**.

8.  Under **Inställningar** kontrollerar du att **blockera inloggning** är inställt på **Nej**.

### <a name="reset-a-users-password"></a>Återställa en användares lösen ord

Följ dessa steg om du vill återställa en användares lösen ord:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på knappen **Återställ lösen ord** överst i fönstret användare.

8.  Klicka på knappen **Återställ lösen ord** i rutan **Återställ lösen ord** som visas.

9.  Kopiera det **tillfälliga lösen ordet** eller **Ange ett nytt lösen ord** för användaren.

10. Skicka det nya lösen ordet till användaren, de måste ändra lösen ordet vid nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Följ dessa distributions steg för att aktivera självbetjäning för återställning av lösen ord:

-   [Gör det möjligt för användare att återställa sina Azure Active Directory lösen ord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Gör det möjligt för användare att återställa eller ändra sina Active Directory lokala lösen ord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrol lera en användares Multi-Factor Authentication-status

Följ dessa steg om du vill kontrol lera en användares Multi-Factor Authentication-status:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. Klicka på knappen **Multi-Factor Authentication** överst i fönstret.

7. Se till att du är på fliken **användare** när **Multi-Factor Authentication administrations Portal** har lästs in.

8. Hitta användaren i listan med användare genom att söka, filtrera eller sortera.

9. Välj användaren i listan med användare och **Aktivera**, **inaktivera**eller **tillämpa** Multi-Factor Authentication som önskade.

   >[!NOTE]
   >Om en användare är i **tvingande** tillstånd kan du ange att de tillfälligt **inaktive ras** för att låta dem gå tillbaka till sitt konto. När du har loggat in igen kan du ändra deras status till **aktive rad** för att kräva att de registrerar sin kontakt information igen under nästa inloggning. Du kan också följa stegen i [kontakt informationen för en användares autentisering](#check-a-users-authentication-contact-info) för att verifiera eller ange data för dem.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Kontrol lera en användares kontakt information för autentisering

Följ dessa steg om du vill kontrol lera en användares kontaktinfo kontakt information som används för Multi-Factor Authentication, villkorlig åtkomst, identitets skydd och lösen ords återställning:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **profil**.

8.  Rulla ned till **autentisering kontakt information**.

9.  **Granska** de data som har registrerats för användaren och uppdatera dem efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrol lera en användares grupp medlemskap

Följ dessa steg om du vill kontrol lera en användares grupp medlemskap:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **grupper** för att se vilka grupper användaren är medlem i.

### <a name="check-a-users-assigned-licenses"></a>Kontrol lera en användares tilldelade licenser

Följ dessa steg om du vill kontrol lera en användares tilldelade licenser:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

### <a name="assign-a-user-a-license"></a>Tilldela en licens för en användare 

Följ dessa steg om du vill tilldela en licens till en användare:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

8.  Klicka på knappen **tilldela** .

9.  Välj **en eller flera produkter** i listan med tillgängliga produkter.

10. **Valfritt** Klicka på objektet **tilldelnings alternativ** om du vill tilldela produkter i detalj. Klicka på **OK** när det är klart.

11. Klicka på knappen **tilldela** för att tilldela dessa licenser till den här användaren.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om de här fel söknings stegen inte löser problemet

öppna ett support ärende med följande information om den är tillgänglig:

-   Korrelations fel-ID

-   UPN (användarens e-postadress)

-   Klientorganisations-ID

-   Typ av webbläsare

-   Tidszon och tid/tidsram vid fel inträffar

-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
