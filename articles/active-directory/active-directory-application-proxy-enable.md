<properties
    pageTitle="Aktivera Azure AD Application Proxy | Microsoft Azure"
    description="Aktivera Azure AD Application Proxy på den klassiska Azure-portalen och installera anslutningsverktyget för den omvända proxyn."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# Aktivera Application Proxy på Azure-portalen

I den här artikeln beskrivs steg för steg hur du aktiverar Microsoft Azure AD Application Proxy för din molnkatalog i Azure AD.

Mer information om hur Application Proxy kan hjälpa dig finns i [Konfigurera säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).

## Krav för Application Proxy
Innan du kan aktivera och använda Application Proxy-tjänster behöver du:

- En [Basic- eller Premium-prenumeration på Microsoft Azure AD](active-directory-editions.md) och en Azure AD-katalog som du är en global administratör för.
- En server som kör Windows Server 2012 R2 eller Windows 8.1 eller senare, som du kan installera Application Proxy Connector på. Servern skickar förfrågningar till Application Proxy-tjänsterna i molnet och behöver en HTTP- eller HTTPS-anslutning till de program som du publicerar.

    - När det gäller enkel inloggning till dina publicerade program ska den här datorn vara domänansluten i samma AD-domän som de program som du publicerar.

- Om det finns en brandvägg i sökvägen måste du se till att den är öppen så att anslutningsappen kan göra HTTPS-förfrågningar (TCP) till programproxyn. Anslutningsappen använder dessa portar tillsammans med underdomäner som ingår i högnivådomänerna msappproxy.net och servicebus.windows.net. Öppna följande portar för **utgående** trafik:

  	| Portnummer | Beskrivning |
  	| --- | --- |
  	| 80 | Aktivera utgående HTTP-trafik för säkerhetsverifiering. |
  	| 443 | Aktivera användarautentisering mot Azure AD (krävs endast för registreringen av anslutningsappen) |
  	| 10100–10120 | Aktivera LOB-HTTP-svar som skickats tillbaka till proxyn |
  	| 9352, 5671 | Aktivera kommunikationen mellan anslutningsappen och Azure-tjänsten vid inkommande förfrågningar. |
  	| 9350 | För att förbättra prestanda vid inkommande förfrågningar (valfritt) |
  	| 8080 | Aktivera startsekvensen och den automatiska uppdateringen av anslutningsappen |
  	| 9090 | Aktivera registreringen av anslutningsappen (krävs endast för registreringsprocessen för anslutningsappen) |
  	| 9091 | Aktivera automatisk förnyelse av anslutningsappens förtroendecertifikat |

    Om brandväggstrafiken hanteras baserat på användarna som genererar den öppnar du dessa portar för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst. Aktivera även port 8080 för NT Authority\System.

- Om din organisation använder proxyservrar för att ansluta till Internet kan du ta en titt på blogginlägget [Arbeta med befintliga lokala proxyservrar](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/). Där hittar du mer information om hur du konfigurerar dem.

## Steg 1: Aktivera Application Proxy i Azure AD
1. Logga in som administratör på [den klassiska Azure-portalen](https://manage.windowsazure.com/).
2. Gå till Active Directory och välj den katalog som du vill aktivera Application Proxy i.

    ![Active Directory – ikon](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Välj **Konfigurera** på katalogsidan och rulla ned till **Webbprogramproxy**.
4. Ändra **Aktivera programproxytjänster för den här katalogen** till **Aktiverad**.

    ![Aktivera Application Proxy](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Välj **Hämta nu**. **Nedladdningssidan för Azure AD Application Proxy Connector** öppnas. Läs och godkänn licensvillkoren och spara Windows Installer-filen (.exe) för anslutningsappen genom att klicka på **Ladda ned**.

## Steg 2: Installera och registrera anslutningsverktyget
1. Kör **AADApplicationProxyConnectorInstaller.exe** på den server som du har förberett och som uppfyller kraven.
2. Följ installationsanvisningarna i guiden.
3. Under installationen uppmanas du att registrera anslutningsappen med din Azure AD-klients programproxy.

  - Ange dina autentiseringsuppgifter som global Azure AD-administratör. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
  - Se till att den administratör som registrerar anslutningsappen finns i samma katalog där du har aktiverat programproxytjänsten. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat alias på den domänen.
  - Om **Förbättrad säkerhetskonfiguration i Internet Explorer** har inställningen **På** på den server där du installerar anslutningsappen så kan registreringsskärmen blockeras. Tillåt åtkomst genom att följa anvisningarna i felmeddelandet. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.
  - Om registreringen av anslutningsappen inte lyckas så gå till [Felsöka programproxyn](active-directory-application-proxy-troubleshoot.md).  

4. När installationen är klar läggs två nya tjänster till på servern:

    - **Microsoft AAD Application Proxy Connector** upprättar anslutningarna.
    - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar som med jämna mellanrum söker efter nya versioner av anslutningsappen och uppdaterar den när det behövs.

    ![Application Proxy Connector-tjänster – skärmbild](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Klicka på **Slutför** i installationsfönstret.

Om du vill upprätthålla hög tillgänglighet bör du distribuera minst två anslutningsappar. Upprepa steg 2 och 3 ovan om du vill distribuera fler anslutningsappar. Varje anslutningsapp måste registreras separat.

Om du vill avinstallera anslutningsappen måste du avinstallera både anslutnings- och uppdateringstjänsten. Ta bort tjänsten helt och hållet genom att starta om datorn.


## Nästa steg

Nu kan du börja [publicera program med Application Proxy](active-directory-application-proxy-publish.md).

Om du har program som finns på olika nätverk eller olika platser kan du använda anslutningsgrupper för att organisera olika anslutningar i logiska enheter. Läs mer i [Arbeta med programproxyanslutningar](active-directory-application-proxy-connectors.md).



<!--HONumber=sep16_HO1-->


