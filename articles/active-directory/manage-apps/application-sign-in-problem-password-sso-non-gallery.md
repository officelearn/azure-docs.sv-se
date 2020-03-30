---
title: Lösenordsbaserad enkel inloggning (SSO) i Åtkomstpanelen | Microsoft-dokument
description: I artikeln beskrivs problemområden som ger vägledning för att felsöka problem relaterade till inloggning till Azure AD Gallery-program som konfigurerats för enkel inloggning med lösenord.
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
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381233"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problem med att logga in på ett Azure AD Gallery-program som konfigurerats för enkel inloggning för lösenord

Åtkomstpanelen är en webbaserad portal som gör det möjligt för en användare som har ett arbets- eller skolkonto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har gett dem åtkomst till. En användare som har Azure AD-versioner kan också använda funktioner för självbetjäningsgrupp och apphantering via åtkomstpanelen. Åtkomstpanelen är skild från Azure-portalen och kräver inte att användare har en Azure-prenumeration.

Om du vill använda lösenordsbaserad enkel inloggning (SSO) på åtkomstpanelen måste tillägget för åtkomstpanelen installeras i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som är konfigurerat för lösenordsbaserad SSO.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Uppfyller webbläsarkraven för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och har CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) på åtkomstpanelen måste tillägget för åtkomstpanelen installeras i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som är konfigurerat för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Chrome – på Windows 7 eller senare och på MacOS X eller senare

-   Firefox 26.0 eller senare – på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare

>[!NOTE]
>Det lösenordsbaserade SSO-tillägget blir tillgängligt för Microsoft Edge i Windows 10 när webbläsartillägg stöds för Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du webbläsartillägget för åtkomstpanelen

Så här installerar du webbläsartillägget för åtkomstpanelen:

1.  Öppna [åtkomstpanelen](https://myapps.microsoft.com) i en av webbläsarna som stöds och logga in som **användare** i din Azure AD.

2.  klicka på ett **lösenords-SSO-program** på åtkomstpanelen.

3.  I prompten som ber om att installera programvaran väljer du **Installera nu**.

4.  Baserat på din webbläsare dirigeras du till nedladdningslänken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du att **aktivera** eller **tillåt** tillägget.

6.  Starta om webbläsarsessionen **när** den har installerats.

7.  Logga in på åtkomstpanelen och se om du kan **starta** dina lösenords-SSO-program

Du kan också ladda ner tillägget för Chrome och Firefox från de direkta länkarna nedan:

-   [Tillägg till panelen Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägg till Firefox-åtkomstpanelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan ställa in en gruppprincip som gör att du kan fjärrinstallation av tillägget för åtkomstpanelen för Internet Explorer på användarnas datorer.

Förutsättningarna inkluderar:

-   Du har konfigurerat [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)och du har anslutit användarnas datorer till domänen.

-   Du måste ha behörigheten "Redigera inställningar" för att kunna redigera grupprincipobjektet. Som standard har medlemmar i följande säkerhetsgrupper den här behörigheten: Domänadministratörer, företagsadministratörer och ägare av grupprincipskapare. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Följ självstudien [Så här distribuerar du tillägg till åtkomstpanelen för Internet Explorer med grupprincip](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) för steg för steg instruktioner om hur du konfigurerar grupprincipen och distribuerar den till användare.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Felsöka åtkomstpanelen i Internet Explorer

Följ [guiden Felsöka tillägg till Åtkomstpanelen för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) för åtkomst till ett diagnostikverktyg och steg för steg instruktioner om hur du konfigurerar tillägget för IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett program som inte är galleri

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program som inte är galleri](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning för lösenord](#configure-the-application-for-password-single-sign-on)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Lägga till ett program som inte är galleri

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Klicka på **Program för icke-galleri.**

7.  Ange namnet på programmet i textrutan **Namn.** Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurationsfönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj läge **Lösenordsbaserad inloggning.**

9. Ange **inloggnings-URL.** Det här är webbadressen där användarna anger sitt användarnamn och lösenord för att logga in på. Kontrollera att inloggningsfälten visas på webbadressen.

10. Tilldela användare till programmet.

11. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

### <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

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

11. Hovra över **användaren** i listan för att visa en **kryssruta**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. **Valfritt:** Om du vill lägga till **fler än en användare**skriver du in ett annat fullständigt namn eller en annan **fullständig** **adress** i sökrutan Sök efter namn **eller e-postadress** och klickar på kryssrutan om du vill lägga till den här användaren i listan **Markerad.**

13. När du är klar med att välja användare klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som du vill tilldela de användare som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade användarna.

Efter en kort period kan de användare som du har valt starta dessa program på åtkomstpanelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om dessa felsökningssteg inte löser problemet

öppna en supportbiljett med följande information om sådan finns:

-   Korrelationsfel-ID

-   UPN (användarens e-postadress)

-   TenantID (Klient-ID)

-   Typ av webbläsare

-   Tidszon och tid/tidsram under fel inträffar

-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)

