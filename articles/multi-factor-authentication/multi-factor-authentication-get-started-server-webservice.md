---
title: "Azure MFA Server-mobilappwebbtjänst | Microsoft Docs"
description: "Microsoft Authenticator-appen erbjuder ytterligare ett autentiseringsalternativ utanför IP-nätverket.  Med det här alternativet kan MFA-servern skicka push-meddelanden till användarna."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.contentlocale: sv-se
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Aktivera mobilappautentisering och Azure Multi-Factor Authentication Server

Microsoft Authenticator-appen erbjuder ytterligare ett verifieringsalternativ utanför IP-nätverket. I stället för att ringa ett automatiserat telefonsamtal eller skicka ett SMS till användaren vid inloggningen skickar Azure Multi-Factor Authentication ett meddelande till Microsoft Authentication-appen på användarens smartphone eller surfplatta. Användaren trycker bara på **Autentisera** (eller anger en PIN-kod och trycker på ”Autentisera”) i appen för att slutföra deras inloggning.

Att använda en mobilapp för tvåstegsverifiering rekommenderas när mobilmottagningen är opålitlig. Om du använder appen som en OATH-tokengenerator behövs ingen nätverks- eller internetanslutning.

Om du installerar användarportalen på en annan server än Azure Multi-Factor Authentication-servern följer du dessa steg:

1. Installera webbtjänst-SDK
2. Installera webbtjänsten Mobile App
3. Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server
4. Aktivera Microsoft Authenticator-appen för slutanvändare

## <a name="requirements"></a>Krav

För att du ska kunna använda Microsoft Authenticator-appen krävs följande så att appen kan kommunicera med mobilappwebbtjänsten:

* Azure Multi-Factor Authentication Server version 6.0 eller senare
* Installera mobilappwebbtjänsten på en Internetuppkopplad webbserver som kör Microsoft® [Internet Information Services (IIS) IIS 7.x eller senare](http://www.iis.net/)
* ASP.NET v4.0.30319 har installerats, registrerats och är inställt på Tillåts
* Rolltjänsterna ASP.NET och IIS 6 Metabase-kompatibilitet krävs.
* Mobilappwebbtjänsten kan nås via en offentlig URL
* Mobilappwebbtjänsten skyddas av ett SSL-certifikat.
* Installera webbtjänst-SDK för Azure Multi-Factor Authentication på IIS 7.x eller senare på samma server som Azure Multi-Factor Authentication-servern är installerad på
* Webbtjänst-SDK för Azure Multi-Factor Authentication skyddas med ett SSL-certifikat.
* Mobilappwebbtjänsten kan ansluta till webbtjänst-SDK för Azure Multi-Factor Authentication via SSL
* Mobilappwebbtjänsten kan autentisera till webbtjänst-SDK för Azure MFA med autentiseringsuppgifterna för ett tjänstkonto som är medlem i säkerhetsgruppen ”PhoneFactor Admins”. Tjänstkontot och gruppen finns i Active Directory om Azure Multi-Factor Authentication-servern finns på en domänansluten server. Det här tjänstkontot och gruppen finns lokalt på Azure Multi-Factor Authentication-servern om den inte är ansluten till en domän.


## <a name="install-the-web-service-sdk"></a>Installera webbtjänst-SDK
Om webbtjänst-SDK för Azure Multi-Factor Authentication inte redan är installerat på Azure Multi-Factor Authentication-servern (MFA) går du till den servern och öppnar Azure MFA-servern.

1. Klicka på ikonen webbtjänst-SDK.
2. Klicka på **Installera webbtjänst-SDK** och följ anvisningarna som visas.

Webbtjänst-SDK måste skyddas med ett SSL-certifikat. Ett självsignerat certifikat är lämpligt för detta ändamål. Importera certifikatet till arkivet ”Betrodda rotcertifikatutfärdare” för det lokala datorkontot på webbservern för användarportalen så att certifikatet blir betrott när SSL-anslutningen initieras.

![Konfiguration](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>Installera webbtjänsten Mobile App
Tänk på följande uppgifter innan du installerar mobilappwebbtjänsten:

* Om Azure MFA-användarportalen redan är installerad på den Internetuppkopplade servern kan användarnamnet, lösenordet och URL:en för webbtjänst-SDK kopieras från användarportalens web.config-fil.
* Det kan vara bra att öppna en webbläsare på den Internetuppkopplade webbservern och gå till URL:en för webbtjänst-SDK som har angetts i web.config-filen. Om webbläsaren kan ansluta till webbtjänsten bör du uppmanas att ange dina autentiseringsuppgifter. Ange användarnamnet och lösenordet som har angetts i web.config-filen exakt som de visas i filen. Se till att inga certifikatvarningar eller fel visas.
* Om det finns en omvänd proxy eller brandvägg framför webbservern för mobilappwebbtjänsten som utför SSL-avlastning kan du redigera filen web.config för mobilappwebbtjänsten så att mobilappwebbtjänsten kan använda http i stället för https. SSL krävs fortfarande från Mobile App till brandväggen/den omvända proxyn. Lägg till följande nyckel i avsnittet \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>Installera tjänsten

1. Öppna Utforskaren på Azure Multi-Factor Authentication-servern och navigera till mappen där Azure MFA-servern är installerad (t.ex. C:\Programfiler\Azure Multi-Factor Authentication). Välj 32- eller 64-bitarsversionen av installationsfilen för Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup. Kopiera installationsfilen till den Internetuppkopplade servern.

2. Installationsfilen måste köras med administratörsbehörighet på den Internetuppkopplade webbservern. Öppna en kommandotolk som administratör och navigera till den plats som installationsfilen kopierades till.

3. Kör Multi-Factor AuthenticationMobileAppWebServiceSetup-installationsfilen, ändra platsen om du vill och ändra namnet på den virtuella katalogen till ett kort namn som ”PA”.

  Vi rekommenderar att du använder ett kort namn för den virtuella katalogen eftersom användarna måste ange Mobile App-webbtjänstens URL på den mobila enheten under aktiveringen.

4. När installationen av Azure Multi-Factor AuthenticationMobileAppWebServiceSetup är klar bläddrar du till C:\inetpub\wwwroot\PA (eller relevant katalog beroende på namnet på den virtuella katalogen) och redigerar filen Web.config.

5. Leta upp nycklarna WEB_SERVICE_SDK_AUTHENTICATION_USERNAME och WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD. Ange värdena för användarnamn och lösenord till tjänstkontot som är medlem av säkerhetsgruppen PhoneFactor Admins. Detta kan vara samma konto som används som identitet för Azure Multi-Factor Authentication-användarportalen om den har installerats tidigare. Var noga med att ange användarnamnet och lösenordet inom citattecken i slutet av raden, (value=””/>). Använd ett kvalificerat användarnamn, t.ex. domän\användarnamn eller dator\användarnamn.  

6. Leta reda på inställningen pfMobile App Web Service_pfwssdk_PfWsSdk. Ändra värdet från *http://localhost:4898/PfWsSdk.asmx* till URL:en för webbtjänst-SDK som körs på Azure Multi-Factor Authentication-servern (t.ex. https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).

  Eftersom SSL används för den här anslutningen måste du referera till webbtjänst-SDK med servernamnet och inte IP-adressen. SSL-certifikatet skulle ha utfärdats för servernamnet och den URL som används måste matcha namnet på certifikatet. Servernamnet får inte att matcha till en IP-adress från den Internetuppkopplade servern. Om så är fallet lägger du till en post i värdfilen på den servern för att mappa namnet på Azure Multi-Factor Authentication-servern med dess IP-adress. Spara web.config-filen när ändringarna har gjorts.

7. Om webbplatsen som mobilappwebbtjänsten installerades under inte redan har knutits till ett offentligt signerat certifikat installerar du certifikatet på servern, öppnar IIS-hanteraren och knyter certifikatet till webbplatsen.

8. Öppna en webbläsare från en dator och gå till URL:en där mobilappswebbtjänsten installerades (t.ex. https://www.publicwebsite.com/PA). Se till att inga certifikatvarningar eller fel visas.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server
Nu när Mobile App-webbtjänsten är installerad måste du konfigurera Azure Multi-Factor Authentication Server så att den fungerar med portalen.

1. Klicka på ikonen Användarportal i Azure MFA-servern. Om användarna ska kunna styra sina autentiseringsmetoder markerar du **Mobilapp** under **Tillåt användare att välja metod** på fliken Inställningar. Om den här funktionen inte är aktiverad måste slutanvändarna kontakta din supportavdelning för att slutföra aktiveringen av mobilappen.
2. Markera kryssrutan **Tillåt användare att aktivera mobilapp**.
3. Markera kryssrutan **Tillåt användarregistrering**.
4. Klicka på ikonen för Mobilapp.
5. Ange URL:en som används med den virtuella katalog som skapades när Azure Multi-Factor AuthenticationMobileAppWebServiceSetup installerades. Du kan ange ett kontonamn i det angivna området. Det här företagsnamnet visas i mobilprogrammet. Om fältet lämnas tomt visas namnet på din Multi-Factor Auth-provider som skapades på den klassiska Azure-portalen.

<center>![Installation](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

