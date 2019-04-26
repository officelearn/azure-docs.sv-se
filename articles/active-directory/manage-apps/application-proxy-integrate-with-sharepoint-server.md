---
title: Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy | Microsoft Docs
description: Beskriver grunderna för hur du integrerar en lokal SharePoint-server med Azure AD-programproxy.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dc80b78bbba369e0ddb5c2c1e9fd90834dc0148
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293225"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy

Den här artikeln beskrivs hur du integrerar en lokal SharePoint-server med Azure Active Directory (Azure AD) Application Proxy.

Följ avsnitten i den här artikeln steg för steg om du vill aktivera fjärråtkomst till SharePoint med Azure AD-programproxy.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har SharePoint 2013 eller senare i din miljö. Dessutom kan du överväga att följande krav:

* SharePoint innehåller inbyggt stöd för Kerberos. Användare som kommer åt via en fjärranslutning interna webbplatser via Azure AD Application Proxy kan därför anta för att ha en enkel inloggning (SSO)-upplevelse.

* Det här scenariot innehåller konfigurationsändringar till SharePoint-servern. Vi rekommenderar att du använder en mellanlagringsmiljö. På så sätt kan du göra uppdateringar till testservern först, och underlätta en testcykel innan du påbörjar produktionen.

* Vi kräver SSL på publicerade URL: en. SSL krävs också på den interna URL: en för att säkerställa att länkar skickas/mappas korrekt.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Steg 1: Konfigurera Kerberos-begränsad delegering (KCD)

För lokala program som använder Windows-autentisering, kan du få enkel inloggning (SSO) med Kerberos-autentiseringsprotokollet och en funktion som kallas Kerberos-begränsad delegering (KCD). KCD när konfigurerad, kan programproxy-kopplingen till en Windows-token för en användare, även om användaren inte har loggat in till Windows direkt. Läs mer om KCD i [Kerberos-begränsad delegering översikt](https://technet.microsoft.com/library/jj553400.aspx).

Använda procedurerna i följande sekventiella avsnitt om du vill konfigurera KCD för en SharePoint-server:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Kontrollera att SharePoint-webbprogrammet körs under ett domänkonto

Kontrollera först att SharePoint-webbprogrammet körs under ett domänkonto--inte lokalt system, lokal tjänst och nätverkstjänst. Gör så att du kan koppla tjänstens huvudnamn (SPN) till det här kontot. SPN-namn är hur Kerberos-protokollet identifierar olika tjänster. Och du behöver konto senare för att konfigurera KCD.

> [!NOTE]
> Du måste ha ett befintligt Azure AD-konto för tjänsten. Vi rekommenderar att du tillåter för en automatisk ändring av lösenord. Mer information om en fullständig uppsättning steg och felsökning av problem finns i [konfigurera automatisk ändring av lösenord i SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

För att säkerställa att dina webbplatser körs under ett tjänstkonto som är definierade, utför du följande steg:

1. Öppna den **Central Administration av SharePoint** plats.
2. Gå till **Security** och välj **Konfigurera tjänstkonton**.
3. Välj **Web programpoolen - SharePoint – 80**. Alternativen kan vara annorlunda ut baserat på namnet på din webb-pool eller om poolen web använder SSL som standard.

   ![Alternativ för att konfigurera ett tjänstkonto](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Om **väljer du ett konto för den här komponenten** anges till **lokal tjänst** eller **nätverkstjänst**, måste du skapa ett konto. Om inte, du är klar och gå till nästa avsnitt.
5. Välj **registrera nytt hanterat konto**. När ditt konto har skapats måste du ange **webbprogrampoolen** innan du kan använda kontot.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ange tjänstens huvudnamn för SharePoint-tjänstkonto

Innan du konfigurerar KCD, måste du:

* Identifiera det domänkonto som kör SharePoint-webbprogrammet som Azure AD Proxy visas.
* Välj en interna URL: en som ska konfigureras i både Azure AD-Proxy och SharePoint. Den här interna URL: en får inte redan användas i webbprogrammet och aldrig kommer att visas i webbläsaren.

Förutsatt att den interna URL: en som valts är <https://sharepoint>, SPN är:

```
HTTP/SharePoint
```

> [!NOTE]
> Respektera följande rekommendationer för den interna URL: en:
> * Använda HTTPS
> * Använd inte anpassade portar
> * I DNS, skapar du en värd (A) på pekar på SharePoint WFE (eller belastningsutjämnare) och inte ett Alias (CName)

Om du vill registrera den här SPN, kör du följande kommando från Kommandotolken som administratör för domänen:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Det här kommandot anger SPN _HTTP/SharePoint_ för SharePoint-programpoolkonto _demo\spAppPoolAccount_.

Ersätt _HTTP/SharePoint_ med SPN-namnet för den interna URL: en och _demo\spAppPoolAccount_ med kontot för programpoolen i din miljö. Kommandot Setspn söker efter SPN innan den lägger till den. I den redan finns, visas en **duplicera SPN-värdet** fel. I det här fallet, Överväg att ta bort befintliga SPN-namnet om det inte har angetts under rätt kontot för programpoolen.

Du kan verifiera att SPN har lagts till genom att köra kommandot Setspn -L-alternativet. Mer information om det här kommandot finns [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Kontrollera att anslutningen är betrodd för delegering till tjänstens Huvudnamn som lagts till i SharePoint-programpoolkonto

Konfigurera KCD så att Azure AD Application Proxy-tjänsten kan delegera användaridentiteter till programpoolkonto för SharePoint. Konfigurera KCD genom att aktivera programproxy-kopplingen att hämta Kerberos-biljetter för de användare som har verifierats i Azure AD. Servern skickar sedan kontexten till målprogrammet eller SharePoint i det här fallet.

Upprepa följande steg för att konfigurera KCD, för varje connector-datorn:

1. Logga in som domänadministratör på en Domänkontrollant och öppna sedan **Active Directory-användare och datorer**.
2. Hitta den dator som kör anslutningstjänsten på. I det här exemplet är det samma SharePoint-servern.
3. Dubbelklicka på datorn och klicka sedan på den **delegering** fliken.
4. Se till att inställningarna för standarddelegering är inställda på **lita på den här datorn för delegering till angivna tjänster endast**. Välj **Använd valfritt autentiseringsprotokoll**.
5. Klicka på den **Lägg till** knapp, klickar du på **användare eller datorer**, och leta upp SharePoint-programpoolkonto till exempel _demo\spAppPoolAccount_.
6. I listan över SPN-namn, väljer du det konto som du skapade tidigare för tjänstkontot.
7. Klicka på **OK**. Klicka på **OK** igen för att spara ändringarna.
  
   ![Delegeringsinställningarna](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Steg 2: Konfigurera Azure AD-Proxy

Nu när du har konfigurerat KCD, är du redo att konfigurera Azure AD-programproxy.

1. Publicera din SharePoint-webbplats med följande inställningar. Stegvisa instruktioner finns i [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md).
   * **Intern URL**: Interna SharePoint-URL som valdes tidigare, till exempel **<https://SharePoint/>**.
   * **Förautentiseringsmetod**: Azure Active Directory
   * **Översätt URL i rubriker**: NO

   >[!TIP]
   >SharePoint använder den _värdhuvud_ värde att leta upp platsen. Det genererar även länkar som baseras på det här värdet. Nettoeffekten är att alla länkar som genereras av SharePoint är en publicerade URL som är korrekt konfigurerad att använda den externa URL: en. Ställer in värdet på **Ja** kan också anslutningen så att vidarebefordra begäran till backend-programmet. Dock ställer in värdet på **nr** innebär att anslutningen inte ska skicka interna värdnamnet. I stället skickar anslutningsappen värdhuvudet som publicerade URL: en för backend-programmet.

   ![Publicera SharePoint som programmet](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. När din app har publicerats kan du konfigurera inställningar för enkel inloggning med följande steg:

   1. På programsidan i portalen väljer **enkel inloggning**.
   2. Enkel inloggningsläge, Välj **integrerad Windows-autentisering**.
   3. Intern program-SPN inställd på värdet som du angav tidigare. I det här exemplet som skulle vara **HTTP/SharePoint**.
   4. I ”delegerad inloggningsidentitet”, väljer **lokala SAM-kontonamn**.

   ![Konfigurera integrerad Windows-autentisering för enkel inloggning](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Om du vill slutföra konfigurationen av ditt program, går du till den **användare och grupper** avsnittet och tilldela användare åtkomst till det här programmet. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Steg 3: Konfigurera SharePoint för att använda Kerberos- och Azure AD Proxy-URL: er

Nästa steg är att utöka SharePoint-webbprogram till en ny zon som konfigurerats med Kerberos och lämpliga alternativ åtkomst-mappningen för att tillåta SharePoint att hantera inkommande begäranden som skickas till den interna URL: en och svara med länkar som skapats för den externa URL: en.

1. Starta den **hanteringsgränssnittet för SharePoint**.
2. Kör följande skript för att utöka det webbaserade programmet och extranät-zon och aktivera Kerberos-autentisering:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. Öppna den **Central Administration av SharePoint** plats.
4. Under **systeminställningar**väljer **konfigurera alternativa åtkomstmappningar**. Alternativa åtkomstmappningar rutan öppnas.
5. Välj din webbplats, till exempel **SharePoint – 80**. För tillfället är saknar extranät zon den interna URL: en som har konfigurerats korrekt ännu:

   ![Alternativa åtkomstmappningar box](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Klicka på **Lägg till interna URL: er**.
7. I **URL-protokoll, värd och port** textrutan skriver den **interna URL: en** konfigurerats i Azure AD-proxy, till exempel <https://SharePoint/>.
8. Markera zon **extranät** i den nedrullningsbara listan.
9. Klicka på **Spara**.
10. Alternativa åtkomstmappningar bör nu se ut så här:

    ![Korrigera alternativa åtkomstmappningar](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Steg 4: Kontrollera att ett HTTPS-certifikat har konfigurerats för IIS-webbplats i zonen extranät

SharePoint-konfigurationen är nu klar, men eftersom den interna URL: en i zonen extranät <https://SharePoint/>, ett certifikat måste anges för den här platsen.

1. Öppna Windows PowerShell-konsolen.
2. Kör följande skript för att generera ett självsignerat certifikat och lägga till den i datorn mitt Arkiv:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Självsignerade certifikat är lämplig endast för testning. I produktionsmiljöer rekommenderas att använda certifikat som utfärdats av en certifikatutfärdare i stället.

3. Öppna ”Internet Information Services Manager”-konsolen.
4. Expandera servern i trädvyn ”platser”, välj platsen ”SharePoint – AAD Proxy” och klicka på **bindningar**.
5. Välj https-bindning och klicka på **redigera...** .
6. Välj i SSL-certifikatet **SharePoint** certifikat och klicka på OK.

Du kan nu komma åt SharePoint-webbplatsen externt via Azure AD-programproxy.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md)
* [Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
