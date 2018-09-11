---
title: Problem vid inloggning ett Microsoft-program | Microsoft Docs
description: Felsöka vanliga problem som kan stöta på när du loggar in på första parts Microsoft Applications med hjälp av Azure AD (t.ex. Office 365)
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: d1987d6774b7fc831b26d6463f280259ee9f961a
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357515"
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Problem vid inloggning ett Microsoft-program

Microsoft Applications (t.ex. Office 365 Exchange, SharePoint, Yammer osv) tilldelas och hanteras lite annorlunda än 3 part SaaS-program eller andra program som du integrerar med Azure AD för enkel inloggning på.

Det finns tre sätt att en användare kan få åtkomst till ett Microsoft-publicerade program.

-   För program i Office 365 eller andra betalda krypteringssviter, användare som beviljas åtkomst via **licensiera tilldelning** antingen direkt till användarkontot eller via en grupp med hjälp av vår gruppbaserad tilldelning licenskapacitet.

-   För program som Microsoft eller tredje part publicerar fritt för allmän användning, kan användare få åtkomst via **användarmedgivande**. This0 innebär att de logga in i program med ett Azure AD för arbete eller skola och att den kan ha åtkomst till vissa begränsat uppsättning data på sitt konto.

-   För program som Microsoft eller 3 part publicerar fritt för allmän användning, användare kan också få åtkomst via **administratörens godkännande**. Det innebär att en administratör har fastställt programmet kan användas av alla i organisationen, så att de logga in på programmet med ett globalt administratörskonto och bevilja åtkomst till alla i organisationen.

Om du vill felsöka problemet, börja med den [allmänna problemområden med programåtkomst att tänka på](#general-problem-areas-with-application-access-to-consider) och sedan läsa de [genomgång: steg för att felsöka Microsoft Application access](#walkthrough-steps-to-troubleshoot-microsoft-application-access) komma in i den information.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Allmänna problemområden med programåtkomst att tänka på

Följande är en lista över de allmänna problemområden som innehåller detaljer om du har en uppfattning om var du ska börja, men vi rekommenderar att du läser den här genomgången att komma igång snabbt: [genomgång: steg för att felsöka Microsoft Application access](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [Problem med användarkontot](#problems-with-the-users-account)

-   [Problem med grupper](#problems-with-groups)

-   [Problem med principer för villkorlig åtkomst](#problems-with-conditional-access-policies)

-   [Problem med programtillåtelser](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Steg för att felsöka Microsoft Application åtkomst

Nedan följer några vanliga problem som folk stöter på när deras användare inte kan logga in till ett Microsoft-program.

-   Allmänna problem att kontrollera först

  * Kontrollera att användaren loggar in till den **korrigera URL: en** och inte ett lokalt program-URL.

  * Kontrollera att användarkontot är **inte låst.**

  * Kontrollera att den **användarkontot finns** i Azure Active Directory. [Kontrollera om ett användarkonto i Azure Active Directory](#problems-with-the-users-account)

  * Kontrollera att användarkontot är **aktiverat** för inloggningar. [Kontrollera status för en användare](#problems-with-the-users-account)

  * Kontrollera att användarens **lösenord inte har upphört att gälla eller glömt.** [Återställa en användares lösenord](#reset-a-users-password) eller [aktivera lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Se till att **Multifaktorautentisering** inte blockerar åtkomst. [Kontrollera status för en användares multifaktorautentisering](#check-a-users-multi-factor-authentication-status) eller [Kontrollera en användares information](#check-a-users-authentication-contact-info)

   * Se till att en **princip för villkorlig åtkomst** eller **Identity Protection** principen blockerar åtkomst. [Kontrollera principer för villkorlig åtkomst för specifika](#problems-with-conditional-access-policies) eller [Kontrollera princip för villkorlig åtkomst för ett visst program](#check-a-specific-applications-conditional-access-policy) eller [inaktivera en specifik villkorlig åtkomstprincip](#disable-a-specific-conditional-access-policy)

   * Se till att en användares **autentiseringskontakt** är uppdaterad så att Multifaktorautentisering eller villkorlig åtkomst-principer ska framtvingas. [Kontrollera status för en användares multifaktorautentisering](#check-a-users-multi-factor-authentication-status) eller [Kontrollera en användares information](#check-a-users-authentication-contact-info)

-   För **Microsoft** **program som kräver en licens** (t.ex. Office 365) sparas, här är några problem med att kontrollera när du har konstaterat tillvägagångssättet ovan:

   * Se till att användaren eller har en **tilldelad licens.** [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses) eller [Kontrollera tilldelade licenser för en grupp](#check-a-groups-assigned-licenses)

   * Om licensen **tilldelats en** **statisk grupp**, se till att den **användaren är medlem** i gruppen. [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

   * Om licensen **tilldelats en** **dynamisk grupp**, se till att den **dynamisk regel är korrekt**. [Kontrollera kriterier för medlemskap i en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

   * Om licensen **tilldelats en** **dynamisk grupp**, kontrollera att den dynamiska gruppen har **bearbetats** dess medlemskap och att den **användaren är medlem**  (det kan ta lite tid). [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

   *  När du kontrollera licensen tilldelas Kontrollera licensen **inte har gått ut**.

   *  Kontrollera att licensen **för programmet** de använder.

-   För **Microsoft** **program som inte kräver en licens**, här följer några saker att kontrollera:

   * Om programmet begär **behörigheter på entitetsnivå** (till exempel ”åt den här användarens postlåda”), se till att användaren har loggat in till programmet och har utfört en **medgivande på användarnivå åtgärden** så att programmet komma åt sina data.

   * Om programmet begär **på administratörsnivå** (till exempel ”åt alla användarpostlådor”), se till att en Global administratör har utfört en **administratörsnivå medgivande åtgärden på räkning av alla användare** i organisationen.

## <a name="problems-with-the-users-account"></a>Problem med användarkontot

Programåtkomst blockeras på grund av ett problem med en användare som har tilldelats till programmet. Följande är några sätt du kan felsöka och lösa problem med användare och deras kontoinställningar:

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

10. Kommunicera det nya lösenordet för användaren, de krävas för att ändra lösenordet vid nästa inloggning i till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Följ distributionsstegen nedan om du vill aktivera återställning av lösenord:

-   [Användarna kan återställa sina Azure Active Directory-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

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

  * **Obs**: om en användare finns i en **tvingande** tillstånd, kan du ange dem till **inaktiverad** tillfälligt och låter dem tillbaka till sitt konto. När de är tillbaka i kan du ändra deras tillstånd att **aktiverad** igen för att kräva att Omregistrera kontaktinformationen vid nästa inloggning i. Du kan också följa stegen i den [Kontrollera en användares autentiseringskontakt](#check-a-users-authentication-contact-info) verifiera eller ange informationen för dessa.

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

## <a name="problems-with-groups"></a>Problem med grupper

Programåtkomst blockeras på grund av ett problem med en grupp som är tilldelad till programmet. Följande är några sätt du kan felsöka och lösa problem med grupper och gruppmedlemskap:

-   [Kontrollera en grupps medlemskap](#check-a-groups-membership)

-   [Kontrollera kriterier för medlemskap i en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

-   [Kontrollera tilldelade licenser för en grupp](#check-a-groups-assigned-licenses)

-   [Ombearbeta licenser för en grupp](#reprocess-a-groups-licenses)

-   [Tilldela en licens för en grupp](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrollera en grupps medlemskap

Följ dessa steg om du vill kontrollera en grupps medlemskap:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **medlemmar** att granska listan över användare som är tilldelade till den här gruppen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Kontrollera kriterier för medlemskap i en dynamisk grupp 

Följ dessa steg om du vill kontrollera en dynamisk grupp kriterier för medlemskap:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **regler för dynamiskt medlemskap.**

8.  Granska den **enkel** eller **avancerade** regel definieras för den här gruppen och kontrollera att den användare du vill vara medlem i den här gruppen uppfyller dessa villkor.

### <a name="check-a-groups-assigned-licenses"></a>Kontrollera tilldelade licenser för en grupp

Följ dessa steg om du vill kontrollera tilldelade licenser för en grupp:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser gruppen för närvarande har tilldelats.

### <a name="reprocess-a-groups-licenses"></a>Ombearbeta licenser för en grupp

Följ dessa steg för att Ombearbeta tilldelade licenser för en grupp:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser gruppen för närvarande har tilldelats.

8.  Klicka på den **Ombearbeta** knappen för att se till att de licenser som tilldelats den här gruppens medlemmar är uppdaterade. Det kan ta lång tid, beroende på storleken och komplexiteten i gruppen.

   >[!NOTE]
   >Överväg att tillfälligt tilldela en licens till användaren direkt om du vill göra detta snabbare. [Tilldela en användare en licens](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Tilldela en licens för en grupp

Följ dessa steg om du vill tilldela en licens till en grupp:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser gruppen för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfritt** klickar du på den **tilldelningsalternativ** objektet om du vill tilldela detaljerat produkter. Klicka på **Ok** när det är klart.

11. Klicka på den **tilldela** knappen för att tilldela dessa licenser till den här gruppen. Det kan ta lång tid, beroende på storleken och komplexiteten i gruppen.

   >[!NOTE]
   >Överväg att tillfälligt tilldela en licens till användaren direkt om du vill göra detta snabbare. [Tilldela en användare en licens](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problem med principer för villkorlig åtkomst

### <a name="check-a-specific-conditional-access-policy"></a>Kontrollera en viss villkorlig åtkomstprincip

Att kontrollera eller validera en enda villkorlig åtkomstprincip:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på den **villkorlig åtkomst** navigering objekt.

6.  Klicka på den princip som du vill granska.

7.  Granska att det finns inga särskilda villkor, tilldelningar och övriga inställningar som kan blockera användaråtkomst.

   >[!NOTE]
   >Kan du tillfälligt inaktiverar den här principen för att säkerställa att det inte påverkar inloggningar. Gör detta genom att ange den **aktiverar principen** växla till **nr** och klicka på den **spara** knappen.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Kontrollera principen för villkorlig åtkomst för ett visst program

Om du vill kontrollera eller verifiera ett enda program för tillfället konfigurerade principen för villkorlig åtkomst:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på **alla program**.

6.  Sök efter program som du är intresserad av, eller användaren försöker att logga in med programmets visningsnamn eller ID: t.

     >[!NOTE]
     >Om programmet du letar efter inte visas klickar du på den **Filter** knappen och expandera omfattningen för listan att **alla program**. Om du vill se fler kolumner, klickar du på den **kolumner** för att lägga till ytterligare information för dina program.
     >
     >

7.  Klicka på den **villkorlig åtkomst** navigering objekt.

8.  Klicka på den princip som du vill granska.

9.  Granska att det finns inga särskilda villkor, tilldelningar och övriga inställningar som kan blockera användaråtkomst.

     >[!NOTE]
     >Kan du tillfälligt inaktiverar den här principen för att säkerställa att det inte påverkar inloggningar. Gör detta genom att ange den **aktiverar principen** växla till **nr** och klicka på den **spara** knappen.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Inaktivera en specifik villkorlig åtkomstprincip

Att kontrollera eller validera en enda villkorlig åtkomstprincip:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på den **villkorlig åtkomst** navigering objekt.

6.  Klicka på den princip som du vill granska.

7.  Inaktivera principen genom att ange den **aktiverar principen** växla till **nr** och klicka på den **spara** knappen.

## <a name="problems-with-application-consent"></a>Problem med programtillåtelser

Programåtkomst blockeras eftersom åtgärden behörighet medgivande ägt rum. Följande är några sätt du kan felsöka och lösa problem för godkännande av programmet:

-   [Utföra en åtgärd på användarnivå medgivande](#perform-a-user-level-consent-operation)

-   [Utföra administratörsnivå medgivande åtgärden för alla program](#perform-administrator-level-consent-operation-for-any-application)

-   [Utföra administratörsnivå medgivande för en enda klient-program](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Utföra administratörsnivå medgivande för ett program med flera innehavare](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Utföra en åtgärd på användarnivå medgivande

-   För alla öppna ID Connect-aktiverade program som begär behörighet, utför navigera till programmets inloggning skärmen en nivå användargodkännande till programmet för den inloggade användaren.

-   Om du vill göra detta via programmering finns i [begär enskilda användargodkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Utföra administratörsnivå medgivande åtgärden för alla program

-   För **endast program som utvecklats med V1-programmodell**, du kan tvinga fram den här nivån administratörens godkännande ska ske genom att lägga till ”**? uppmana = admin\_godkänna**” i slutet av en programmets tecken i URL: en.

-   För **alla program som utvecklats med V2-programmodell**, kan du tillämpa den här administratörsnivå medgivande till att inträffa genom att följa anvisningarna under den **begär behörigheter från en katalogadministratör** delen av [med medgivande adminslutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Utföra administratörsnivå medgivande för en enda klient-program

-   För **enda klient program** som begär behörighet (t.ex. de som du utvecklar eller äger i din organisation), kan du utföra en **på administrativ nivå medgivande** åtgärden för alla användare genom att logga in som Global administratör och klicka på den **bevilja** längst upp på den **programregistret -&gt; alla program -&gt; Välj en App -&gt; Nödvändiga behörigheter** fönstret.

-   För **alla program som utvecklats med programmodell V1 eller V2**, kan du tillämpa den här administratörsnivå medgivande till att inträffa genom att följa anvisningarna under den **begär behörigheter från en directory-administratör**  delen av [med medgivande adminslutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Utföra administratörsnivå medgivande för ett program med flera innehavare

-   För **fleranvändarprogram** som begär behörighet (t.ex. ett program en tredje part eller Microsoft, utvecklar), kan du utföra en **på administrativ nivå medgivande** igen. Logga in som Global administratör och klicka på den **bevilja** knappen den **företagsprogram -&gt; alla program -&gt; Välj en App -&gt; behörigheter**  fönstret (tillgänglig snart).

-   Du kan även framtvinga den här administratörsnivå medgivande till att inträffa genom att följa anvisningarna under den **begär behörigheter från en directory-administratör** delen av [med medgivande adminslutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Nästa steg
[Med hjälp av administratören medgivande slutpunkt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

