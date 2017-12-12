---
title: "Problem med att installera programåtkomst panelen webbläsartillägget | Microsoft Docs"
description: "Hur du löser vanliga fel som uppstod när du installerar webbläsartillägget för åtkomst till Kontrollpanelen"
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
ms.reviewer: japere
ms.openlocfilehash: 66e002b092e02f92a33c6e498b176331ebdc190f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>Problem med att installera programåtkomst panelen webbläsartillägg

Åtkomstpanelen är en webbaserad portal som gör att en användare som har ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program som Azure AD-administratör har gett dem åtkomst till. En användare med Azure AD-versioner kan också använda självbetjäning och funktioner för hantering av appen via åtkomstpanelen. Åtkomstpanelen är separat från Azure portal och kräver inte att användare ska ha en Azure-prenumeration.

Om du vill använda lösenordsbaserade enkel inloggning (SSO) på åtkomstpanelen måste åtkomstpanelen tillägget installeras i webbläsaren. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Möte Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du vill använda lösenordsbaserade enkel inloggning (SSO) på åtkomstpanelen måste åtkomstpanelen tillägget installeras i webbläsaren. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

Användarens webbläsare kan vara för lösenordsbaserad enkel inloggning:

-   Internet Explorer 8, 9, 10, 11--på Windows 7 eller senare

-   Kanten på Windows 10 årsdagar Edition eller senare 

-   Chrome--På Windows 7 eller senare, och i MacOS X eller senare

-   Firefox 26.0 eller senare--på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du Access panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** på åtkomstpanelen.

3.  Fråga om att installera programvara, Välj **installera nu**.

4.  Baserat på din webbläsare du dirigeras till länken. **Lägg till** tillägg till webbläsaren.

5.  Om din webbläsare, Välj antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in till åtkomstpanelen och se om kan du **starta** lösenord SSO-program

Du kan också hämta tillägget för Chrome och kanten på direkt med länkarna nedan:

-   [Tillägget för Chrome åtkomst panelen](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägget för Microsoft Edge åtkomst panelen](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan konfigurera en grupprincip som gör det möjligt att fjärrinstallera åtkomstpanelen-tillägg för Internet Explorer på användarnas datorer.

Kraven är:

-   Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit användarnas datorer till domänen.

-   Du måste ha behörigheten ”Redigera inställningar” så här redigerar du den grupprincipobjektet (GPO). Som standard medlemmar i följande säkerhetsgrupper har denna behörighet: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Följ guiden [hur du distribuerar Access panelen-tillägg för Internet Explorer med hjälp av Grupprincip](active-directory-saas-ie-group-policy.md) steg för steg instruktioner om hur du konfigurerar grupprincipen och distribuera till användare.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Felsöka åtkomstpanelen i Internet Explorer

Följ den [felsöka Access panelen-tillägg för Internet Explorer](active-directory-saas-ie-troubleshooting.md) guide för åtkomst till en diagnostik och steg-för-steg-instruktioner om hur du konfigurerar tillägget för Internet Explorer.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om felsökningen inte löser problemet

Öppna ett supportärende med följande information om de är tillgängliga:

-   Fel-ID för korrelation

-   UPN (användarens e-postadress)

-   Klient-ID

-   Typ av webbläsare

-   Tidszon och tid/tidsperioden under fel inträffar

-   Fiddler spårningar

## <a name="next-steps"></a>Nästa steg
[Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
