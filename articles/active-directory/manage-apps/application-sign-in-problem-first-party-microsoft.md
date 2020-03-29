---
title: Problem med att logga in på ett Microsoft-program | Microsoft-dokument
description: Felsöka vanliga problem när du loggar in på första parts Microsoft-program med Azure AD (till exempel Office 365)
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
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee8802aeb2a760e255ab4f5e99010dfedc45e0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108306"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problem med att logga in på ett Microsoft-program

Microsoft-program (som Office 365 Exchange, SharePoint, Yammer, etc.) tilldelas och hanteras lite annorlunda än SaaS-program från tredje part eller andra program som du integrerar med Azure AD för enkel inloggning.

Det finns tre huvudsakliga sätt att en användare kan få tillgång till ett Microsoft-publicerat program.

-   För program i Office 365 eller andra betalda sviter beviljas användare åtkomst via **licenstilldelning** antingen direkt till sitt användarkonto eller via en grupp med hjälp av vår gruppbaserade licenstilldelningsfunktion.

-   För program som Microsoft eller en tredje part publicerar fritt för vem som helst att använda, kan användare beviljas åtkomst via **användarens medgivande**. Det innebär att de loggar in på programmet med sitt Azure AD Work- eller School-konto och tillåter att de har åtkomst till vissa begränsade data för sitt konto.

-   För program som Microsoft eller en tredje part publicerar fritt för alla att använda, kan användare också beviljas åtkomst via **administratörsmedgivande**. Det innebär att en administratör har fastställt att programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörskonto och ger åtkomst till alla i organisationen.

Om du vill felsöka problemet börjar du med de [allmänna problemområdena med programåtkomst att tänka på](#general-problem-areas-with-application-access-to-consider) och läser sedan genomgången: Steg för felsökning av Åtkomst till Microsoft-program för att komma in på detaljer.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Allmänna problemområden med application access att överväga

Nedan följer en lista över de allmänna problemområden som du kan gå igenom om du har en uppfattning om var du ska börja, men vi rekommenderar att du läser genomgången för att komma igång snabbt: Genomgång: Steg för felsökning av Åtkomst till Microsoft-program.

-   [Problem med användarens konto](#problems-with-the-users-account)

-   [Problem med grupper](#problems-with-groups)

-   [Problem med principer för villkorlig åtkomst](#problems-with-conditional-access-policies)

-   [Problem med ansökan samtycke](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Åtgärder för felsökning av åtkomst till Microsoft-program

Följande är några vanliga problem folk stöter på när deras användare inte kan logga in på ett Microsoft-program.

- Allmänna problem att kontrollera först

  * Kontrollera att användaren loggar in på **rätt WEBBADRESS** och inte en lokal program-URL.

  * Kontrollera att användarens konto inte är **utelåst.**

  * Kontrollera att **användarens konto finns** i Azure Active Directory. [Kontrollera om det finns ett användarkonto i Azure Active Directory](#problems-with-the-users-account)

  * Kontrollera att användarens konto är **aktiverat** för [inloggningar.](#problems-with-the-users-account)

  * Kontrollera att användarens **lösenord inte har upphört att gälla eller glömts bort.** [Återställa en användares lösenord](#reset-a-users-password) eller [Aktivera återställning av lösenord för självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Kontrollera att **multifaktorautentisering** inte blockerar användaråtkomst. [Kontrollera en användares multifaktorautentiseringsstatus](#check-a-users-multi-factor-authentication-status) eller [Kontrollera en användares autentiseringsinformation](#check-a-users-authentication-contact-info)

  * Kontrollera att en **princip för villkorlig åtkomst** eller **identitetsskydd** inte blockerar användaråtkomst. [Kontrollera en specifik princip för villkorlig åtkomst](#problems-with-conditional-access-policies) eller Kontrollera ett visst programs princip för villkorlig [åtkomst](#check-a-specific-applications-conditional-access-policy) eller inaktivera en specifik princip för [villkorlig åtkomst](#disable-a-specific-conditional-access-policy)

  * Kontrollera att en användares **autentiseringskontaktinformation** är uppdaterad så att principer för multifaktorautentisering eller villkorlig åtkomst kan tillämpas. [Kontrollera en användares multifaktorautentiseringsstatus](#check-a-users-multi-factor-authentication-status) eller [Kontrollera en användares autentiseringsinformation](#check-a-users-authentication-contact-info)

- För **Microsoft** **Microsoft-program som kräver en licens** (som Office365) här är några specifika problem att kontrollera när du har uteslutit de allmänna problemen ovan:

  * Kontrollera att användaren eller har en **licens tilldelad.** [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses) eller [Kontrollera en grupps tilldelade licenser](#check-a-groups-assigned-licenses)

  * Om licensen **har tilldelats en** statisk **grupp**kontrollerar du att användaren är **medlem i** den gruppen. [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

  * Om licensen **har tilldelats en** dynamisk **grupp**kontrollerar du att den **dynamiska gruppregeln är korrekt inställd**. [Kontrollera medlemskapskriterierna för en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

  * Om licensen **har tilldelats en** dynamisk **grupp**kontrollerar du att den dynamiska gruppen har **bearbetat** medlemskapet och att **användaren är medlem** (det kan ta lite tid). [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

  *  När du har kontrollerat att licensen har tilldelats kontrollerar du att licensen **inte har upphört att gälla**.

  *  Kontrollera att licensen är **för det program** de använder.

- För **Microsoft** **Microsoft-program som inte kräver en licens,** här är några andra saker att kontrollera:

  * Om programmet begär **behörighet på användarnivå** (till exempel "Få åtkomst till den här användarens postlåda" kontrollerar du att användaren har loggat in på programmet och har utfört en **medgivandeåtgärd på användarnivå** för att låta programmet komma åt sina data.

  * Om programmet begär **behörigheter på administratörsnivå** (till exempel "Få åtkomst till alla användares postlådor" kontrollerar du att en global administratör har utfört en **medgivandeåtgärd på administratörsnivå för alla användare** i organisationen.

## <a name="problems-with-the-users-account"></a>Problem med användarens konto

Programåtkomst kan blockeras på grund av ett problem med en användare som har tilldelats programmet. Här följer några sätt att felsöka och lösa problem med användare och deras kontoinställningar:

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

10. Kommunicera det här nya lösenordet till användaren, de måste ändra det här lösenordet under nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Så här aktiverar du återställning av lösenord för självbetjäning nedan:

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

7. När **multifaktorautentiseringsadministrationsportalen** har läses in kontrollerar du att du är på fliken **Användare.**

8. Leta reda på användaren i listan över användare genom att söka, filtrera eller sortera.

9. Välj användaren i listan över användare och **Aktivera**, **Inaktivera**eller **Framtvinga** multifaktorautentisering som du vill.

   * **Om**en användare är i ett **verkställt** tillstånd kan du ställa in dem på **Inaktiverat** tillfälligt så att de kan komma tillbaka till sitt konto. När de är tillbaka i, kan du sedan ändra deras tillstånd till **Aktiverad** igen för att kräva att de ska registrera sina kontaktuppgifter under sin nästa inloggning. Du kan också följa stegen i [autentiseringsinformationen för kontrollera en användares autentiseringsinformation](#check-a-users-authentication-contact-info) för att verifiera eller ange dessa data.

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

## <a name="problems-with-groups"></a>Problem med grupper

Programåtkomst kan blockeras på grund av ett problem med en grupp som har tilldelats programmet. Här följer några sätt att felsöka och lösa problem med grupper och gruppmedlemskap:

-   [Kontrollera en grupps medlemskap](#check-a-groups-membership)

-   [Kontrollera medlemskapskriterierna för en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

-   [Kontrollera en grupps tilldelade licenser](#check-a-groups-assigned-licenses)

-   [Bearbeta om en grupps licenser](#reprocess-a-groups-licenses)

-   [Tilldela en grupp en licens](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrollera en grupps medlemskap

Så här kontrollerar du en grupps medlemskap:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  Klicka på **Alla grupper**.

6.  **Sök efter** den grupp du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Medlemmar** om du vill granska listan över användare som tilldelats den här gruppen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Kontrollera medlemskapskriterierna för en dynamisk grupp 

Så här kontrollerar du medlemskapskriterierna för en dynamisk grupp:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  Klicka på **Alla grupper**.

6.  **Sök efter** den grupp du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Regler för dynamiskt medlemskap.**

8.  Granska den **enkla** eller **avancerade** regeln som definierats för den här gruppen och se till att användaren som du vill ska vara medlem i den här gruppen uppfyller dessa kriterier.

### <a name="check-a-groups-assigned-licenses"></a>Kontrollera en grupps tilldelade licenser

Så här kontrollerar du en grupps tilldelade licenser:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  Klicka på **Alla grupper**.

6.  **Sök efter** den grupp du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Licenser** för att se vilka licenser gruppen har tilldelat.

### <a name="reprocess-a-groups-licenses"></a>Bearbeta om en grupps licenser

Så här bearbetar du om en grupps tilldelade licenser:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. Klicka på **Alla grupper**.

6. **Sök efter** den grupp du är intresserad av och **klicka på raden** för att välja.

7. Klicka på **Licenser** för att se vilka licenser gruppen har tilldelat.

8. Klicka på knappen **Bearbeta om** för att se till att de licenser som tilldelats den här gruppens medlemmar är uppdaterade. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

   >[!NOTE]
   >Om du vill göra detta snabbare bör du tillfälligt tilldela en licens till användaren direkt. [Tilldela en användare en licens](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Tilldela en grupp en licens

Så här tilldelar du en licens till en grupp:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. Klicka på **Alla grupper**.

6. **Sök efter** den grupp du är intresserad av och **klicka på raden** för att välja.

7. Klicka på **Licenser** för att se vilka licenser gruppen har tilldelat.

8. klicka på knappen **Tilldela.**

9. Välj **en eller flera produkter** i listan över tillgängliga produkter.

10. **Valfritt** klicka på **tilldelningsalternativsartikeln** om du vill tilldela produkter på ett detaljerat sätt. Klicka på **Ok** när detta är klart.

11. Klicka på knappen **Tilldela** om du vill tilldela dessa licenser till den här gruppen. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

    >[!NOTE]
    >Om du vill göra detta snabbare bör du tillfälligt tilldela en licens till användaren direkt. [Tilldela en användare en licens](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problem med principer för villkorlig åtkomst

### <a name="check-a-specific-conditional-access-policy"></a>Kontrollera en specifik princip för villkorlig åtkomst

Så här kontrollerar eller validerar du en princip för villkorlig åtkomst:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Företagsprogram** i navigeringsmenyn.

5. klicka på **navigeringsobjektet Villkorlig åtkomst.**

6. klicka på den policy du är intresserad av att inspektera.

7. Granska att det inte finns några specifika villkor, tilldelningar eller andra inställningar som kan blockera användaråtkomst.

   >[!NOTE]
   >Du kanske tillfälligt vill inaktivera den här principen för att säkerställa att den inte påverkar inloggningar. Det gör du genom att ange växlingsknappen **Aktivera princip** till **Nej** och klicka på knappen **Spara.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Kontrollera ett visst programs princip för villkorlig åtkomst

Så här kontrollerar eller validerar du ett enda programs för närvarande konfigurerade princip för villkorlig åtkomst:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Företagsprogram** i navigeringsmenyn.

5.  klicka på **Alla program**.

6.  Sök efter det program du är intresserad av, eller så försöker användaren logga in på efter programvisningsnamn eller program-ID.

     >[!NOTE]
     >Om du inte ser det program du letar efter klickar du på **knappen Filter** och utökar listans omfattning till **alla program**. Om du vill se fler kolumner klickar du på knappen **Kolumner** för att lägga till ytterligare information för dina program.
     >
     >

7.  klicka på **navigeringsobjektet Villkorlig åtkomst.**

8.  klicka på den policy du är intresserad av att inspektera.

9.  Granska att det inte finns några specifika villkor, tilldelningar eller andra inställningar som kan blockera användaråtkomst.

     >[!NOTE]
     >Du kanske tillfälligt vill inaktivera den här principen för att säkerställa att den inte påverkar inloggningar. Det gör du genom att ange växlingsknappen **Aktivera princip** till **Nej** och klicka på knappen **Spara.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Inaktivera en specifik princip för villkorlig åtkomst

Så här kontrollerar eller validerar du en princip för villkorlig åtkomst:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Företagsprogram** i navigeringsmenyn.

5.  klicka på **navigeringsobjektet Villkorlig åtkomst.**

6.  klicka på den policy du är intresserad av att inspektera.

7.  Inaktivera principen genom att ange **alternativet Aktivera principväxling** till **Nej** och klicka på knappen **Spara.**

## <a name="problems-with-application-consent"></a>Problem med ansökan samtycke

Programåtkomst kan blockeras eftersom rätt behörighetstillståndstillstånd inte har inträffat. Här följer några sätt att felsöka och lösa problem med programmedgivande:

-   [Utföra en medgivandeåtgärd på användarnivå](#perform-a-user-level-consent-operation)

-   [Utföra medgivandeåtgärd på administratörsnivå för alla program](#perform-administrator-level-consent-operation-for-any-application)

-   [Utföra medgivande på administratörsnivå för ett program med en enda klient](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Utföra medgivande på administratörsnivå för ett program med flera innehavare](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Utföra en medgivandeåtgärd på användarnivå

-   För alla Open ID Connect-aktiverade program som begär behörigheter, utför navigering till programmets inloggningsskärm ett medgivande på användarnivå till programmet för den inloggade användaren.

-   Om du vill göra detta programmässigt, se [Begära individuellt användarsamtycke](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Utföra medgivandeåtgärd på administratörsnivå för alla program

-   För endast program som utvecklats med hjälp av **V1-programmodellen**kan du tvinga den här administratörsnivåens samtycke att ske genom att lägga till "**\_?prompt=admin consent**" i slutet av ett programs inloggnings-URL.

-   För alla program som utvecklats med hjälp av **V2-programmodellen**kan du framtvinga samtycke på administratörsnivå genom att följa instruktionerna under **begäran om behörigheterna från avsnittet Katalogadministratör** [i Använda slutpunkten för administratörsgodkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Utföra medgivande på administratörsnivå för ett program med en enda klient

-   För program med **en klient** som begär behörigheter (som de du utvecklar eller äger i din organisation) kan du utföra en **medgivandeåtgärd** på administrativ nivå för alla användares räkning genom att logga in som global administratör och klicka på knappen Bevilja **behörigheter** högst upp i fönstret **Programregister&gt; - Alla program -&gt; Välj en app -&gt; Nödvändiga behörigheter.**

-   För alla program som utvecklats med hjälp av **V1- eller V2-programmodellen**kan du framtvinga det här medgivandet på administratörsnivå genom att följa instruktionerna under **avsnittet Begär behörigheterna från en katalogadministratör** [i Använda slutpunkten för administratörsgodkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Utföra medgivande på administratörsnivå för ett program med flera innehavare

-   För **program med flera innehavare** som begär behörigheter (till exempel ett program som en tredje part eller Microsoft utvecklar) kan du utföra en **medgivandeåtgärd på administrativ nivå.** Logga in som global administratör och klicka på knappen **Bevilja behörigheter** under fönstret **Företagsprogram&gt; - Alla program&gt; - Välj en app -&gt; Behörigheter** (tillgänglig snart).

-   Du kan också tvinga fram att det här medgivandet på administratörsnivå ska ske genom att följa instruktionerna under **avsnittet Begär behörigheterna från en katalogadministratör** [i Slutpunkten för administratörsgodkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Nästa steg
[Använda slutpunkten för administratörsmedgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

