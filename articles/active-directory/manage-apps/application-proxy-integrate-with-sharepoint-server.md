---
title: Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy | Microsoft Docs
description: Beskriver grunderna för hur du integrerar en lokal SharePoint-server med Azure AD-programproxy.
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
ms.date: 08/28/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1265341ecfdb7f418ea89bb0ec848a20c6b430cd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127673"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy

Den här artikeln beskrivs hur du integrerar en lokal SharePoint-server med Azure Active Directory (Azure AD) Application Proxy.

Följ avsnitten i den här artikeln steg för steg om du vill aktivera fjärråtkomst till SharePoint med Azure AD-programproxy.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har SharePoint 2013 eller senare i din miljö. Dessutom kan du överväga att följande krav:

* SharePoint innehåller inbyggt stöd för Kerberos. Användare som kommer åt via en fjärranslutning interna webbplatser via Azure AD Application Proxy kan därför anta för att ha en enkel inloggning (SSO)-upplevelse.
* Det här scenariot innehåller konfigurationsändringar till SharePoint-servern. Vi rekommenderar att du använder en mellanlagringsmiljö. På så sätt kan du göra uppdateringar till testservern först, och underlätta en testcykel innan du påbörjar produktionen.
* Vi kräver SSL på publicerade URL: en. SSL krävs även på den interna URL: en för att se till att länkarna skickas/mappas korrekt.

> [!NOTE]
> Vi rekommenderar att du använder anpassade domäner närhelst det är möjligt. Med en anpassad domän kan du konfigurera samma URL för både den interna URL: en och den externa URL: en. Sedan kan samma länk användas för att få åtkomst till programmet från antingen i eller utanför nätverket. Den här konfigurationen optimerar upplevelsen för användare och andra program som behöver åtkomst till ditt program. Lär dig mer om att [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Steg 1: Konfigurera Kerberos-begränsad delegering (KCD)

För lokala program som använder Windows-autentisering, kan du få enkel inloggning (SSO) med Kerberos-autentiseringsprotokollet och en funktion som kallas Kerberos-begränsad delegering (KCD). KCD när konfigurerad, kan programproxy-kopplingen till en Windows-token för en användare, även om användaren inte har loggat in till Windows direkt. Läs mer om KCD i [Kerberos-begränsad delegering översikt](https://technet.microsoft.com/library/jj553400.aspx).

Använda procedurerna i följande sekventiella avsnitt om du vill konfigurera KCD för en SharePoint-server:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Se till att SharePoint-webbprogrammet körs under ett domän konto

Kontrol lera först att SharePoint-webbprogrammet körs under ett domän konto – inte lokalt system, lokal tjänst eller nätverks tjänst. Gör detta så att du kan koppla SPN-namn (Service Principal Name) till det här kontot. SPN-namn är hur Kerberos-protokollet identifierar olika tjänster. Och du behöver konto senare för att konfigurera KCD.

> [!NOTE]
> Du måste ha ett befintligt Azure AD-konto för tjänsten. Vi rekommenderar att du tillåter för en automatisk ändring av lösenord. Mer information om en fullständig uppsättning steg och fel söknings problem finns i [Konfigurera automatisk lösen ords ändring i SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

För att säkerställa att dina webbplatser körs under ett tjänstkonto som är definierade, utför du följande steg:

1. Öppna den **centrala administrations** webbplatsen för SharePoint.
1. Gå till **Security** och välj **Konfigurera tjänstkonton**.
1. Välj **Web programpoolen - SharePoint – 80**. Alternativen kan vara annorlunda ut baserat på namnet på din webb-pool eller om poolen web använder SSL som standard.

   ![Alternativ för att konfigurera ett tjänstkonto](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Om **väljer du ett konto för den här komponenten** anges till **lokal tjänst** eller **nätverkstjänst**, måste du skapa ett konto. Om inte, du är klar och gå till nästa avsnitt.
1. Välj **registrera nytt hanterat konto**. När ditt konto har skapats måste du ange **webbprogrampoolen** innan du kan använda kontot.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ange tjänstens huvudnamn för SharePoint-tjänstkonto

Innan du konfigurerar KCD måste du:

* Identifiera det domän konto som kör SharePoint-webbappen som Azure AD-proxyn kommer att exponera.
* Välj en intern URL som ska konfigureras i både Azure AD-proxy och SharePoint. Den här interna URL: en får inte redan användas i webb programmet och visas aldrig i webbläsaren.

Förutsatt att den interna URL: en <https://sharepoint>har valts är SPN:

```
HTTP/SharePoint
```

> [!NOTE]
> Följ dessa rekommendationer för den interna URL: en:
> * Använd HTTPS
> * Använd inte anpassade portar
> * I DNS skapar du en värd (A) så att den pekar på SharePoint WFE (eller Load Balancer) och inte ett alias (CName)

Registrera detta SPN genom att köra följande kommando från kommando tolken som administratör för domänen:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Det här kommandot anger SPN _http/SharePoint_ för SharePoint-programpoolens konto _demo\spAppPoolAccount_.

Ersätt _http/SharePoint_ med SPN för din interna URL och _demo\spAppPoolAccount_ med programpoolskontot i din miljö. Kommandot Setspn söker efter SPN innan den lägger till den. Det finns redan en **dubblett av SPN-värdet** . I så fall bör du ta bort det befintliga SPN-namnet om det inte anges under rätt konto för programpoolen.

Du kan kontrol lera att SPN har lagts till genom att köra Setspn-kommandot med alternativet-L. Mer information om det här kommandot finns [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Kontrol lera att anslutningen är betrodd för delegering till SPN som lagts till i SharePoint-programpoolens konto

Konfigurera KCD så att Azure AD-programproxy-tjänsten kan delegera användar identiteter till kontot för SharePoint-programpoolen. Konfigurera KCD genom att aktivera programproxy-kopplingen att hämta Kerberos-biljetter för de användare som har verifierats i Azure AD. Servern skickar sedan kontexten till målprogrammet eller SharePoint i det här fallet.

Upprepa följande steg för att konfigurera KCD, för varje connector-datorn:

1. Logga in som domänadministratör på en Domänkontrollant och öppna sedan **Active Directory-användare och datorer**.
1. Hitta den dator som kör anslutningstjänsten på. I det här exemplet är det samma SharePoint-servern.
1. Dubbelklicka på datorn och klicka sedan på den **delegering** fliken.
1. Se till att inställningarna för standarddelegering är inställda på **lita på den här datorn för delegering till angivna tjänster endast**. Välj **Använd valfritt autentiseringsprotokoll**.
1. Klicka på knappen **Lägg till** , klicka på **användare eller datorer**och leta upp kontot för SharePoint-programpoolen, till exempel _demo\spAppPoolAccount_.
1. I listan över SPN-namn, väljer du det konto som du skapade tidigare för tjänstkontot.
1. Klicka på **OK**. Klicka på **OK** igen för att spara ändringarna.
  
   ![Delegeringsinställningarna](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Steg 2: Konfigurera Azure AD-proxy

Nu när du har konfigurerat KCD är du redo att konfigurera Azure AD-programproxy.

1. Publicera din SharePoint-webbplats med följande inställningar. Stegvisa instruktioner finns i [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Intern URL**: Intern SharePoint-URL som valdes tidigare, till **<https://SharePoint/>** exempel.
   * **Metod för förautentisering**: Azure Active Directory
   * **Översätt URL i rubriker**: NO

   > [!TIP]
   > SharePoint använder den _värdhuvud_ värde att leta upp platsen. Det genererar även länkar som baseras på det här värdet. Nettoeffekten är att alla länkar som genereras av SharePoint är en publicerade URL som är korrekt konfigurerad att använda den externa URL: en. Ställer in värdet på **Ja** kan också anslutningen så att vidarebefordra begäran till backend-programmet. Dock ställer in värdet på **nr** innebär att anslutningen inte ska skicka interna värdnamnet. I stället skickar anslutningsappen värdhuvudet som publicerade URL: en för backend-programmet.

   ![Publicera SharePoint som programmet](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. När din app har publicerats kan du konfigurera inställningar för enkel inloggning med följande steg:

   1. På programsidan i portalen väljer **enkel inloggning**.
   1. Enkel inloggningsläge, Välj **integrerad Windows-autentisering**.
   1. Intern program-SPN inställd på värdet som du angav tidigare. Det här exemplet är **http/SharePoint**.
   1. I "delegerad inloggnings identitet" väljer du det lämpligaste alternativet för din Active Directory skogs konfiguration. Om du till exempel har en enda AD-domän i skogen väljer du **lokalt Sam-** kontonamn (se nedan), men om dina användare inte finns i samma domän som SharePoint och App proxy Connector-servrarna väljer du **lokala User Principal Name** (visas inte).

   ![Konfigurera integrerad Windows-autentisering för enkel inloggning](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Om du vill slutföra konfigurationen av ditt program, går du till den **användare och grupper** avsnittet och tilldela användare åtkomst till det här programmet. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Steg 3: Konfigurera SharePoint för användning av Kerberos-och Azure AD-proxy-URL: er

Nästa steg är att utöka SharePoint-webbprogrammet till en ny zon som kon figurer ATS med Kerberos och lämplig alternativ åtkomst mappning för att tillåta att SharePoint hanterar inkommande begär Anden som skickas till den interna URL: en och svarar med länkar som skapats för den externa URL: en.

1. Starta **hanterings gränssnittet för SharePoint**.
1. Kör följande skript för att utöka webb programmet till extra nät zonen och aktivera Kerberos-autentisering:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Öppna den **centrala administrations** webbplatsen för SharePoint.
1. Under **systeminställningar**väljer **konfigurera alternativa åtkomstmappningar**. Alternativa åtkomstmappningar rutan öppnas.
1. Välj din webbplats, till exempel **SharePoint-80**. För tillfället har extra nät zonen inte den interna URL: en korrekt angiven än:

   ![Visar rutan alternativa åtkomst mappningar](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Klicka på **Lägg till interna URL: er**.
1. I text rutan för **URL-protokoll, värd och port** skriver du den **interna URL:** en som kon figurer <https://SharePoint/>ATS i Azure AD-proxy, till exempel.
1. Välj Zone **extra nät** i list rutan.
1. Klicka på **Spara**.
1. De alternativa åtkomst mappningarna bör nu se ut så här:

    ![Korrigera alternativa åtkomst mappningar](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Steg 4: Se till att ett HTTPS-certifikat har kon figurer ATS för IIS-platsen i extra nät zonen

SharePoint-konfigurationen är nu slutförd, men eftersom den interna URL: en för <https://SharePoint/>extra nät zonen är, måste ett certifikat anges för den här platsen.

1. Öppna Windows PowerShell-konsolen.
1. Kör följande skript för att skapa ett självsignerat certifikat och Lägg till det i datorn mitt arkiv:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Självsignerade certifikat är bara lämpliga i test syfte. I produktions miljöer rekommenderar vi starkt att du använder certifikat utfärdade av en certifikat utfärdare i stället.

1. Öppna konsolen Internet Information Services Manager.
1. Expandera servern i trädvyn, expandera "platser", Välj platsen "SharePoint-AAD proxy" och klicka på **bindningar**.
1. Välj HTTPS-bindning och klicka på **Redigera...** .
1. I fältet SSL-certifikat väljer du **SharePoint** -certifikat och klickar på OK.

Du kan nu komma åt SharePoint-webbplatsen externt via Azure AD-programproxy.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md)
* [Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
