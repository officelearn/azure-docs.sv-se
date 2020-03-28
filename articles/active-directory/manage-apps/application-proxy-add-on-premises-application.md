---
title: Självstudiekurs - Lägga till en lokal app - Programproxy i Azure AD
description: Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien visar hur du förbereder din miljö för användning med Programproxy. Sedan använder den Azure-portalen för att lägga till ett lokalt program till din Azure AD-klientorganisation.
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
ms.openlocfilehash: 73aa01ea08c8bab1395516c31bb46dbfd88045db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481423"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Självstudiekurs: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory

Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien förbereder din miljö för användning med programproxy. När din miljö är redo använder du Azure-portalen för att lägga till ett lokalt program till Azure AD-klientorganisationen.

I den här självstudien:

> [!div class="checklist"]
> * Öppnar portar för utgående trafik och ger åtkomst till specifika webbadresser
> * Installerar anslutningsprogrammet på din Windows-server och registrerar den med programproxy
> * Verifierar att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lägger till ett lokalt program till Azure AD-klientorganisationen
> * Verifierar att en testanvändare kan logga in på programmet med hjälp av ett Azure AD-konto

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett lokalt program i Azure AD behöver du:

* En [Microsoft Azure AD-premiumprenumeration](https://azure.microsoft.com/pricing/details/active-directory)
* Ett programadministratörskonto
* Användaridentiteter måste synkroniseras från en lokal katalog eller skapas direkt i dina Azure AD-klienter. Identitetssynkronisering gör att Azure AD kan förberätta användare innan de får åtkomst till appproxy-publicerade program och har nödvändig användaridentifierare för att utföra enkel inloggning (SSO).

### <a name="windows-server"></a>Windows-server

Om du vill använda programproxy behöver du en Windows-server som kör Windows Server 2012 R2 eller senare. Du installerar anslutningsappen för programproxy på servern. Anslutningsservern måste ansluta till programproxytjänsten i Azure och de lokala programmen som du har för avsikt att publicera.

Vi rekommenderar att du har mer än en Windows-server för att säkra hög tillgänglighet i produktionsmiljön. I den här självstudien räcker det med en Windows-server.

> [!IMPORTANT]
> Om du installerar anslutningen på Windows Server 2019 finns det en HTTP2-begränsning. En lösning för att använda anslutningen på den här versionen är att lägga till följande registernyckel och starta om servern. Observera att detta är en breddnyckel för maskinregistret. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Rekommendationer för anslutningsservern

1. Placera fysiskt anslutningsservern nära programservrarna för att optimera prestandan mellan anslutningsprogrammet och programmet. Mer information finns i [Överväganden för nätverkstopologi](application-proxy-network-topology.md).
1. Anslutningsservern och webbprogramservrarna bör tillhöra samma Active Directory-domän eller spana till domäner. Att ha servrarna i samma domän eller lita på domäner är ett krav för att använda enkel inloggning (SSO) med integrerad Windows-autentisering (IWA) och Kerberos Constrained Delegation (KCD). Om anslutningsservern och webbprogramservrarna är i olika Active Directory-domäner så måste du använda resursbaserad delegering för enkel inloggning. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Om du har distribuerat Azure AD Password Protection Proxy ska du inte installera Azure AD Application Proxy och Azure AD Password Protection Proxy tillsammans på samma dator. Azure AD Application Proxy och Azure AD Password Protection Proxy installerar olika versioner av Azure AD Connect Agent Updater-tjänsten. Dessa olika versioner är inkompatibla när de installeras tillsammans på samma dator.

#### <a name="tls-requirements"></a>TLS-krav

Windows-anslutningsservern måste ha TLS 1.2 aktiverat innan du installerar anslutningsprogrammet för programproxyn.

Aktivera TLS 1.2:

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern.

> [!IMPORTANT]
> För att ge våra kunder den bästa krypteringen i klassen begränsar tjänsten Application Proxy åtkomst till endast TLS 1.2-protokoll. Dessa förändringar har successivt rullats ut och varit effektiva sedan den 31 augusti 2019. Kontrollera att alla kombinationer av klientserver och webbläsare och servrar uppdateras för att använda TLS 1.2 för att upprätthålla anslutningen till application proxy-tjänsten. Dessa inkluderar klienter som användarna använder för att komma åt program som publiceras via Application Proxy. Mer information och resurser finns i Förbereda för [TLS 1.2 i Office 365.](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365)

## <a name="prepare-your-on-premises-environment"></a>Förbered din lokala miljö

Börja med att aktivera kommunikation till Azure-datacenter för att förbereda din miljö för Azure AD Application Proxy. Om det finns en brandvägg i sökvägen kontrollerar du att den är öppen. En öppen brandvägg gör att anslutningen kan göra HTTPS-begäranden (TCP) till programproxyn.

### <a name="open-ports"></a>Öppna portar

Öppna följande portar för **utgående** trafik.

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Hämta listor över återkallade certifikat (CRL: er) när TLS/SSL-certifikatet valideras |
   | 443 | All utgående kommunikation med programproxytjänsten |

Om brandväggstrafiken hanteras baserat på användarna som genererar den ska du även öppna portarna 80 och 443 för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst.

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Kopplingen använder dessa url:er för att verifiera certifikat. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Du kan tillåta \*anslutningar till .msappproxy.net \*och .servicebus.windows.net om brandväggen eller proxyn låter dig konfigurera DNS-tillåt listor. Om inte, måste du tillåta åtkomst till [Azure IP-intervall och tjänsttaggar - Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). IP-adressintervallen uppdateras varje vecka.

## <a name="install-and-register-a-connector"></a>Installera och registrera ett anslutningsprogram

Om du vill använda Programproxy installerar du en anslutningsappar på varje Windows-server som du använder med tjänsten Programproxy. Anslutningsprogrammet är en agent som hanterar den utgående anslutningen från lokala programservrar till programproxy i Azure Active Directory. Du kan installera ett anslutningsprogram på servrar som även har andra autentiseringsagenter installerade, till exempel Azure Active Directory Connect.

Så här installerar du anslutningsprogrammet:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
1. Välj ditt användarnamn i det övre högra hörnet. Kontrollera att du är inloggad på en katalog som använder Programproxy. Om du behöver ändra kataloger väljer du **Växla katalog** och väljer en katalog som använder Programproxy.
1. Välj Azure Active **Directory**i den vänstra navigeringspanelen .
1. Under **Hantera**väljer du **Programproxy**.
1. Välj **Tjänsten Hämta anslutning**.

    ![Ladda ned anslutningstjänsten för att se användarvillkoren](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Läs användningsvillkoren. När du är klar väljer du **Acceptera villkor & Hämta**.
1. Längst ned i fönstret väljer du **Kör** för att installera kopplingen. Installationsguiden öppnas.
1. Installera tjänsten genom att följa instruktionerna i guiden. När du uppmanas att registrera anslutningsprogrammet med programproxyn för Azure AD-klientorganisationen anger du autentiseringsuppgifterna för programadministratören.
    - Om **IE Förbättrad säkerhetskonfiguration** är satt till **På** för Internet Explorer (IE) kanske inte registreringsskärmen visas. Följ instruktionerna i felmeddelandet för att få åtkomst. Kontrollera att **förbättrad säkerhetskonfiguration** i Internet Explorer är inställd **på Av**.

### <a name="general-remarks"></a>Allmänna anmärkningar

Om du tidigare har installerat ett anslutningsprogram måste du installera om för att hämta den senaste versionen. Information om tidigare utgivna versioner och vilka ändringar de innehåller finns i [Programproxy: Versionsversionshistorik](application-proxy-release-version-history.md).

Väljer du att ha mer än en Windows-server för dina lokala program måste du installera och registrera anslutningsprogrammet på varje server. Du kan ordna anslutningsprogrammen i anslutningsgrupper. Mer information finns i [anslutningsgrupper](application-proxy-connector-groups.md).

Om din organisation använder proxyservrar för att ansluta till internet måste du konfigurera dem för programproxy.  Mer information finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). 

Information om anslutningsprogram, kapacitetsplanering och hur de håller sig uppdaterade finns i avsnittet [Förstå anslutningsprogram för Azure AD programproxy](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifiera att anslutningsprogrammet installerats och registrerats på rätt sätt

Du kan använda Azure-portalen eller din Windows-server för att bekräfta att ett nytt anslutningsprogram har installerats.

### <a name="verify-the-installation-through-azure-portal"></a>Verifiera installationen via Azure Portal

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Logga in på din klientkatalog i [Azure-portalen](https://portal.azure.com).
1. På den vänstra navigeringspanelen väljer du **Azure Active Directory**och väljer sedan **Programproxy** under avsnittet **Hantera.** Alla dina anslutningsprogram och anslutningsgrupper visas på den här sidan.
1. Visa en koppling för att verifiera dess information. Kopplingarna bör utökas som standard. Om kopplingen som du vill visa inte är expanderad expanderar du kopplingen för att visa informationen. En aktiv grön etikett innebär att ditt anslutningsprogram kan ansluta till tjänsten. Även om etiketten är grön kan dock ett nätverksproblem fortfarande blockera anslutningsprogrammet från att ta emot meddelanden.

    ![Proxykopplingar för Azure AD-program](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Mer hjälp med att installera en anslutningsapp finns i [Problem med att installera Application Proxy Connector](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verifiera installationen via Windows-servern

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Öppna Hanteraren för Windows-tjänster genom att klicka på nyckeln **Windows** och ange *services.msc*.
1. Kontrollera om statusen för följande två tjänster är **Körs**.
   - **Microsoft AAD Application Proxy Connector** möjliggör anslutning.
   - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsverktyget söker efter nya versioner av anslutningsprogrammet och uppdaterar det efter behov.

     ![Application Proxy Connector-tjänster – skärmbild](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Om statusen för tjänsterna inte **körs**högerklickar du för att välja varje tjänst och väljer **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Lägg till ett lokalt program till Azure Active Directory

Nu när du har förberett din miljö och installerat ett anslutningsprogram är du redo att lägga till lokala program till Azure Active Directory.  

1. Logga in som administratör i [Azure-portalen](https://portal.azure.com/).
2. Välj **Azure Active Directory**på den vänstra navigeringspanelen .
3. Välj **Företagsprogram**och välj sedan **Nytt program**.
4. I avsnittet **Lokala program** väljer du Lägg till ett **lokalt program**.
5. I avsnittet **Lägg till din egen lokala applikation** anger du följande information om ditt program:

    | Field | Beskrivning |
    | :---- | :---------- |
    | **Namn** | Namnet på programmet som ska visas på åtkomstpanelen och i Azure-portalen. |
    | **Intern webbadress** | Det här är webbadressen för att komma åt programmet från inuti ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server som olika program och ge varje webbplats sitt eget namn och sina egna åtkomstregler.<br><br>Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Om din app till exempel\/finns på https: /yourapp/app\/och använder bilder som finns på\/https: /yourapp/media, bör du publicera https: /yourapp/ som sökväg. Den interna webbadressen måste inte vara landningssidan som användarna ser. Mer information finns i [Ange en anpassad startsida för publicerade program](application-proxy-configure-custom-home-page.md). |
    | **Extern webbadress** | Adressen som ger användare åtkomst till programmet från utanför ditt nätverk. Om du inte vill använda standarddomänen för programproxy kan du läsa om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Förautentisering** | Hur programproxyn verifierar användare innan de ges åtkomst till ditt program.<br><br>**Azure Active Directory** – Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. Vi rekommenderar att du behåller det här alternativet som standard så att du kan dra nytta av Azure AD-säkerhetsfunktioner som villkorlig åtkomst och multifaktorautentisering. **Azure Active Directory** krävs för övervakning av program med Microsoft Cloud Application Security.<br><br>**Passthrough** - Användare behöver inte autentisera mot Azure AD för att komma åt programmet. Du kan fortfarande konfigurera autentiseringskrav från serverdelen. |
    | **Anslutningsgrupp** | Anslutningsprogram bearbetar fjärråtkomsten till programmet och anslutningsgrupper hjälper dig att organisera anslutningsprogram och program efter region, nätverk eller syfte. Om du inte har skapat några anslutningsgrupper än kommer programmet att tilldelas **Standard**.<br><br>Om ditt program använder WebSockets för att ansluta måste alla anslutningsprogram i gruppen vara version 1.5.612.0 eller senare.|

6. Om det behövs konfigurerar du **Ytterligare inställningar**. De flesta programmen bör behålla dessa inställningarna i standardtillstånden. 

    | Field | Beskrivning |
    | :---- | :---------- |
    | **Tidsgränsen för serverdels-programmet** | Ställ endast in värdet på **Lång** om programmet autentiserar och ansluter långsamt. Som standard har tidsgränsen för backend-program en längd på 85 sekunder. När den är inställd på lång ökas tidsgränsen för backend till 180 sekunder. |
    | **Använd endast HTTP-cookie** | Ställ in värdet på **Ja** för att programproxycookies ska inkluderas i HTTPOnly-flaggan i HTTP-svarsrubriken. Ställ in värdet på **Nej** om du använder fjärrskrivbordstjänster.|
    | **Använd säker cookie**| Ställ in värdet på **Ja** för att skicka cookies via en säker kanal, som en krypterad HTTPS-begäran.
    | **Använd beständig cookie**| Behåll det här värdet inställt på **Nej**. Använd endast den här inställningen för program som inte kan dela cookies mellan processer. Mer information om cookie-inställningar finns i [Cookie-inställningar för åtkomst till lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Översätt webbadresser i rubriker** | Behåll det här värdet som **Ja** såvida inte programmets ursprungliga värdrubrik krävs i autentiseringsbegäran. |
    | **Översätt webbadresser i brödtext för program** | Behåll det här värdet som **nej** om du inte har hårdkodade HTML-länkar till andra lokala program och inte använder anpassade domäner. Mer information finns i [Länka översättning med programproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ställ in det här värdet på **Ja** om du planerar att övervaka programmet med Microsoft Cloud App Security (MCAS). Mer information finns i [Konfigurera övervakning av programåtkomst i realtid med Microsoft Cloud App Security och Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Välj **Lägg till**.

## <a name="test-the-application"></a>Testa programmet

Du är redo att testa om programmet har lagts till korrekt. I följande steg kan du lägga till ett användarkonto i programmet och försöka logga in.

### <a name="add-a-user-for-testing"></a>Lägg till en användare för testning

Verifiera att användarkontot redan har behörighet att komma åt programmet inifrån företagsnätverket innan du lägger till en användare till programmet.

Lägga till en testanvändare:

1. Välj **Enterprise-program**och välj sedan det program som du vill testa.
2. Välj **Komma igång**och välj sedan Tilldela en användare för **testning**.
3. Under **Användare och grupper**väljer du Lägg till **användare**.
4. Under **Lägg till tilldelning**väljer du Användare och **grupper**. Avsnittet **Användare och grupper** visas.
5. Välj det konto som du vill lägga till.
6. Välj **Markera**och välj sedan **Tilldela**.

### <a name="test-the-sign-on"></a>Testa inloggningen

Så här testar du inloggningen till programmet:

1. Välj **Programproxy**i programmet som du vill testa .
2. Högst upp på sidan väljer du **Testa program** för att köra ett test på programmet och kontrollera om det finns några konfigurationsproblem.
3. Se till att först starta programmet för att testa signeringen i programmet och hämta sedan diagnostikrapporten för att granska lösningsvägledningen för alla upptäckta problem.

Mer information om felsökning finns i [Felsöka problem med programproxyn och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien förberedde du din lokala miljö för arbete med programproxy och installerade och registrerade sedan anslutningsprogrammet för programproxy. Sedan la du till ett program till din Azure AD-klientorganisation. Du verifierade att en användare kan logga in på programmet med hjälp av ett Azure AD-konto.

Du gjorde detta:
> [!div class="checklist"]
> * Öppnade portar för utgående trafik och gav åtkomst till specifika webbadresser
> * Installerade anslutningsprogrammet på din Windows-server och registrerade den med programproxy
> * Verifierade att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lade till ett lokalt program till Azure AD-klientorganisationen
> * Verifierade en testanvändare kan logga in på programmet med hjälp av ett Azure AD-konto

Nu är du redo att konfigurera programmet för enkel inloggning. Använd följande länk för att välja en enda inloggningsmetod och för att hitta enstaka inloggningskurser.

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
