---
title: Problem med att logga in på Azure AD-galleriappen konfigurerad för SSO | Microsoft-dokument
description: Felsöka problem med ett Azure AD Gallery-program som är konfigurerat för enkel inloggning för lösenord.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381303"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Inloggningsproblem med en Azure AD-galleriapp konfigurerad för SSO

Access Panel är en webbaserad portal. Det gör det möjligt för användare som har Azure Active Directory (Azure AD) arbete eller skolkonton att komma åt molnbaserade appar som de har behörighet för. Användare som har Azure AD-versioner kan också använda funktioner för självbetjäningsgrupp och apphantering via Åtkomstpanelen.

Åtkomstpanelen är separat från Azure-portalen. Användare behöver inte en Azure-prenumeration för att använda Åtkomstpanelen.

Om du vill använda lösenordsbaserad enkel inloggning (SSO) på Åtkomstpanelen måste tillägget för åtkomstpanelen installeras i webbläsaren. Tillägget hämtas automatiskt när du väljer en app som är konfigurerad för lösenordsbaserad SSO.

## <a name="browser-requirements-for-access-panel"></a>Webbläsarkrav för åtkomstpanelen

Access Panel kräver en webbläsare som stöder JavaScript och har CSS aktiverat.

Följande webbläsare stöder lösenordsbaserad SSO:

- Internet Explorer 8, 9, 10 och 11 i Windows 7 eller senare

- Chrome på Windows 7 eller senare eller på MacOS X eller senare

- Firefox 26.0 eller senare i Windows XP SP2 eller senare eller på Mac OS X 10.6 eller senare

>[!NOTE]
>Det lösenordsbaserade SSO-tillägget blir tillgängligt för Microsoft Edge i Windows 10 när stöd för webbläsartillägg lades till i Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget för åtkomstpanelen

Följ de här stegen:

1. Öppna [Åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som användare i Azure AD.

2. Välj en lösenords-SSO-aktiverad app på Åtkomstpanelen.

3. När du uppmanas att göra det väljer du **Installera nu**.

4. Du kommer att hänvisas till en nedladdningslänk baserat på din webbläsare. Välj **Lägg till** för att installera webbläsartillägget.

5. Om du uppmanas att göra en fråga väljer du **Aktivera** eller **Tillåt**.

6. Starta om webbläsaren efter installationen.

7.  Logga in på Åtkomstpanelen och se om du kan starta dina lösenords-SSO-aktiverade appar.

Du kan också direkt ladda ner tillägg för Chrome och Firefox via dessa länkar:

-   [Tillägg till Chrome Access Panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägg till Firefox Access Panel](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan ställa in en grupprincip som gör att du kan fjärr installera tillägget Access Panel för Internet Explorer på användarnas datorer.

Dessa är förutsättningarna:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) måste konfigureras och användarnas datorer måste vara anslutna till domänen.

-   Du har behörigheten "Redigera inställningar" för att redigera grupprincipobjektet . Som standard har medlemmar i följande säkerhetsgrupper den här behörigheten: Domänadministratörer, företagsadministratörer och ägare av grupprincipskapare. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Information om hur du konfigurerar grupprincipen och distribuerar den till användare finns i Så här distribuerar du [tillägget Åtkomstpanel för Internet Explorer med hjälp av grupprincipen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Felsöka åtkomstpanelen i Internet Explorer

Mer om du vill komma åt ett diagnostikverktyg och instruktioner för att konfigurera tillägget finns i [Felsöka tillägget för Åtkomstpanelen för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurera lösenord SSO för en Azure AD-galleriapp

Om du vill konfigurera en app från Azure AD-galleriet måste du göra följande:

-   Lägga till appen från Azure AD-galleriet
-   [Konfigurera appen för enkel inloggning för lösenord](#configure-the-app-for-password-sso)
-   [Tilldela användare till appen](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Lägga till appen från Azure AD-galleriet

Följ de här stegen:

1. Öppna [Azure-portalen](https://portal.azure.com) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** högst upp i navigeringsfönstret till vänster om du vill öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Lägg till** i det övre högra hörnet i fönstret **Företagsprogram.**

6. Skriv namnet på appen i rutan Ange ett **namn** i avsnittet **Lägg till från galleriet.**

7. Välj den app som du vill konfigurera för SSO.

8. *Valfritt:* Innan du lägger till appen kan du ändra dess namn i rutan **Namn.**

9. Klicka på **Lägg till** för att lägga till appen.

   Efter en kort fördröjning kan du se appens konfigurationsfönster.

### <a name="configure-the-app-for-password-sso"></a>Konfigurera appen för lösenord SSO

Följ de här stegen:

1. Öppna [Azure-portalen](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** högst upp i navigeringsfönstret till vänster om du vill öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filterkontrollen** högst upp i **listan Alla program**. Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har läses in väljer du **Enkel inloggning** i fönstret till vänster i appen.

8. Välj **Lösenordsbaserat inloggningsläge.**

9. Tilldela användare till appen.

10. Du kan också ange autentiseringsuppgifter för användare. (Annars uppmanas användarna att ange autentiseringsuppgifter vid appstart.) Det gör du genom att markera användarnas rader. Välj sedan **Uppdatera autentiseringsuppgifter** och ange deras användarnamn och lösenord.

### <a name="assign-users-to-the-app"></a>Tilldela användare till appen

Så här tilldelar du användare till en app:

1. Öppna [Azure-portalen](https://portal.azure.com/) och logga in som global administratör.

2. Välj **Alla tjänster** i navigeringssmärtan till vänster om du vill öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filterkontrollen** högst upp i **listan Alla program**. Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill tilldela en användare till i listan.

7. När programmet har läses in väljer du **Användare och grupper** i appens navigeringsfönster till vänster.

8. Välj **Lägg till** högst upp i listan Användare och **grupper** om du vill öppna fönstret Lägg **till tilldelning.**

9. Välj **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv det fullständiga namnet eller e-postadressen för den användare som du vill tilldela i rutan **Sök efter namn eller e-postadress.**

11. Hovra över användaren i listan. Markera kryssrutan bredvid användarens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. *Valfritt:* Om du vill lägga till en annan användare skriver du ett annat namn eller en annan e-postadress i rutan **Sök efter namn eller e-postadress** och markerar sedan kryssrutan för att lägga till användaren i listan **Markerad.**

13. När du är klar med att välja användare klickar du på **Välj** om du vill lägga till dem i listan över användare och grupper som har tilldelats appen.

14. *Valfritt:* Klicka på **Välj roll** i fönstret Lägg **till tilldelning** om du vill välja en roll som du vill tilldela de användare som du har markerat.

15. Välj **Tilldela** om du vill tilldela appen till de markerade användarna.

    Efter en kort fördröjning kan användarna komma åt dessa appar från Åtkomstpanelen.

## <a name="request-support"></a>Begär support 
Om du får ett felmeddelande när du konfigurerar SSO och tilldelar användare öppnar du en supportbiljett. Inkludera så mycket av följande information som möjligt:

-   Korrelationsfel-ID
-   UPN (användarens e-postadress)
-   TenantID (Klient-ID)
-   Typ av webbläsare
-   Tidszon och tid/tidsram när felet uppstod
-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
