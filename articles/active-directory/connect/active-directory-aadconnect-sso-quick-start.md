---
title: 'Azure AD Connect: Sömlös enkel inloggning – Snabbstart | Microsoft Docs'
description: Den här artikeln beskrivs hur du kommer igång med Azure Active Directory sömlös enkel inloggning
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: df936c697f500f5ab98becd1529cd321f9f3f5c4
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259127"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory sömlös enkel inloggning: Snabbstart

## <a name="deploy-seamless-single-sign-on"></a>Distribuera sömlös enkel inloggning

Azure Active Directory (Azure AD) sömlös enkel inloggning (sömlös SSO) loggar automatiskt in användare när de är på sina företagets datorer som är anslutna till företagsnätverket. Sömlös enkel inloggning ger användarna med enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.

Följ dessa steg om du vill distribuera sömlös enkel inloggning.

## <a name="step-1-check-the-prerequisites"></a>Steg 1: Kontrollera krav

Se till att följande krav är uppfyllda:

* **Konfigurera Azure AD Connect-servern**: Om du använder [direktautentisering](active-directory-aadconnect-pass-through-authentication.md) som din inloggningsmetod, krävs ingen ytterligare kravkontrollen. Om du använder [lösenordshashsynkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md) som din inloggningsmetod, och om det finns en brandvägg mellan Azure AD Connect och Azure AD, kontrollerar du att:
   - Du använder version 1.1.644.0 eller senare av Azure AD Connect. 
   - Om din brandvägg eller proxy tillåter DNS-lista över tillåtna, lista över tillåtna anslutningar till den  **\*. msappproxy.net** URL: er via port 443. Om den inte tillåter åtkomst till den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka. Det här kravet gäller bara när du aktiverar funktionen. Det krävs inte för faktiska användarinloggningar.

    >[!NOTE]
    >Azure AD Connect-versioner 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem med synkronisering av lösenordshash. Om du _inte_ planerar att använda synkronisering av lösenordshash tillsammans med direktautentisering, läsa den [viktig information om Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) vill veta mer.

* **Använda en stödd Azure AD Connect-topologi**: Kontrollera att du använder en av Azure AD Connect topologier som stöds beskrivs [här](active-directory-aadconnect-topologies.md).

* **Ställ in autentiseringsuppgifter som domänadministratör**: du måste ha administratörsbehörighet för domänen för varje Active Directory-skogen som:
    * Du kan synkronisera till Azure AD via Azure AD Connect.
    * Innehåller användare som du vill aktivera för sömlös enkel inloggning.
    
* **Aktivera modern autentisering**: du måste aktivera [modern autentisering](https://aka.ms/modernauthga) på din klient för den här funktionen ska fungera.

* **Använd de senaste versionerna av Office 365-klienter**: för att få en tyst inloggningsupplevelse med Office 365-klienter (Outlook, Word, Excel och andra) kan du behöver 16.0.8730.xxxx versioner eller senare.

## <a name="step-2-enable-the-feature"></a>Steg 2: Aktivera funktionen

Aktivera sömlös SSO via [Azure AD Connect](active-directory-aadconnect.md).

Om du gör en ny installation av Azure AD Connect, väljer du den [anpassade installationssökväg](active-directory-aadconnect-get-started-custom.md). På den **användarinloggning** väljer den **aktivera enkel inloggning** alternativet.

![Azure AD Connect: Användarinloggning](./media/active-directory-aadconnect-sso/sso8.png)

Om du redan har en installation av Azure AD Connect, Välj den **ändra användarinloggning** i Azure AD Connect och välj sedan **nästa**.

![Azure AD Connect: Ändra användarinloggning](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Fortsätt genom guiden tills du kommer till den **aktivera enkel inloggning** sidan. Ange autentiseringsuppgifter som domänadministratör för varje Active Directory-skogen som:
    * Du kan synkronisera till Azure AD via Azure AD Connect.
    * Innehåller användare som du vill aktivera för sömlös enkel inloggning.

När du har slutfört guiden är sömlös enkel inloggning aktiverat på din klient.

>[!NOTE]
> Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen.

Följ dessa instruktioner för att kontrollera att du har aktiverat sömlös enkel inloggning på rätt sätt:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med autentiseringsuppgifterna för global administratör för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**.
4. Kontrollera att den **sömlös enkel inloggning** funktionen visas som **aktiverad**.

![Azure-portalen: Azure AD Connect-fönstret](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Steg 3: Distribuera funktionen

Du kan gradvis rulla ut sömlös enkel inloggning till dina användare med hjälp av anvisningarna nedan. Du startar genom att lägga till följande Azure AD-URL för alla eller valt användares Zoninställningar för intranätet med hjälp av Grupprincip i Active Directory:

- https://autologon.microsoftazuread-sso.com

Dessutom måste du aktivera en princip för intranät-zonen som heter **tillåta uppdateringar till statusfältet via skript** via en Grupprincip. 

>[!NOTE]
> Följande instruktioner fungerar endast för Internet Explorer och Google Chrome på Windows (om den delar en uppsättning tillförlitlig URL: er med Internet Explorer). Läs nästa avsnitt för anvisningar om hur du ställer in Mozilla Firefox och Google Chrome på macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Varför behöver du ändra användares Zoninställningar för intranätet?

Som standard beräknas automatiskt rätt zonen Internet- eller intranätvärdar från en specifik URL i webbläsaren. Till exempel ”http://contoso/” mappar till zonen Intranät, medan ”http://intranet.contoso.com/” mappar till zonen Internet (eftersom URL: en innehåller en punkt). Webbläsare skickar inte Kerberos-biljetter till en slutpunkt i molnet, t.ex. Azure AD-URL, såvida inte du uttryckligen lägga till URL: en till webbläsarens intranätzonen.

Det finns två sätt att ändra användares Zoninställningar för intranätet:

| Alternativ | Admin-överväganden | Användarupplevelse |
| --- | --- | --- |
| Grupprincip | Admin-Lås ned redigering av Zoninställningar för intranätet | Användare kan inte ändra sina egna inställningar |
| Grupp principen till inställningar |  Administratören tillåter redigering i Zoninställningar för intranätet | Användarna kan ändra sina egna inställningar |

### <a name="group-policy-option---detailed-steps"></a>”Grupprincip” alternativet - detaljerade steg

1. Öppna Redigeraren för Grupprinciphantering-verktyget.
2. Redigera grupprincipen som tillämpas på vissa eller alla användare. Det här exemplet används **Standarddomänprincip**.
3. Bläddra till **Användarkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Internet Explorer** > **Internet på Kontrollpanelen** > **säkerhetssidan**. Välj sedan **platser till zoner tilldelning**.
    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso6.png)
4. Aktivera principen och ange följande värden i dialogrutan:
   - **Värdenamn**: Azure AD-URL som Kerberos-biljetter vidarebefordras.
   - **Värdet** (Data): **1** indikerar att zonen Intranät.

    Resultatet ser ut så här:

    Värde:https://autologon.microsoftazuread-sso.com
  
    Data: 1

   >[!NOTE]
   > Om du vill neka vissa användare från att använda sömlös enkel inloggning (till exempel om dessa användare logga in på delade kiosker), ange föregående värdena till **4**. Den här åtgärden lägger till Azure AD-URL för zonen Ej tillförlitliga platser och misslyckas sömlös SSO hela tiden.
   >

5. Välj **OK**, och välj sedan **OK** igen.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso7.png)

6. Bläddra till **Användarkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Internet Explorer** > **Internet på Kontrollpanelen** > **säkerhetssidan** > **intranätzonen**. Välj sedan **tillåta uppdateringar till statusfältet via skript**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso11.png)

7. Aktivera den här inställningen och väljer sedan **OK**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>”Grupp principen till inställningar” alternativet - detaljerade steg

1. Öppna Redigeraren för Grupprinciphantering-verktyget.
2. Redigera grupprincipen som tillämpas på vissa eller alla användare. Det här exemplet används **Standarddomänprincip**.
3. Bläddra till **Användarkonfiguration** > **inställningar** > **Windows-inställningar** > **registret**  >  **New** > **registerobjekt**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso15.png)

4. Ange följande värden i lämpliga fält och klicka på **OK**.
   - **Nyckeln sökväg**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Värdenamn**: ***https***.
   - **Värdetypen**: ***REG_DWORD***.
   - **Värdedata**: ***00000001***.
 
    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso16.png)
 
    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso17.png)

6. Bläddra till **Användarkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Internet Explorer** > **Internet på Kontrollpanelen** > **säkerhetssidan** > **intranätzonen**. Välj sedan **tillåta uppdateringar till statusfältet via skript**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso11.png)

7. Aktivera den här inställningen och väljer sedan **OK**.

    ![Enkel inloggning](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Överväganden för webbläsare

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alla plattformar)

Mozilla Firefox använda inte automatiskt Kerberos-autentisering. Varje användare måste manuellt lägga till Azure AD-URL till sina Firefox-inställningar med hjälp av följande steg:
1. Kör Firefox och ange `about:config` i adressfältet. Ignorera alla meddelanden som visas.
2. Sök efter den **network.negotiate-auth.trusted-URI: er** inställningar. Den här inställningen visar Firefoxs betrodda platser för Kerberos-autentisering.
3. Högerklicka och välj **ändra**.
4. Ange https://autologon.microsoftazuread-sso.com i fältet.
5. Välj **OK** och öppnar sedan webbläsaren igen.

#### <a name="safari-macos"></a>Safari (macOS)

Se till att den dator som kör macOS är ansluten till AD. Anvisningar om hur du ansluter AD finns i [bästa praxis för integrering med Active Directory i OS X](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (alla plattformar)

Om du har åsidosätts den [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) eller [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) principinställningar i din miljö, se till att du lägger till Azure AD-URL (https://autologon.microsoftazuread-sso.com) till dem också.

#### <a name="google-chrome-macos-only"></a>Google Chrome (endast macOS)

Google Chrome på Mac OS och andra icke-Windows-plattformar finns i [krom projekt Policy List](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) information om hur du godkänna Azure AD-URL för integrerad autentisering.

Användning av tredje parts Grupprincip i Active Directory-tillägg för att distribuera Azure AD-URL till Firefox och Google Chrome på Mac-användare inte omfattas av den här artikeln.

#### <a name="known-browser-limitations"></a>Kända webbläsare begränsningar

Sömlös SSO fungerar inte i privat bläddrat läge på Firefox och Edge-webbläsare. Det också fungerar inte i Internet Explorer om webbläsaren körs i förbättrad skyddat läge.

## <a name="step-4-test-the-feature"></a>Steg 4: Testa funktionen

Om du vill testa funktionen för en viss användare att se till att följande villkor är uppfyllda:
  - Användaren loggar in på företagets enhet.
  - Enheten är ansluten till din Active Directory-domän.
  - Enheten har en direkt anslutning till domänkontrollanten (DC) i företagets kabelanslutna eller trådlösa nätverk eller via en fjärranslutning, till exempel en VPN-anslutning.
  - Du har [lanseras funktionen](##step-3-roll-out-the-feature) till den här användaren via en Grupprincip.

Att testa scenariot där användaren anger bara användarnamnet, men inte lösenordet:
   - Logga in på https://myapps.microsoft.com/ i en ny privat webbläsarsession.

Använd någon av de här stegen för att testa scenariot där användaren inte behöver ange användarnamnet eller lösenordet: 
   - Logga in på https://myapps.microsoft.com/contoso.onmicrosoft.com i en ny privat webbläsarsession. Ersätt *contoso* med klientens namn.
   - Logga in på https://myapps.microsoft.com/contoso.com i en ny privat webbläsarsession. Ersätt *contoso.com* med en verifierad domän (inte en federerad domän) på din klient.

## <a name="step-5-roll-over-keys"></a>Steg 5: Förnya nycklar

I steg 2 skapar datorkonton (Visa Azure AD) i alla Active Directory-skogar som du har aktiverat sömlös enkel inloggning i Azure AD Connect. Mer information finns i [Azure Active Directory sömlös enkel inloggning: teknisk djupdykning](active-directory-aadconnect-sso-how-it-works.md).

>[!IMPORTANT]
>Kerberos-krypteringsnyckel på ett datorkonto kan om sprids, användas att generera Kerberos-biljetter för alla användare i dess AD-skog. Skadliga aktörer kan sedan personifiera Azure AD-inloggningar för komprometterade användare. Vi rekommenderar starkt att du byter periodvis över dessa Kerberos-dekrypteringsnycklar – minst en gång var 30: e dag.

Anvisningar om hur du förnyar nycklar finns i [Azure Active Directory sömlös enkel inloggning: vanliga frågor och svar](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Vi arbetar på en funktion för att introducera automatiserad distribution över av nycklar.

>[!IMPORTANT]
>Du behöver inte göra det här steget _omedelbart_ när du har aktiverat funktionen. Om du förnya Kerberos-dekrypteringsnycklar på minst en gång var 30: e dag.

## <a name="next-steps"></a>Nästa steg

- [Teknisk djupdykning](active-directory-aadconnect-sso-how-it-works.md): Förstå hur funktionen sömlös enkel inloggning fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-sso-faq.md): få svar på vanliga frågor och svar om sömlös enkel inloggning.
- [Felsöka](active-directory-aadconnect-troubleshoot-sso.md): Lär dig att lösa vanliga problem med funktionen för sömlös enkel inloggning.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktionbegäran.
