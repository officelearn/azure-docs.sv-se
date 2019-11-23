---
title: Tutorial - Add an on-premises app - Application Proxy in Azure AD
description: Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. This tutorial shows you how to prepare your environment for use with Application Proxy. Then, it uses the Azure portal to add an on-premises application to your Azure AD tenant.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f812bb7d31319476e6b940443e067fac895f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420446"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory

Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien förbereder din miljö för användning med programproxy. När din miljö är redo använder du Azure-portalen för att lägga till ett lokalt program till Azure AD-klientorganisationen.

I den här självstudien:

> [!div class="checklist"]
> * Öppnar portar för utgående trafik och ger åtkomst till specifika webbadresser
> * Installerar anslutningsprogrammet på din Windows-server och registrerar den med programproxy
> * Verifierar att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lägger till ett lokalt program till Azure AD-klientorganisationen
> * Verifies a test user can sign on to the application by using an Azure AD account

## <a name="before-you-begin"></a>Innan du börjar

To add an on-premises application to Azure AD, you need:

* A [Microsoft Azure AD premium subscription](https://azure.microsoft.com/pricing/details/active-directory)
* An application administrator account
* User identities must be synchronized from an on-premises directory or created directly within your Azure AD tenants. Identity synchronization allows Azure AD to pre-authenticate users before granting them access to App Proxy published applications and to have the necessary user identifier information to perform single sign-on (SSO).

### <a name="windows-server"></a>Windows-server

Om du vill använda programproxy behöver du en Windows-server som kör Windows Server 2012 R2 eller senare. Du installerar anslutningsappen för programproxy på servern. Anslutningsservern måste ansluta till programproxytjänsten i Azure och de lokala programmen som du har för avsikt att publicera.

Vi rekommenderar att du har mer än en Windows-server för att säkra hög tillgänglighet i produktionsmiljön. I den här självstudien räcker det med en Windows-server.

> [!IMPORTANT]
> If you are installing the connector on Windows Server 2019 there is a HTTP2 limitation. A workaround to use the connector on this version is adding the following registry key and restarting the server. Note, this is a machine registry wide key. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Recommendations for the connector server

1. Placera fysiskt anslutningsservern nära programservrarna för att optimera prestandan mellan anslutningsprogrammet och programmet. Mer information finns i [Överväganden för nätverkstopologi](application-proxy-network-topology.md).
1. The connector server and the web applications servers should belong to the same Active Directory domain or span trusting domains. Having the servers in the same domain or trusting domains is a requirement for using single sign-on (SSO) with Integrated Windows Authentication (IWA) and Kerberos Constrained Delegation (KCD). Om anslutningsservern och webbprogramservrarna är i olika Active Directory-domäner så måste du använda resursbaserad delegering för enkel inloggning. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> If you've deployed Azure AD Password Protection Proxy, do not install Azure AD Application Proxy and Azure AD Password Protection Proxy together on the same machine. Azure AD Application Proxy and Azure AD Password Protection Proxy install different versions of the Azure AD Connect Agent Updater service. These different versions are incompatible when installed together on the same machine.

#### <a name="tls-requirements"></a>TLS requirements

Windows-anslutningsservern måste ha TLS 1.2 aktiverat innan du installerar anslutningsprogrammet för programproxyn.

Aktivera TLS 1.2:

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restart the server.

> [!IMPORTANT]
> To provide the best-in-class encryption to our customers, the Application Proxy service limits access to only TLS 1.2 protocols. These changes were gradually rolled out and effective since August 31, 2019. Make sure that all your client-server and browser-server combinations are updated to use TLS 1.2 to maintain connection to Application Proxy service. These include clients your users are using to access applications published through Application Proxy. See Preparing for [TLS 1.2 in Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) for useful references and resources.

## <a name="prepare-your-on-premises-environment"></a>Förbered din lokala miljö

Start by enabling communication to Azure data centers to prepare your environment for Azure AD Application Proxy. If there's a firewall in the path, make sure it's open. An open firewall allows the connector to make HTTPS (TCP) requests to the Application Proxy.

### <a name="open-ports"></a>Öppna portar

Öppna följande portar för **utgående** trafik.

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Laddar ner listor över återkallade certifikat (CRL) vid verifiering av SSL-certifikatet |
   | 443 | All utgående kommunikation med programproxytjänsten |

Om brandväggstrafiken hanteras baserat på användarna som genererar den ska du även öppna portarna 80 och 443 för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst.

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure uses these URLs to verify certificates. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

You can allow connections to \*.msappproxy.net and \*.servicebus.windows.net if your firewall or proxy lets you configure DNS allow lists. If not, you need to allow access to the [Azure IP ranges and Service Tags - Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). IP-adressintervallen uppdateras varje vecka.

## <a name="install-and-register-a-connector"></a>Installera och registrera ett anslutningsprogram

To use Application Proxy, install a connector on each Windows server you're using with the Application Proxy service. Anslutningsprogrammet är en agent som hanterar den utgående anslutningen från lokala programservrar till programproxy i Azure Active Directory. Du kan installera ett anslutningsprogram på servrar som även har andra autentiseringsagenter installerade, till exempel Azure Active Directory Connect.

Så här installerar du anslutningsprogrammet:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
1. Select your username in the upper-right corner. Verify you're signed in to a directory that uses Application Proxy. If you need to change directories, select **Switch directory** and choose a directory that uses Application Proxy.
1. In left navigation panel, select **Azure Active Directory**.
1. Under **Manage**, select **Application proxy**.
1. Select **Download connector service**.

    ![Download connector service to see the Terms of Service](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Läs användningsvillkoren. When you're ready, select **Accept terms & Download**.
1. At the bottom of the window, select **Run** to install the connector. Installationsguiden öppnas.
1. Follow the instructions in the wizard to install the service. När du uppmanas att registrera anslutningsprogrammet med programproxyn för Azure AD-klientorganisationen anger du autentiseringsuppgifterna för programadministratören.
    - Om **IE Förbättrad säkerhetskonfiguration** är satt till **På** för Internet Explorer (IE) kanske inte registreringsskärmen visas. Följ instruktionerna i felmeddelandet för att få åtkomst. Make sure that **Internet Explorer Enhanced Security Configuration** is set to **Off**.

### <a name="general-remarks"></a>Allmänna anmärkningar

Om du tidigare har installerat ett anslutningsprogram måste du installera om för att hämta den senaste versionen. To see information about previously released versions and what changes they include, see [Application Proxy: Version Release History](application-proxy-release-version-history.md).

Väljer du att ha mer än en Windows-server för dina lokala program måste du installera och registrera anslutningsprogrammet på varje server. Du kan ordna anslutningsprogrammen i anslutningsgrupper. Mer information finns i [anslutningsgrupper](application-proxy-connector-groups.md).

Om din organisation använder proxyservrar för att ansluta till internet måste du konfigurera dem för programproxy.  Mer information finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). 

Information om anslutningsprogram, kapacitetsplanering och hur de håller sig uppdaterade finns i avsnittet [Förstå anslutningsprogram för Azure AD programproxy](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifiera att anslutningsprogrammet installerats och registrerats på rätt sätt

Du kan använda Azure-portalen eller din Windows-server för att bekräfta att ett nytt anslutningsprogram har installerats.

### <a name="verify-the-installation-through-azure-portal"></a>Verify the installation through Azure portal

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Logga in på din klientkatalog i [Azure-portalen](https://portal.azure.com).
1. In the left navigation panel, select **Azure Active Directory**, and then select **Application Proxy** under the **Manage** section. Alla dina anslutningsprogram och anslutningsgrupper visas på den här sidan.
1. View a connector to verify its details. The connectors should be expanded by default. If the connector you want to view isn't expanded, expand the connector to view the details. En aktiv grön etikett innebär att ditt anslutningsprogram kan ansluta till tjänsten. Även om etiketten är grön kan dock ett nätverksproblem fortfarande blockera anslutningsprogrammet från att ta emot meddelanden.

    ![Azure AD Application Proxy Connectors](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

For more help with installing a connector, see [Problem installing the Application Proxy Connector](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verify the installation through your Windows server

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Öppna Hanteraren för Windows-tjänster genom att klicka på nyckeln **Windows** och ange *services.msc*.
1. Kontrollera om statusen för följande två tjänster är **Körs**.
   - **Microsoft AAD Application Proxy Connector** enables connectivity.
   - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsverktyget söker efter nya versioner av anslutningsprogrammet och uppdaterar det efter behov.

     ![Application Proxy Connector-tjänster – skärmbild](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. If the status for the services isn't **Running**, right-click to select each service and choose **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Lägg till ett lokalt program till Azure Active Directory

Nu när du har förberett din miljö och installerat ett anslutningsprogram är du redo att lägga till lokala program till Azure Active Directory.  

1. Logga in som administratör i [Azure-portalen](https://portal.azure.com/).
2. In the left navigation panel, select **Azure Active Directory**.
3. Select **Enterprise applications**, and then select **New application**.
4. In the **On-premises applications** section, select **Add an on-premises application**.
5. In the **Add your own on-premises application** section, provide the following information about your application:

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Namn** | Namnet på programmet som ska visas på åtkomstpanelen och i Azure-portalen. |
    | **Intern webbadress** | Det här är webbadressen för att komma åt programmet från inuti ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server som olika program och ge varje webbplats sitt eget namn och sina egna åtkomstregler.<br><br>Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. For example, if your app is at https:\//yourapp/app and uses images located at https:\//yourapp/media, then you should publish https:\//yourapp/ as the path. Den interna webbadressen måste inte vara landningssidan som användarna ser. Mer information finns i [Ange en anpassad startsida för publicerade program](application-proxy-configure-custom-home-page.md). |
    | **Extern webbadress** | Adressen som ger användare åtkomst till programmet från utanför ditt nätverk. Om du inte vill använda standarddomänen för programproxy kan du läsa om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Förautentisering** | Hur programproxyn verifierar användare innan de ges åtkomst till ditt program.<br><br>**Azure Active Directory** – Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. We recommend keeping this option as the default so that you can take advantage of Azure AD security features like Conditional Access and Multi-Factor Authentication. **Azure Active Directory** krävs för övervakning av program med Microsoft Cloud Application Security.<br><br>**Passthrough** - Users don't have to authenticate against Azure AD to access the application. Du kan fortfarande konfigurera autentiseringskrav från serverdelen. |
    | **Anslutningsgrupp** | Anslutningsprogram bearbetar fjärråtkomsten till programmet och anslutningsgrupper hjälper dig att organisera anslutningsprogram och program efter region, nätverk eller syfte. Om du inte har skapat några anslutningsgrupper än kommer programmet att tilldelas **Standard**.<br><br>Om ditt program använder WebSockets för att ansluta måste alla anslutningsprogram i gruppen vara version 1.5.612.0 eller senare.|

6. Konfigurera **ytterligare inställningar** om det behövs. De flesta programmen bör behålla dessa inställningarna i standardtillstånden. 

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Tidsgränsen för serverdels-programmet** | Ställ endast in värdet på **Lång** om programmet autentiserar och ansluter långsamt. At default, the backend application timeout has a length of 85 seconds. When set to long, the backend timeout is increased to 180 seconds. |
    | **Använd endast HTTP-cookie** | Ställ in värdet på **Ja** för att programproxycookies ska inkluderas i HTTPOnly-flaggan i HTTP-svarsrubriken. Ställ in värdet på **Nej** om du använder fjärrskrivbordstjänster.|
    | **Använd säker cookie**| Ställ in värdet på **Ja** för att skicka cookies via en säker kanal, som en krypterad HTTPS-begäran.
    | **Använd beständig cookie**| Behåll det här värdet inställt på **Nej**. Only use this setting for applications that can't share cookies between processes. For more information about cookie settings, see [Cookie settings for accessing on-premises applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Översätt webbadresser i rubriker** | Behåll det här värdet som **Ja** såvida inte programmets ursprungliga värdrubrik krävs i autentiseringsbegäran. |
    | **Översätt webbadresser i brödtext för program** | Keep this value as **No** unless you have hardcoded HTML links to other on-premises applications and don't use custom domains. Mer information finns i [Länka översättning med programproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ställ in det här värdet på **Ja** om du planerar att övervaka programmet med Microsoft Cloud App Security (MCAS). For more information, see [Configure real-time application access monitoring with Microsoft Cloud App Security and Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Välj **Lägg till**.

## <a name="test-the-application"></a>Testa programmet

Du är redo att testa om programmet har lagts till korrekt. I följande steg kan du lägga till ett användarkonto i programmet och försöka logga in.

### <a name="add-a-user-for-testing"></a>Lägg till en användare för testning

Verifiera att användarkontot redan har behörighet att komma åt programmet inifrån företagsnätverket innan du lägger till en användare till programmet.

Lägga till en testanvändare:

1. Select **Enterprise applications**, and then select the application you want to test.
2. Select **Getting started**, and then select **Assign a user for testing**.
3. Under **Users and groups**, select **Add user**.
4. Under **Add assignment**, select **Users and groups**. The **User and groups** section appears.
5. Choose the account you want to add.
6. Choose **Select**, and then select **Assign**.

### <a name="test-the-sign-on"></a>Testa inloggningen

To test the sign-on to the application:

1. I webbläsaren navigerar du till den externa webbadressen som du konfigurerade under steget publicera. Du bör se startskärmen.
1. Sign in as the user you created in the previous section.

Mer information om felsökning finns i [Felsöka problem med programproxyn och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien förberedde du din lokala miljö för arbete med programproxy och installerade och registrerade sedan anslutningsprogrammet för programproxy. Sedan la du till ett program till din Azure AD-klientorganisation. Du verifierade att en användare kan logga in på programmet med hjälp av ett Azure AD-konto.

Du gjorde detta:
> [!div class="checklist"]
> * Öppnade portar för utgående trafik och gav åtkomst till specifika webbadresser
> * Installerade anslutningsprogrammet på din Windows-server och registrerade den med programproxy
> * Verifierade att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lade till ett lokalt program till Azure AD-klientorganisationen
> * Verified a test user can sign on to the application by using an Azure AD account

Nu är du redo att konfigurera programmet för enkel inloggning. Use the following link to choose a single sign-on method and to find single sign-on tutorials.

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
