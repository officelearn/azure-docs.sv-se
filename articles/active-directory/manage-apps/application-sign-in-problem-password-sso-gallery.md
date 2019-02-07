---
title: Problem med inloggning till en Azure AD-galleriprogram konfigurerad för enkel inloggning | Microsoft Docs
description: Så här felsöker du problem med Azure AD-galleriprogram som konfigurerats för lösenord för enkel inloggning
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 734169a0bce546ee2492d100b2abcb2ba8b6ffc9
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822535"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problem med inloggning till ett program för Azure AD-Galleriprogram som konfigurerats för lösenord för enkel inloggning

Åtkomstpanelen är en webbaserad portal där en användare som har ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program som Azure AD-administratör har gett dem åtkomst till. En användare som har Azure AD-versioner kan också använda självbetjäning och apphanteringsfunktionerna via åtkomstpanelen. Åtkomstpanelen är separat från Azure-portalen och kräver inte användare har en Azure-prenumeration.

Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomstpanelen, måste tillägget åtkomstpanelen installeras i användarens webbläsare. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad SSO.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Uppfyller Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomstpanelen, måste tillägget åtkomstpanelen installeras i användarens webbläsare. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – i Windows 7 eller senare

-   Chrome - på Windows 7 eller senare, och i Mac OS X eller senare

-   Firefox 26.0 eller senare - på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare

>[!NOTE]
>Tillägget lösenordsbaserad SSO blir tillgängliga för Microsoft Edge i Windows 10 när webbläsartillägg blir stöd för Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst till panelen webbläsartillägg

Följ dessa steg om du vill installera webbläsartillägget för åtkomst till panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en av de webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** i åtkomstpanelen.

3.  I meddelandet som ber att installera programvaran, väljer **installera nu**.

4.  Beroende på din webbläsare du omdirigerad till länken. **Lägg till** tillägget till din webbläsare.

5.  Om webbläsaren ber, väljer du antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in på åtkomstpanelen på och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Firefox från direkt länkarna nedan:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan konfigurera en grupprincip som gör det möjligt att fjärrinstallera Access Panel-tillägg för Internet Explorer på användarnas datorer.

Kraven är:

-   Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit dina användares datorer till domänen.

-   Du måste ha behörigheten ”Redigera inställningar” så här redigerar du den grupprincipobjekt (GPO). Som standard har medlemmar i de följande säkerhetsgrupperna denna behörighet: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Följ självstudien [hur du distribuerar Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) steg för steg instruktioner om hur du konfigurerar en Grupprincip och distribuera till användare.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Felsöka åtkomstpanelen i Internet Explorer

Följ den [Felsök Access Panel-tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) guide för åtkomst till ett verktyg för diagnostik och stegvisa instruktioner om hur du konfigurerar tillägget för Internet Explorer.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett Azure AD-galleriprogram

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   Lägga till ett program från Azure AD-galleriet

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ dessa steg om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  I den **anger du ett namn** textrutan från den **Lägg till från galleriet** Skriv namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textrutan.

9.  Klicka på **Lägg till** för att lägga till programmet.

Du att kunna se programmets konfigurationsruta efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Tilldela användare till programmet.

10. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

### <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program i åtkomstpanelen efter en kort period.

## <a name="if-these-troubleshoot-steps-dont-resolve-the-issue"></a>Om felsökning av de här löser stegen inte problemet 
Öppna ett supportärende med följande information om de är tillgängliga:

-   Korrelations-ID för fel

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsramen under fel inträffar

-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
