---
title: Komma igång med webbtjänsten MFA Server Mobile App
description: Azure Multi-Factor Authentication-appen erbjuder ett autentiseringsalternativ utanför IP-nätverket.  Med det här alternativet kan MFA-servern skicka push-meddelanden till användarna.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Komma igång med webbtjänsten MFA Server Mobile App
Azure Multi-Factor Authentication-appen erbjuder ett autentiseringsalternativ utanför IP-nätverket. I stället för ett automatiserat telefonsamtal eller SMS till användaren vid inloggningen skickar Azure Multi-Factor Authentication ett meddelande till Azure Multi-Factor Authentication-appen på användarens smartphone eller surfplatta. Användaren trycker bara på ”Autentisera” (eller anger en PIN-kod och trycker på ”Autentisera”) i appen för att logga in.

För att du ska kunna använda Azure Multi-Factor Authentication-appen krävs följande så att appen kan kommunicera med Mobile App-webbtjänsten:

* Information om maskin- och programvarukraven finns i avsnittet Maskin- och programvarukrav.
* Du måste använda v6.0 eller senare av Azure Multi-Factor Authentication Server.
* Webbtjänsten Mobile App måste installeras på en Internetuppkopplad webbserver som kör Microsoft® Internet Information Services (IIS) IIS 7.x eller senare.  Mer information om IIS finns i [IIS.NET](http://www.iis.net/).
* Kontrollera att ASP.NET v4.0.30319 har installerats, registrerats och är inställt på Tillåts.
* Rolltjänsterna ASP.NET och IIS 6 Metabase-kompatibilitet krävs.
* Webbtjänsten Mobile App måste kunna nås via en offentlig URL.
* Webbtjänsten Mobile App måste skyddas med ett SSL-certifikat.
* Webbtjänst-SDK för Azure Multi-Factor Authentication måste vara installerat i IIS 7.x eller senare på den server som Azure Multi-Factor Authentication-servern är installerad på.
* Azure Multi-Factor Authentication webbtjänst-SDK måste skyddas med ett SSL-certifikat.
* Webbtjänsten Mobile App måste kunna ansluta till webbtjänst-SDK för Azure Multi-Factor Authentication via SSL.
* Webbtjänsten Mobile App måste kunna autentisera till webbtjänst-SDK för Azure Multi-Factor Authentication med autentiseringsuppgifterna för ett tjänstkonto som är medlem i säkerhetsgruppen ”PhoneFactor Admins”. Det här kontot och gruppen finns i Active Directory om Azure Multi-Factor Authentication-servern körs på en domänansluten server. Det här tjänstkontot och gruppen finns lokalt på Azure Multi-Factor Authentication-servern om den inte är ansluten till en domän.

Om du installerar användarportalen på en annan server än Azure Multi-Factor Authentication-servern följer du dessa tre steg:

1. Installera webbtjänst-SDK
2. Installera webbtjänsten Mobile App
3. Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server
4. Aktivera Azure Multi-Factor Authentication-appen för slutanvändare

## Installera webbtjänst-SDK
Om Azure Multi-Factor Authentication webbtjänst-SDK inte redan är installerat på Azure Multi-Factor Authentication-servern går du till den servern och öppnar Azure Multi-Factor Authentication-servern. Klicka på ikonen för webbtjänst-SDK, klicka på Installera webbtjänst-SDK och följ instruktionerna. Webbtjänst-SDK måste skyddas med ett SSL-certifikat. Ett självsignerat certifikat kan användas för detta ändamål, men det måste importeras till arkivet ”Betrodda rotcertifikatutfärdare” för det lokala datorkontot på webbservern för användarportalen så att det litar på certifikatet när SSL-anslutningen initieras.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Installera webbtjänsten Mobile App
Tänk på följande innan du installerar Mobile App-webbtjänsten:

* Om Azure Multi-Factor Authentication-användarportalen redan är installerad på den Internetuppkopplade servern kan användarnamnet, lösenordet och URL:en för webbtjänst-SDK kopieras från användarportalens web.config-fil.
* Det kan vara bra att öppna en webbläsare på den Internetuppkopplade webbservern och gå till URL:en för webbtjänst-SDK som har angetts i web.config-filen. Om webbläsaren kan ansluta till webbtjänsten bör du uppmanas att ange dina autentiseringsuppgifter. Ange användarnamnet och lösenordet som har angetts i web.config-filen exakt som de visas i filen. Se till att inga certifikatvarningar eller fel visas.
* Om det finns en omvänd proxy eller brandvägg framför webbservern för Mobile App-webbtjänsten som utför SSL-avlastning kan du redigera filen web.config för Mobile App-webbtjänsten och lägga till följande nyckel i avsnittet <appSettings> så att Mobile App-webbtjänsten kan använda http i stället för https. SSL krävs dock fortfarande från Mobile App till brandväggen/den omvända proxyn. <add key="SSL_REQUIRED" value="false"/>

### Så här installerar du Mobile App-webbtjänsten
<ol>

<li>Öppna Utforskaren på Azure Multi-Factor Authentication-servern och navigera till mappen där Azure Multi-Factor Authentication Server är installerat (t.ex. C:\Program\Microsoft Files\Azure Multi-Factor Authentication). Välj 32- eller 64-bitarsversionen av Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup-installationsfilen beroende på vilken typ av server som Mobile App-webbtjänsten ska installeras på. Kopiera installationsfilen till den Internetuppkopplade servern.</li>

<li>Installationsfilen måste köras med administratörsbehörighet på den Internetuppkopplade webbservern. Det enklaste sättet att göra detta är att öppna en kommandotolk som administratör och navigera till den plats som installationsfilen kopierades till.</li>  

<li>Kör Multi-Factor AuthenticationMobileAppWebServiceSetup-installationsfilen, ändra platsen om du vill och ändra namnet för den virtuella katalogen till ett kort namn som ”PA”. Vi rekommenderar att du använder ett kort namn för den virtuella katalogen eftersom användarna måste ange Mobile App-webbtjänstens URL på den mobila enheten under aktiveringen.</li>

<li>När installationen av Azure Multi-Factor AuthenticationMobileAppWebServiceSetup är klar bläddrar du till C:\inetpub\wwwroot\PA (eller relevant katalog beroende på namnet på den virtuella katalogen) och redigerar filen Web.config.</li>  

<li>Leta upp nycklarna WEB_SERVICE_SDK_AUTHENTICATION_USERNAME och WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD och ange värdena till användarnamnet och lösenordet för tjänstkontot som är medlem i säkerhetsgruppen PhoneFactor Admins (se avsnittet Krav ovan). Detta kan vara samma konto som används som identitet för Azure Multi-Factor Authentication-användarportalen om den har installerats tidigare. Var noga med att ange användarnamnet och lösenordet inom citattecken i slutet av raden, (value=””/>). Vi rekommenderar att du använder ett kvalificerat användarnamn (t.ex. domän\användarnamn eller dator\användarnamn).</li>  

<li>Leta upp inställningen pfMobile App Web Service_pfwssdk_PfWsSdk och ändra värdet från ”http://localhost:4898/PfWsSdk.asmx” till URL:en för webbtjänst-SDK som körs på Azure Multi-Factor Authentication-servern (t.ex. https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Eftersom SSL används för den här anslutningen måste du referera till webbtjänst-SDK med servernamnet och inte IP-adressen eftersom SSL-certifikatet är utfärdat för servernamnet och den URL som används måste matcha namnet på certifikatet. Om servernamnet inte matchas till en IP-adress från den Internetuppkopplade servern lägger du till en post i värdfilen på den servern för att mappa namnet på Azure Multi-Factor Authentication-servern med dess IP-adress. Spara web.config-filen när ändringarna har gjorts.</li>  

<li>Om webbplatsen som Mobile App-webbtjänsten installerades under (t.ex. Standardwebbplats) inte redan har bundits till ett offentligt signerat certifikat installerar du certifikatet på servern om det inte redan har installerats, öppnar IIS-hanteraren och binder certifikatet till webbplatsen.</li>  

<li>Öppna en webbläsare från en dator och gå till URL:en där Mobile App-webbtjänsten installerades (t.ex. https://www.publicwebsite.com/PA). Se till att inga certifikatvarningar eller fel visas.</li>

### Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server
Nu när Mobile App-webbtjänsten är installerad måste du konfigurera Azure Multi-Factor Authentication Server så att den fungerar med portalen.

#### Så här konfigurerar du Mobile App-inställningarna i Azure Multi-Factor Authentication Server
1. Klicka på ikonen för användarportalen i Azure Multi-Factor Authentication Server. Om användarna ska kunna styra sina autentiseringsmetoder markerar du Mobilapp under Tillåt användare att välja metod på fliken Inställningar. Om den här funktionen inte är aktiverad måste slutanvändarna kontakta din supportavdelning för att slutföra aktiveringen av mobilappen.
2. Markera kryssrutan Tillåt användare att aktivera mobilapp.
3. Markera kryssrutan Tillåt användarregistrering.
4. Klicka på ikonen för Mobilapp.
5. Ange URL:en som används med den virtuella katalogen som skapades när Azure Multi-Factor AuthenticationMobileAppWebServiceSetup installerades. Du kan ange ett kontonamn i det angivna området. Det här företagsnamnet visas i mobilprogrammet. Om fältet lämnas tomt visas namnet på din Multi-Factor Auth-provider som skapades på Azure-hanteringsportalen.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>



<!--HONumber=Sep16_HO3-->


