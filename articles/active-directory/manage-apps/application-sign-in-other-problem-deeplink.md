---
title: Problem med att logga in till ett program med hjälp av en djup länks | Microsoft Docs
description: Så här felsöker du problem med att komma åt ett program från en djup länks-URL med hjälp av Azure AD
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "65825418"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problem med att logga in till ett program med hjälp av en djup länks

Åtkomst panelen är en webbaserad portal som gör det möjligt för en användare med ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. 

Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Programmet måste vara korrekt konfigurerat och tilldelat användaren eller en grupp som användaren är medlem i för att kunna se programmet i åtkomst panelen.

Djup länkar eller URL: er för användar åtkomst är länkar som användarna kan använda för att komma åt sina lösen ords-SSO-program direkt från webbläsarens URL-fält. Genom att navigera till den här länken loggas användarna automatiskt in i programmet utan att först behöva gå till åtkomst panelen. Det här är samma länk som användarna använder för att komma åt dessa program från Office 365-program start.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först

-   Kontrol lera att du använder en **webbläsare** som uppfyller minimi kraven för åtkomst panelen.

-   Kontrol lera att användarens webbläsare har lagt till URL: en för programmet på **Betrodda platser**.

-   Kontrol lera att programmet har **kon figurer ATS** korrekt.

-   Kontrol lera att användar kontot är **aktiverat** för inloggningar.

-   Kontrol lera att användarens konto **inte är utelåst.**

-   Kontrol lera att användarens **lösen ord inte har upphört att gälla eller är bortglömt.**

-   Kontrol lera att **Multi-Factor Authentication** inte blockerar användar åtkomsten.

-   Se till att en **princip för villkorlig åtkomst** eller **identitets skydds** princip inte blockerar användar åtkomst.

-   Se till att en användares **kontakt information för autentisering** är uppdaterad för att tillåta att Multi-Factor Authentication eller villkorliga åtkomst principer tillämpas.

-   Se till att du också försöker rensa webbläsarens cookies och försöka logga in igen.

## <a name="checking-the-deeplink"></a>Kontrollerar djup länks

Följ dessa steg om du vill kontrol lera att du har rätt djup länks:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

7. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

8. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

9. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

10. Klicka på **alla program** om du vill visa en lista över alla dina program.

    * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

11. Välj det program som du vill kontrol lera djup länks för.

12. Hitta URL: en för etikettens **användar åtkomst**. Din djup länks ska matcha denna URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst panelens webb läsar tillägg

Följ dessa steg om du vill installera åtkomst panelens webb läsar tillägg:

1.  Öppna [åtkomst panelen](https://myapps.microsoft.com) i någon av de webbläsare som stöds och logga in som en **användare** i Azure AD.

2.  Klicka på ett **Password-SSO-program** på åtkomst panelen.

3.  I uppmana att du uppmanas att installera program varan väljer du **Installera nu**.

4.  Baserat på din webbläsare dirigeras du till nedladdnings länken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du antingen **Aktivera** eller **Tillåt** tillägget.

6.  **Starta om** webbläsarsessionen när den har installerats.

7.  Logga in på åtkomst panelen och se om du kan **Starta** dina inloggnings program för lösen ord

Du kan också ladda ned tillägget för Chrome och Firefox från dessa direkta länkar:

-   [Tillägg för Chrome Access panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Åtkomst panels tillägg för Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ dessa steg om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**.

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I text rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i text rutan **namn** .

9.  Klicka på **Lägg**till för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj läge för **lösenordsbaserad inloggning.**

9. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du enkel inloggning för lösen ord för ett program som inte är ett galleri program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägg till ett program som inte är ett galleri program](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägg till ett program som inte är ett galleri program

Följ dessa steg om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**.

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  Klicka på **program som inte är Galleri.**

7.  Ange namnet på ditt program i text rutan **namn** . Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurations fönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på **alla program** om du vill visa en lista över alla dina program.

    1.  Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning för.

7.  När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8.  Välj läge för **lösenordsbaserad inloggning.**

9.  Ange **inloggnings-URL**, URL: en där användarna anger sitt användar namn och lösen ord för att logga in. Se till att inloggnings fälten visas på URL: en.

10. Tilldela användare till programmet.

11. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Så här tilldelar du en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill tilldela en användare till från listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga namnet** eller **e-postadressen** för den användare som du är intresse rad av att tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **användaren** i listan för att visa en **kryss ruta**. Om du vill lägga till användaren i den **markerade** listan klickar du på kryss rutan bredvid användarens profil bild eller logo typ.

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är färdig med att välja användare klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda användarna.

Efter en kort period kan de användare du har valt kunna starta dessa program på åtkomst panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här fel söknings stegen inte löser problemet. 

öppna ett support ärende med följande information om den är tillgänglig:

-   Korrelations fel-ID

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsram vid fel inträffar

-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
