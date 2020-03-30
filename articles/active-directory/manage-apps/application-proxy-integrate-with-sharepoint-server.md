---
title: Aktivera fjärråtkomst till SharePoint - Azure AD Application Proxy
description: Beskriver grunderna om hur du integrerar en lokal SharePoint-server med Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481304"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy

I den här steg-för-steg-guiden beskrivs hur du integrerar en lokal SharePoint-servergrupp med Azure Active Directory (Azure AD) Application Proxy.

## <a name="prerequisites"></a>Krav

För att utföra konfigurationen behöver du följande resurser:
- En SharePoint 2013-grupp eller nyare.
- En Azure AD-klient med en plan som innehåller programproxy. Läs mer om [Azure AD-abonnemang och priser](https://azure.microsoft.com/pricing/details/active-directory/).
- En [anpassad, verifierad domän](../fundamentals/add-custom-domain.md) i Azure AD-klienten.
- Lokala Active Directory synkroniserade med Azure AD Connect, genom vilken användare kan [logga in på Azure](../hybrid/plan-connect-user-signin.md).
- En Application Proxy-anslutning installerad och körs på en dator inom företagsdomänen.

Konfigurera SharePoint med Programproxy kräver två webbadresser:
- En extern URL som är synlig för slutanvändare och bestämd i Azure AD. Den här URL:en kan använda en anpassad domän. Läs mer om [hur du arbetar med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- En intern URL, känd endast inom företagsdomänen och aldrig används direkt.

> [!IMPORTANT]
> Följ dessa rekommendationer för den interna webbadressen för att se till att länkarna är korrekt mappade:
> - Använd HTTPS.
> - Använd inte anpassade portar.
> - Skapa en värd (A) i DNS (Corporate Domain Name System) för att peka på SharePoint WFE (eller belastningsutjämnaren) och inte ett alias (CName).

I den här artikeln används följande värden:
- Intern URL:`https://sharepoint`
- Extern URL:`https://spsites-demo1984.msappproxy.net/`
- Programpoolkonto för SharePoint-webbprogrammet:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Steg 1: Konfigurera ett program i Azure AD som använder programproxy

I det här steget skapar du ett program i din Azure Active Directory-klient som använder programproxy. Du anger den externa URL:en och anger den interna URL:en, som båda används senare i SharePoint.

1. Skapa appen enligt beskrivningen med följande inställningar. Stegvisa instruktioner finns i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Intern URL:** Intern URL i SharePoint som kommer `https://sharepoint`att anges senare i SharePoint, till exempel .
   * **Förautentisering**: Azure Active Directory
   * **Översätt webbadresser i rubriker:** Nej
   * **Översätt webbadresser i programtexten:** Nej

   ![Publicera SharePoint som program](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. När appen har publicerats följer du dessa steg för att konfigurera inställningarna för enkel inloggning:

   1. På programsidan i portalen väljer du **Enkel inloggning**.
   1. För **enkel inloggningsläge**väljer du Integrerad **Windows-autentisering**.
   1. Ange **SPN för internt program** till det värde som du angav tidigare. I det här exemplet `HTTP/sharepoint`är värdet .
   1. Under **Delegerad inloggningsidentitet**väljer du det lämpligaste alternativet för Active Directory-skogskonfigurationen. Om du till exempel har en enda Active Directory-domän i skogen väljer du **Lokalt SAM-kontonamn** (som visas i följande skärmbild). Men om användarna inte finns i samma domän som SharePoint- och Application Proxy **Connector-servrarna** väljer du Lokalt användarnamn (visas inte i skärmbilden).

   ![Konfigurera integrerad Windows-autentisering för SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Om du vill slutföra inrättandet av programmet går du till avsnittet **Användare och grupper** och tilldelar användare att komma åt det här programmet. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Steg 2: Konfigurera SharePoint-webbprogrammet

SharePoint-webbprogrammet måste konfigureras med Kerberos och lämpliga alternativa åtkomstmappningar för att fungera korrekt med Azure AD Application Proxy. Det finns två möjliga alternativ:

- Skapa ett nytt webbprogram och använd endast standardzonen. Detta är det alternativ som föredras, eftersom det ger den bästa upplevelsen med SharePoint (till exempel pekar länkarna i e-postaviseringarna som genereras av SharePoint alltid på standardzonen).
- Utöka ett befintligt webbprogram för att konfigurera Kerberos i en zon som inte är standard.

> [!IMPORTANT]
> Oavsett den zon som används måste programpoolkontot för SharePoint-webbprogrammet vara ett domänkonto för att Kerberos ska fungera korrekt.

### <a name="provision-the-sharepoint-web-application"></a>Etablera SharePoint-webbprogrammet

- Om du skapar ett nytt webbprogram och bara använder standardzonen (önskat alternativ):

    1. Starta **SharePoint Management Shell** och kör följande skript:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Öppna **sharepoint-webbplatsen för central administration.**
    1. Under **Systeminställningar**väljer du **Konfigurera alternativa åtkomstmappningar**. Rutan **Alternativ åtkomstmappningssamling** öppnas.
    1. Filtrera skärmen med det nya webbprogrammet och bekräfta att du ser något liknande:

       ![Alternativa åtkomstmappningar av webbprogram](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Om du utökar ett befintligt webbprogram till en ny zon (om du inte kan använda standardzonen):

    1. Starta SharePoint Management Shell och kör följande skript:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Öppna **sharepoint-webbplatsen för central administration.**
    1. Under **Systeminställningar**väljer du **Konfigurera alternativa åtkomstmappningar**. Rutan **Alternativ åtkomstmappningssamling** öppnas.
    1. Filtrera skärmen med webbprogrammet som har utökats och bekräfta att du ser något liknande:

        ![Alternativa åtkomstmappningar av utökat program](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Kontrollera att SharePoint-webbprogrammet körs under ett domänkonto

Så här identifierar du kontot som kör programpoolen för SharePoint-webbprogrammet och kontrollerar att det är ett domänkonto:

1. Öppna **sharepoint-webbplatsen för central administration.**
1. Gå till **Säkerhet** och välj **Konfigurera tjänstkonton**.
1. Välj **Webbprogrampool - YourWebApplicationName**.

   ![Alternativ för att konfigurera ett tjänstkonto](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Bekräfta att **Välj ett konto för den här komponenten** returnerar ett domänkonto och kom ihåg det, eftersom det kommer att behövas i nästa steg.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Kontrollera att ett HTTPS-certifikat är konfigurerat för IIS-platsen för zonen Extranät

Eftersom den interna URL:en använder HTTPS-protokoll (`https://SharePoint/`) måste ett certifikat anges på IIS-webbplatsen (Internet Information Services).

1. Öppna Windows PowerShell-konsolen.
1. Kör följande skript för att generera ett självsignerat certifikat och lägg till det i datorns MY-arkiv:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Självsignerade certifikat är endast lämpliga för teständamål. I produktionsmiljöer rekommenderar vi starkt att du använder certifikat som utfärdats av en certifikatutfärdare i stället.

1. Öppna konsolen Internet Information Services Manager.
1. Expandera servern i trädvyn, expandera **Platser,** välj **sharepoint - AAD-proxywebbplatsen** och välj **Bindningar**.
1. Välj **https-bindning** och välj sedan **Redigera**.
1. I fältet TLS/SSL-certifikat väljer du **SharePoint-certifikat** och väljer sedan **OK**.

Du kan nu komma åt SharePoint-webbplatsen externt via Azure AD Application Proxy.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Steg 3: Konfigurera Kerberos-begränsad delegering

Användare autentiserar först i Azure AD och sedan till SharePoint med Hjälp av Kerberos via Azure AD Proxy-anslutningsappen. Om du vill att anslutningen ska kunna hämta en Kerberos-token för Azure AD-användarens räkning måste du konfigurera Kerberos Constrained Delegation (KCD) med protokollövergång. Mer information om KCD finns i [översikt över begränsad delegering av Kerberos.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Ange SPN för SharePoint-tjänstkontot

I den här artikeln `https://sharepoint`är den interna URL:en , `HTTP/sharepoint`och därför är tjänstens huvudnamn (SPN) . Du måste ersätta dessa värden med de värden som motsvarar din miljö.
Om du `HTTP/sharepoint` vill registrera SPN `Contoso\spapppool`för SharePoint-programpoolkontot kör du följande kommando från en kommandotolk som administratör för domänen:

`setspn -S HTTP/sharepoint Contoso\spapppool`

`Setspn` Kommandot söker efter SPN innan det läggs till det. Om SPN redan finns visas ett fel **för duplicerat SPN-värde.** I så fall bör du överväga att ta bort det befintliga SPN-programmet om det inte anges under rätt programpoolkonto. Du kan kontrollera att SPN har lagts `Setspn` till genom att köra kommandot med alternativet -L. Mer information om det här kommandot finns i [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Kontrollera att anslutningen är betrodd för delegering till SPN som har lagts till i SharePoint-programpoolkontot

Konfigurera KCD så att Azure AD Application Proxy-tjänsten kan delegera användaridentiteter till SharePoint-programpoolkontot. Konfigurera KCD genom att aktivera Application Proxy-anslutningen för att hämta Kerberos-biljetter för dina användare som har autentiserats i Azure AD. Sedan skickar den servern kontexten till målprogrammet (SharePoint i det här fallet).

Så här konfigurerar du KCD:00 för varje kopplingsmaskin:

1. Logga in på en domänkontrollant som domänadministratör och öppna sedan Active Directory – användare och datorer.
1. Leta reda på datorn som kör Azure AD Proxy-anslutningsappen. I det här exemplet är det själva SharePoint-servern.
1. Dubbelklicka på datorn och välj sedan fliken **Delegering.**
1. Kontrollera att delegeringsalternativen är **inställda på Lita på den här datorn endast för delegering till de angivna tjänsterna**. Välj sedan **Använd ett autentiseringsprotokoll**.
1. Välj knappen **Lägg till,** välj **Användare eller Datorer**och leta reda på SharePoint-programpoolkontot. Till exempel: `Contoso\spapppool`.
1. Välj den som du skapade tidigare för tjänstkontot i listan över SN:er.
1. Välj **OK** och välj sedan **OK** igen för att spara ändringarna.
  
   ![Inställningar för delegering](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Du är nu redo att logga in på SharePoint med hjälp av den externa URL:en och autentisera med Azure.

## <a name="troubleshoot-sign-in-errors"></a>Felsöka inloggningsfel

Om inloggningen till webbplatsen inte fungerar kan du få mer information om problemet i anslutningsloggarna: Från den dator som kör kopplingen öppnar du loggboken, går till **Program och tjänster Loggar** > **Microsoft** > **AadApplicationProxy** > **Connector**och inspektera **administratörsloggen.**

## <a name="next-steps"></a>Nästa steg

* [Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md)
* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
