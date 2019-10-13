---
title: Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy | Microsoft Docs
description: Beskriver grunderna för hur du integrerar en lokal SharePoint-Server med Azure AD-programproxy.
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286079"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy

I den här guiden beskrivs steg för steg hur du integrerar en lokal SharePoint-grupp med Azure Active Directory (Azure AD) Application Proxy.

## <a name="prerequisites"></a>Krav

För att utföra konfigurationen behöver du följande resurser:
- En SharePoint 2013-Server grupp eller senare.
- En Azure Active Directory-klient med en plan som inkluderar programproxy. Läs mer om [Azure Active Directory planer och priser](https://azure.microsoft.com/pricing/details/active-directory/).
- En [anpassad domän som verifierats](../fundamentals/add-custom-domain.md) i Azure AD-klienten.
- Lokala Active Directory synkroniserade med Azure AD Connect med användare som kan [Logga in på Azure](../hybrid/plan-connect-user-signin.md).
- En Application Proxy-koppling installeras och körs på en dator i företags domänen.

Att konfigurera SharePoint med Application Proxy kräver två URL: er:
- En extern URL som är synlig för slutanvändare och som fastställs i Azure Active Directory. URL: en kan använda en anpassad domän. Lär dig mer om att [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).
- En intern URL, som endast är känd inom företags domänen och som aldrig har använts direkt.

> [!IMPORTANT]
> Följ dessa rekommendationer för den interna URL: en för att säkerställa att länkarna mappas korrekt:
> - Använd HTTPS
> - Använd inte anpassade portar
> - I företags-DNS skapar du en värd (A) som pekar på SharePoint WFE (eller belastningsutjämnaren) och inte ett alias (CName)

I den här artikeln används följande värden:
- Intern URL: `https://sharepoint`
- Extern URL: `https://spsites-demo1984.msappproxy.net/`
- Konto för programpool för SharePoint-webbprogram: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Steg 1: Konfigurera ett program i Azure AD som använder Application Proxy

I det här steget skapar du ett program i Azure Active Directory-klienten som ska använda programproxyn. Du anger den externa URL: en och anger den interna URL: en som används senare i SharePoint.

1. Skapa appen enligt beskrivningen i följande inställningar. Stegvisa instruktioner finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Intern URL**: den interna SharePoint-URL: en som ska ställas in senare i SharePoint, till exempel `https://sharepoint`.
   * **Metod för förautentisering**: Azure Active Directory
   * **Översätt URL i huvuden**: Nej
   * **Översätt URL i program texten**: Nej

   ![Publicera SharePoint som program](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. När din app har publicerats konfigurerar du inställningarna för enkel inloggning med följande steg:

   1. På sidan program i portalen väljer du **enkel inloggning**.
   1. För **läge för enkel inloggning**väljer du **integrerad Windows-autentisering**.
   1. Ange det **interna programmets SPN** till det värde som du angav tidigare. I det här exemplet skulle värdet vara `HTTP/sharepoint`.
   1. I **delegerad inloggnings identitet**väljer du det lämpligaste alternativet för din Active Directory skogs konfiguration. Om du till exempel har en enda AD-domän i skogen väljer du **lokalt Sam-** kontonamn (se nedan), men om dina användare inte finns i samma domän som SharePoint och Application Proxy Connector-servrar, väljer du **lokalt användar huvud namn namn** (visas inte).

   ![Konfigurera integrerad Windows-autentisering för SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Slutför konfigurationen av programmet genom att gå till avsnittet **användare och grupper** och tilldela användare åtkomst till det här programmet. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Steg 2: konfigurera SharePoint-webbprogrammet

SharePoint-webbprogrammet måste konfigureras med Kerberos och lämpliga alternativa åtkomst mappningar fungerar korrekt med Azure AD-programproxy. Det finns två möjliga alternativ:

1. Skapa ett nytt webb program och Använd bara standard zonen. Detta är det bästa alternativet att få den bästa upplevelsen av SharePoint (till exempel länkar i e-postaviseringar som genereras av SharePoint alltid till standard zonen).
1. Utöka ett befintligt webb program för att konfigurera Kerberos på en zon som inte är standard.

> [!IMPORTANT]
> Oavsett vilken zon som ska användas måste programpoolens konto för SharePoint-webbappen vara ett domän konto för att Kerberos ska fungera korrekt.

### <a name="provision-the-sharepoint-web-application"></a>Etablera SharePoint-webbprogrammet

- Om du skapar ett nytt webb program och bara använder standard zonen (önskat alternativ):

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

    1. Öppna den **centrala administrations** webbplatsen för SharePoint.
    1. Under **Systeminställningar**väljer du **Konfigurera alternativa åtkomst mappningar**. Rutan alternativa åtkomst mappningar öppnas.
    1. Filtrera visningen med det nya webb programmet och bekräfta att du ser något som liknar detta:

       ![Alternativa åtkomst mappningar för webb program](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Om du utökar ett befintligt webb program till en ny zon (om du inte kan använda standard zonen):

    1. Starta **SharePoint Management Shell** och kör följande skript:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Öppna den **centrala administrations** webbplatsen för SharePoint.
    1. Under **Systeminställningar**väljer du **Konfigurera alternativa åtkomst mappningar**. Rutan alternativa åtkomst mappningar öppnas.
    1. Filtrera visningen med det webb program som utökats och bekräfta att du ser något som liknar detta:

        ![Alternativa åtkomst mappningar för utökade program](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Se till att SharePoint-webbappen körs under ett domän konto

Använd följande steg för att identifiera det konto som kör programpoolen för SharePoint-webbprogrammet och se till att det är ett domän konto:

1. Öppna den **centrala administrations** webbplatsen för SharePoint.
1. Gå till **säkerhet** och välj **Konfigurera tjänst konton**.
1. Välj **Web Application pool-YourWebApplicationName**.

   ![Alternativ för att konfigurera ett tjänst konto](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Bekräfta att **Välj ett konto för den här komponenten** returnerar ett domän konto och kom ihåg det eftersom det kommer att behövas i nästa steg.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Se till att ett HTTPS-certifikat har kon figurer ATS för IIS-platsen i extra nät zonen

Eftersom den interna URL: en använder HTTPS-protokoll (`https://SharePoint/`) måste ett certifikat anges på IIS-platsen.

1. Öppna Windows PowerShell-konsolen.
1. Kör följande skript för att skapa ett självsignerat certifikat och Lägg till det i datorn mitt arkiv:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Självsignerade certifikat är bara lämpliga i test syfte. I produktions miljöer rekommenderar vi starkt att du använder certifikat utfärdade av en certifikat utfärdare i stället.

1. Öppna konsolen Internet Information Services Manager.
1. Expandera servern i trädvyn, expandera "platser", Välj platsen "SharePoint-AAD proxy" och klicka på **bindningar**.
1. Välj HTTPS-bindning och klicka på **Redigera...** .
1. I fältet SSL-certifikat väljer du **SharePoint** -certifikat och klickar på OK.

Nu kan du få åtkomst till SharePoint-webbplatsen externt via Azure AD-programproxy.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>Steg 3: Konfigurera Kerberos-begränsad delegering (KCD)

Användare autentiseras inlednings vis i Azure Active Directory och sedan till SharePoint med hjälp av Kerberos via Azure AD-proxy-anslutaren. Om du vill tillåta att anslutningen får en Kerberos-token åt den Azure Active Directory användaren, måste du konfigurera Kerberos-begränsad delegering med protokoll över gång. Mer information om KCD finns i [Översikt över Kerberos-begränsad delegering](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Ange tjänstens huvud namn för SharePoint-tjänstkontot

I den här artikeln är den interna URL: en `https://sharepoint`, vilket innebär att SPN är `HTTP/sharepoint`. Du måste ersätta dessa värden med de som motsvarar din miljö.
Registrera SPN-`HTTP/sharepoint` för SharePoint-programpoolens konto `Contoso\spapppool` genom att köra följande kommando från en kommando tolk som administratör för domänen:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Kommandot Setspn söker efter SPN innan det läggs till. Om det redan finns ett **duplicerat SPN-värde** visas ett fel meddelande. I det här fallet bör du ta bort det befintliga SPN-namnet om det inte anges under rätt konto för programpool.  
Du kan kontrol lera att SPN har lagts till genom att köra Setspn-kommandot med alternativet-L. Mer information om det här kommandot finns i [setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Kontrol lera att anslutningen är betrodd för delegering till SPN som lagts till i SharePoint-programpoolens konto

Konfigurera KCD så att Azure AD-programproxy-tjänsten kan delegera användar identiteter till kontot för SharePoint-programpoolen. Konfigurera KCD genom att aktivera Application Proxy Connector för att hämta Kerberos-biljetter för användare som har autentiserats i Azure AD. Sedan skickar servern kontexten till mål programmet eller SharePoint i det här fallet.

Om du vill konfigurera KCD upprepar du följande steg för varje anslutnings dator:

1. Logga in på en domänkontrollant som domän administratör och öppna sedan **Active Directory användare och datorer**.
1. Hitta datorn som kör Azure AD proxy-anslutningsprogrammet. I det här exemplet är det själva SharePoint-servern.
1. Dubbelklicka på datorn och klicka sedan på fliken **delegering** .
1. Kontrol lera att Delegerings inställningarna är inställda på att **lita på den här datorn för delegering endast till de angivna tjänsterna**. Välj sedan **Använd valfritt autentiseringsprotokoll**.
1. Klicka på knappen **Lägg till** , klicka på **användare eller datorer**och leta upp kontot för SharePoint-programpoolen, till exempel `Contoso\spapppool`.
1. I listan över SPN väljer du det som du skapade tidigare för tjänst kontot.
1. Klicka på **OK** Klicka på **OK** igen för att spara ändringarna.
  
   ![Delegerings inställningar](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Nu kan du logga in på SharePoint med den externa URL: en och autentisera med Azure.

## <a name="troubleshoot-sign-in-errors"></a>Felsöka inloggnings fel

Om inloggningen till platsen inte fungerar kan du få mer information om problemet i anslutnings loggarna: från den dator som kör-anslutningen öppnar du logg boken, går till **program-och tjänst loggar** > **Microsoft** >  **AadApplicationProxy** > -**anslutning**och granska **Administratörs** loggen.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md)
* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
