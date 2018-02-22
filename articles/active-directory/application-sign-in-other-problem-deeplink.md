---
title: "Problem med att logga att ett program med en djuplänken | Microsoft Docs"
description: "Felsökning av problem med åtkomst till ett program från en Webbadress med hjälp av Azure AD-djuplänken"
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
ms.openlocfilehash: f4c67961316c4b78b691a46b35bd0c19bb409fd8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Logga in på ett program med en djuplänken problem

Åtkomstpanelen är en webbaserad portal som gör att en användare med ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program att Azure AD-administratör har beviljats dem åtkomst till. 

Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Programmet måste konfigureras och tilldelats användaren eller en grupp som användaren är medlem i för att se hur programmet åtkomstpanelen.

Djuplänkar eller användarbehörighet är URL: er länkar som kan används för att komma åt sina lösenord SSO-program direkt från sina webbläsare URL staplar. Genom att navigera till den här länken användare att automatiskt inloggad på programmet utan att behöva gå till åtkomstpanelen först. Det här är samma länk som användarna använder för att komma åt dessa program från startprogrammet för Office 365.

## <a name="general-issues-to-check-first"></a>Allmänna problem med att kontrollera först

-   Kontrollera att du med hjälp av en **webbläsare** som uppfyller minimikraven för åtkomstpanelen.

-   Kontrollera att användarens webbläsare har lagt till URL: en för att dess **tillförlitliga platser**.

-   Kontrollera att programmet är **konfigurerats** korrekt.

-   Kontrollera att användarkontot är **aktiverat** för inloggningar.

-   Kontrollera att användarkontot är **inte låst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller har glömt.**

-   Kontrollera att **Multifaktorautentisering** inte blockerar åtkomst.

-   Kontrollera att en **principen för villkorlig åtkomst** eller **identitetsskydd** principen inte blockerar åtkomst.

-   Se till att en användares **autentisering kontaktinformation** är uppdaterade så att Multi-Factor Authentication eller villkorlig åtkomst principer som ska framtvingas.

-   Se till att även försök att rensa cookies i webbläsaren och försök att logga in igen.

## <a name="checking-the-deeplink"></a>Kontroll av djuplänken

Följ dessa steg för att kontrollera om du har rätt djuplänken:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

7.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

8.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

9.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

10. Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

11. Välj det program som du vill använda kontrollen djuplänken för.

12. Hitta etiketten **användaren åtkomst-URL**. Din djuplänken ska matcha denna URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du Access panelen webbläsartillägg

Följ dessa steg om du vill installera webbläsartillägget för åtkomst panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** på åtkomstpanelen.

3.  Fråga om att installera programvara, Välj **installera nu**.

4.  Baserat på din webbläsare som du dirigeras till länken. **Lägg till** tillägg till webbläsaren.

5.  Om din webbläsare, Välj antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in till åtkomstpanelen och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Firefox från dessa Direktlänkar:

-   [Tillägget för Chrome åtkomst panelen](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägget för Firefox åtkomst panelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett program för Azure AD-galleriet

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-Azure-AD-gallery)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ anvisningarna nedan om du vill lägga till ett program från Azure AD-galleriet

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** fönstret.

6.  I den **anger du ett namn** textruta från den **Lägg till från galleriet** avsnittet, skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textruta.

9.  Om du vill lägga till programmet, klickar du på **Lägg till**.

Du ska kunna se programmets konfiguration fönstret efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Dessutom kan du också ange autentiseringsuppgifter för användarens räkning genom att markera rader användare och klicka på **referenser uppdatering** och ange användarnamn och lösenord för användarna. Annars uppmanas användarna att ange autentiseringsuppgifterna sig vid start.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett program för icke-galleriet

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett icke-galleriet program](#add-a-non-gallery-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägga till ett icke-galleriet program

Följ anvisningarna nedan om du vill lägga till ett program från Azure AD-galleriet

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** fönstret.

6.  Klicka på **icke-galleriet program.**

7.  Ange namnet på ditt program i den **namn** textruta. Välj **lägga till.**

Du ska kunna se programmets konfiguration fönstret efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

    1.  Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  Ange den **inloggnings-URL**, där användarna anger sina användarnamn och lösenord för att logga in URL: en. Kontrollera fälten inloggning är synliga på URL.

10. Tilldela användare till programmet.

11. Dessutom kan du också ange autentiseringsuppgifter för användarens räkning genom att markera rader användare och klicka på **referenser uppdatering** och ange användarnamn och lösenord för användarna. Annars uppmanas användarna att ange autentiseringsuppgifterna sig vid start.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmenyn vänstra navigeringsfönstret.

8.  Klickar du på den **Lägg till** knappen ovanpå det **användare och grupper** att öppna den **Lägg uppdrag** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användare** i listan för att visa en **kryssrutan**. Att lägga till användaren till den **valda** klickar du på kryssrutan bredvid användarens profilfoto eller logotyp.

12. **Valfritt:** om du vill **lägga till fler än en användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** ruta för att välja en roll för att tilldela användare som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda användarna.

Användare som du har valt att kunna starta programmen på åtkomstpanelen efter en kort period.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om felsökningen gör inte åtgärda problemet. 

Öppna ett supportärende med följande information om de är tillgängliga:

-   Fel-ID för korrelation

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsperioden under fel inträffar

-   Fiddler spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
