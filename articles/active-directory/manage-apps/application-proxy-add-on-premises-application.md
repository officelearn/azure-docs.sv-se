---
title: Lägg till en lokal app – programproxy i Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien visar hur du förbereder din miljö för användning med programproxy och använder sedan Azure-portalen för att lägga till ett lokalt program till Azure AD-klientorganisationen.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc454fdba6ec875c3d3b572a7aba91bb9d389845
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617232"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Självstudie: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory

Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien förbereder din miljö för användning med programproxy. När din miljö är redo använder du Azure-portalen för att lägga till ett lokalt program till Azure AD-klientorganisationen.

I den här självstudien:

> [!div class="checklist"]
> * Öppnar portar för utgående trafik och ger åtkomst till specifika webbadresser.
> * Installerar anslutningsprogrammet på din Windows-server och registrerar den med Programproxy.
> * Verifierar att anslutningsprogrammet installerats och registrerats på rätt sätt.
> * Lägger till ett lokalt program till Azure AD-klientorganisationen.
> * Verifierar att en testanvändare kan logga in på programmet med hjälp av ett Azure AD-konto.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett program till din klientorganisation behöver du:

* En [Microsoft Azure AD grundläggande eller premiumprenumeration](https://azure.microsoft.com/pricing/details/active-directory). 
* Ett administratörskonto för programmet.

### <a name="windows-server"></a>Windows-server

Om du vill använda programproxy behöver du en Windows-server som kör Windows Server 2012 R2 eller senare. Du installerar anslutningsappen för programproxy på servern. Anslutningsservern måste ansluta till programproxytjänsten i Azure och de lokala programmen som du har för avsikt att publicera.

Vi rekommenderar att du har mer än en Windows-server för att säkra hög tillgänglighet i produktionsmiljön. I den här självstudien räcker det med en Windows-server.

#### <a name="recommendations-for-the-connector-server"></a>Rekommendationer för connector-server

1. Placera fysiskt anslutningsservern nära programservrarna för att optimera prestandan mellan anslutningsprogrammet och programmet. Mer information finns i [Överväganden för nätverkstopologi](application-proxy-network-topology.md).

2. Connector-server och program webbservrarna ska tillhöra samma Active Directory-domän eller sträcker sig över domäner. Med servrar i samma domän eller domäner med förtroende är ett krav för att använda enkel inloggning (SSO) med integrerad Windows-autentisering (IWA) och Kerberos-begränsad delegering (KCD). Om anslutningsservern och webbprogramservrarna är i olika Active Directory-domäner så måste du använda resursbaserad delegering för enkel inloggning. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="software-requirements"></a>Programvarukrav

Windows-anslutningsservern måste ha TLS 1.2 aktiverat innan du installerar anslutningsprogrammet för programproxyn. Befintliga anslutningar med versioner under 1.5.612.0 fortsätter tills vidare att fungera i tidigare versioner av TLS. 

Aktivera TLS 1.2:

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Starta om servern

## <a name="prepare-your-on-premises-environment"></a>Förbered din lokala miljö

Du måste först aktivera kommunikation till Azure-datacenter för att förbereda din miljö för Azure AD-programproxy. Om det finns en brandvägg i sökvägen måste du se till att den är öppen så att anslutningsprogrammet kan göra HTTPS-förfrågningar (TCP) till programproxyn.

### <a name="open-ports"></a>Öppna portar

Öppna följande portar för **utgående** trafik. 

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Laddar ner listor över återkallade certifikat (CRL) vid verifiering av SSL-certifikatet |
   | 443 | All utgående kommunikation med programproxytjänsten |

Om brandväggstrafiken hanteras baserat på användarna som genererar den ska du även öppna portarna 80 och 443 för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst.

Om du redan använder programproxy är kanske en äldre version av anslutningsprogrammet installerat.  Följ den här självstudien för att installera den senaste versionen av anslutningsprogrammet. Tidigare versioner än 1.5.132.0 kräver även att följande portar är öppna: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure använder dessa webbadresser för att verifiera certifikat |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Om din brandvägg eller proxyserver tillåter DNS-listor över tillåtna program kan du tillåta anslutningsprogram till \*.msappproxy.net och \*.servicebus.windows.net. Om inte måste du tillåta åtkomst till [Azure DataCenter IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653). IP-adressintervallen uppdateras varje vecka.

## <a name="install-and-register-a-connector"></a>Installera och registrera ett anslutningsprogram

Om du vill använda programproxy måste du installera ett anslutningsprogram på varje Windows-server du väljer att använda med programproxytjänsten. Anslutningsprogrammet är en agent som hanterar den utgående anslutningen från lokala programservrar till programproxy i Azure Active Directory. Du kan installera ett anslutningsprogram på servrar som även har andra autentiseringsagenter installerade, till exempel Azure Active Directory Connect.

Så här installerar du anslutningsprogrammet:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
2. Din aktuella katalog visas under ditt användarnamn i det övre högra hörnet. Kontrollera att du är inloggad på katalogen som använder programproxy. Om du behöver ändra katalogen väljer du ikonen.
3. På det vänstra bladet klickar du på **Azure Active Directory** och sedan **programproxy**.
4. Klicka på **Ladda ner anslutningsprogramtjänsten**.
5. Läs användningsvillkoren.  När du är redo klickar du på **Godkänn villkoren och ladda ner**.
6. Längst ned i fönstret ser du en uppmaning om att ladda ner **AADApplicationProxyConnectorInstaller.exe**. Klicka på **Kör** för att installera anslutningsprogrammet. Installationsguiden öppnas. 
7. Följ installationsanvisningarna i guiden. När du uppmanas att registrera anslutningsprogrammet med programproxyn för Azure AD-klientorganisationen anger du autentiseringsuppgifterna för programadministratören.
    - Om **IE Förbättrad säkerhetskonfiguration** är satt till **På** för Internet Explorer (IE) kanske inte registreringsskärmen visas. Följ instruktionerna i felmeddelandet för att få åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inställt på **Av**.

### <a name="general-remarks"></a>Allmänna anmärkningar

Om du tidigare har installerat ett anslutningsprogram måste du installera om för att hämta den senaste versionen. Att visa information om tidigare versioner och vilka ändringar de innehåller, finns i [Application Proxy - versionshistorik](application-proxy-release-version-history.md).

Väljer du att ha mer än en Windows-server för dina lokala program måste du installera och registrera anslutningsprogrammet på varje server. Du kan ordna anslutningsprogrammen i anslutningsgrupper. Mer information finns i [anslutningsgrupper](application-proxy-connector-groups.md). 

Om din organisation använder proxyservrar för att ansluta till internet måste du konfigurera dem för programproxy.  Mer information finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). 

Information om anslutningsprogram, kapacitetsplanering och hur de håller sig uppdaterade finns i avsnittet [Förstå anslutningsprogram för Azure AD programproxy](application-proxy-connectors.md). 


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifiera att anslutningsprogrammet installerats och registrerats på rätt sätt

Du kan använda Azure-portalen eller din Windows-server för att bekräfta att ett nytt anslutningsprogram har installerats.

### <a name="verify---azure-portal"></a>Verifiera – Azure-portalen

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Logga in på din klientkatalog i [Azure-portalen](https://portal.azure.com).
2. Klicka på **Azure Active Directory** och sedan **programproxy**. Alla dina anslutningsprogram och anslutningsgrupper visas på den här sidan. 
3. Välj ett anslutningsprogram för att verifiera egenskaperna. En aktiv grön etikett innebär att ditt anslutningsprogram kan ansluta till tjänsten. Även om etiketten är grön kan dock ett nätverksproblem fortfarande blockera anslutningsprogrammet från att ta emot meddelanden. 

    ![AzureAD anslutningsprogram för programproxy](./media/application-proxy-connectors/app-proxy-connectors.png)

Mer information om att installera ett anslutningsprogram finns i [Problem vid installationen av anslutningsprogram för programproxy](application-proxy-connector-installation-problem.md).

### <a name="verify---windows-server"></a>Verifiera – Windows-server

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Öppna Hanteraren för Windows-tjänster genom att klicka på nyckeln **Windows** och ange *services.msc*.
2. Kontrollera om statusen för följande två tjänster är **Körs**.
   - **Microsoft AAD Application Proxy Connector** upprättar anslutningarna.
   - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsverktyget söker efter nya versioner av anslutningsprogrammet och uppdaterar det efter behov.

     ![Application Proxy Connector-tjänster – skärmbild](./media/application-proxy-enable/app_proxy_services.png)

3. Om statusen för tjänsterna inte **körs** kan du högerklicka på varje tjänst och välja **starta**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Lägg till ett lokalt program till Azure Active Directory

Nu när du har förberett din miljö och installerat ett anslutningsprogram är du redo att lägga till lokala program till Azure Active Directory.  

1. Logga in som administratör i [Azure-portalen](https://portal.azure.com/).
2. Välj **Azure Active Directory** > **företagsprogram** > **nytt program**.

    ![Lägg till ett företagsprogram](./media/application-proxy-publish-azure-portal/add-app.png)

3. Välj **alla**och välj därefter **lokala program**.  

    ![Lägg till ditt eget program](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. På bladet **Lägg till ditt eget lokala program** anger du följande information om ditt program:

    ![Konfigurera ditt lokala program](./media/application-proxy-add-on-premises-application/add-on-premises-app-with-application-proxy-updated.png)

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Namn** | Namnet på programmet som ska visas på åtkomstpanelen och i Azure-portalen. |
    | **Intern webbadress** | Det här är webbadressen för att komma åt programmet från inuti ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server som olika program och ge varje webbplats sitt eget namn och sina egna åtkomstregler.<br><br>Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Exempel: om din app ligger i https:\//yourapp/app och använder bilder som finns på https:\//yourapp/media och du bör publicera https:\//yourapp/ som sökväg. Den interna webbadressen måste inte vara landningssidan som användarna ser. Mer information finns i [Ange en anpassad startsida för publicerade program](application-proxy-configure-custom-home-page.md). |
    | **Extern webbadress** | Adressen som ger användare åtkomst till programmet från utanför ditt nätverk. Om du inte vill använda standarddomänen för programproxy kan du läsa om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Förautentisering** | Hur programproxyn verifierar användare innan de ges åtkomst till ditt program.<br><br>**Azure Active Directory** – Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. Vi rekommenderar det här alternativet som standard så att du kan dra nytta av säkerhetsfunktioner i Azure AD, exempelvis villkorlig åtkomst och multifaktorautentisering. **Azure Active Directory** krävs för övervakning av program med Microsoft Cloud Application Security.<br><br>**Genomströmning** – Användarna behöver inte autentisera sig mot Azure Active Directory för att få åtkomst till programmet. Du kan fortfarande konfigurera autentiseringskrav från serverdelen. |
    | **Anslutningsgrupp** | Anslutningsprogram bearbetar fjärråtkomsten till programmet och anslutningsgrupper hjälper dig att organisera anslutningsprogram och program efter region, nätverk eller syfte. Om du inte har skapat några anslutningsgrupper än kommer programmet att tilldelas **Standard**.<br><br>Om ditt program använder WebSockets för att ansluta måste alla anslutningsprogram i gruppen vara version 1.5.612.0 eller senare.|

5. Konfigurera **ytterligare inställningar** om det behövs. De flesta programmen bör behålla dessa inställningarna i standardtillstånden. 

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Tidsgränsen för serverdels-programmet** | Ställ endast in värdet på **Lång** om programmet autentiserar och ansluter långsamt. |
    | **Använd endast HTTP-cookie** | Ställ in värdet på **Ja** för att programproxycookies ska inkluderas i HTTPOnly-flaggan i HTTP-svarsrubriken. Ställ in värdet på **Nej** om du använder fjärrskrivbordstjänster.|
    | **Använd säker cookie**| Ställ in värdet på **Ja** för att skicka cookies via en säker kanal, som en krypterad HTTPS-begäran.
    | **Använd beständig cookie**| Behåll det här värdet inställt på **Nej**. Den här inställningen bör endast användas för program som inte kan dela cookies mellan processer. Mer information om cookie-inställningar finns i avsnittet om [cookie-inställningar för åtkomst till lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)
    | **Översätt webbadresser i rubriker** | Behåll det här värdet som **Ja** såvida inte programmets ursprungliga värdrubrik krävs i autentiseringsbegäran. |
    | **Översätt webbadresser i brödtext för program** | Behåll det här värdet som **Nej** såvida du inte har hårdkodade HTML-länkar till andra lokala program och inte använder anpassade domäner. Mer information finns i [Länka översättning med programproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ställ in det här värdet på **Ja** om du planerar att övervaka programmet med Microsoft Cloud App Security (MCAS). Mer information finns i [Configure real-time application access monitoring with Microsoft Cloud App Security and Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md) (Konfigurera övervakning av realtidsprogramåtkomst med Microsoft Cloud App Security och Azure Active Directory) |
   
6. Välj **Lägg till**.

## <a name="test-the-application"></a>Testa programmet

Du är redo att testa om programmet har lagts till korrekt. I följande steg kan du lägga till ett användarkonto i programmet och försöka logga in.

### <a name="add-a-user-for-testing"></a>Lägg till en användare för testning

Verifiera att användarkontot redan har behörighet att komma åt programmet inifrån företagsnätverket innan du lägger till en användare till programmet.

Lägga till en testanvändare:

1. Gå tillbaka till bladet **Snabbstart** och välj **Tilldela en användare för testning**.

    ![Tilldela en användare för testning](./media/application-proxy-publish-azure-portal/assign-user.png)

2. På bladet **Användare och grupper** väljer du **Lägg till användare**.

    ![Lägg till en användare eller grupp](./media/application-proxy-publish-azure-portal/add-user.png)

3. På bladet **Lägg till tilldelning** väljer du **Användare och grupper** och sedan det konto som du vill lägga till. 
4. Välj **Tilldela**.

### <a name="test-the-sign-on"></a>Testa inloggningen

Testa inloggningen till programmet:

1. I webbläsaren navigerar du till den externa webbadressen som du konfigurerade under steget publicera. 
2. Du bör se startskärmen.
3. Försök att logga in som användaren du skapade i föregående avsnitt.

    ![Testa ditt publicerade program](./media/application-proxy-publish-azure-portal/test-app.png)

Mer information om felsökning finns i [Felsöka problem med programproxyn och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien förberedde du din lokala miljö för arbete med programproxy och installerade och registrerade sedan anslutningsprogrammet för programproxy. Sedan la du till ett program till din Azure AD-klientorganisation. Du verifierade att en användare kan logga in på programmet med hjälp av ett Azure AD-konto.

Du gjorde detta:
> [!div class="checklist"]
> * Öppnade portar för utgående trafik och gav åtkomst till specifika webbadresser
> * Installerade anslutningsprogrammet på din Windows-server och registrerade den med programproxy
> * Verifierade att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lade till ett lokalt program till Azure AD-klientorganisationen
> * Verifierade att en testanvändare kan logga in på programmet med hjälp av ett Azure AD-konto.

Nu är du redo att konfigurera programmet för enkel inloggning. Använd länken nedan för att välja en metod för enkel inloggning och självstudier för enkel inloggning. 

> [!div class="nextstepaction"]
>[Konfigurera enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
