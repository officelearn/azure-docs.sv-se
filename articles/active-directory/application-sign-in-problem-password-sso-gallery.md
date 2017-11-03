---
title: "Problem med att logga att ett program för Azure AD-galleriet som konfigurerats för federerad enkel inloggning | Microsoft Docs"
description: "Felsökning av problem med Azure AD-galleriet program konfigurerade för lösenord för enkel inloggning"
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: f8521d1386bba8004e84fe8862a5f59a65ea19ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-federated-single-sign-on"></a>Problem med att logga att ett program för Azure AD-galleriet som konfigurerats för federerad enkel inloggning

Åtkomstpanelen är en webbaserad portal som gör att en användare som har ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program som Azure AD-administratör har gett dem åtkomst till. En användare med Azure AD-versioner kan också använda självbetjäning och funktioner för hantering av appen via åtkomstpanelen. Åtkomstpanelen är separat från Azure portal och kräver inte att användare ska ha en Azure-prenumeration.

Om du vill använda lösenordsbaserade enkel inloggning (SSO) på åtkomstpanelen måste åtkomstpanelen tillägget installeras i webbläsaren. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Möte Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du vill använda lösenordsbaserade enkel inloggning (SSO) på åtkomstpanelen måste åtkomstpanelen tillägget installeras i webbläsaren. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

Användarens webbläsare kan vara för lösenordsbaserad enkel inloggning:

-   Internet Explorer 8, 9, 10, 11 - på Windows 7 eller senare

-   Chrome - på Windows 7 eller senare, och i MacOS X eller senare

-   Firefox 26.0 eller senare - på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare

>[!NOTE]
>Tillägget lösenordsbaserade SSO blir tillgängliga för kvalificerade i Windows 10 när webbläsartillägg blir stöd för kant.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du Access panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** på åtkomstpanelen.

3.  Fråga om att installera programvara, Välj **installera nu**.

4.  Baserat på din webbläsare du dirigeras till länken. **Lägg till** tillägg till webbläsaren.

5.  Om din webbläsare, Välj antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in till åtkomstpanelen och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Firefox från direkt med länkarna nedan:

-   [Tillägget för Chrome åtkomst panelen](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägget för Firefox åtkomst panelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan konfigurera en grupprincip som gör det möjligt att fjärrinstallera åtkomstpanelen-tillägg för Internet Explorer på användarnas datorer.

Kraven är:

-   Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit användarnas datorer till domänen.

-   Du måste ha behörigheten ”Redigera inställningar” så här redigerar du den grupprincipobjektet (GPO). Som standard medlemmar i följande säkerhetsgrupper har denna behörighet: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Följ guiden [hur du distribuerar Access panelen-tillägg för Internet Explorer med hjälp av Grupprincip](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) steg för steg instruktioner om hur du konfigurerar grupprincipen och distribuera till användare.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Felsöka åtkomstpanelen i Internet Explorer

Följ den [felsöka Access panelen-tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot) guide för åtkomst till en diagnostik och steg-för-steg-instruktioner om hur du konfigurerar tillägget för Internet Explorer.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett program för Azure AD-galleriet

Så här konfigurerar du ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#_Add_an_application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program från galleriet Azure AD:

1.  Öppna den [Azure Portal](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** bladet.

6.  I den **anger du ett namn** textruta från den **Lägg till från galleriet** avsnittet, skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textruta.

9.  Klicka på **Lägg till** för att lägga till programmet.

Efter en kort period kunna du se programmets konfiguration bladet.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  [Tilldela användare till programmet](#_How_to_assign).

10. Dessutom kan du också ange autentiseringsuppgifter för användarens räkning genom att markera rader användare och klicka på **referenser uppdatering** och ange användarnamn och lösenord för användarna. Annars uppmanas användarna att ange autentiseringsuppgifterna sig vid start.

### <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användare** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll att tilldela användare som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda användarna.

Användare som du har valt att kunna starta programmen på åtkomstpanelen efter en kort period.

## <a name="if-these-troubleshoot-steps-dont-resolve-the-issue"></a>Om felsökning av dessa löser åtgärder inte problemet 
Öppna ett supportärende med följande information om de är tillgängliga:

-   Fel-ID för korrelation

-   UPN (användarens e-postadress)

-   Klient-ID

-   Typ av webbläsare

-   Tidszon och tid/tidsperioden under fel inträffar

-   Fiddler spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
