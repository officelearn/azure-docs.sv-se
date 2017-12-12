---
title: Problem med att logga till en Microsoft-program | Microsoft Docs
description: "Felsöka vanliga problem med inför när de loggar in på från första part Microsoft Applications med Azure AD (till exempel Office 365)"
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
ms.openlocfilehash: 880be5f27ca4198004511e01e63cdb3d72f107d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Problem med att logga till en Microsoft-program

Microsoft Applications (till exempel Office 365 Exchange, SharePoint, Yammer, etc.) tilldelas och hanteras annorlunda än 3 part SaaS-program eller andra program som du har integrerat med Azure AD för enkel inloggning på.

Det finns tre sätt att en användare kan få åtkomst till ett Microsoft-publicerade program.

-   För program i Office 365 eller andra betald paket användare som beviljas åtkomst via **licens tilldelning** antingen direkt till användarkontot eller via en grupp med vår gruppbaserade licens tilldelning-funktionen.

-   För program som Microsoft eller tredje part publicerar fritt för alla använda användare beviljas åtkomst via **användargodkännande**. This0 innebär att de loggar in på programmet med ett konto med Azure AD-arbetsplatsen eller skolan och tillåta att den har åtkomst till vissa begränsad uppsättning data på sitt konto.

-   För program som Microsoft eller 3 part publicerar fritt för alla använda användare även beviljas åtkomst via **administratör medgivande**. Det innebär att en administratör har fastställt programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörskonto och bevilja åtkomst till alla i organisationen.

Om du vill felsöka problemet, börja med den [allmänna problemområden med programåtkomst att tänka på](#general-problem-areas-with-application-access-to-consider) och Läs den [genomgång: steg för att felsöka Microsoft Application åtkomst](#walkthrough-steps-to-troubleshoot-microsoft-application-access) att komma till den information.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Allmänna problemområden med programåtkomst att tänka på

Nedan visas en lista över allmänna problemområden som du kan detaljerat om du har en uppfattning om var du startar, men vi rekommenderar att du läser den här genomgången för att komma igång snabbt: [genomgång: steg för att felsöka Microsoft Application åtkomst](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [Problem med användarkontot](#problems-with-the-users-account)

-   [Problem med grupper](#problems-with-groups)

-   [Problem med principer för villkorlig åtkomst](#problems-with-conditional-access-policies)

-   [Problem med programmet medgivande](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Steg för att felsöka Microsoft Application åtkomst

Nedan är några vanliga problem avdelningen stöter på när användarna inte logga in i ett Microsoft-program.

-   Allmänna problem med att kontrollera först

  * Kontrollera att användaren loggar in på den **korrigera URL** och inte en lokal programmets URL.

  * Kontrollera att användarkontot är **inte låst.**

  * Kontrollera att den **användarkontot finns** i Azure Active Directory. [Kontrollera om det finns ett konto i Azure Active Directory](#problems-with-the-users-account)

  * Kontrollera att användarkontot är **aktiverat** för inloggningar. [Kontrollera status för en användare](#problems-with-the-users-account)

  * Kontrollera att användarens **lösenord inte har upphört att gälla eller har glömt.** [Återställa en användares lösenord](#reset-a-users-password) eller [aktivera lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Kontrollera att **Multifaktorautentisering** inte blockerar åtkomst. [Kontrollera status för en användares multifaktorautentisering](#check-a-users-multi-factor-authentication-status) eller [Kontrollera en användares kontaktinformation för autentisering](#check-a-users-authentication-contact-info)

   * Kontrollera att en **principen för villkorlig åtkomst** eller **identitetsskydd** principen inte blockerar åtkomst. [Kontrollera en viss villkorlig åtkomstprincip](#problems-with-conditional-access-policies) eller [Kontrollera principen för villkorlig åtkomst för ett visst program](#check-a-specific-applications-conditional-access-policy) eller [inaktivera en princip för specifik villkorlig åtkomst](#disable-a-specific-conditional-access-policy)

   * Se till att en användares **autentisering kontaktinformation** är uppdaterade så att Multi-Factor Authentication eller villkorlig åtkomst principer som ska framtvingas. [Kontrollera status för en användares multifaktorautentisering](#check-a-users-multi-factor-authentication-status) eller [Kontrollera en användares kontaktinformation för autentisering](#check-a-users-authentication-contact-info)

-   För **Microsoft** **program som kräver en licens** (till exempel Office 365) sparas här är några problem med att kontrollera när du har konstaterat tillvägagångssättet ovan:

   * Se till att användaren eller har en **-licens.** [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses) eller [Kontrollera tilldelade licenser för en grupp](#check-a-groups-assigned-licenses)

   * Om licensen **tilldelas en** **statisk grupp**, se till att den **användaren är medlem** i gruppen. [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

   * Om licensen **tilldelas en** **dynamisk grupp**, se till att den **dynamisk gruppregeln är korrekt**. [Kontrollera kriterier för medlemskap i en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

   * Om licensen **tilldelas en** **dynamisk grupp**, se till att den dynamiska gruppen har **har behandlats klart** sitt medlemskap och att den **användaren är medlem**  (Detta kan ta lite tid). [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

   *  När du kontrollera licensen tilldelas Kontrollera licensen **inte gått**.

   *  Kontrollera att licensen **för programmet** de använder.

-   För **Microsoft** **program som inte kräver en licens**, här är några saker att kontrollera:

   * Om programmet begär **behörigheter på objektnivå** (till exempel ”komma åt den här användarens postlåda”), se till att användaren har loggat in till programmet och har utfört en **användarnivå medgivande åtgärden** så att appen kan komma åt sina data.

   * Om programmet begär **på administratörsnivå** (till exempel ”åt postlådor för alla användare”), se till att en Global administratör har genomfört en **administratörsnivå medgivande åtgärden på räkning av alla användare** i organisationen.

## <a name="problems-with-the-users-account"></a>Problem med användarkontot

Programåtkomst blockeras på grund av ett problem med en användare som har tilldelats programmet. Här följer några metoder som du kan felsöka och lösa problem med användare och deras kontoinställningar:

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

Följ stegen nedan om du vill kontrollera om det finns ett användarkonto:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Kontrollera egenskaperna för användarobjektet för att säkerställa att de ser ut som förväntat och inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrollera status för en användare

Följ stegen nedan om du vill kontrollera status för en användare:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **profil**.

8.  Under **inställningar** se till att **Block logga in** är inställd på **nr**.

### <a name="reset-a-users-password"></a>Återställa en användares lösenord

Följ stegen nedan om du vill återställa en användares lösenord:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på den **Återställ lösenord** längst upp på bladet för användaren.

8.  Klicka på den **Återställ lösenord** knappen på den **Återställ lösenord** bladet som visas.

9.  Kopiera den **tillfälligt lösenord** eller **ange ett nytt lösenord** för användaren.

10. Meddela detta nya lösenord för användaren, de krävas för att ändra lösenordet vid nästa inloggning i till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Om du vill aktivera lösenordsåterställning via självbetjäning åtgärderna distribution nedan:

-   [Användarna kan återställa sina Azure Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Användarna kan återställa eller ändra sina lokala Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrollera status för multifaktorautentisering för en användare

Följ stegen nedan om du vill kontrollera status för multifaktorautentisering för en användare:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  Klicka på den **Multifaktorautentisering** längst upp på bladet.

7.  En gång i **Multi-Factor Authentication-administrationsportalen** belastning, kontrollera att du är på den **användare** fliken.

8.  Hitta användaren i listan över användare genom att söka, filtrera eller sortera.

9.  Välj användaren i listan över användare och **aktivera**, **inaktivera**, eller **framtvinga** multifaktorautentisering efter behov.

  * **Obs**: om en användare finns i en **tvingande** tillstånd, så kan du ange dem **inaktiverad** tillfälligt och låter dem tillbaka till deras konto. När de är tillbaka i sedan kan du ändra tillståndet till **aktiverad** igen för att kräva att registrera kontaktuppgifter under nästa inloggning i. Alternativt, följer du stegen i den [Kontrollera en användares autentisering kontaktinformation](#check-a-users-authentication-contact-info) att kontrollera eller ange informationen för dessa.

### <a name="check-a-users-authentication-contact-info"></a>Kontrollera en användares kontaktinformation för autentisering

Följ stegen nedan om du vill kontrollera en användares autentisering kontaktinformation som användes för multifaktorautentisering, villkorlig åtkomst, identitetsskydd och återställning av lösenord:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **profil**.

8.  Rulla ned till **autentisering kontaktinformation**.

9.  **Granska** data som registrerats för användaren och uppdatera efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Följ stegen nedan om du vill kontrollera en användares gruppmedlemskap:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper** att se vilka grupper användaren är medlem i.

### <a name="check-a-users-assigned-licenses"></a>Kontrollera en användares tilldelade licenser

Följ stegen nedan om du vill kontrollera en användares tilldelade licenser:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser användaren för närvarande har tilldelats.

### <a name="assign-a-user-a-license"></a>Tilldela en användare en licens 

Följ stegen nedan om du vill tilldela en licens till en användare:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser användaren för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfria** klickar du på den **tilldelning alternativ** objektet om du vill tilldela granularly produkter. Klicka på **Ok** när detta har slutförts.

11. Klicka på den **tilldela** för att tilldela licenserna till den här användaren.

## <a name="problems-with-groups"></a>Problem med grupper

Programåtkomst blockeras på grund av ett problem med en grupp som har tilldelats programmet. Här följer några metoder som du kan felsöka och lösa problem med grupper och gruppmedlemskap:

-   [Kontrollera medlemskapet för en grupp](#check-a-groups-membership)

-   [Kontrollera kriterier för medlemskap i en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

-   [Kontrollera tilldelade licenser för en grupp](#check-a-groups-assigned-licenses)

-   [Ombearbeta licenser för en grupp](#reprocess-a-groups-licenses)

-   [Tilldela en licens för en grupp](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrollera medlemskapet för en grupp

Följ stegen nedan om du vill kontrollera en grupps medlemskap:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **medlemmar** att granska listan över användare som är tilldelade till den här gruppen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Kontrollera kriterier för medlemskap i en dynamisk grupp 

Följ stegen nedan om du vill kontrollera kriterier för medlemskap i en dynamisk grupp:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **dynamiskt medlemskapsregler.**

8.  Granska de **enkel** eller **avancerade** regel definierad för den här gruppen och kontrollera att den användare som du vill ska vara medlem i den här gruppen uppfyller dessa villkor.

### <a name="check-a-groups-assigned-licenses"></a>Kontrollera tilldelade licenser för en grupp

Följ stegen nedan om du vill kontrollera tilldelade licenser för en grupp:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser gruppen för närvarande har tilldelats.

### <a name="reprocess-a-groups-licenses"></a>Ombearbeta licenser för en grupp

Följ stegen nedan för att Ombearbeta tilldelade licenser för en grupp:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser gruppen för närvarande har tilldelats.

8.  Klicka på den **Ombearbeta** för att säkerställa att de licenser som tilldelats den här gruppens medlemmar är uppdaterade. Det kan ta lång tid, beroende på storleken och komplexiteten i gruppen.

   >[!NOTE]
   >Överväg att tillfälligt tilldela en licens till användaren direkt om du vill göra detta snabbare. [Tilldela en användare en licens](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Tilldela en licens för en grupp

Följ stegen nedan om du vill tilldela en licens till en grupp:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser gruppen för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfria** klickar du på den **tilldelning alternativ** objektet om du vill tilldela granularly produkter. Klicka på **Ok** när detta har slutförts.

11. Klicka på den **tilldela** för att tilldela licenserna till den här gruppen. Det kan ta lång tid, beroende på storleken och komplexiteten i gruppen.

   >[!NOTE]
   >Överväg att tillfälligt tilldela en licens till användaren direkt om du vill göra detta snabbare. [Tilldela en användare en licens](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problem med principer för villkorlig åtkomst

### <a name="check-a-specific-conditional-access-policy"></a>Kontrollera en princip för specifik villkorlig åtkomst

Kontrollera eller validera en princip för enskild villkorlig åtkomst:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på den **villkorlig åtkomst** navigeringsobjektet.

6.  Klicka på den princip som du är intresserad undersöks.

7.  Granska att det finns inga särskilda villkor, tilldelningar och andra inställningar som kan blockera åtkomst.

   >[!NOTE]
   >Kan du tillfälligt inaktiverar den här principen för att säkerställa att det inte påverkar inloggningar. Gör detta genom att ange den **aktiverar principen** växla till **nr** och klicka på den **spara** knappen.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Kontrollera principen för villkorlig åtkomst för ett visst program

Om du vill kontrollera eller verifiera ett enda program för tillfället konfigurerade principen för villkorlig åtkomst:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på **alla program**.

6.  Sök efter programmet som du är intresserad eller användaren försöker logga in med programmets visningsnamn eller id.

     >[!NOTE]
     >Om programmet som du söker efter inte visas klickar du på den **Filter** knappen och expandera omfattningen i listan för att **alla program**. Om du vill se fler kolumner klickar du på den **kolumner** för att lägga till ytterligare information om dina program.
     >
     >

7.  Klicka på den **villkorlig åtkomst** navigeringsobjektet.

8.  Klicka på den princip som du är intresserad undersöks.

9.  Granska att det finns inga särskilda villkor, tilldelningar och andra inställningar som kan blockera åtkomst.

     >[!NOTE]
     >Kan du tillfälligt inaktiverar den här principen för att säkerställa att det inte påverkar inloggningar. Gör detta genom att ange den **aktiverar principen** växla till **nr** och klicka på den **spara** knappen.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Inaktivera en princip för specifik villkorlig åtkomst

Kontrollera eller validera en princip för enskild villkorlig åtkomst:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på den **villkorlig åtkomst** navigeringsobjektet.

6.  Klicka på den princip som du är intresserad undersöks.

7.  Inaktivera principen genom att ange den **aktiverar principen** växla till **nr** och klicka på den **spara** knappen.

## <a name="problems-with-application-consent"></a>Problem med programmet medgivande

Programåtkomst blockeras eftersom åtkomstbehörighet medgivande åtgärden inte har inträffat. Här följer några metoder som du kan felsöka och lösa problem med programmet medgivande:

-   [Utföra en åtgärd på användarnivå medgivande](#perform-a-user-level-consent-operation)

-   [Utföra administratörsnivå medgivande åtgärden för alla program](#perform-administrator-level-consent-operation-for-any-application)

-   [Utföra administratörsnivå medgivande för en enskild klient-program](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Utföra administratörsnivå medgivande för ett program med flera innehavare](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Utföra en åtgärd på användarnivå medgivande

-   Alla öppna ID Connect-aktiverade program som begär behörighet, utför navigera till programmets inloggning skärmen en nivå tillstånd till programmet för den inloggade användaren.

-   Om du vill göra det programmässigt finns [begär enskilda användargodkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Utföra administratörsnivå medgivande åtgärden för alla program

-   För **bara program som utvecklats med programmodell V1**, du kan framtvinga den här nivån medgivande administratör ska ske genom att lägga till ”**? uppmana = admin\_medgivande**” i slutet av en programmets tecken i URL: en.

-   För **alla program som utvecklats med programmodell V2**, kan du använda den här nivån administratör medgivande till uppstå genom att följa anvisningarna under den **begära behörigheter från katalogadministratör** avsnitt i [med medgivande adminslutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Utföra administratörsnivå medgivande för en enskild klient-program

-   För **program enkel klienter** som begär behörighet (till exempel de som du utvecklar eller äger i din organisation), kan du utföra en **administrativ nivå medgivande** åtgärden för alla användare genom att logga in som Global administratör och klicka på den **bevilja behörigheter** längst upp i den **programmet registret -&gt; alla program -&gt; Välj en App -&gt; Nödvändiga behörigheter** bladet.

-   För **alla program som utvecklats med programmodell V1 eller V2**, kan du använda den här nivån administratör medgivande till uppstå genom att följa anvisningarna under den **begära behörigheter från en katalogadministratör**  avsnitt i [med medgivande adminslutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Utföra administratörsnivå medgivande för ett program med flera innehavare

-   För **program med flera klienter** som begär behörighet (t.ex. ett program en tredje part eller Microsoft, utvecklar), kan du utföra en **administrativ nivå medgivande** igen. Logga in som Global administratör och klicka på den **bevilja behörigheter** knappen den **företagsprogram -&gt; alla program -&gt; Välj en App -&gt; behörigheter**  bladet (tillgänglig snart).

-   Kan du också använda den här nivån administratör medgivande inträffar genom att följa anvisningarna i avsnittet den **begära behörigheter från en katalogadministratör** avsnitt i [med medgivande adminslutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Nästa steg
[Med hjälp av admin medgivande-slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

