---
title: Problem vid inloggning i ett program med hjälp av en djuplänk | Microsoft Docs
description: Så här felsöker du problem med åtkomst till ett program från en Djuplänken med hjälp av Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ad6eeba09c81de33b92722992c6762a4b4327c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443257"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problem vid inloggning i ett program med hjälp av en djuplänk

Åtkomstpanelen är en webbaserad portal där en användare med ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program att Azure AD-administratör har gett dem åtkomst till. 

Dessa program är konfigurerade för användarens räkning på Azure AD-portalen. Programmet måste korrekt konfigurerat och tilldelat till användaren eller en grupp som användaren är medlem i vill se programmet på åtkomstpanelen.

Djuplänkar eller användaråtkomst URL: er är länkar som användarna kan använda för att komma åt sina lösenord SSO-program direkt från sina webbläsare URL staplar. Genom att navigera till den här länken användare att automatiskt loggat in i programmet utan att behöva gå till åtkomstpanelen först. Det här är samma länk som användare använder för att komma åt programmen från startprogrammet för Office 365.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Kontrollera att din med hjälp av en **webbläsare** som uppfyller minimikraven för åtkomstpanelen.

-   Kontrollera att användarens webbläsare har lagt till URL: en för programmet till dess **betrodda platser**.

-   Se till att kontrollera att programmet är **konfigurerats** korrekt.

-   Kontrollera att användarkontot är **aktiverat** för inloggningar.

-   Kontrollera att användarkontot är **inte låst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller glömt.**

-   Se till att **Multifaktorautentisering** inte blockerar åtkomst.

-   Se till att en **princip för villkorlig åtkomst** eller **Identity Protection** principen blockerar åtkomst.

-   Se till att en användares **autentiseringskontakt** är uppdaterad så att Multifaktorautentisering eller villkorlig åtkomst-principer ska framtvingas.

-   Se till att även försök rensa cookies i webbläsaren och försök att logga in igen.

## <a name="checking-the-deeplink"></a>Kontrollera djuplänk

Följ dessa steg för att kontrollera om du har rätt djuplänk:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

7. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

8. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

9. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

10. Klicka på **alla program** att visa en lista över alla dina program.

    * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

11. Välj det program som du vill att kontrollen djuplänk för.

12. Hitta etiketten **URL för användaråtkomst**. Din djuplänk måste matcha den här URL: en.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst till panelen webbläsartillägg

Följ dessa steg om du vill installera webbläsartillägget för åtkomst till panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en av de webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** i åtkomstpanelen.

3.  I meddelandet som ber att installera programvaran, väljer **installera nu**.

4.  Baserat på din webbläsare dirigeras du till länken. **Lägg till** tillägget till din webbläsare.

5.  Om webbläsaren ber, väljer du antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in på åtkomstpanelen på och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Firefox från dessa länkar:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett Azure AD-galleriprogram

Om du vill konfigurera ett program från Azure AD-galleriet, måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ dessa steg om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  I den **anger du ett namn** textrutan från den **Lägg till från galleriet** Skriv namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textrutan.

9.  Om du vill lägga till programmet, klickar du på **Lägg till**.

Du ska kunna visa programmets konfigurationsruta efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Välj läget **lösenordsbaserad inloggning.**

9. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för en icke-galleriprogram

Om du vill konfigurera ett program från Azure AD-galleriet, måste du:

-   [Lägg till en icke-galleriprogram](#add-a-non-gallery-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägg till en icke-galleriprogram

Följ dessa steg om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  Klicka på **icke-galleriprogram.**

7.  Ange namnet på ditt program i den **namn** textrutan. Välj **lägga till.**

Du ska kunna visa programmets konfigurationsruta efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

    1.  Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Ange den **inloggnings-URL**, URL: en där användare anger sina användarnamn och lösenord för att logga in. Kontrollera fälten inloggning syns i URL: en.

10. Tilldela användare till programmet.

11. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8. Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Att lägga till dina användare till den **valda** klickar du på kryssrutan bredvid användarens profilfoto eller logotyp.

12. **Valfritt:** Om du vill **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program i åtkomstpanelen efter en kort period.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här felsökningsstegen gör inte lösa problemet. 

Öppna ett supportärende med följande information om de är tillgängliga:

-   Korrelations-ID för fel

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsramen under fel inträffar

-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
