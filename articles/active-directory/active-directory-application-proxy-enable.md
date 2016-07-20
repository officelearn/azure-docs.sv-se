<properties
    pageTitle="Aktivera Azure AD Application Proxy | Microsoft Azure"
    description="Aktivera Azure AD Application Proxy på den klassiska Azure-portalen och installera anslutningsverktyget för den omvända proxyn."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="kgremban"/>

# Aktivera Application Proxy på Azure-portalen

Den här artikeln beskriver hur du aktiverar Microsoft Azure AD Application Proxy för din molnkatalog i Azure AD. Detta innebär bland annat att du installerar Application Proxy Connector i ditt privata nätverk, som hanterar anslutningen från nätverket till proxytjänsten. Du måste också registrera anslutningsverktyget med din Microsoft Azure AD-klientprenumeration. Mer information om hur Application Proxy kan hjälpa dig finns i [Konfigurera säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).

När du har slutfört den här guiden och aktiverat Azure AD Application Proxy är du redo att publicera dina lokala appar för fjärråtkomst.

> [AZURE.NOTE] Application Proxy är en funktion som bara är tillgänglig om du har uppgraderat till Premium- eller Basic-versionen av Azure Active Directory. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

## Krav för Application Proxy
Innan du kan aktivera och använda Application Proxy-tjänster behöver du:

- En [Basic- eller Premium-prenumeration på Microsoft Azure AD](active-directory-editions.md) och en Azure AD-katalog som du är en global administratör för.
- En server som kör Windows Server 2012 R2 eller Windows 8.1 eller senare, som du kan installera Application Proxy Connector på. Servern skickar HTTPS-förfrågningar till Application Proxy-tjänsterna i molnet och behöver en HTTPS-anslutning till de program som du vill publicera.
- Om det finns en brandvägg i vägen kontrollerar du att den är öppen så att anslutningsverktyget kan göra HTTPS-förfrågningar (TCP) till Application Proxy. Anslutningsverktyget använder dessa portar tillsammans med underdomäner som ingår i domänerna på hög nivå: *msappproxy.net* och *servicebus.windows.net*. Öppna **samtliga** av följande portar för **utgående** trafik:

  	| Portnummer | Beskrivning |
  	| --- | --- |
  	| 80 | För att aktivera utgående HTTP-trafik för säkerhetsvalidering. |
  	| 443 | För att aktivera användarautentisering mot Azure AD (krävs endast för registreringen av anslutningsverktyget) |
  	| 10100–10120 | För att aktivera LOB-HTTP-svar som skickas tillbaka till proxyservern |
  	| 9352, 5671 | För att aktivera kommunikationen mellan anslutningsverktyget och Azure-tjänsten vid inkommande förfrågningar. |
  	| 9350 | För att förbättra prestanda vid inkommande förfrågningar (valfritt) |
  	| 8080 | För att aktivera startsekvensen och automatisk uppdatering för anslutningsverktyget |
  	| 9090 | För att aktivera registreringen av anslutningsverktyget (krävs endast för registreringsprocessen för anslutningsverktyget) |
  	| 9091 | För att aktivera automatisk förnyelse av förtroendecertifikatet för anslutningsverktyget |

Om brandväggstrafiken hanteras baserat på användarna som genererar den öppnar du dessa portar för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst. Aktivera även port 8080 för NT Authority\System.


## Steg 1: Aktivera Application Proxy i Azure AD
1. Logga in som administratör på [den klassiska Azure-portalen](https://manage.windowsazure.com/).
2. Gå till Active Directory och välj den katalog som du vill aktivera Application Proxy i.

    ![Active Directory – ikon](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Välj **Konfigurera** på katalogsidan och rulla ned till **Webbprogramproxy**.
4. Ändra **Aktivera programproxytjänster för den här katalogen** till **Aktiverad**.

    ![Aktivera Application Proxy](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Välj **Hämta nu**. **Nedladdningssidan för Azure AD Application Proxy Connector** öppnas. Läs och godkänn licensvillkoren och klicka på **Ladda ned** för att spara Windows Installer-filen (.exe) för Application Proxy Connector.

## Steg 2: Installera och registrera anslutningsverktyget
1. Kör *AADApplicationProxyConnectorInstaller.exe* på den server som du har förberett och som uppfyller kraven ovan.
2. Följ installationsanvisningarna i guiden.
3. Under installationen uppmanas du att registrera anslutningsverktyget med programproxyn för din Azure AD-klient.

  - Ange dina autentiseringsuppgifter som global Azure AD-administratör. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
  - Kontrollera att administratören som registrerar anslutningsverktyget finns i samma katalog som du har aktiverat tjänsten Application Proxy i. Om klientdomänen till exempel är contoso.com är administratören admin@contoso.com eller ett annat alias för den domänen.
  - Om **Förbättrad säkerhetskonfiguration i Internet Explorer** har inställningen **På** på servern som du installerar Azure AD Connector på kan registreringsskärmen blockeras. Om det händer följer du instruktionerna i felmeddelandet för att tillåta åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.
  - Om registreringen av anslutningsverktyget inte lyckas läser du [Felsöka Application Proxy](active-directory-application-proxy-troubleshoot.md).  

4. När installationen är klar läggs två nya tjänster till på servern, som du ser nedan.

    - **Microsoft AAD Application Proxy Connector** upprättar anslutningarna.
    - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar som med jämna mellanrum söker efter nya versioner av anslutningsverktyget och uppdaterar det när det behövs.

    ![Application Proxy Connector-tjänster – skärmbild](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Slutför installationen genom att klicka på **Slutför** i installationsfönstret.

Nu kan du börja [publicera program med Application Proxy](active-directory-application-proxy-publish.md).

För att upprätthålla hög tillgänglighet bör du distribuera minst en ytterligare anslutning. Upprepa steg 2 och 3 ovan om du vill distribuera fler kopplingar. Varje anslutning måste registreras separat.

Om du vill avinstallera anslutningsverktyget avinstallerar du både anslutningstjänsten och uppdateringstjänsten och startar sedan om datorn så att tjänsten tas bort helt.


## Nästa steg

- [Publicera program med Application Proxy](active-directory-application-proxy-publish.md)
- [Publicera program med ditt domännamn](active-directory-application-proxy-custom-domains.md)
- [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
- [Felsöka problem med Application Proxy](active-directory-application-proxy-troubleshoot.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Jun16_HO2-->


