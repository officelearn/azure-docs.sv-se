---
title: "Migrera AD FS på lokala appar till Azure. | Microsoft Docs"
description: "Det här dokumentet är avsett att hjälpa företag att förstå hur de ska migrera lokala program till Azure AD, med fokus på externa SaaS-program."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/29/2018
ms.author: billmath
ms.openlocfilehash: ec0731534da2543d48bedc575bf882b790fa136b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migrera AD FS på lokala appar till Azure 

Det här dokumentet är avsett att hjälpa företag att förstå hur de ska migrera lokala program till Azure AD.  Det har fokus på externa SaaS-program.  I det här dokumentet finns det ingen stegvis vägledning.  Det ger konceptuell vägledning för att hjälpa dig att genomföra migreringen genom att förstå hur lokala konfigurationer översätts till Azure AD. Det tar även upp vad de vanligaste scenarierna kräver.

## <a name="introduction"></a>Introduktion

Om du har en lokal katalog som innehåller användarkonton har du kanske minst en eller två appar.  Och apparna är konfigurerade för användare som ska få åtkomst via inloggning med dessa identiteter.

Och om din organisation är som många andra håller ni förmodligen på att anpassa er till molnprogram och -identiteter.  Ni kanske har kommit igång med Office 365 och Azure AD Connect.  Ni kanske har konfigurerat molnbaserade SaaS-program för några viktiga arbetsbelastningar, men inte alla.  

Många organisationer har SaaS-appar eller anpassade LOB-appar (verksamhetsspecifika appar) federerade direkt till en lokal inloggningstjänst som Active Directory Federation Service (AD FS) tillsammans med Office 365- och Azure AD-baserade appar.  Den här migreringsvägledningen beskriver varför och hur du migrerar lokala program till Azure AD.

>[!NOTE]
>Den här guiden innehåller detaljerad information om SaaS-appkonfiguration och -migrering med översiktlig information om anpassade LoB-appar.  Mer detaljerad vägledning för anpassade LoB-appar planeras i framtiden.

Bild 1: Appar anslutna direkt lokalt ![lokalt](media/migrate-adfs-apps-to-azure/migrate1.png)

Bild 2: Federerade appar via Azure AD ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>Varför ska man migrera appar till Azure AD?

För organisationer som redan använder AD FS, Ping eller någon annan lokal autentiseringsprovider möjliggör migrering till Azure AD följande fördelar:

**Säkrare åtkomst**
- Konfigurera detaljerade programspecifika åtkomstkontroller, däribland Multi-Factor Authentication (MFA), med [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).  Principerna kan tillämpas på SaaS- och anpassade appar på samma sätt som du kanske gör idag för Office 365
- För att upptäcka hot och skydda inloggning som baseras på Machine Learning och heuristik som identifierar riskfylld trafik bör du dra nytta av de inbyggda och ständigt utvecklade funktionerna i Azure AD med [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

**Azure AD B2B-samarbete**
- När inloggningen till SaaS-appar baseras på Azure AD kan du ge partner åtkomst till molnresurser med [Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Enklare administration och fler funktioner i Azure AD**
- Azure AD som är en IdP för SaaS-appar stöder ytterligare funktioner som certifikat för tokensignering per program, [konfigurerbara förfallodatum för certifikat](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs) och [automatiserad etablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) av användarkonton (viktiga Gallery-appar) baserat på Azure AD-identiteter

**Behåll fördelarna med en lokal identitetsprovider**
- Samtidigt som du får fördelarna med Azure AD kan du fortsätta att använda din lokala lösning för autentisering, så att fördelar som lokala MFA-lösningar, loggning och granskning finns kvar 

**Gör en rivstart när du tar den lokala identitetsprovidern ur drift**
- För organisationer som vill ta sin lokala autentiseringsprodukt ur drift blir övergången enkel om de tar en del av arbetet ur drift via migrering av appar till Azure AD 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mappa typer av appar lokalt till typer av appar i Azure AD
De flesta appar hamnar under någon av kategorierna som baseras på typ av inloggning.  Kategorierna bestämmer hur appen representeras i Azure AD.

SAML 2.0-program kan kort sagt integreras med Azure AD via antingen Azure AD Application Gallery eller som icke-galleriprogram.  Appar som använder OAuth 2.0 eller OpenID Connect kan integreras med Azure AD på samma sätt som ”appregistreringar”.  Läs vidare om du vill ha mer information.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federerade SaaS-appar jämfört med anpassade LoB-appar
Federerade appar är appar i de angivna kategorierna.

- SaaS-appar 
    - Om dina användare loggar in på SaaS-appar som Salesforce, ServiceNow eller Workday och du integrerar till en lokal identititetsprovider som AD FS eller Ping använder du sammansluten inloggning för SaaS-appar.
    - Appar använder vanligtvis SAML 2.0-protokollet för sammansluten autentisering.
    - Program som hamnar under den här kategorin kan integreras med Azure AD som företagsprogram, antingen från galleriet eller som icke-galleriprogram.
- Anpassade LoB-program
    - Det gäller icke-SaaS-appar som har utvecklats internt av din organisation eller är tillgängliga som en standardpaketerad produkt som är installerad i datacentret.  Det innefattar SharePoint-appar och inbyggda appar i Windows Identity Foundation (WIF).
    - Appar kan använda SAML 2.0, WS-Federation, OAuth eller OpenID Connect för sammansluten inloggning
    - Anpassade appar som använder Oauth 2.0, OpenID Connect eller WS-Federation kan integreras med Azure AD som appregistreringar, och anpassade appar som använder SAML 2.0 eller WS-Federation kan integreras som icke-galleriprogram i företagsprogram

### <a name="non-federated-apps"></a>Icke-federerade appar
Icke-federerade appar kan dessutom integreras med Azure AD med Azure AD Application Proxy och relaterade funktioner.  Mer information om funktioner får du om du följer länkarna:
- Appar som använder WIA (Windows Integrated Auth) direkt till Active Directory
    - Apparna kan integreras till Azure AD via [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)
- Appar som integreras med din provider för enkel inloggning via en agent och använder rubriker för auktorisering
    - lokala appar som använder en installerad agent för inloggning och rubrikbaserad auktorisering kan konfigureras för Azure AD-baserad inloggning med Azure AD Application Proxy med [Ping Access for AzureAD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Översättning av lokala federerade appar till Azure AD 
Lyckligtvis fungerar AD FS och Azure AD på samma sätt, så koncepten för att konfigurera webbadresser för tillit, in- och utloggning och identifierare gäller i båda fallen.  Men det finns några små skillnader som du behöver känna till för att göra övergången.

I tabellen har vi mappat flera viktiga förslag som delas av AD FS, Azure AD och SaaS-appar för att hjälpa dig att översätta. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Representera appen i Azure AD eller AD FS
Migreringen börjar med att utvärdera hur programmet är konfigurerat lokalt och mappar konfigurationen till Azure AD.  Här följer en mappning av AD FS-konfigurationselement för förlitande part till motsvarande element i Azure AD.  
- AD FS-term: Förlitande part eller förtroende för förlitande part
- Azure AD-term: Företagsprogram eller appregistrering (beroende på apptypen)

|Appkonfigurationselement|Beskrivning|I AD FS-konfiguration|Motsvarande plats i Azure AD-konfiguration|SAML-tokenelement|
|-----|-----|-----|-----|-----|
|Inloggnings-URL för appen|URL till programmets inloggningssida. Hit går användaren för att initiera en inloggning till en app i ett SP-initierat SAML-flöde.|Saknas|I Azure AD konfigureras inloggnings-URL:en i Azure-portalen i programmets egenskaper för enkel inloggning som inloggnings-URL.</br></br>(du kanske måste klicka på Visa avancerade URL-inställningar för att se inloggnings-URL:en)||
|Appens svarswebbadress|Appens webbadress från IdP:ns perspektiv.  Hit skickas användaren och token när användaren har loggat in på den IDP:n.</br></br>  Detta kallas ibland för SAML-intygsmottagarslutpunkt.|Finns i appens AD FS-förtroende för betrodd part.  Högerklicka på den förlitande parten och välj ”Egenskaper” -> fliken ”Slutpunkter”.|I Azure AD konfigureras svars-URL:en i Azure-portalen i programmets egenskaper för enkel inloggning som svars-URL.</br></br>(du kanske måste klicka på Visa avancerade URL-inställningar för att se svars-URL:en)|Mappas till målelementet i SAML-token.</br></br>  Exempelvärde:  https://contoso.my.salesforce.com|
|Appens inloggnings-URL|URL till vilka begäran om ”utloggningsrensning” skickas när en användare loggar ut från en app för att logga ut alla andra appar som IDP:n har loggat in användaren på.|Finns i AD FS-hantering under: Förlitande partsförtroenden.  Högerklicka på RP:n och välj ”Egenskaper” -> Klicka på fliken ”Slutpunkter”|Ej tillämpligt – Azure AD stöder inte ”enkel utloggning”, vilket innebär utloggning av alla appar.  Den loggar bara ut användaren från själva Azure AD.|Ej tillämpligt|
|Programidentifierare|Identifierare för appen från IdP:ns perspektiv Inloggningens URL-värde används ofta för identifierare (men inte alltid)</br></br>  Ibland kallas appen ”Enhets-id”.|I AD FS är detta ID för förlitande part:  Högerklicka på förlitande partsförtroenden och välj ”Egenskaper” -> Klicka på fliken ”Identifierare”|I Azure AD konfigureras identifieraren i Azure-portalen i programmets egenskaper för enkel inloggning som identifierare under domän och URL:er (du kanske behöver markera kryssrutan ”Visa avancerade URL-inställningar”)|Motsvarar Audience-elementet i SAML-token|
|Federationsmetadata för appen|Platsen för appens federationsmetadata.  Används av IdP:N för att automatiskt uppdatera specifika konfigurationsinställningar som slutpunkter eller krypteringscertifikat.|URL för appens federationsmetadata finns i appens AD FS-förtroende för betrodd part.  Högerklicka på förtroendet och välj Egenskaper och klicka sedan på fliken Övervakning.|Ej tillämpligt – Azure AD stöder inte konsumerande appars federationsmetadata direkt|Ej tillämpligt|
|Användaridentifierare/NameID|Attribut som används för att unikt ange användarens identitet från Azure AD eller AD FS till din app.</br></br>  Vanligtvis antingen användarens UPN eller e-postadress.|I AD FS finns detta som en anspråksregel hos den förlitande parten.  I de flesta fall är det anspråksregeln som utfärdar ett anspråk med en typ som slutar med ”nameidentifier”|I Azure AD finns UID i Azure-portalen i programmets egenskaper för enkel inloggning under rubriken Användarattribut.</br></br>Som standard används UPN.|Meddelas från IDP till appen som ”NameID”-element i SAML-token.|
|Övriga anspråk som ska skickas till appen|Förutom till Användaridentifierare/NameID skickas övrig anspråksinformation ofta från IDP till appen, till exempel förnamn, efternamn, e-postadress och grupper som användaren är medlem i|I AD FS finns detta som andra anspråksregler hos den förlitande parten.|I Azure AD i Azure-portalen i programmets egenskaper för enkel inloggning under rubriken Användarattribut klickar du på Visa och redigera alla andra användarattribut.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>Representera Azure AD som en identitetsprovider (IdP) i en SaaS-app
Som en del av migreringen måste appen vara konfigurerad för att peka på Azure AD (kontra den lokala identitetsprovidern).  Det här avsnittet fokuserar främst på SaaS-appar som använder SAML-protokoll och inte anpassade/LOB-appar. Men begreppen som beskrivs kan utökas till anpassade/LOB-appar. 

Det finns några viktiga saker att tänka på för att peka på en SaaS-app till Azure AD på en hög nivå
- Identifiera providerutgivare:  https&#58;//sts.windows.net/{tenant-id}/
- Inloggnings-URL för identitetsprovider: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- Utloggnings-URL för identitetsprovider: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Plats för federationsmetadata:  https&#58;//login.windows.net/{tenant-id} <tenant-id>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

där {tenant-id} är ersatt med ditt klient-ID som finns i Azure-portalen under Azure Active Directory -> Egenskaper som ”Katalog-ID”, och {application-id} ersätts av programmets ID som finns under programmets egenskaper som ”Program-ID”

Tabellen beskriver mer ingående viktiga IdP-konfigurationselement för att konfigurera SSO-inställningar i appen och deras värden eller platser inom AD FS och Azure AD.  Tabellens referensram är SaaS-appen, som måste veta var begäranden om autentisering ska skickas och hur mottagna token ska valideras.

|Konfigurationselement|Beskrivning|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>inloggning </br>URL|utloggnings-URL för IdP:n från appens perspektiv (där användare omdirigeras för inloggning).|AD FS-inloggningens URL är AD FS-federationstjänstens namn följt av ”/adfs/ls/”, till exempel: https&#58;//fs.contoso.com/adfs/ls/|Motsvarande värde för Azure AD följer mönstret, och {tenant-id} ersätts med klient-ID:t som finns i Azure-portalen under Azure Active Directory -> Egenskaper som ”Katalog-ID”.</br></br>För appar som använder SAML-P-protokollet: https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>För appar som använder WS-Federation-protokollet https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|IdP </br>Logga ut </br>URL|Utloggnings-URL för IdP:n från appens perspektiv (där användaren omdirigeras när de väljer att ”logga ut” från appen).|För AD FS är utloggnings-URL:en antingen samma som inloggnings-URL:en eller samma URL med tillägget ”wa=wsignout1.0”, till exempel https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|Motsvarande värde för Azure AD beror på om appen kan stödja SAML 2.0-utloggning eller inte.</br></br>Om appen stöder SAML-utloggning följer värdet mönstret, och värdet för {tenant-id} ersätts med klient-ID:t som finns i Azure-portalen under Azure Active Directory -> Egenskaper som ”Katalog-ID”. https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>Om appen inte har stöd för SAML-utloggning: https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|Token </br>Signering </br>Certifikat|Certifikat vars privata nyckel IDP:n använder för att signera utfärdade token.  Verifierar att token kom från samma IDP som appen är konfigurerad för att lita på.|AD FS-certifikat för tokensignering finns i AD FS-hantering under Certifikat.|I Azure AD finns certifikatet för tokensignering i Azure-portalen i programmets egenskaper för enkel inloggning under rubriken SAML-signeringscertifikat, där du kan ladda ned certifikatet och ladda upp det till appen.</br></br>  Om programmet har fler än ett certifikat finns alla certifikat i federationsmetadatas xml-fil.|
|Identifierare / </br>”Utgivare”|Identifierare för IdP:n från appens perspektiv (kallas ibland för ”Utfärdare” eller ”Utfärdar-ID”)</br></br>I SAML-token visas värdet som ”Utfärdar”-element|Identifieraren för AD FS är vanligen federationstjänstens identifierare i AD FS-hantering under: Tjänst -> Redigera federationstjänstens egenskaper.  Till exempel: http&#58;//fs.contoso.com/adfs/services/trust|Motsvarande värde för Azure AD följer mönstret, och värdet för {tenant-id} ersätts med klient-ID:t som finns i Azure-portalen under Azure Active Directory -> Egenskaper som ”Katalog-ID”.  https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>Federation </br>Metadata|Plats för IDP:ns offentligt tillgängliga federationsmetadata.  (Federationsmetadata används av vissa appar som ett alternativ till administratören och konfigurerar URL:er, identifierare och certifikat för tokensignering individuellt)|Leta reda på URL:en för AD FS-federationsmetadata i AD FS-hantering under Tjänst -> Slutpunkter-> Metadata -> Typ: Federationsmetadata, till exempel: https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|Motsvarande värde för Azure AD följer mönstret https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml där värdet för {TenantDomainName} ersätts med klientens namn i formatet ”contoso.onmicrosoft.com” </br></br>[Mer information](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata) om federationsmetadata i Azure AD.

## <a name="migrating-saas-apps"></a>Migrera SaaS-appar
Idag migreras SaaS-appar från AD FS eller någon annan identitetsprovider manuellt till Azure AD. För appspecifik vägledning kan du [titta i listan över självstudier om integrering av SaaS-appar i galleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

I självstudierna om integrering förutsätts att du gör en integrering med grönt fält.  Det finns några viktiga begrepp om migrering som du bör kanna till när du planerar, utvärderar, konfigurerar och utför snabba lösningar för dina appar.  
- Medan vissa appar kan migreras enkelt kan appar med mer komplexa krav som anpassade anspråk kräva ytterligare konfiguration i Azure AD och/eller Azure AD Connect
- I det flesta vanliga scenarier krävs endast NameId-anspråket och andra vanliga UID-anspråk för en app. För att avgöra om det krävs några ytterligare anspråk bör du titta närmare på vilka anspråk du utfärdar från AD FS eller din identitetsprovider från tredje part
- När du har fastställt att det krävs ytterligare anspråk måste du garantera att de är tillgängliga i Azure AD.  Du måste kontrollera Azure AD Connect-synkroniseringen för att garantera att ett attribut som krävs, exempelvis samAccountName, synkroniseras till Azure AD
- När attributen är tillgängliga i Azure AD ska du lägga till regler för anspråksutfärdande i Azure AD för att inkludera dessa attribut som anspråk i utfärdade token.  Görs via egenskaperna för enkel inloggning i appen i Azure AD.

### <a name="assessing-what-can-be-migrated"></a>Utvärdera vad som kan migreras
SAML 2.0-program kan integreras med Azure AD via antingen Azure AD Application Gallery eller som icke-galleriprogram.  

Vissa konfigurationer kräver ytterligare åtgärder för att konfigureras i Azure AD, varav vissa inte stöds i nuläget.  För att fastställa vilka som kan flyttas kan du ta en titt på varje apps nuvarande konfiguration, särskilt följande:
- Konfigurerade anspråksregler (utfärdande av transformeringsregler)
- SAML NameID-format och -attribut
- Utfärdade SAML-tokenversioner
- Andra konfigurationer, till exempel auktoriseringsregler eller principer för kontroll och Multi Factor Authentication-regler (ytterligare autentisering)

#### <a name="what-can-be-migrated-today"></a>Vad som kan migreras idag
Appar som går att migrera enkelt idag inkluderar SAML 2.0-appar som använder standarduppsättningen med konfigurationselement och -anspråk.  Apparna kan bestå av
- user principal name
- e-postadress
- Förnamn
- Efternamn
- Alternativa attribut som SAML NameID, inklusive attribut för Azure AD-e-post, e-postprefix, employeeid, tilläggsattribut 1–15 eller lokalt SamAccountName (se [informationen om att redigera NameIdentifier-anspråket)](./develop/active-directory-saml-claims-customization.md)
- Anpassade anspråk (se dokumentet [här](active-directory-claims-mapping.md) och [här](./develop/active-directory-saml-claims-customization.md) för information om anspråksmappningar som stöds)

Utöver anpassade anspråk och nameID-element är konfigurationer som kräver extra konfigurationssteg i Azure AD som en del av migreringen följande:
- Anpassad auktorisering eller MFA-regler i AD FS (konfigureras med funktionen [Azure AD villkorsstyrd åtkomst](active-directory-conditional-access-azure-portal.md))
- Appar med flera SAML-slutpunkter kan konfigureras i Azure AD med hjälp av PowerShell (den här funktionen är inte tillgänglig i portalen)
- WS-Federation-appar som SharePoint-appar som kräver token i SAML 1.1-version måste konfigureras manuellt med PowerShell

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>Appar/konfigurationer stöds inte i Azure AD i nuläget
Appar som kräver följande funktioner kan inte migreras idag.  Ge feedback i kommentarsavsnittet om du har appar som kräver dessa funktioner.
- Protokollfunktioner
    - Stöd för SAML SLO (enkel SAML-utloggning) för alla inloggade appar
    - Stöd för WS-Trust ActAs-mönster
    - SAML-artefaktmatchning 
    - Signaturverifiering av signerade SAML-begäranden (lägg märke till att signerade begäranden accepteras, men signaturen är inte verifierad)
 - Token-funktioner 
     - SAML-tokenkryptering 
     - SAML version 1.1-token i SAML-protokollsvar 
- Anspråk i token-funktioner
    - Utfärda lokala gruppnamn som anspråk
    - Anspråk i andra lager än Azure AD
    - Komplexa anspråksregler för utfärdandetransformering (se det här dokumentet och det är dokumentet för information om anspråksmappningar som stöds)
    - Utfärda katalogtillägg som anspråk
    - Anpassad specifikation av NameID-format
    - Utfärda flervärdesattribut

>[!NOTE]
>Azure AD utvecklas ständigt för att lägga till ytterligare funktioner i det här området. Vi uppdaterar det här dokumentet regelbundet. 

### <a name="configuring-azure-ad"></a>Konfigurera Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Konfigurera inställningar för enkel inloggning (SSO) för SaaS-appen

I Azure AD ska konfiguration av SAML-inloggning som krävs av din app göras inom egenskaperna för enkel inloggning för appen under Användarattribut enligt följande:

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- Klicka på ”Visa och redigera alla andra användarattribut” om du vill se attributen och skicka dem som anspråk i säkerhetstoken

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- Klicka på en specifik attributrad för att redigera eller klicka på ”Lägg till attribut” för ett nytt attribut. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Tilldela användare till appen
För att dina användare i Azure AD ska kunna logga in på en viss SaaS-app måste du få åtkomst inifrån Azure AD.

Om du vill tilldela användare i Azure AD-portalen går du till SaaS-appens skärm i portalen och klickar på ”Användare och grupper” i sidorutan. För att lägga till antingen en användare eller en grupp klickar du på ”Lägg till användare” högst upp på skärmen. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png) För att verifiera åtkomst bör användare se nämnda SaaS-app i sin [åtkomstpanel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när han/hon loggar in. Den finns på http://myapps.microsoft.com. Användaren har till exempel tilldelats åtkomst till både Salesforce och ServiceNow.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>Konfigurera SaaS-appen
Snabbprocessen från lokal federation till Azure AD beror på om SaaS-appen du arbetar med stöder flera identitetsproviders (IdP:er).  Här följer några vanliga frågor om stöd för flera IdP:er:

   **F: Vad betyder det att en app stöder flera IDP:er?**
    
   S: SaaS-appar som stöder flera IDP:er gör att du kan ange all information om den nya IDP:n (i vårt fall Azure AD) innan du ändrar inloggningsupplevelsen.  När konfigurationen är klar kan du växla appens autentiseringskonfiguration så att den pekar på Azure AD.

   F: Varför spelar det någon roll om SaaS-appen stöder flera IDP:er?

   S: Om flera IDP:er inte stöds måste administratören avsätta lite tid som ett avbrott i tjänsten eller ett underhållsavbrott då han/hon konfigurerar Azure AD som en apps nya IDP. Under avbrottet ska användarna meddelas att de inte kan logga in på sina konton.

   Om en app stöder flera IDP:er kan konfigurationen av den extra IDP:n göras i förväg, så administratören bara kan växla IdP till Azure snabbt.

   Om appen stöder flera IdP:er och du väljer att flera IdP:er samtidigt ska hantera autentisering för inloggning kan användaren dessutom välja vilken IdP som ska autentisera på deras inloggningssida bland flera IdP:er.

#### <a name="example-multiple-idp-support"></a>Exempel: stöd för flera IDP:er
I Salesforce till exempel finns IDP-konfigurationen under Inställningar -> Företagsinställningar-> Min domän -> Autentiseringskonfiguration.

![](media/migrate-adfs-apps-to-azure/migrate9.png)

Eftersom konfigurationen skapades tidigare vid inställningarna för Identitet-> Enkel inloggning bör du kunna ändra IDP för autentiseringskonfiguration från till exempel AD FS till Azure AD. 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Valfritt: konfigurera användaretablering i Azure AD
Om du vill att Azure AD direkt ska hantera användaretablering för en specifik SaaS-app kan du läsa det här dokumentet om hantering av användarkontoetablering för företagsappar i Azure AD.

## <a name="next-steps"></a>Nästa steg

- [Hantera program med Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Hantera åtkomst till appar](active-directory-managing-access-to-apps.md)
- [Azure AD Connect Federation](active-directory-aadconnectfed-whatis.md)