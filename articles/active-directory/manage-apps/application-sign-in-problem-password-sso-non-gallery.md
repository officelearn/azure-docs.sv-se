---
title: Lösenordsbaserad enkel inloggning (SSO) i åtkomst panelen | Microsoft Docs
description: Diskuterar problemområden som ger vägledning för fel sökning av problem som rör inloggning i Azure AD Gallery-program som kon figurer ATS för enkel inloggning med lösen ord.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4a645038dfb49e7c79995b90b159bdc07bea4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763779"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problem med att logga in till ett Azure AD Gallery-program som kon figurer ATS för enkel inloggning med lösen ord

Åtkomst panelen är en webbaserad portal som gör det möjligt för en användare som har ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. En användare som har Azure AD-versioner kan också använda funktioner för självbetjänings grupp och program hantering via åtkomst panelen. Åtkomst panelen är separat från Azure Portal och kräver inte att användare har en Azure-prenumeration.

Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomst panelen måste åtkomst panels tillägget vara installerat i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som har kon figurer ATS för lösenordsbaserad SSO.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Mötes webbläsarens krav på åtkomst panelen

Åtkomst panelen kräver en webbläsare som stöder Java Script och har CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomst panelen måste åtkomst panels tillägget vara installerat i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som har kon figurer ATS för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan användarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Chrome – på Windows 7 eller senare och på MacOS X eller senare

-   Firefox 26,0 eller senare –-på Windows XP SP2 eller senare och på Mac OS X 10,6 eller senare

>[!NOTE]
>Det lösenordsbaserade SSO-tillägget blir tillgängligt för Microsoft Edge i Windows 10 när webb läsar tillägg stöds för Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst panelens webb läsar tillägg

Om du vill installera åtkomst panelens webb läsar tillägg följer du stegen nedan:

1.  Öppna [åtkomst panelen](https://myapps.microsoft.com) i någon av de webbläsare som stöds och logga in som en **användare** i Azure AD.

2.  Klicka på ett **Password-SSO-program** på åtkomst panelen.

3.  I uppmana att du uppmanas att installera program varan väljer du **Installera nu**.

4.  Baserat på din webbläsare dirigeras du till nedladdnings länken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du antingen **Aktivera** eller **Tillåt** tillägget.

6.  **Starta om** webbläsarsessionen när den har installerats.

7.  Logga in på åtkomst panelen och se om du kan **Starta** dina inloggnings program för lösen ord

Du kan också ladda ned tillägget för Chrome och Firefox från de direkta länkarna nedan:

-   [Tillägg för Chrome Access panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Åtkomst panels tillägg för Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grup princip för Internet Explorer

Du kan konfigurera en grup princip som gör att du kan fjärrinstallera åtkomst panels tillägget för Internet Explorer på användarnas datorer.

Kraven är:

-   Du har konfigurerat [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)och du har anslutit användarnas datorer till din domän.

-   Du måste ha behörigheten "Redigera inställningar" för att kunna redigera grupprincip-objektet (GPO). Som standard har medlemmar i följande säkerhets grupper den här behörigheten: domän administratörer, företags administratörer och grupprincip Skapare ägare. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Följ självstudien så [här distribuerar du åtkomst panels tillägget för Internet Explorer med hjälp av Grupprincip](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) för steg-för-steg-instruktioner om hur du konfigurerar grup principen och distribuerar den till användare.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Felsöka åtkomst panelen i Internet Explorer

Följ [fel söknings tillägget åtkomst panel för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) för att få åtkomst till ett diagnostik-verktyg och stegvisa anvisningar om hur du konfigurerar tillägget för Internet Explorer.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du enkel inloggning för lösen ord för ett program som inte är ett galleri program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägg till ett program som inte är ett galleri program](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Lägg till ett program som inte är ett galleri program

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  Klicka på **program som inte är Galleri.**

7.  Ange namnet på ditt program i text rutan **namn** . Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurations fönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj läge för **lösenordsbaserad inloggning.**

9. Ange **inloggnings-URL: en**. Detta är URL: en där användarna anger sitt användar namn och lösen ord för att logga in på. Se till att inloggnings fälten är synliga i URL: en.

10. Tilldela användare till programmet.

11. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

### <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Om du vill tilldela en eller flera användare till ett program direkt följer du stegen nedan:

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

11. Hovra över **användaren** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid användarens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är färdig med att välja användare klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda användarna.

Efter en kort period kan de användare du har valt kunna starta dessa program på åtkomst panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här fel söknings stegen inte löser problemet

öppna ett support ärende med följande information om den är tillgänglig:

-   Korrelations fel-ID

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsram vid fel inträffar

-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

