---
title: Azure MFA Server-mobilappwebbtjänst | Microsoft Docs
description: Microsoft Authenticator-appen erbjuder ytterligare ett autentiseringsalternativ utanför IP-nätverket.  Med det här alternativet kan MFA-servern skicka push-meddelanden till användarna.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 7ca5c7bcc82f0a77276f4f39a02d8abf2f47bc10
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
ms.locfileid: "31614143"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Aktivera mobilappautentisering och Azure Multi-Factor Authentication Server

Microsoft Authenticator-appen erbjuder ytterligare ett verifieringsalternativ utanför IP-nätverket. I stället för att ringa ett automatiserat telefonsamtal eller skicka ett SMS till användaren vid inloggningen skickar Azure Multi-Factor Authentication ett meddelande till Microsoft Authentication-appen på användarens smartphone eller surfplatta. Användaren trycker bara på **Autentisera** (eller anger en PIN-kod och trycker på ”Autentisera”) i appen för att slutföra deras inloggning.

Att använda en mobilapp för tvåstegsverifiering rekommenderas när mobilmottagningen är opålitlig. Om du använder appen som en OATH-tokengenerator behövs ingen nätverks- eller internetanslutning.

Beroende på din miljö kan du distribuera mobilappwebbtjänsten på samma server som Azure Multi-Factor Authentication-servern eller på en annan Internet-ansluten server.

Om du har installerat MFA Server version 8.0 eller senare behöver du inte utföra de flesta av stegen nedan. Mobilappsautentisering kan konfigureras med anvisningarna under [Konfigurera mobilappen](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Krav

För att du ska kunna använda Microsoft Authenticator-appen krävs följande så att appen kan kommunicera med mobilappwebbtjänsten:

* Azure Multi-Factor Authentication Server version 6.x eller senare
* Installera mobilappwebbtjänsten på en Internetuppkopplad webbserver som kör Microsoft® [Internet Information Services (IIS) IIS 7.x eller senare](http://www.iis.net/)
* ASP.NET v4.0.30319 har installerats, registrerats och är inställt på Tillåts
* Rolltjänsterna ASP.NET och IIS 6 Metabase-kompatibilitet krävs.
* Mobilappwebbtjänsten kan nås via en offentlig URL
* Mobilappwebbtjänsten skyddas av ett SSL-certifikat.
* Installera webbtjänst-SDK för Azure Multi-Factor Authentication på IIS 7.x eller senare på **samma server som Azure Multi-Factor Authentication-servern** är installerad på
* Webbtjänst-SDK för Azure Multi-Factor Authentication skyddas med ett SSL-certifikat.
* Mobilappwebbtjänsten kan ansluta till webbtjänst-SDK för Azure Multi-Factor Authentication via SSL
* Mobilappwebbtjänsten kan autentisera till webbtjänst-SDK för Azure MFA med autentiseringsuppgifterna för ett tjänstkonto som är medlem i säkerhetsgruppen ”PhoneFactor Admins”. Tjänstkontot och gruppen finns i Active Directory om Azure Multi-Factor Authentication-servern finns på en domänansluten server. Det här tjänstkontot och gruppen finns lokalt på Azure Multi-Factor Authentication-servern om den inte är ansluten till en domän.

## <a name="install-the-mobile-app-web-service"></a>Installera webbtjänsten Mobile App

Tänk på följande uppgifter innan du installerar mobilappwebbtjänsten:

* Mobilappwebbtjänsten behöver inte installeras för version 8.0 eller senare. Slutför bara stegen under [Konfigurera mobilappen](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).
* Du behöver ett tjänstkonto som är en del av gruppen "PhoneFactor Admins". Kontot kan vara detsamma som det du använde för att installera användarportalen.
* Det kan vara bra att öppna en webbläsare på den Internetuppkopplade webbservern och gå till URL:en för webbtjänst-SDK som har angetts i web.config-filen. Om webbläsaren kan ansluta till webbtjänsten bör du uppmanas att ange dina autentiseringsuppgifter. Ange användarnamnet och lösenordet som har angetts i web.config-filen exakt som de visas i filen. Se till att inga certifikatvarningar eller fel visas.
* Om det finns en omvänd proxy eller brandvägg framför webbservern för mobilappwebbtjänsten som utför SSL-avlastning kan du redigera filen web.config för mobilappwebbtjänsten så att mobilappwebbtjänsten kan använda http i stället för https. SSL krävs fortfarande från Mobile App till brandväggen/den omvända proxyn. Lägg till följande nyckel i avsnittet \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Installera webbtjänst-SDK

Om webbtjänst-SDK för Azure Multi-Factor Authentication **inte** redan är installerat på Azure Multi-Factor Authentication-servern (MFA) slutför du stegen nedan för båda scenarierna.

1. Öppna Multi-Factor Authentication-serverkonsolen.
2. Gå till **Webbtjänst-SDK** och välj **Installera Webbtjänst-SDK:n**.
3. Slutför installationen med hjälp av standardinställningarna om du inte behöver ändra dem av någon anledning.
4. Binda ett SSL-certifikat till webbplatsen i IIS.

Om du har frågor om att konfigurera ett SSL-certifikat på en IIS-server kan du läsa artikeln [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Konfigurera SSL på IIS).

Webbtjänst-SDK måste skyddas med ett SSL-certifikat. Ett självsignerat certifikat är lämpligt för detta ändamål. Importera certifikatet till arkivet ”Betrodda rotcertifikatutfärdare” för det lokala datorkontot på webbservern för användarportalen så att certifikatet blir betrott när SSL-anslutningen initieras.

![MFA-serverkonfiguration för webbtjänst-SDK](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Installera tjänsten

1. **På MFA-servern** bläddrar du till installationssökvägen.
2. Gå till mappen där Azure MFA-servern är installerad, vilket brukar vara **C:\Program\Azure Multi-Factor Authentication**.
3. Leta upp installationsfilen **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Om servern **inte** är Internet-ansluten kopierar du installationsfilen till den Internetuppkopplade servern.
4. Om MFA-servern **inte** är Internet-ansluten byter du till den **Internet-anslutna servern**.
5. Kör installationsfilen **MultiFactorAuthenticationMobileAppWebServiceSetup64** som administratör, ändra platsen om du vill och ändra namnet på den virtuella katalogen till ett kort namn om du så önskar.
6. När installationen har slutförts bläddrar du till **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (eller relevant katalog beroende på namnet på den virtuella katalogen) och redigerarWeb.Config-filen.

   * Leta rätt på nycklarna **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** och ändra **värde=""** till **värde="DOMAIN\User"** där DOMAIN\User är ett tjänstkonto som är en del av gruppen "PhoneFactor Admins".
   * Leta reda på nyckeln **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** och ändra **värde=""** till **värde="Password"** där Password är lösenordet för tjänstkontot som angavs på föregående rad.
   * Leta reda på inställningen **pfMobile App Web Service_pfwssdk_PfWsSdk** och ändra värdet från **http://localhost:4898/PfWsSdk.asmx** till URL:en för webbtjänst-SDK (till exempel: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Spara filen Web.Config och stäng Anteckningar.

   > [!NOTE]
   > Eftersom SSL används för den här anslutningen måste du referera till webbtjänst-SDK:n med **ett fullständigt kvalificerat domännamn (FQDN)** och **inte IP-adressen**. SSL-certifikatet skulle ha utfärdats för det fullständiga domännamnen och den URL som används måste matcha namnet på certifikatet.

7. Om webbplatsen som mobilappwebbtjänsten installerades under inte redan har knutits till ett offentligt signerat certifikat installerar du certifikatet på servern, öppnar IIS-hanteraren och knyter certifikatet till webbplatsen.
8. Öppna en webbläsare från en dator och gå till URL:en där mobilappwebbtjänsten installerades (till exempel https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Se till att inga certifikatvarningar eller fel visas.
9. Mer information om metoderna som är tillgängliga i SDK för webbtjänsterna finns i hjälpfilen för MFA-servern.
10. Nu när Mobile App-webbtjänsten är installerad måste du konfigurera Azure Multi-Factor Authentication Server så att den fungerar med portalen.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server

1. I Multi-Factor Authentication-servern klickar du på ikonen Användarportal. Om användarna ska kunna styra sina autentiseringsmetoder markerar du **Mobilapp** under **Tillåt användare att välja metod** på fliken Inställningar. Om den här funktionen inte är aktiverad måste slutanvändarna kontakta din supportavdelning för att slutföra aktiveringen av mobilappen.
2. Markera kryssrutan **Tillåt användare att aktivera mobilapp**.
3. Markera kryssrutan **Tillåt användarregistrering**.
4. Klicka på ikonen för **Mobilapp**.
5. Om du använder version 8.0 eller senare hoppar du över följande steg: Ange URL:en som används med den virtuella katalog som skapades när MultiFactorAuthenticationMobileAppWebServiceSetup64 installerades (till exempel https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) i fältet **URL för mobilappwebbtjänsten:**.
6. Fyll i fältet **Kontonamn** med företagets eller organisationens namn som ska visas i kontots mobilapp.
   ![MFA-serverkonfiguration för mobilappinställningar](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Nästa steg

- [Avancerade scenarier med Azure Multi-Factor Authentication och virtuella privata nätverk från tredje part](multi-factor-authentication-advanced-vpn-configurations.md).
