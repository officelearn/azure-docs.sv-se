---
title: Lägg till en lokal app – programproxy i Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien visar hur du förbereder din miljö för användning med Application Proxy. Sedan används Azure-portalen för att lägga till ett lokalt program till Azure AD-klienten.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82c7b698f655b82ba95f66127f27a921def02cde
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472990"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Självstudier: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory

Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien förbereder din miljö för användning med programproxy. När din miljö är redo använder du Azure-portalen för att lägga till ett lokalt program till Azure AD-klientorganisationen.

I den här självstudien:

> [!div class="checklist"]
> * Öppnar portar för utgående trafik och ger åtkomst till specifika webbadresser
> * Installerar anslutningsprogrammet på din Windows-server och registrerar den med programproxy
> * Verifierar att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lägger till ett lokalt program till Azure AD-klientorganisationen
> * Verifierar att en användare kan logga in på programmet med hjälp av en Azure AD-konto

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett lokalt program till Azure AD, behöver du:

* En [Microsoft Azure AD basic eller premium-prenumeration](https://azure.microsoft.com/pricing/details/active-directory)
* Ett administratörskonto för program
* Användaridentiteter måste synkroniseras från en lokal katalog eller skapat direkt i din Azure AD-klienter. Identitetssynkronisering kan Azure AD att autentisera användare förväg innan bevilja dem åtkomst till App Proxy publicerade program och ha nödvändiga användarinformationen identifierare för enkel inloggning (SSO).

### <a name="windows-server"></a>Windows server

Om du vill använda programproxy behöver du en Windows-server som kör Windows Server 2012 R2 eller senare. Du installerar anslutningsappen för programproxy på servern. Anslutningsservern måste ansluta till programproxytjänsten i Azure och de lokala programmen som du har för avsikt att publicera.

Vi rekommenderar att du har mer än en Windows-server för att säkra hög tillgänglighet i produktionsmiljön. I den här självstudien räcker det med en Windows-server.

#### <a name="recommendations-for-the-connector-server"></a>Rekommendationer för connector-server

1. Placera fysiskt anslutningsservern nära programservrarna för att optimera prestandan mellan anslutningsprogrammet och programmet. Mer information finns i [Överväganden för nätverkstopologi](application-proxy-network-topology.md).

2. Connector-server och program webbservrarna ska tillhöra samma Active Directory-domän eller sträcker sig över domäner. Med servrar i samma domän eller domäner med förtroende är ett krav för att använda enkel inloggning (SSO) med integrerad Windows-autentisering (IWA) och Kerberos-begränsad delegering (KCD). Om anslutningsservern och webbprogramservrarna är i olika Active Directory-domäner så måste du använda resursbaserad delegering för enkel inloggning. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

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

2. Starta om servern.

>[!Important] 
> För att ge bästa i klassen-kryptering för våra kunder, gör vi uppdateringar till Application Proxy-tjänsten för att begränsa åtkomsten till endast TLS 1.2-protokoll. Baserat på kundernas beredskap ändringarna distribueras gradvis till kunder som bara använder TLS 1.2-protokoll och kommer inte se någon inverkan från den här ändringen. TLS 1.0 och 1.1 utfasning slutförs den 31 augusti 2019 och kunder får alltid att förbereda för den här ändringen. Förbereda för den här ändringen gör att som uppdateras alla kombinationer av klient-server och webbläsaren servrar för att använda TLS 1.2 för att upprätthålla anslutning till Application Proxy-tjänsten. Dessa inkluderar klienter som dina användare använder för att få åtkomst till program som publicerats via programproxy. Se förbereda för [TLS 1.2 i Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) för användbar-referenser och resurser.

## <a name="prepare-your-on-premises-environment"></a>Förbered din lokala miljö

Starta genom att aktivera kommunikation till Azure-Datacenter att förbereda miljön för Azure AD-programproxy. Om det finns en brandvägg i sökvägen måste du kontrollera att den är öppen. En öppen brandvägg tillåter anslutningsapp för att göra förfrågningar HTTPS (TCP) till programproxyn.

### <a name="open-ports"></a>Öppna portar

Öppna följande portar för **utgående** trafik. 

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Laddar ner listor över återkallade certifikat (CRL) vid verifiering av SSL-certifikatet |
   | 443 | All utgående kommunikation med programproxytjänsten |

Om brandväggstrafiken hanteras baserat på användarna som genererar den ska du även öppna portarna 80 och 443 för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst.

Du kanske en äldre version av kopplingen installeras om du redan använder Application Proxy. Följ den här självstudien för att installera den senaste versionen av anslutningsprogrammet. Tidigare versioner än 1.5.132.0 kräver även att följande portar är öppna: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure använder dessa URL: er för att verifiera certifikat. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Du kan tillåta anslutningar till \*. msappproxy.net och \*. servicebus.windows.net om din brandvägg eller proxyserver kan du konfigurera DNS Tillåt listor. Om inte måste du tillåta åtkomst till [Azure DataCenter IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653). IP-adressintervallen uppdateras varje vecka.

## <a name="install-and-register-a-connector"></a>Installera och registrera ett anslutningsprogram

Om du vill använda Application Proxy installerar du en anslutning på varje Windows-server som du använder med Application Proxy-tjänsten. Anslutningsprogrammet är en agent som hanterar den utgående anslutningen från lokala programservrar till programproxy i Azure Active Directory. Du kan installera ett anslutningsprogram på servrar som även har andra autentiseringsagenter installerade, till exempel Azure Active Directory Connect.

Så här installerar du anslutningsprogrammet:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
2. Välj ditt användarnamn i det övre högra hörnet. Kontrollera att du har loggat in till en katalog som använder Application Proxy. Om du vill ändra katalog väljer **växla katalog** och välj en katalog som använder Application Proxy.
3. I den vänstra navigeringspanelen väljer **Azure Active Directory**. 
4. Under **hantera**väljer **programproxy**.
5. Välj **hämta anslutningsapptjänsten**.
    
    ![Hämta anslutningsapptjänsten](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

6. Läs användningsvillkoren.  När du är klar väljer du **acceptera villkoren och hämta**.
7. Längst ned i fönstret Välj **kör** att installera anslutningen. Installationsguiden öppnas. 
8. Följ anvisningarna i guiden för att installera tjänsten. När du uppmanas att registrera anslutningsprogrammet med programproxyn för Azure AD-klientorganisationen anger du autentiseringsuppgifterna för programadministratören.
    - Om **IE Förbättrad säkerhetskonfiguration** är satt till **På** för Internet Explorer (IE) kanske inte registreringsskärmen visas. Följ instruktionerna i felmeddelandet för att få åtkomst. Se till att **Förbättrad säkerhetskonfiguration i Internet Explorer** är inställd på **av**.

### <a name="general-remarks"></a>Allmänna anmärkningar

Om du tidigare har installerat ett anslutningsprogram måste du installera om för att hämta den senaste versionen. Att visa information om tidigare versioner och vilka ändringar de innehåller, finns i [Application Proxy: Versionshistorik](application-proxy-release-version-history.md).

Väljer du att ha mer än en Windows-server för dina lokala program måste du installera och registrera anslutningsprogrammet på varje server. Du kan ordna anslutningsprogrammen i anslutningsgrupper. Mer information finns i [anslutningsgrupper](application-proxy-connector-groups.md). 

Om din organisation använder proxyservrar för att ansluta till internet måste du konfigurera dem för programproxy.  Mer information finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). 

Information om anslutningsprogram, kapacitetsplanering och hur de håller sig uppdaterade finns i avsnittet [Förstå anslutningsprogram för Azure AD programproxy](application-proxy-connectors.md). 


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifiera att anslutningsprogrammet installerats och registrerats på rätt sätt

Du kan använda Azure-portalen eller din Windows-server för att bekräfta att ett nytt anslutningsprogram har installerats.

### <a name="verify-the-installation-through-azure-portal"></a>Verifiera installationen via Azure-portalen

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Logga in på din klientkatalog i [Azure-portalen](https://portal.azure.com).
2. I den vänstra navigeringspanelen väljer **Azure Active Directory**, och välj sedan **Application Proxy** under den **hantera** avsnittet. Alla dina anslutningsprogram och anslutningsgrupper visas på den här sidan. 
3. Visa en koppling för att verifiera dess egenskaper. Kopplingar ska visas som standard. Expandera anslutningsprogrammet för att visa information om anslutningen som du vill visa inte visas. En aktiv grön etikett innebär att ditt anslutningsprogram kan ansluta till tjänsten. Även om etiketten är grön kan dock ett nätverksproblem fortfarande blockera anslutningsprogrammet från att ta emot meddelanden. 

    ![AzureAD Programproxyanslutningar](./media/application-proxy-connectors/app-proxy-connectors.png)

Mer hjälp med att installera en koppling finns i [Problem med att installera Application Proxy Connector](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verifiera installationen via Windows-server

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Öppna Hanteraren för Windows-tjänster genom att klicka på nyckeln **Windows** och ange *services.msc*.
2. Kontrollera om statusen för följande två tjänster är **Körs**.
   - **Microsoft AAD Application Proxy Connector** möjliggör anslutningar.
   - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsverktyget söker efter nya versioner av anslutningsprogrammet och uppdaterar det efter behov.

     ![Application Proxy Connector-tjänster – skärmbild](./media/application-proxy-enable/app_proxy_services.png)

3. Om status för tjänsterna inte är **kör**, högerklicka för att markera varje tjänst och välj **starta**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Lägg till ett lokalt program till Azure Active Directory

Nu när du har förberett din miljö och installerat ett anslutningsprogram är du redo att lägga till lokala program till Azure Active Directory.  

1. Logga in som administratör i [Azure-portalen](https://portal.azure.com/).
2. I den vänstra navigeringspanelen väljer **Azure Active Directory**.
3. Välj **företagsprogram**, och välj sedan **nytt program**.
4. Välj **lokala program**.  
5. I den **lägga till dina egna lokala program** och ange följande information om ditt program:

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Namn** | Namnet på programmet som ska visas på åtkomstpanelen och i Azure-portalen. |
    | **Intern webbadress** | Det här är webbadressen för att komma åt programmet från inuti ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server som olika program och ge varje webbplats sitt eget namn och sina egna åtkomstregler.<br><br>Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Exempel: om din app ligger i https:\//yourapp/app och använder bilder som finns på https:\//yourapp/media och du bör publicera https:\//yourapp/ som sökväg. Den interna webbadressen måste inte vara landningssidan som användarna ser. Mer information finns i [Ange en anpassad startsida för publicerade program](application-proxy-configure-custom-home-page.md). |
    | **Extern webbadress** | Adressen som ger användare åtkomst till programmet från utanför ditt nätverk. Om du inte vill använda standarddomänen för programproxy kan du läsa om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Förautentisering** | Hur programproxyn verifierar användare innan de ges åtkomst till ditt program.<br><br>**Azure Active Directory** – Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. Vi rekommenderar att det här alternativet som standard så att du kan dra nytta av Azure AD-säkerhetsfunktioner, t.ex. villkorlig åtkomst och Multi-Factor Authentication. **Azure Active Directory** krävs för övervakning av program med Microsoft Cloud Application Security.<br><br>**PASSTHROUGH** -användare behöver inte autentisera mot Azure AD för att få åtkomst till programmet. Du kan fortfarande konfigurera autentiseringskrav från serverdelen. |
    | **Anslutningsgrupp** | Anslutningsprogram bearbetar fjärråtkomsten till programmet och anslutningsgrupper hjälper dig att organisera anslutningsprogram och program efter region, nätverk eller syfte. Om du inte har skapat några anslutningsgrupper än kommer programmet att tilldelas **Standard**.<br><br>Om ditt program använder WebSockets för att ansluta måste alla anslutningsprogram i gruppen vara version 1.5.612.0 eller senare.|

5. Konfigurera **ytterligare inställningar** om det behövs. De flesta programmen bör behålla dessa inställningarna i standardtillstånden. 

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Tidsgränsen för serverdels-programmet** | Ställ endast in värdet på **Lång** om programmet autentiserar och ansluter långsamt. |
    | **Använd endast HTTP-cookie** | Ställ in värdet på **Ja** för att programproxycookies ska inkluderas i HTTPOnly-flaggan i HTTP-svarsrubriken. Ställ in värdet på **Nej** om du använder fjärrskrivbordstjänster.|
    | **Använd säker cookie**| Ställ in värdet på **Ja** för att skicka cookies via en säker kanal, som en krypterad HTTPS-begäran.
    | **Använd beständig cookie**| Behåll det här värdet inställt på **Nej**. Använd bara den här inställningen för program som inte kan dela cookies mellan processer. Mer information om cookie-inställningar finns i [Cookie-inställningar för att komma åt lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Översätt webbadresser i rubriker** | Behåll det här värdet som **Ja** såvida inte programmets ursprungliga värdrubrik krävs i autentiseringsbegäran. |
    | **Översätt webbadresser i brödtext för program** | Behåll det här värdet som **nr** om du inte har hårdkodad HTML länkar till andra lokala program och Använd inte anpassade domäner. Mer information finns i [Länka översättning med programproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ställ in det här värdet på **Ja** om du planerar att övervaka programmet med Microsoft Cloud App Security (MCAS). Mer information finns i [Konfigurera övervakning av realtidsprogram åtkomst med Microsoft Cloud App Security och Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |
   
6. Välj **Lägg till**.

## <a name="test-the-application"></a>Testa programmet

Du är redo att testa om programmet har lagts till korrekt. I följande steg kan du lägga till ett användarkonto i programmet och försöka logga in.

### <a name="add-a-user-for-testing"></a>Lägg till en användare för testning

Verifiera att användarkontot redan har behörighet att komma åt programmet inifrån företagsnätverket innan du lägger till en användare till programmet.

Lägga till en testanvändare:

1. Välj **företagsprogram**, och välj sedan det program som du vill testa.
2. Välj **komma igång**, och välj sedan **tilldela en användare för att testa**.
3. Under **användare och grupper**väljer **Lägg till användare**.
4. Under **Lägg till tilldelning**väljer **användare och grupper**. Den **användare och grupper** visas. 
5. Välj det konto som du vill lägga till. 
6. Välj **Välj**, och välj sedan **tilldela**.

### <a name="test-the-sign-on"></a>Testa inloggningen

Så här testar inloggning till programmet:

1. I webbläsaren navigerar du till den externa webbadressen som du konfigurerade under steget publicera. Du bör se startskärmen.
2. Logga in som den användare du skapade i föregående avsnitt.

Mer information om felsökning finns i [Felsöka problem med programproxyn och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien förberedde du din lokala miljö för arbete med programproxy och installerade och registrerade sedan anslutningsprogrammet för programproxy. Sedan la du till ett program till din Azure AD-klientorganisation. Du verifierade att en användare kan logga in på programmet med hjälp av ett Azure AD-konto.

Du gjorde detta:
> [!div class="checklist"]
> * Öppnade portar för utgående trafik och gav åtkomst till specifika webbadresser
> * Installerade anslutningsprogrammet på din Windows-server och registrerade den med programproxy
> * Verifierade att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lade till ett lokalt program till Azure AD-klientorganisationen
> * Verifiera att en användare kan logga in på programmet med hjälp av en Azure AD-konto

Nu är du redo att konfigurera programmet för enkel inloggning. Använd länken nedan för att välja en metod för enkel inloggning och hitta självstudier för enkel inloggning. 

> [!div class="nextstepaction"]
>[Konfigurera enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
