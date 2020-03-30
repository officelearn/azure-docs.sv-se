---
title: 'Azure AD Connect: Sömlös enkel inloggning – snabbstart | Microsoft-dokument'
description: I den här artikeln beskrivs hur du kommer igång med Azure Active Directory Seamless Single Sign-On
services: active-directory
keywords: vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec56d37ca2c0a199968707b3d93f4797be2beca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261208"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory Sömlös enkel inloggning: Snabbstart

## <a name="deploy-seamless-single-sign-on"></a>Distribuera sömlös enkel inloggning

Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless SSO) signerar automatiskt användare när de finns på sina företagsdator som är anslutna till ditt företagsnätverk. Sömlös SSO ger dina användare enkel åtkomst till dina molnbaserade program utan att behöva några ytterligare lokala komponenter.

Så här distribuerar du Sömlös SSO.

## <a name="step-1-check-the-prerequisites"></a>Steg 1: Kontrollera förutsättningarna

Se till att följande förutsättningar finns på plats:

* **Konfigurera Din Azure AD Connect-server:** Om du använder [direktautentisering](how-to-connect-pta.md) som inloggningsmetod krävs ingen ytterligare nödvändig kontroll. Om du använder [synkronisering av lösenordshã¥1er](how-to-connect-password-hash-synchronization.md) som inloggningsmetod och om det finns en brandvägg mellan Azure AD Connect och Azure AD kontrollerar du att:
   - Du använder version 1.1.644.0 eller senare av Azure AD Connect. 
   - Om brandväggen eller proxyn tillåter DNS-vitlistning ** \*** vitlistar du anslutningarna till .msappproxy.net url:er över port 443. Om inte, tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka. Den här förutsättningen gäller endast när du aktiverar funktionen. Det krävs inte för faktiska användarloggningar.

    >[!NOTE]
    >Azure AD Connect-versionerna 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem relaterat till synkronisering av lösenord hash.0. Om du _inte_ tänker använda synkronisering av lösenordsh hash-synkronisering i samband med direktautentisering läser du [azure AD Connect-viktig information](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) om du vill veta mer.

* **Använd en Azure AD Connect-topologi som stöds:** Kontrollera att du använder en av Azure AD Connects topologier som stöds som beskrivs [här](plan-connect-topologies.md).

    >[!NOTE]
    >Sömlös SSO stöder flera AD-skogar, oavsett om det finns AD-förtroenden mellan dem eller inte.

* **Konfigurera autentiseringsuppgifter för domänadministratören**: Du måste ha autentiseringsuppgifter för domänadministratören för varje Active Directory-skog som:
    * Du synkroniserar till Azure AD via Azure AD Connect.
    * Innehåller användare som du vill aktivera för Sömlös SSO.
    
* **Aktivera modern autentisering:** Du måste aktivera [modern autentisering](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) på din klient för att den här funktionen ska fungera.

* **Använd de senaste versionerna av Office 365-klienter:** För att få en tyst inloggningsupplevelse med Office 365-klienter (Outlook, Word, Excel och andra) måste användarna använda versionerna 16.0.8730.xxxx eller senare.

## <a name="step-2-enable-the-feature"></a>Steg 2: Aktivera funktionen

Aktivera sömlös SSO via [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Du kan också [aktivera Sömlös SSO med PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) om Azure AD Connect inte uppfyller dina krav. Använd det här alternativet om du har mer än en domän per Active Directory-skog och du vill vara mer inriktad på den domän som du vill aktivera Sömlös SSO för.

Om du gör en ny installation av Azure AD Connect väljer du den [anpassade installationssökvägen](how-to-connect-install-custom.md). På sidan **Använda inloggning** väljer du alternativet Aktivera **enkel inloggning.**

>[!NOTE]
> Alternativet är endast tillgängligt för val om inloggningsmetoden är **synkronisering av lösenord hash** eller **direktautentisering**.

![Azure AD Connect: Användarloggning](./media/how-to-connect-sso-quick-start/sso8.png)

Om du redan har en installation av Azure AD Connect väljer du inloggningssidan **Ändra användare i** Azure AD Connect och väljer sedan **Nästa**. Om du använder Azure AD Connect-versioner 1.1.880.0 eller högre väljs alternativet **Aktivera enkel inloggning** som standard. Om du använder äldre versioner av Azure AD Connect väljer du alternativet **Aktivera enkel inloggning.**

![Azure AD Connect: Ändra användarloggningen](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Fortsätt genom guiden tills du kommer till sidan **Aktivera enkel inloggning.** Ange autentiseringsuppgifter för domänadministratören för varje Active Directory-skog som:

* Du synkroniserar till Azure AD via Azure AD Connect.
* Innehåller användare som du vill aktivera för Sömlös SSO.

När guiden har slutförts är Sömlös SSO aktiverat för din klientorganisation.

>[!NOTE]
> Domänadministratörsuppgifterna lagras inte i Azure AD Connect eller i Azure AD. De används bara för att aktivera funktionen.

Följ dessa instruktioner för att kontrollera att du har aktiverat Seamless SSO korrekt:

1. Logga in på [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory med globala administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**.
4. Kontrollera att funktionen **Sömlös enkel inloggning** visas som **Aktiverad**.

![Azure-portal: Azure AD Connect-fönster](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Sömlös SSO skapar ett `AZUREADSSOACC` datorkonto som namnges i din lokala Active Directory (AD) i varje AD-skog. Datorkontot `AZUREADSSOACC` måste skyddas starkt av säkerhetsskäl. Endast domänadministratörer bör kunna hantera datorkontot. Kontrollera att Kerberos-delegering på datorkontot är inaktiverat och att `AZUREADSSOACC` inget annat konto i Active Directory har delegeringsbehörigheter för datorkontot. Lagra datorkontot i en organisationsenhet (OU) där de är säkra från oavsiktliga borttagningar och där endast domänadministratörer har åtkomst.

>[!NOTE]
> Om du använder arkitekturer för pass-the-hash- och autentiseringsuppgifter för stöld i din `AZUREADSSOACC` lokala miljö gör du lämpliga ändringar för att säkerställa att datorkontot inte hamnar i karantänbehållaren. 

## <a name="step-3-roll-out-the-feature"></a>Steg 3: Rulla ut funktionen

Du kan gradvis distribuera Sömlös SSO till användarna med hjälp av instruktionerna nedan. Du börjar med att lägga till följande Azure AD-URL i alla eller valda användares zoninställningar för intranät med hjälp av Grupprincip i Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Dessutom måste du aktivera en zonprincipinställning för intranät som heter **Tillåt uppdateringar av statusfältet via skript** via grupprincip. 

>[!NOTE]
> Följande instruktioner fungerar bara för Internet Explorer och Google Chrome i Windows (om den delar en uppsättning betrodda webbadresser med Internet Explorer). Läs nästa avsnitt för instruktioner om hur du ställer in Mozilla Firefox och Google Chrome på macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Varför behöver du ändra användarnas intranätzonsinställningar?

Som standard beräknar webbläsaren automatiskt rätt zon, antingen Internet eller intranät, från en viss URL. Kartor till `http://contoso/` zonen Intranät, medan `http://intranet.contoso.com/` kartor till zonen Internet (eftersom URL:en innehåller en punkt). Webbläsare skickar inte Kerberos-biljetter till en molnslutpunkt, till exempel Azure AD-URL:en, såvida du inte uttryckligen lägger till URL:en i webbläsarens intranätzon.

Det finns två sätt att ändra användarnas zoninställningar för intranät:

| Alternativ | Admin övervägande | Användarupplevelse |
| --- | --- | --- |
| Grupprincip | Administratör låser redigering av intranätzonsinställningar | Användare kan inte ändra sina egna inställningar |
| Inställningar för grupprincip |  Admin tillåter redigering på intranät zoninställningar | Användare kan ändra sina egna inställningar |

### <a name="group-policy-option---detailed-steps"></a>Alternativet "Grupprincip" - Detaljerade steg

1. Öppna redigeraren för grupprinciphantering.
2. Redigera grupprincipen som tillämpas på vissa eller alla användare. I det här exemplet används **standarddomänprincip**.
3. Bläddra till**administrativa mallar för** >  >  > **användarkonfigurationsprinciper** > **i Windows Components** > **Internet Explorer**Internet **User Configuration****Kontrollpanelen.** > **Security Page** Välj sedan **Plats till zontilldelningslista**.
    ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso6.png)
4. Aktivera principen och ange sedan följande värden i dialogrutan:
   - **Värdenamn**: Azure AD-URL:en där Kerberos-biljetterna vidarebefordras.
   - **Värde** (Data): **1** anger zonen Intranät.

     Resultatet ser ut så här:

     Värdenamn:`https://autologon.microsoftazuread-sso.com`
  
     Värde (Data): 1

   >[!NOTE]
   > Om du inte vill tillåta vissa användare från att använda Sömlös SSO (till exempel om dessa användare loggar in på delade kiosker) ställer du in föregående värden på **4**. Den här åtgärden lägger till Azure AD-URL:en i zonen Begränsad och misslyckas sömlös SSO hela tiden.
   >

5. Välj **OK** och sedan **OK** igen.

    ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso7.png)

6. Bläddra till**Policy** > **administrativa mallar för** >  **användarkonfigurationsprinciper** > **Windows Components** > **Internet Explorer** > **Internet Kontrollpanelens** > **Security Page** > **intranätzon**. Välj sedan **Tillåt uppdateringar av statusfältet via skript**.

    ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso11.png)

7. Aktivera principinställningen och välj sedan **OK**.

    ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Alternativet "Grupprincip" - Detaljerade steg

1. Öppna redigeraren för grupprinciphantering.
2. Redigera grupprincipen som tillämpas på vissa eller alla användare. I det här exemplet används **standarddomänprincip**.
3. Bläddra till Inställningar för **användarkonfiguration** >  >  > **Windows-inställningar****För register** > **nytt** > **register**.**Preferences**

    ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso15.png)

4. Ange följande värden i lämpliga fält och klicka på **OK**.
   - **Nyckelväg:** ***Programvara\Microsoft\Windows\CurrentVersion\Internet-inställningar\ZoneMap\Domäner\microsoftazuread-sso.com\autologon***
   - **Värdenamn**: ***https***.
   - **Värdetyp**: ***REG_DWORD***.
   - **Värdedata**: ***000000001***.
 
     ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Enkel inloggning](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Webbläsaröverväganden

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alla plattformar)

Mozilla Firefox använder inte automatiskt Kerberos-autentisering. Varje användare måste manuellt lägga till Azure AD-URL:en i sina Firefox-inställningar med hjälp av följande steg:
1. Kör Firefox `about:config` och skriv in i adressfältet. Avvisa alla aviseringar som visas.
2. Sök efter inställningarna **network.negotiate-auth.trusted-uris.** Den här inställningen visar Firefox betrodda platser för Kerberos-autentisering.
3. Högerklicka och välj **Ändra**.
4. Skriv `https://autologon.microsoftazuread-sso.com` in i fältet .
5. Välj **OK** och öppna sedan webbläsaren igen.

#### <a name="safari-macos"></a>Safari (macOS)

Kontrollera att datorn som kör macOS är ansluten till AD. Instruktioner för AD-anslutning till din macOS-enhet ligger utanför den här artikelns omfattning.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baserat på krom (alla plattformar)

Om du har åsidosatt [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) eller [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) principinställningar i din miljö, se till`https://autologon.microsoftazuread-sso.com`att du lägger till Azure AD:s URL ( ) till dem också.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge baserat på Krom (macOS och andra plattformar som inte kommer från Windows)

För Microsoft Edge baserat på Krom på Mac OS och andra plattformar som inte tillhör Windows läser du [Microsoft Edge baserat på Chromium Policy List](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) för information om hur du lägger till Azure AD-URL:en för integrerad autentisering i din tillåt-lista.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (alla plattformar)

Om du har åsidosatt [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) eller [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) principinställningar i din miljö, se till`https://autologon.microsoftazuread-sso.com`att du lägger till Azure AD URL ( ) till dem också.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS och andra plattformar som inte kommer från Windows)

För Google Chrome på Mac OS och andra plattformar som inte kommer från Windows läser du Policylistan för [Kromprojekt](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) för information om hur du vitlistar Azure AD-URL:en för integrerad autentisering.

Användningen av active directory-grupprinklagelser från tredje part för att distribuera Azure AD-URL:en till Firefox- och Google Chrome-användare på Mac-användare omfattas inte av den här artikeln.

#### <a name="known-browser-limitations"></a>Kända webbläsarbegränsningar

Sömlös SSO fungerar inte i privat surfningsläge på webbläsaren Firefox och Microsoft Edge. Det fungerar inte heller på Internet Explorer om webbläsaren körs i utökat skyddat läge. För nästa version av Microsoft Edge baserat på Krom, kommer det inte att fungera i InPrivate och gästläge av design.

## <a name="step-4-test-the-feature"></a>Steg 4: Testa funktionen

Om du vill testa funktionen för en viss användare kontrollerar du att alla följande villkor är uppfyllda:
  - Användaren loggar in på en företagsenhet.
  - Enheten är ansluten till Active Directory-domänen. Enheten behöver _inte_ vara [Azure AD-ansluten](../active-directory-azureadjoin-overview.md).
  - Enheten har en direkt anslutning till domänkontrollanten (DC), antingen i företagets kabelanslutna eller trådlösa nätverk eller via en fjärråtkomstanslutning, till exempel en VPN-anslutning.
  - Du har [distribuerat funktionen](#step-3-roll-out-the-feature) till den här användaren via grupprincipen.

Så här testar du scenariot där användaren bara anger användarnamnet, men inte lösenordet:
   - Logga in `https://myapps.microsoft.com/` på i en ny privat webbläsarsession.

Om du vill testa scenariot där användaren inte behöver ange användarnamnet eller lösenordet använder du något av följande steg: 
   - Logga in `https://myapps.microsoft.com/contoso.onmicrosoft.com` på i en ny privat webbläsarsession. Ersätt *contoso* med din klients namn.
   - Logga in `https://myapps.microsoft.com/contoso.com` på i en ny privat webbläsarsession. Ersätt *contoso.com* med en verifierad domän (inte en federerad domän) på din klientorganisation.

## <a name="step-5-roll-over-keys"></a>Steg 5: Rulla över tangenter

I steg 2 skapar Azure AD Connect datorkonton (som representerar Azure AD) i alla Active Directory-skogar där du har aktiverat Sömlös SSO. Mer information finns i [Azure Active Directory Seamless Single Sign-On: Technical deep dive](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Kerberos-dekrypteringsnyckeln på ett datorkonto, om den läcker, kan användas för att generera Kerberos-biljetter för alla användare i AD-skogen. Skadliga aktörer kan sedan personifiera Azure AD-inloggningar för komprometterade användare. Vi rekommenderar starkt att du regelbundet rullar över dessa Kerberos dekrypteringsnycklar - minst en gång var 30:e dag.

Instruktioner om hur du överför nycklar finns i [Azure Active Directory Seamless Single Sign-On: Vanliga frågor och svar](how-to-connect-sso-faq.md). Vi arbetar på en förmåga att införa automatiserad roll over av nycklar.

>[!IMPORTANT]
>Du behöver inte göra det här steget _direkt_ efter att du har aktiverat funktionen. Rulla över Kerberos dekrypteringsnycklarna minst en gång var 30:e dag.

## <a name="next-steps"></a>Nästa steg

- [Teknisk djupdykning:](how-to-connect-sso-how-it-works.md)Förstå hur seamless single sign-on-funktionen fungerar.
- Vanliga frågor och [svar:](how-to-connect-sso-faq.md)Få svar på vanliga frågor om Sömlös enkel inloggning.
- [Felsöka:](tshoot-connect-sso.md)Lär dig hur du löser vanliga problem med sömlös enkel inloggning.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory Forum för att fila nya funktionsbegäranden.
