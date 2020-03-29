---
title: Problem med att logga in på ett program med hjälp av en deeplink | Microsoft-dokument
description: Felsöka problem med åtkomst till ett program från en deeplink-URL med Azure AD
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825418"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problem med att logga in på ett program med hjälp av en djuplänk

Åtkomstpanelen är en webbaserad portal som gör det möjligt för en användare med ett arbets- eller skolkonto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har gett dem åtkomst till. 

Dessa program konfigureras för användarens räkning i Azure AD-portalen. Programmet måste konfigureras korrekt och tilldelas användaren eller en grupp som användaren är medlem i för att kunna se programmet på åtkomstpanelen.

Djuplänkar eller användaråtkomstadresser är länkar som användarna kan använda för att komma åt sina lösenords-SSO-program direkt från sina webbadressfält för webbläsare. Genom att navigera till den här länken loggas användarna automatiskt in i programmet utan att först behöva gå till åtkomstpanelen. Det här är samma länk som användare använder för att komma åt dessa program från Programstartprogrammet i Office 365.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Kontrollera att du använder en **webbläsare** som uppfyller minimikraven för åtkomstpanelen.

-   Kontrollera att användarens webbläsare har lagt till url:en för programmet på dess **betrodda platser**.

-   Kontrollera att programmet är **korrekt konfigurerat.**

-   Kontrollera att användarens konto är **aktiverat** för inloggningar.

-   Kontrollera att användarens konto inte är **utelåst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller glömts bort.**

-   Kontrollera att **multifaktorautentisering** inte blockerar användaråtkomst.

-   Kontrollera att en **princip för villkorlig åtkomst** eller **identitetsskydd** inte blockerar användaråtkomst.

-   Kontrollera att en användares **autentiseringskontaktinformation** är uppdaterad så att principer för multifaktorautentisering eller villkorlig åtkomst kan tillämpas.

-   Se till att också försöka rensa webbläsarens cookies och försöka logga in igen.

## <a name="checking-the-deeplink"></a>Kontrollera djuplänken

Så här kontrollerar du om du har rätt djuplänk:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

7. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

8. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

9. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

10. Klicka på **Alla program om** du vill visa en lista över alla dina program.

    * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

11. Välj det program som du vill kontrollera djuplänken för.

12. Leta reda på etiketten **URL för användaråtkomst**. Din deeplink bör matcha den här webbadressen.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du webbläsartillägget för åtkomstpanelen

Så här installerar du webbläsartillägget för åtkomstpanelen:

1.  Öppna [åtkomstpanelen](https://myapps.microsoft.com) i en av webbläsarna som stöds och logga in som **användare** i din Azure AD.

2.  klicka på ett **lösenords-SSO-program** på åtkomstpanelen.

3.  I prompten som ber om att installera programvaran väljer du **Installera nu**.

4.  Baserat på din webbläsare är du riktad till nedladdningslänken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du att **aktivera** eller **tillåt** tillägget.

6.  Starta om webbläsarsessionen **när** den har installerats.

7.  Logga in på åtkomstpanelen och se om du kan **starta** dina lösenords-SSO-program

Du kan också ladda ner tillägget för Chrome och Firefox från dessa direkta länkar:

-   [Tillägg till panelen Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägg till Firefox-åtkomstpanelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett Azure AD-galleriprogram

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmet för enkel inloggning för lösenord](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**.

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Skriv namnet på programmet i textrutan **Ange ett namn** från **galleriet.**

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn från textrutan **Namn.**

9.  Om du vill lägga till programmet klickar du på **Lägg till**.

Efter en kort period kan du se programmets konfigurationsfönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj läge **Lösenordsbaserad inloggning.**

9. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett program som inte är galleri

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program som inte är galleri](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning för lösenord](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägga till ett program som inte är galleri

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**.

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Klicka på **Program för icke-galleri.**

7.  Ange namnet på programmet i textrutan **Namn.** Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurationsfönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på **Alla program om** du vill visa en lista över alla dina program.

    1.  Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8.  Välj läge **Lösenordsbaserad inloggning.**

9.  Ange **inloggnings-URL:en**, webbadressen där användarna anger sitt användarnamn och lösenord för att logga in. Kontrollera att inloggningsfälten visas på webbadressen.

10. Tilldela användare till programmet.

11. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Så här tilldelar du en användare till ett program direkt

Så här tilldelar du en eller flera användare ett program direkt:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill tilldela en användare till i listan.

7. När programmet har läses in klickar du på **Användare och grupper** från programmets navigeringsmeny till vänster.

8. Klicka på knappen **Lägg** till högst upp i listan **Användare och grupper** för att öppna fönstret Lägg till **tilldelning.**

9. Klicka på väljaren **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv in det **fullständiga namnet** eller **e-postadressen** till den användare som du är intresserad av att tilldela sökrutan Sök efter **namn eller e-postadress.**

11. Hovra över **användaren** i listan för att visa en **kryssruta**. Om du vill lägga till användaren i listan **Markerad** klickar du på kryssrutan bredvid användarens profilfoto eller logotyp.

12. **Valfritt:** Om du vill lägga till **fler än en användare**skriver du in ett annat fullständigt namn eller en annan **fullständig** **adress** i sökrutan Sök efter namn **eller e-postadress** och klickar på kryssrutan om du vill lägga till den här användaren i listan **Markerad.**

13. När du är klar med att välja användare klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som du vill tilldela de användare som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade användarna.

Efter en kort period kan de användare som du har valt starta dessa program på åtkomstpanelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om dessa felsökningssteg inte löser problemet. 

öppna en supportbiljett med följande information om sådan finns:

-   Korrelationsfel-ID

-   UPN (användarens e-postadress)

-   TenantID (Klient-ID)

-   Typ av webbläsare

-   Tidszon och tid/tidsram under fel inträffar

-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
