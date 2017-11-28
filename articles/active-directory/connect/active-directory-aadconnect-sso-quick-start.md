---
title: "Azure AD Connect: Sömlös Single Sign-On - Snabbstart | Microsoft Docs"
description: "Den här artikeln beskriver hur du kommer igång med Azure Active Directory sömlös enkel inloggning"
services: active-directory
keywords: "Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: b85afe54832319fae2ea3a2501ec268bc63fc7c1
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory sömlös enkel inloggning: Snabbstart

## <a name="deploy-seamless-single-sign-on"></a>Distribuera sömlös enkel inloggning

Azure Active Directory (AD Azure) sömlös enkel inloggning (SSO sömlös) loggar automatiskt in användare när de är på sina företagets datorer som är anslutna till företagsnätverket. Sömlös SSO ger dina användare enkel åtkomst till dina molnbaserade program utan att behöva ytterligare lokala komponenter.

Följ dessa steg om du vill distribuera sömlös SSO.

## <a name="step-1-check-the-prerequisites"></a>Steg 1: Kontrollera krav

Se till att följande krav är uppfyllda:

* **Konfigurera Azure AD Connect servern**: Om du använder [direkt autentisering](active-directory-aadconnect-pass-through-authentication.md) som din inloggningsmetod, krävs ingen ytterligare kravkontrollen. Om du använder [synkronisering av lösenords-hash](active-directory-aadconnectsync-implement-password-synchronization.md) som din inloggningsmetod, och om det finns en brandvägg mellan Azure AD Connect och Azure AD, kontrollera att:
   - Du använder version 1.1.644.0 eller senare av Azure AD Connect. 
   - Om din brandvägg eller proxyserver tillåter DNS, vitlistning av tillåtna anslutningar till den  **\*. msappproxy.net** URL: er via port 443. Om inte, Tillåt åtkomst till den [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka. Det här kravet gäller bara när du aktiverar funktionen. Det krävs inte för faktiska användarinloggningar.

    >[!NOTE]
    >Azure AD Connect-versioner 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem med synkronisering av lösenords-hash. Om du _inte_ avser att använda Lösenordssynkronisering för hash-tillsammans med direkt-autentisering, läsa den [Azure AD Connect viktig information](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) vill veta mer.

* **Ställ in autentiseringsuppgifter som domänadministratör**: du måste ha autentiseringsuppgifter som domänadministratör för varje Active Directory-skogen som:
    * Du kan synkronisera till Azure AD via Azure AD Connect.
    * Innehåller användare som du vill aktivera för sömlös SSO.

## <a name="step-2-enable-the-feature"></a>Steg 2: Aktivera funktionen

Aktivera sömlös SSO via [Azure AD Connect](active-directory-aadconnect.md).

Om du gör en ren installation av Azure AD Connect, väljer du den [anpassade installationssökvägen](active-directory-aadconnect-get-started-custom.md). På den **användarinloggning** väljer den **aktivera enkel inloggning på** alternativet.

![Azure AD Connect: Användarens inloggning](./media/active-directory-aadconnect-sso/sso8.png)

Om du redan har en installation av Azure AD Connect, väljer du den **ändra användarens inloggning** i Azure AD Connect och väljer sedan **nästa**.

![Azure AD Connect: Ändra användare logga in](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Fortsätt genom guiden tills du kommer till den **aktivera enkel inloggning på** sida. Ange autentiseringsuppgifter som domänadministratör för varje Active Directory-skogen som:
    * Du kan synkronisera till Azure AD via Azure AD Connect.
    * Innehåller användare som du vill aktivera för sömlös SSO.

När guiden har slutförts, är sömlös SSO aktiverat på din klient.

>[!NOTE]
> Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen.

Följ instruktionerna för att kontrollera att du har aktiverat sömlös SSO korrekt:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med global administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**.
4. Kontrollera att den **sömlös enkel inloggning** funktionen visas som **aktiverad**.

![Azure-portalen: Azure AD Connect-fönstret](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Steg 3: Lansera funktionen

Du måste lägga till följande Azure AD-webbadresser till användarnas intranätsinställningar för zonen med hjälp av Grupprincip i Active Directory för att lansera funktionen till dina användare:

- https://AutoLogon.microsoftazuread-sso.com
- https://aadg.Windows.NET.nsatc.NET

Dessutom måste du aktivera en intranät-zonen principinställningen kallas **tillåta uppdateringar av statusfältet via skript** via en Grupprincip. 

>[!NOTE]
> Följande instruktioner fungerar endast för Internet Explorer och Google Chrome på Windows (om den delar en uppsättning URL: er för betrodda webbplatser i Internet Explorer). Läs avsnittet för instruktioner om hur du ställer in Mozilla Firefox och Google Chrome på Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Varför behöver du ändra användarnas zonen intranätsinställningar?

Som standard beräknas automatiskt rätt zonen Internet eller intranätet från en specifik URL i webbläsaren. Till exempel mappas ”http://contoso/” till zonen Intranät, medan ”http://intranet.contoso.com/” mappar till zonen Internet (eftersom den innehåller en punkt). Webbläsare skickar inte Kerberos-biljetter till en molnslutpunkt som de två webbadresserna i Azure AD såvida du inte uttryckligen lägger URL: en webbläsarens intranätzonen.

### <a name="detailed-steps"></a>Detaljerade steg

1. Öppna verktyget Redigeraren för Grupprinciphantering.
2. Redigera en grupprincip som tillämpas på vissa eller alla användare. Det här exemplet används **Standarddomänprincip**.
3. Bläddra till **Användarkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Internet Explorer** > **Internet på Kontrollpanelen** > **sidan säkerhet**. Välj sedan **plats till zon Tilldelningslista**.
    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso6.png)
4. Aktivera principen och ange följande värden i dialogrutan:
   - **Värdenamn**: Azure AD-URL: er som Kerberos-biljetter vidarebefordras.
   - **Värdet** (Data): **1** anger zonen Intranät.

   Resultatet ser ut så här:

    Värde: https://autologon.microsoftazuread-sso.com
  
    Data: 1
        
   Värde: https://aadg.windows.net.nsatc.net

    Data: 1

   >[!NOTE]
   > Om du vill neka vissa användare från att använda sömlös SSO (till exempel om dessa användare logga in på delade kiosker), anger ovanstående värden till **4**. Den här åtgärden lägger till URL: er för Azure AD begränsad zon och misslyckas sömlös SSO hela tiden.
   >

5. Välj **OK**, och välj sedan **OK** igen.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso7.png)

6. Bläddra till **Användarkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Internet Explorer** > **Internet på Kontrollpanelen** > **sidan säkerhet** > **intranätzon**. Välj sedan **tillåta uppdateringar av statusfältet via skript**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso11.png)

7. Aktivera principinställningen och välj sedan **OK**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Överväganden för webbläsare

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox använda inte automatiskt Kerberos-autentisering. Varje användare måste manuellt lägga till URL: er för Azure AD att deras Firefox inställningar med hjälp av följande steg:
1. Kör Firefox och ange `about:config` i adressfältet. Ignorera alla meddelanden som visas.
2. Sök efter den **network.negotiate-auth.trusted-URI: er** inställningar. Den här inställningen visar Firefoxs betrodda platser för Kerberos-autentisering.
3. Högerklicka och välj **ändra**.
4. Ange https://autologon.microsoftazuread-sso.com https://aadg.windows.net.nsatc.net i fältet.
5. Välj **OK** och sedan öppna webbläsaren.

#### <a name="safari-on-mac-os"></a>Safari på Mac OS

Se till att datorn som kör Mac OS är ansluten till Azure AD. Anvisningar för att ansluta till Azure AD finns [bästa praxis för integrering med Active Directory i OS X](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-on-mac-os"></a>Google Chrome på Mac OS

Google Chrome på Mac OS x och andra icke-Windows-plattformar finns i [av krom princip projektlista](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) för information om hur du godkända Azure AD-URL: er för integrerad autentisering.

Användning av Grupprincip i Active Directory tredjepartstillägg lansera URL: er för Azure AD Firefox och Google Chrome på Mac-användare ligger utanför omfånget för den här artikeln.

#### <a name="known-browser-limitations"></a>Kända webbläsare begränsningar

Sömlös SSO fungerar inte i privat webbläsarens läge i Firefox och Edge-webbläsare. Den också fungerar inte på Internet Explorer om webbläsaren körs i förbättrad skyddat läge.

>[!IMPORTANT]
>Vi nyligen återställde stöd för kant för att undersöka problem som rapporteras av kunden.

## <a name="step-4-test-the-feature"></a>Steg 4: Testa funktionen

Om du vill testa funktionen för en viss användare, kontrollerar du att följande villkor är uppfyllda:
  - Användaren loggar in på företagets enheter.
  - Enheten är ansluten till Active Directory-domänen.
  - Enheten har en direkt anslutning till domänkontrollanten (DC) på företagets kabelanslutna eller trådlösa nätverk eller via en fjärranslutning, till exempel en VPN-anslutning.
  - Du har [distribuerat funktionen](##step-3-roll-out-the-feature) till den här användaren via en Grupprincip.

Att testa scenariot där användaren anger endast användarnamnet men inte lösenordet:
   - Logga in på https://myapps.microsoft.com/ i en ny privat webbläsarsession.

Använd någon av de här stegen för att testa scenariot där användaren inte behöver ange användarnamnet eller lösenordet: 
   - Logga in på https://myapps.microsoft.com/contoso.onmicrosoft.com i en ny privat webbläsarsession. Ersätt *contoso* med namnet för din klient.
   - Logga in på https://myapps.microsoft.com/contoso.com i en ny privat webbläsarsession. Ersätt *contoso.com* med en verifierad domän (inte en federerad domän) på din klient.

## <a name="step-5-roll-over-keys"></a>Steg 5: Återställ över nycklar

I steg 2 skapar Azure AD Connect datorkonton (som representerar Azure AD) i alla Active Directory-skogar som du har aktiverat sömlös SSO. Läs mer i [Azure Active Directory sömlös enkel inloggning: tekniska ingående](active-directory-aadconnect-sso-how-it-works.md). För bättre säkerhet rekommenderar vi att du regelbundet rulla över Kerberos dekrypteringsnycklar för dessa konton. Anvisningar om hur du distribuerar över nycklar finns [Azure Active Directory sömlös enkel inloggning: vanliga frågor och](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Du behöver inte göra det här steget _omedelbart_ när du har aktiverat funktionen. Rulla över dekrypteringsnycklar Kerberos minst en gång var 30: e dag.

## <a name="next-steps"></a>Nästa steg

- [Tekniska ingående](active-directory-aadconnect-sso-how-it-works.md): Förstå hur sömlös Single Sign-On-funktionen fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-sso-faq.md): få svar på vanliga frågor och svar om sömlös enkel inloggning.
- [Felsöka](active-directory-aadconnect-troubleshoot-sso.md): Lär dig att lösa vanliga problem med funktionen sömlös enkel inloggning.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktioner som efterfrågas.
