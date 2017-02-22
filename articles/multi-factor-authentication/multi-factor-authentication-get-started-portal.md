---
title: "Användarportal för Azure MFA Server | Microsoft Docs"
description: "Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och användarportalen."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Distribuera användarportalen för Azure Multi-Factor Authentication-servern
Användarportalen låter administratören installera och konfigurera Azure Multi-Factor Authentication-användarportalen. Användarportalen är en IIS-webbplats där användarna kan registrera sig för Azure Multi-Factor Authentication och hantera sina konton. En användare kan ändra sina telefonnummer, sin PIN-kod eller kringgå Azure Multi-Factor Authentication vid nästa inloggning.

Användarna loggar in på användarportalen med sitt vanliga användarnamn och lösenord och genomför antingen ett tvåstegsverifieringssamtal eller svarar på säkerhetsfrågor för att slutföra autentiseringen. Om användarregistrering tillåts, konfigurerar användarna sina telefonnummer och PIN-koder första gången de loggar in på användarportalen.

Administratörer av användarportalen kan konfigureras och beviljas behörighet att lägga till nya användare och uppdatera befintliga användare.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Distribuera användarportalen på samma server som Azure Multi-Factor Authentication-servern
Följande krav måste vara uppfyllda för att installera användarportalen på samma server som Azure Multi-Factor Authentication-servern:

* IIS, inklusive asp.net och IIS 6 metabase-kompatibilitet (för IIS 7 eller senare)
* Den inloggade användaren måste ha administratörsrättigheter för datorn och domänen om tillämpligt.  Detta beror på att kontot måste ha behörighet att skapa Active Directory-säkerhetsgrupper.

### <a name="to-deploy-the-user-portal"></a>Distribuera användarportalen
1. Inom Azure Multi-Factor Authentication-servern klickar du på ikonen **Användarportal** i vänster meny och klickar sedan på **Installera användarportal**.
2. Klicka på **Next**.
3. Klicka på **Next**.
4. Om datorn är domänansluten och Active Directory inte har konfigurerats att säkra kommunikationen mellan användarportalen och Azure Multi-Factor Authentication-tjänsten så visas Active Directory-steget. Klicka på **Nästa**-knappen för att slutföra konfigurationen automatiskt.
5. Klicka på **Next**.
6. Klicka på **Next**.
7. Klicka på **Stäng**.
8. Öppna en webbläsare från valfri dator och gå till URL:en där användarportalen installerades (t.ex. https://www.publicwebsite.com/MultiFactorAuth). Se till att inga certifikatvarningar eller fel visas.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Distribuera användarportalen för Azure Multi-Factor Authentication-servern på en separat server
Om du vill tillåta Microsoft Authenticator-appen att kommunicera med användarportalen, slutför du följande krav: 

* Du måste använda v6.0 eller senare för Azure Multi-Factor Authentication Server.
* Användarportalen måste vara installerad på en Internetriktad webbserver som kör Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x eller senare.
* Om du använder IIS 6.x, kontrollerar du att ASP.NET v2.0.50727 är installerat, registrerat och inställt på **Tillåten**.
* Rolltjänsterna ASP.NET och IIS 6 Metabase-kompatibilitet krävs om du använder IIS 7.x eller senare.
* Användarportalen bör skyddas med ett SSL-certifikat.
* Webbtjänst-SDK:n för Azure Multi-Factor Authentication måste vara installerat i IIS 6.x, IIS 7.x eller senare på den server där Azure Multi-Factor Authentication-server är installerad.
* Azure Multi-Factor Authentication webbtjänst-SDK måste skyddas med ett SSL-certifikat.
* Användarportalen måste kunna ansluta till webbtjänst-SDK:n för Azure Multi-Factor Authentication via SSL.
* Användarportalen måste kunna autentisera till webbtjänst-SDK:n för Azure Multi-Factor Authentication med autentiseringsuppgifterna för ett tjänstkonto som är medlem i säkerhetsgruppen ”PhoneFactor Admins”. Det här kontot och gruppen finns i Active Directory om Azure Multi-Factor Authentication-servern körs på en domänansluten server. Det här tjänstkontot och gruppen finns lokalt på Azure Multi-Factor Authentication-servern om den inte är ansluten till en domän.

Om du installerar användarportalen på en annan server än Azure Multi-Factor Authentication-servern följer du dessa tre steg:

1. Installera webbtjänst-SDK
2. Installera användarportalen
3. Konfigurera inställningarna för användarportalen i Azure Multi-Factor Authentication Server

### <a name="step-1-install-the-web-service-sdk"></a>Steg 1: Installera webbtjänst-SDK:n
Om Azure Multi-Factor Authentication webbtjänst-SDK inte redan är installerat på Azure Multi-Factor Authentication-servern går du till den servern och öppnar Azure Multi-Factor Authentication-servern. Klicka på ikonen **Webbtjänst-SDK** och därefter **Installera Webbtjänst-SDK:n**. Följ instruktionerna som visas. 

Webbtjänst-SDK måste skyddas med ett SSL-certifikat. Ett självsignerat certifikat kan användas för det här ändamålet, men det måste importeras till arkivet ”Betrodda rotcertifikatutfärdare” för det lokala datorkontot på servern. Nu kan webbtjänst-SDK:n lita på certifikatet när den initierar SSL-anslutningen.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>Steg 2: Installera användarportalen
Tänk på följande metodtips innan du installerar användarportalen på en separat server:

* Det kan vara bra att öppna en webbläsare på den Internetuppkopplade webbservern och gå till URL:en för webbtjänst-SDK som har angetts i web.config-filen. Om webbläsaren kan ansluta till webbtjänsten bör du uppmanas att ange dina autentiseringsuppgifter. Ange användarnamnet och lösenordet som har angetts i web.config-filen exakt som de visas i filen. Se till att inga certifikatvarningar eller fel visas.
* Om en omvänd proxy eller brandvägg finns framför webbservern för användarportalen och utför SSL-avlastning, kan du redigera användarportalens web.config-fil och lägga till följande nyckel i `<appSettings>`-avsnittet så att användarportalen kan använda http istället för https:

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>Så här installerar du användarportalen
1. Öppna Utforskaren på Azure Multi-Factor Authentication-servern och navigera till mappen där Azure Multi-Factor Authentication Server är installerat (t.ex. C:\Program\Microsoft Files\Multi-Factor Authentication Server). Välj 32- eller 64-bitarsversionen av MultiFactorAuthenticationUserPortalSetup-installationsfilen beroende på vilken server som användarportalen ska installeras på. Kopiera installationsfilen till den Internetuppkopplade servern.
2. Installationsfilen måste köras med administratörsbehörighet på den Internetuppkopplade webbservern. Det enklaste sättet att göra detta är att öppna en kommandotolk som administratör och navigera till den plats som installationsfilen kopierades till.
3. Kör MultiFactorAuthenticationUserPortalSetup64-installationsfilen och ändra namnet på platsen och den virtuella katalogen om du vill.
4. När installationen av användarportalen har slutförts bläddrar du till C:\inetpub\wwwroot\MultiFactorAuth (eller relevant katalog beroende på namnet på den virtuella katalogen) och redigerar web.config-filen.
5. Leta upp nyckeln USE_WEB_SERVICE_SDK och ändra värdet från false till true. Leta upp nycklarna WEB_SERVICE_SDK_AUTHENTICATION_USERNAME och WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD och ange värdena till användarnamnet och lösenordet för tjänstkontot som är medlem i säkerhetsgruppen PhoneFactor Admins (se avsnittet förutsättningar ovan). Var noga med att ange användarnamnet och lösenordet inom citattecken i slutet av raden, (value=””/>). Vi rekommenderar att du använder ett kvalificerat användarnamn (t.ex. domän\användarnamn eller dator\användarnamn)
6. Leta upp inställningen pfup_pfwssdk_PfWsSdk och ändra värdet från ”http://localhost:4898/PfWsSdk.asmx” till URL:en för webbtjänst-SDK som körs på Azure Multi-Factor Authentication-servern (t.ex. https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Eftersom SSL används för den här anslutningen, refererar du till webbtjänst-SDK:n med servernamn, inte IP-adress, eftersom SSL-certifikatet har utfärdats för servernamnet. Om servernamnet inte matchas till en IP-adress från den Internetuppkopplade servern lägger du till en post i värdfilen på den servern för att mappa namnet på Azure Multi-Factor Authentication-servern med dess IP-adress. Spara web.config-filen när ändringarna har gjorts.

    Gå till [Skydda dina resurser med Azure Multi-Factor Authentication-server med AD FS](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file) för mer information om hur du redigerar konfigurationsfilen.

7. Om webbplatsen som användarportalen installerades under (t.ex. Standardwebbplats) inte redan har bundits till ett offentligt signerat certifikat, installerar du certifikatet på servern, öppnar IIS-hanteraren och binder certifikatet till webbplatsen.
8. Öppna en webbläsare från valfri dator och gå till URL:en där användarportalen installerades (t.ex. https://www.publicwebsite.com/MultiFactorAuth). Se till att inga certifikatvarningar eller fel visas.

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Steg 3: Konfigurera inställningarna för användarportalen på Azure Multi-Factor Authentication-servern
Nu när användarportalen har installerats måste du konfigurera Azure Multi-Factor Authentication-servern så att den fungerar med portalen.

Azure Multi-Factor Authentication-servern tillhandahåller flera alternativ för användarportalen.  Följande tabell innehåller en lista över dessa alternativ och en förklaring av hur de används.

| Inställningar för användarportalen | Beskrivning |
|:--- |:--- |
| URL till användarportalen | Ange URL:en till den plats där portalen finns. |
| Primär autentisering | Ange vilken typ av autentisering som ska användas för inloggning på portalen.  Du kan välja mellan Windows-, RADIUS- eller LDAP-autentisering. |
| Tillåt användare att logga in | Låt användare ange ett användarnamn och lösenord på inloggningssidan för användarportalen.  Om den här inställningen inte är vald är rutorna nedtonade. |
| Tillåt användarregistrering | Låt en användare registrera sig i Multi-Factor Authentication genom att ta dem till en installationsskärm där de uppmanas att ange ytterligare information, till exempel telefonnummer. Inställningen Fråga efter reservtelefonnummer gör att användaren kan ange ett sekundärt telefonnummer. Fråga efter tredje parts OATH-token gör att användaren kan ange en OATH-token från en tredje part. |
| Tillåt användare att starta en engångsförbikoppling | Tillåt användare att starta en engångsförbikoppling.  Om en användare väljer att göra detta börjar inställningen gälla nästa gång användaren loggar in. Inställningen Fråga efter antal förbikopplingssekunder visar en ruta där användaren kan ändra standardinställningen på 300 sekunder. Annars är engångsförbikopplingen bara giltig i 300 sekunder. |
| Tillåt användare att välja metod | Låt användare ange sin primära kontaktmetod.  Detta kan vara ett telefonsamtal, ett textmeddelande, en mobilapp eller en OATH-token. |
| Tillåt användare att välja språk | Låt användare ändra språket som används för telefonsamtalet, textmeddelandet, mobilappen eller OATH-token. |
| Tillåt användare att aktivera mobilapp | Låt användare generera en aktiveringskod för att slutföra aktiveringen för mobilappar som används med servern.  Du kan också ange antalet enheter som de kan aktivera appen på, mellan 1 och 10. |
| Använd säkerhetsfrågor som reserv | Tillåt säkerhetsfrågor om tvåstegsverifiering misslyckas.  Du kan ange antalet säkerhetsfrågor som måste besvaras korrekt. |
| Tillåt användare att koppla en utomstående OATH-token | Låt användare ange en OATH-token från en tredje part. |
| Använd OATH-token som reserv | Tillåt användning av en OATH-token om tvåstegsverifiering misslyckas. Du kan också ange tidsgränsen för sessionen i antal minuter. |
| Aktivera loggning | Aktivera loggning på användarportalen. Loggfilerna finns på: C:\Program Files\Multi-Factor Authentication Server\Logs. |

De flesta av de här inställningarna är synliga för användaren när de är aktiverade och användaren loggar in på användarportalen.

![Inställningar för användarportalen](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Så här konfigurerar du inställningarna för användarportalen i Azure Multi-Factor Authentication Server
1. I Azure Multi-Factor Authentication-server klickar du på ikonen**Användarportal**. På inställningar-fliken, anger du URL:en för användarportalen i textrutan **Användarportal-URL**. Den här URL:en infogas i e-postmeddelanden som skickas till användare när de importeras till Azure Multi-Factor Authentication-servern om e-postfunktionen har aktiverats.
2. Välj de inställningar som du vill använda på användarportalen. Om användarna till exempel tillåts styra sin autentiseringsmetod, kontrollera att **Tillåt användare välja metod** har markerats, med de metoder som de får välja mellan.
3. Klicka på **Hjälp**-länken i övre högra hörnet för att få hjälp att förstå inställningarna som visas.

<center>![Installation](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Fliken Administratörer
På den här fliken kan du lägga till användare som har administratörsbehörighet.  När du lägger till en administratör kan du finjustera de behörigheter som de ska beviljas. Klicka på knappen **Lägg till**, välj en användare och deras behörigheter och klicka sedan på **Lägg till**.

![Administratörer av användarportalen](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Säkerhetsfrågor
Den här fliken låter dig ange de säkerhetsfrågor som användare måste svara på om alternativet **Använd säkerhetsfrågor som reserv** har valts.  Azure Multi-Factor Authentication-server har ett antal standardfrågor som du kan använda. Du kan också ändra ordningen eller lägga till egna frågor.  Om du lägger till egna frågor kan du även ange på vilket språk frågorna ska visas.

![Säkerhetsfrågor för användarportalen](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
Använd den här fliken för att konfigurera användarportalen så att den accepterar anspråk från en identitetsprovider som använder SAML.  Du kan ange tidsgränser för sessionen, verifieringscertifikatet och omdirigerings-URL:en vid utloggning.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser
Den här fliken låter dig ange antingen enskilda IP-adresser eller IP-adressintervall som kan läggas till så att användare som loggar in därifrån kringgår tvåstegsverifiering.

![Tillförlitliga IP-adresser för användarportalen](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Användarregistrering via självbetjäning
Om du vill att dina användare loggar in och registrerar sig, måste du välja alternativen **Tillåt användare att logga in** och **Tillåt användarregistrering** under fliken inställningar. Kom ihåg att de inställningar som du väljer påverkar användarens inloggningsupplevelse.

När en användare till exempel loggar in på användarportalen för första gången, tas de sedan till inställningssidan för Azure Multi-Factor Authentication.  Beroende på hur du har konfigurerat Azure Multi-Factor Authentication kan användaren välja autentiseringsmetod.  

Om användaren väljer autentiseringsmetoden Röstsamtal eller om användaren har förkonfigurerats att använda den metoden så uppmanas han eller hon att ange sitt primära telefonnummer och sin anknytning om en sådan krävs.  Användaren kanske också kan ange ett reservtelefonnummer.  

![Tillförlitliga IP-adresser för användarportalen](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att skapa en PIN-kod.  Efter att ha angett sitt telefonnummer och PIN-kod (om en sådan krävs), klickar användaren på knappen **Ring mig nu för att autentisera**.  Azure Multi-Factor Authentication utför en autentisering via ett telefonsamtal till användarens primära telefonnummer.  Användaren måste besvara samtalet och ange sin PIN-kod (om en sådan krävs) och trycka på # för att gå vidare till nästa steg i självregistreringsprocessen.   

Om användaren väljer autentiseringsmetoden för SMS-meddelanden eller om användaren har förkonfigurerats att använda den metoden uppmanas han eller hon att ange sitt mobiltelefonnummer.  Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod.  Efter att de angett sitt telefonnummer och PIN-kod (om en sådan krävs), klickar användaren på knappen **Skicka mig ett textmeddelande för att autentisera**.  Azure Multi-Factor Authentication utför en SMS-autentisering till användarens mobiltelefon.  Användaren får SMS:et med ett engångslösenord (OTP) och svarar på meddelandet med OTP:t och sin PIN-kod (om sådan krävs).

![SMS på användarportalen](./media/multi-factor-authentication-get-started-portal/text.png)   

Om användaren väljer autentiseringsmetoden Mobilapp eller om användaren har förkonfigurerats att använda den metoden uppmanas han eller hon att installera Microsoft Authenticator-appen på sin enhet och att generera en aktiveringskod.  Efter installation, klickar användaren på knappen Generera aktiveringskod.    

> [!NOTE]
> Användaren måste aktivera push-meddelanden för sin enhet för att kunna använda Microsoft Authenticator-appen.

Nu visas en aktiveringskod, en URL och en streckkodsbild på sidan.  Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod.  Användaren anger aktiveringskoden och URL:en i Microsoft Authenticator-appen eller använder streckkodsläsaren för att skanna streckkodsbilden och klickar sedan på knappen Aktivera.    

När aktiveringen slutförts, klickar användaren på knappen **Autentisera mig nu**.  Azure Multi-Factor Authentication utför en verifiering till användarens mobilapp.  Användaren måste ange sin PIN-kod (om en sådan krävs) och trycka på knappen Autentisera i mobilappen för att gå vidare till nästa steg i självregistreringsprocessen.  

Om administratörerna har konfigurerat Azure Multi-Factor Authentication Server att kräva svar på säkerhetsfrågor visas sidan Säkerhetsfrågor.  Användaren måste välja fyra säkerhetsfrågor och ge svar på de frågor som han eller hon har valt.    

![Säkerhetsfrågor för användarportalen](./media/multi-factor-authentication-get-started-portal/secq.png)  

Användarens självregistreringen är nu klar och användaren loggas in på användarportalen.  Användare kan logga in på användarportalen igen när som helst för att ändra sina telefonnummer, PIN-koder, autentiseringsmetoder och säkerhetsfrågor om detta tillåts av administratören.




<!--HONumber=Feb17_HO3-->


