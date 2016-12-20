---
title: "Distribuera användarportalen för Azure Multi-Factor Authentication Server"
description: "Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och användarportalen."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: cdbf4b164af4a8077b8f9a166d6c4ad752b25ff4


---
# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Distribuera användarportalen för Azure Multi-Factor Authentication Server
På användarportalen kan administratören installera och konfigurera Azure Multi-Factor Authentication-användarportalen. Användarportalen är en IIS-webbplats där användarna kan registrera sig i Azure Multi-Factor Authentication och hantera sina konton. Användarna kan ändra sina telefonnummer, ändra sina PIN-koder eller kringgå Azure Multi-Factor Authentication vid nästa inloggning.

Användarna kan logga in på användarportalen med sina vanliga användarnamn och lösenord och genomför antingen ett Azure Multi-Factor Authentication-samtal eller svarar på säkerhetsfrågor för att slutföra autentiseringen. Om användarregistrering tillåts konfigurerar användarna sina telefonnummer och sina PIN-koder första gången de loggar in på användarportalen.

Administratörer av användarportalen kan konfigureras och beviljas behörighet att lägga till nya användare och uppdatera befintliga användare.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Distribuera användarportalen på samma server som Azure Multi-Factor Authentication Server
Följande krav måste vara uppfyllda för att du ska kunna installera användarportalen på samma server som Azure Multi-Factor Authentication-servern:

* IIS måste vara installerat inklusive asp.net och IIS 6 Metabase-kompatibilitet (för IIS 7 eller senare)
* Den inloggade användaren måste ha administratörsrättigheter för datorn och domänen om tillämpligt.  Detta beror på att kontot måste ha behörighet att skapa Active Directory-säkerhetsgrupper.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Så här distribuerar du användarportalen för Azure Multi-Factor Authentication Server
1. På Azure Multi-Factor Authentication-servern: Klicka på ikonen för användarportalen på den vänstra menyn och klicka på knappen Installera användarportal.
2. Klicka på Nästa.
3. Klicka på Nästa.
4. Om datorn är ansluten till en domän och Active Directory-konfigurationen för att skydda kommunikationen mellan användarportalen och Azure Multi-Factor Authentication-tjänsten är ofullständig, visas Active Directory-steget. Klicka på Nästa för att slutföra konfigurationen automatiskt.
5. Klicka på Nästa.
6. Klicka på Nästa.
7. Klicka på Stäng.
8. Öppna en webbläsare från valfri dator och gå till URL:en där användarportalen installerades (t.ex. https://www.publicwebsite.com/MultiFactorAuth). Se till att inga certifikatvarningar eller fel visas.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Distribuera Azure Multi-Factor Authentication Server-användarportalen på en separat server
För att du ska kunna använda Azure Multi-Factor Authentication-appen krävs följande så att appen kan kommunicera med användarportalen:

Se avsnittet Maskin- och programvarukrav för maskin- och programvarukrav:

* Du måste använda v6.0 eller senare för Azure Multi-Factor Authentication Server.
* Användarportalen måste vara installerad på en Internetuppkopplad webbserver som kör Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x eller senare.
* Om du använder IIS 6.x kontrollerar du att ASP.NET v2.0.50727 är installerat, registrerat och inställt på Tillåts.
* Rolltjänsterna ASP.NET och IIS 6 Metabase-kompatibilitet krävs om du använder IIS 7.x eller senare.
* Användarportalen måste skyddas med ett SSL-certifikat.
* Azure Multi-Factor Authentication webbtjänst-SDK måste vara installerat i IIS 6.x, IIS 7.x eller senare på den server som Azure Multi-Factor Authentication-servern är installerad på.
* Azure Multi-Factor Authentication webbtjänst-SDK måste skyddas med ett SSL-certifikat.
* Användarportalen måste kunna ansluta till Azure Multi-Factor Authentication webbtjänst-SDK via SSL.
* Användarportalen måste kunna autentisera till Azure Multi-Factor Authentication webbtjänst-SDK med autentiseringsuppgifterna för ett tjänstkonto som är medlem i säkerhetsgruppen ”PhoneFactor Admins”. Det här kontot och gruppen finns i Active Directory om Azure Multi-Factor Authentication-servern körs på en domänansluten server. Det här tjänstkontot och gruppen finns lokalt på Azure Multi-Factor Authentication-servern om den inte är ansluten till en domän.

Om du installerar användarportalen på en annan server än Azure Multi-Factor Authentication-servern följer du dessa tre steg:

1. Installera webbtjänst-SDK
2. Installera användarportalen
3. Konfigurera inställningarna för användarportalen i Azure Multi-Factor Authentication Server

### <a name="install-the-web-service-sdk"></a>Installera webbtjänst-SDK
Om Azure Multi-Factor Authentication webbtjänst-SDK inte redan är installerat på Azure Multi-Factor Authentication-servern går du till den servern och öppnar Azure Multi-Factor Authentication-servern. Klicka på ikonen för webbtjänst-SDK, klicka på Installera webbtjänst-SDK och följ instruktionerna. Webbtjänst-SDK måste skyddas med ett SSL-certifikat. Ett självsignerat certifikat kan användas för detta ändamål, men det måste importeras till arkivet ”Betrodda rotcertifikatutfärdare” för det lokala datorkontot på webbservern för användarportalen så att det litar på certifikatet när SSL-anslutningen initieras.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Installera användarportalen
Tänk på följande innan du installerar användarportalen på en separat server:

* Det kan vara bra att öppna en webbläsare på den Internetuppkopplade webbservern och gå till URL:en för webbtjänst-SDK som har angetts i web.config-filen. Om webbläsaren kan ansluta till webbtjänsten bör du uppmanas att ange dina autentiseringsuppgifter. Ange användarnamnet och lösenordet som har angetts i web.config-filen exakt som de visas i filen. Se till att inga certifikatvarningar eller fel visas.
* Om en omvänd proxy eller brandvägg finns framför webbservern för användarportalen och utför SSL-avlastning kan du redigera filen web.config för användarportalen och lägga till följande nyckel i avsnittet <appSettings> så att användarportalen kan använda http i stället för https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Så här installerar du användarportalen
1. Öppna Utforskaren på Azure Multi-Factor Authentication-servern och navigera till mappen där Azure Multi-Factor Authentication Server är installerat (t.ex. C:\Program\Microsoft Files\Multi-Factor Authentication Server). Välj 32- eller 64-bitarsversionen av MultiFactorAuthenticationUserPortalSetup-installationsfilen beroende på vilken server som användarportalen ska installeras på. Kopiera installationsfilen till den Internetuppkopplade servern.
2. Installationsfilen måste köras med administratörsbehörighet på den Internetuppkopplade webbservern. Det enklaste sättet att göra detta är att öppna en kommandotolk som administratör och navigera till den plats som installationsfilen kopierades till.
3. Kör MultiFactorAuthenticationUserPortalSetup64-installationsfilen och ändra namnet på platsen och den virtuella katalogen om du vill.
4. När installationen av användarportalen har slutförts bläddrar du till C:\inetpub\wwwroot\MultiFactorAuth (eller relevant katalog beroende på namnet på den virtuella katalogen) och redigerar web.config-filen.
5. Leta upp nyckeln USE_WEB_SERVICE_SDK och ändra värdet från false till true. Leta upp nycklarna WEB_SERVICE_SDK_AUTHENTICATION_USERNAME och WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD och ange värdena till användarnamnet och lösenordet för tjänstkontot som är medlem i säkerhetsgruppen PhoneFactor Admins (se avsnittet Krav ovan). Var noga med att ange användarnamnet och lösenordet inom citattecken i slutet av raden, (value=””/>). Vi rekommenderar att du använder ett kvalificerat användarnamn (t.ex. domän\användarnamn eller dator\användarnamn)
6. Leta upp inställningen pfup_pfwssdk_PfWsSdk och ändra värdet från ”http://localhost:4898/PfWsSdk.asmx” till URL:en för webbtjänst-SDK som körs på Azure Multi-Factor Authentication-servern (t.ex. https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Eftersom SSL används för den här anslutningen måste du referera till webbtjänst-SDK med servernamnet och inte IP-adressen eftersom SSL-certifikatet är utfärdat för servernamnet och den URL som används måste matcha namnet på certifikatet. Om servernamnet inte matchas till en IP-adress från den Internetuppkopplade servern lägger du till en post i värdfilen på den servern för att mappa namnet på Azure Multi-Factor Authentication-servern med dess IP-adress. Spara web.config-filen när ändringarna har gjorts.
7. Om webbplatsen som användarportalen installerades under (t.ex. Standardwebbplats) inte redan har bundits till ett offentligt signerat certifikat installerar du certifikatet på servern om det inte redan har installerats, öppnar IIS-hanteraren och binder certifikatet till webbplatsen.
8. Öppna en webbläsare från valfri dator och gå till URL:en där användarportalen installerades (t.ex. https://www.publicwebsite.com/MultiFactorAuth). Se till att inga certifikatvarningar eller fel visas.

## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera inställningarna för användarportalen i Azure Multi-Factor Authentication Server
Nu när portalen har installerats måste du konfigurera Azure Multi-Factor Authentication-server så att den fungerar med portalen.

Azure Multi-Factor Authentication-servern tillhandahåller flera alternativ för användarportalen.  Följande tabell innehåller en lista över dessa alternativ och en förklaring av hur de används.

| Inställningar för användarportalen | Beskrivning |
|:--- |:--- |
| URL till användarportalen |Gör att du kan ange URL:en till den plats där portalen finns. |
| Primär autentisering |Gör att du kan ange vilken typ av autentisering som ska användas för inloggning på portalen.  Du kan välja mellan Windows-, RADIUS- eller LDAP-autentisering. |
| Tillåt användare att logga in |Gör att användaren kan ange ett användarnamn och lösenord på inloggningssidan för användarportalen.  Om den här inställningen inte är vald är rutorna nedtonade. |
| Tillåt användarregistrering |Gör att användaren kan registrera sig i Multi-Factor Authentication. En installationsskärm visas där användaren uppmanas att ange ytterligare information, till exempel telefonnummer.  Inställningen Fråga efter reservtelefonnummer gör att användaren kan ange ett sekundärt telefonnummer.  Inställningen Fråga efter utomstående OATH-token gör att användaren kan ange en OATH-token från en tredje part. |
| Tillåt användare att starta en engångsförbikoppling |Gör att användaren kan initiera en engångsförbikoppling.  Om en användare väljer att göra detta börjar inställningen gälla nästa gång användaren loggar in.  Inställningen Fråga efter antal förbikopplingssekunder visar en ruta där användaren kan ändra standardinställningen på 300 sekunder.  Annars är engångsförbikopplingen bara giltig i 300 sekunder. |
| Tillåt användare att välja metod |Gör att användaren kan ange sin primära kontaktmetod.  Detta kan vara ett telefonsamtal, ett textmeddelande, en mobilapp eller en OATH-token. |
| Tillåt användare att välja språk |Gör att användaren kan ändra språket som används för telefonsamtalet, textmeddelandet, mobilappen eller OATH-token. |
| Tillåt användare att aktivera mobilapp |Gör att användare kan generera en aktiveringskod för att slutföra aktiveringen för mobilappar som används med servern.  Du kan också ange antalet enheter som de kan aktivera detta på.  Mellan 1 och 10. |
| Använd säkerhetsfrågor som reserv |Gör att du kan använda säkerhetsfrågor om multifaktorautentiseringen misslyckas.  Du kan ange antalet säkerhetsfrågor som måste besvaras korrekt. |
| Tillåt användare att koppla en utomstående OATH-token |Gör att användaren kan ange en OATH-token från en tredje part. |
| Använd OATH-token som reserv |Gör att användaren kan använda en OATH-token om multifaktorautentiseringen inte lyckas.  Du kan också ange tidsgränsen för sessionen i antal minuter. |
| Aktivera loggning |Aktiverar loggning på användarportalen.  Loggfilerna finns på: C:\Program Files\Multi-Factor Authentication Server\Logs. |

De flesta av dessa inställningar är synliga för användaren när de är aktiverade och användaren loggar in på användarportalen.

![Inställningar för användarportalen](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Så här konfigurerar du inställningarna för användarportalen i Azure Multi-Factor Authentication Server
1. Klicka på ikonen för användarportalen i Azure Multi-Factor Authentication Server. På fliken Inställningar anger du URL:en för användarportalen i textrutan URL till användarportalen. Den här URL:en infogas i e-postmeddelanden som skickas till användare när de importeras till Azure Multi-Factor Authentication-servern om e-postfunktionen har aktiverats.
2. Välj de inställningar som du vill använda på användarportalen. Om användarna till exempel kan styra sina autentiseringsmetoder kontrollera du att Tillåt användare att välja metod är markerat samt de metoder som de kan välja mellan.
3. Klicka på länken Hjälp längst upp till höger om du vill visa hjälp om de tillgängliga inställningarna.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Fliken Administratörer
På den här fliken kan du lägga till användare som har administratörsbehörighet.  När du lägger till en administratör kan du finjustera de behörigheter som de ska beviljas.  På så sätt kan du vara säker på att administratörerna endast beviljas de behörigheter som de behöver.  Klicka bara på knappen Lägg till, välj en användare och dennes behörigheter och klicka sedan på Lägg till.

![Administratörer av användarportalen](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Säkerhetsfrågor
På den här fliken kan du ange säkerhetsfrågorna som användarna måste besvara om alternativet Använd säkerhetsfrågor som reserv har valts.  Azure Multi-Factor Authenticaton Server har ett antal standardfrågor som du kan använda.  Du kan också ändra ordningen eller lägga till egna frågor.  Om du lägger till egna frågor kan du även ange på vilket språk frågorna ska visas.

![Säkerhetsfrågor för användarportalen](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="passed-sessions"></a>Slutförda sessioner
## <a name="saml"></a>SAML
Gör att du kan konfigurera användarportalen så att den accepterar anspråk från en identitetsprovider med hjälp av SAML.  Du kan ange tidsgränser för sessioner, ange verifieringscertifikatet och omdirigerings-URL:en för utloggning.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser
På den här fliken kan du ange antingen enstaka IP-adresser eller IP-adressintervall som kan läggas till så att multifaktorautentiseringen kringgås om en användare loggar in från någon av dessa IP-adresser.

![Tillförlitliga IP-adresser för användarportalen](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Användarregistrering via självbetjäning
Om du vill att användarna ska logga in och registrera sig måste du markera alternativen Tillåt användare att logga in och Tillåt användarregistrering. Kom ihåg att de inställningar som du väljer påverkar användarens inloggningsupplevelse.

Om användaren till exempel loggar in på användarportalen och klickar på knappen Logga in visas sidan för Azure Multi-Factor Authentication-användarinställningar.  Beroende på hur du har konfigurerat Azure Multi-Factor Authentication kan användaren välja autentiseringsmetod.  

Om användaren väljer autentiseringsmetoden Röstsamtal eller om användaren har förkonfigurerats att använda den metoden så uppmanas han eller hon att ange sitt primära telefonnummer och sin  anknytning om en sådan krävs.  Användaren kanske också kan ange ett reservtelefonnummer.  

![Tillförlitliga IP-adresser för användarportalen](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod.  När användaren har angett sitt telefonnummer och sin PIN-kod (om en sådan krävs) klickar han eller hon på Ring mig nu så att jag kan autentisera mig.  Azure Multi-Factor Authentication utför en autentisering via ett telefonsamtal till användarens primära telefonnummer.  Användaren måste besvara samtalet och ange sin PIN-kod (om en sådan krävs) och trycka på # för att gå vidare till nästa steg i självregistreringsprocessen.   

Om användaren väljer autentiseringsmetoden för SMS-meddelanden eller om användaren har förkonfigurerats att använda den metoden uppmanas han eller hon att ange sitt mobiltelefonnummer.  Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod.  När användaren har angett sitt telefonnummer och sin PIN-kod (om en sådan krävs) klickar han eller hon på Skicka ett textmeddelande till mig nu så att jag kan autentisera mig.  Azure Multi-Factor Authentication utför en SMS-autentisering till användarens mobiltelefon.  Användaren måste ta emot SMS-meddelandet som innehåller ett engångslösenord och svara på meddelandet med detta lösenord plus hans eller hennes PIN-kod (om en sådan krävs) för att gå vidare till nästa steg i självregistreringsprocessen.

![SMS på användarportalen](./media/multi-factor-authentication-get-started-portal/text.png)   

Om användaren väljer autentiseringsmetoden Mobilapp eller om användaren har förkonfigurerats att använda den metoden uppmanas han eller hon att installera Azure Multi-Factor Authentication-appen på sin enhet och att generera en aktiveringskod.  När användaren har installerat Azure Multi-Factor Authentication-appen klickar han eller hon på knappen Generera aktiveringskod.    

> [!NOTE]
> Användaren måste aktivera push-meddelanden för sin enhet för att kunna använda Azure Multi-Factor Authentication-appen.
> 
> 

Nu visas en aktiveringskod, en URL och en streckkodsbild på sidan.  Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod.  Användaren anger aktiveringskoden och URL:en i Azure Multi-Factor Authentication-appen eller använder streckkodsläsaren för att skanna streckkodsbilden och klickar sedan på knappen Aktivera.    

När aktiveringen är klar klickar användaren på knappen Autentisera mig nu.  Azure Multi-Factor Authentication utför en autentisering till användarens mobilapp.  Användaren måste ange sin PIN-kod (om en sådan krävs) och trycka på knappen Autentisera i mobilappen för att gå vidare till nästa steg i självregistreringsprocessen.  

Om administratörerna har konfigurerat Azure Multi-Factor Authentication Server att kräva svar på säkerhetsfrågor visas sidan Säkerhetsfrågor.  Användaren måste välja fyra säkerhetsfrågor och ge svar på de frågor som han eller hon har valt.    

![Säkerhetsfrågor för användarportalen](./media/multi-factor-authentication-get-started-portal/secq.png)  

Nu är självregistreringen klar och användaren loggas in på användarportalen.  Användare kan logga in på användarportalen igen när som helst för att ändra sina telefonnummer, PIN-koder, autentiseringsmetoder och säkerhetsfrågor om detta tillåts av administratören.




<!--HONumber=Dec16_HO1-->


