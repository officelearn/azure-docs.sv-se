---
title: Migrera AD FS på lokala appar till Azure. | Microsoft Docs
description: Den här artikeln är avsedd att hjälpa organisationer förstå hur de ska migrera lokala program till Azure AD, med fokus på federerade SaaS-program.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: barbkess
ms.openlocfilehash: cdce0ae223a637ac30ed472d2bf711aa36958414
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346771"
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migrera AD FS på lokala appar till Azure 

Den här artikeln hjälper dig förstå hur du migrerar lokala program till Azure Active Directory (Azure AD). Den fokuserar på federerade SaaS-program. 

I artikeln finns det inte någon stegvis vägledning. Det ger konceptuell vägledning för att hjälpa dig att genomföra migreringen genom att förstå hur lokala konfigurationer översätts till Azure AD. Den beskriver också vanliga scenarier.

## <a name="introduction"></a>Introduktion

Om du har en lokal katalog som innehåller användarkonton har du kanske minst en eller två appar. Och apparna är konfigurerade för användare som ska få åtkomst via inloggning med dessa identiteter.

Och om din organisation är som många andra håller ni förmodligen på att anpassa er till molnprogram och -identiteter. Ni kanske har kommit igång med Office 365 och Azure AD Connect. Du kanske har konfigurerat molnbaserade SaaS-program för några viktiga arbetsbelastningar, men inte för alla.  

Många organisationer har SaaS-appar eller anpassade verksamhetsspecifika appar (LOB) som har federerats direkt till en lokal inloggningstjänst, som exempelvis Active Directory Federation Service (AD FS) tillsammans med Office 365- och Azure AD-baserade appar. Den här migreringsvägledningen beskriver varför och hur du migrerar lokala program till Azure AD.

>[!NOTE]
>Den här guiden innehåller detaljerad information om appkonfiguration och migrering i SaaS, med översiktlig information om anpassade LOB-appar. Mer detaljerad vägledning för anpassade LOB-appar planeras i framtiden.

![Appar som ansluts direkt lokalt](media/migrate-adfs-apps-to-azure/migrate1.png)

![Appar som federeras via Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-migrating-apps-to-azure-ad"></a>Skäl till att migrera appar till Azure AD

För organisationer som redan använder AD FS, Ping eller någon annan lokal autentiseringsprovider ger migrering till Azure AD följande fördelar:

**Säkrare åtkomst**
- Konfigurera detaljerade programspecifika åtkomstkontroller, däribland Azure Multi-Factor Authentication (MFA), med [villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md). Principerna kan tillämpas på SaaS-appar och anpassade appar på samma sätt som du kanske gör i dag för Office 365.
- För att upptäcka hot och skydda inloggning som baseras på maskininlärning och heuristik som identifierar riskfylld trafik, kan du använda [Azure AD Identity Protection](../active-directory-identityprotection.md).

**Azure AD B2B-samarbete**
- Eftersom inloggningen till SaaS-appar har baserats på Azure AD, kan du ge dina partners åtkomst till molnresurser med [Azure AD B2B-samarbetet](../b2b/what-is-b2b.md).

**Enklare administration och fler funktioner i Azure AD**
- Azure AD, som är en identitetsprovider för SaaS-appar, stöder ytterligare funktioner som exempelvis:
  - Certifikat för tokensignering per program.
  - [Konfigurerbara förfallodatum för certifikat](manage-certificates-for-federated-single-sign-on.md).
  - [Automatiserad etablering](user-provisioning.md) av användarkonton (i viktiga Azure Marketplace-appar), baserat på Azure AD-identiteter.

**Behåll fördelarna med en lokal identitetsprovider**
- Samtidigt som du får fördelarna med Azure AD, kan du fortsätta att använda en lokal lösning för autentisering. Det innebär att fördelar som lokala Multi-Factor Authentication-lösningar, loggning och granskning finns kvar. 

**Hjälp till med tillbakadragning av den lokala identitetsprovidern.**
- För organisationer som vill dra tillbaka sin lokala autentiseringsprodukt blir övergången enklare om de slipper en del av arbetet via migreringen av appar till Azure AD. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mappa typer av appar lokalt till typer av appar i Azure AD
De flesta appar hamnar i någon av kategorierna som baseras på inloggningstypen. Kategorierna avgör hur appen visas i Azure AD.

SAML 2.0-program kan i korthet antingen integreras med Azure AD via Azure AD-programgalleriet i Marketplace eller som program som inte finns på Marketplace. Appar som använder OAuth 2.0 eller OpenID Connect kan integreras med Azure AD på samma sätt som *appregistreringar*. Läs vidare om du vill ha mer information.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federerade SaaS-appar jämfört med anpassade LOB-appar
Federerade appar är appar som finns i följande kategorier:

- SaaS-appar 
    - Om dina användare loggar in på SaaS-appar som exempelvis Salesforce, ServiceNow eller Workday och du integrerar med en lokal identitetsprovider som AD FS eller Ping, använder du sammansluten inloggning för SaaS-appar.
    - Appar använder vanligtvis SAML 2.0-protokollet för sammansluten autentisering.
    - Appar i den här kategorin kan integreras med Azure AD som företagsprogram, antingen från Marketplace eller som program som inte finns i Marketplace.
- Anpassade LOB-appar
    - Det gäller icke-SaaS-appar som har utvecklats internt av din organisation eller är tillgängliga som en standardpaketerad produkt som är installerad i datacentret. Det innefattar SharePoint-appar och inbyggda appar i Windows Identity Foundation.
    - Appar kan använda SAML 2.0, WS-Federation, OAuth eller OpenID Connect för sammansluten inloggning.
    - Anpassade appar som använder OAuth 2.0, OpenID Connect eller WS-Federation kan integreras med Azure AD som appregistreringar. Anpassade appar som använder SAML 2.0 eller WS-Federation kan integreras som program som inte finns i Marketplace inom företagsprogrammen.

### <a name="non-federated-apps"></a>Icke-federerade appar
Du kan integrera icke-federerade appar med Azure AD med hjälp av Azure AD-programproxy och relaterade funktioner. Icke-federerade appar är:
- Appar som använder WIA (Windows Integrated Authentication) direkt med Active Directory. Dessa appar kan integreras med Azure AD via [Azure AD-programproxy](application-proxy-publish-azure-portal.md).
- Appar som integreras med din provider för enkel inloggning via en agent och som använder rubriker för auktorisering. Lokala appar som använder en installerad agent för inloggning och rubrikbaserad auktorisering kan konfigureras för Azure AD-baserad inloggning via Azure AD-programproxy med [Ping Access för Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Översättning av lokala federerade appar till Azure AD 
AD FS och Azure AD fungerar på samma sätt, så koncepten för att konfigurera webbadresser för förtroende, in- och utloggning samt identifierare gäller i båda fallen. Du måste dock känna till några mindre skillnader när övergången görs.

I följande tabeller visas viktiga delar i AD FS-, Azure AD- och SaaS-appar som kan vara till hjälp. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Representera appen i Azure AD eller AD FS
Migreringen börjar med att utvärdera hur programmet är konfigurerat lokalt och mappar konfigurationen till Azure AD. I följande tabell visas en mappning av AD FS-konfigurationselement för förlitande part till motsvarande element i Azure AD.  
- AD FS-term: Förlitande part eller förlitande partsförtroende.
- Azure AD-term: Företagsprogram eller appregistrering (beroende på apptyp).

|Appkonfigurationselement|Beskrivning|Plats i AD FS-konfigurationen|Motsvarande plats i Azure AD-konfiguration|SAML-tokenelement|
|-----|-----|-----|-----|-----|
|Inloggnings-URL för appen|Webbadress till programmets inloggningssida. Det är här som användaren loggar in på appen i ett SP-initierat SAML-flöde.|Gäller inte|I Azure AD konfigureras inloggnings-URL:en i Azure-portalen i programmets egenskaper för **Enkel inloggning** som inloggnings-URL.</br></br>(Du kanske måste välja **Visa avancerade URL-inställningar** för att kunna se inloggnings-URL:en.)|Gäller inte|
|Appens svars-URL|Appens webbadress ur identitetsproviderns (IdP) perspektiv. Hit skickas användare och token när användaren har loggat in på IdP:n.</br></br> Detta kallas ibland för ”slutpunkt för SAML-intygsmottagare”.|Den finns i appens AD FS-förlitande partsförtroende. Högerklicka på den förlitande parten, välj **Egenskaper** och välj sedan fliken **Slutpunkter**.|I Azure AD konfigureras svars-URL:en i Azure-portalen i programmets egenskaper för **Enkel inloggning** som svars-URL.</br></br>(Du kanske måste välja **Visa avancerade URL-inställningar** för att kunna se svars-URL:en.)|Mappas till **Mål**-elementet i SAML-token.</br></br> Exempelvärde: https://contoso.my.salesforce.com|
|Appens webbadress för utloggning|URL dit begäranden om ”utloggningsrensning” skickas när en användare loggar ut från en app, vilket loggar ut alla andra appar som IdP:n har loggat in användaren på.|Den finns i AD FS-hantering under **Förlitande partsförtroenden**. Högerklicka på den förlitande parten, välj **Egenskaper** och välj sedan fliken **Slutpunkter**.|Ej tillämpligt. Azure AD stöder inte ”enkel utloggning”, vilket innebär utloggning från alla appar. Den loggar bara ut användaren från själva Azure AD.|Gäller inte|
|Appidentifierare|Appens identifierare ur IdP:ns perspektiv. Inloggningens URL-värde används ofta för identifierare (men inte alltid).</br></br> Ibland kallar appen detta för ”Entitets-ID”.|I AD FS är detta den förlitande partens ID. Högerklicka på det förlitande partsförtroendet, välj **Egenskaper** och sedan fliken **Identifierare**.|I Azure AD konfigureras identifieraren i Azure-portalen i programmets egenskaper för **Enkel inloggning** som identifierare under **Domän och URL:er**. (Du kan behöva markera kryssrutan **Visa avancerade URL-inställningar**.)|Motsvarar elementet **Målgrupp** i SAML-token.|
|Federationsmetadata för appen|Platsen för appens federationsmetadata. IdP:n använder den till att automatiskt uppdatera specifika konfigurationsinställningar, som t.ex. slutpunkter eller krypteringscertifikat.|URL:en för appens federationsmetadata finns i appens AD FS-förlitande partsförtroende. Högerklicka på förtroendet, välj **Egenskaper** och sedan fliken **Övervakning**.|Ej tillämpligt. Azure AD stöder inte konsumerande appars federationsmetadata direkt.|Gäller inte|
|Användaridentifierare/**NameID**|Attribut som används för att unikt ange användarens identitet från Azure AD eller AD FS till din app.</br></br> Attributet är vanligtvis användarens UPN eller e-postadress.|I AD FS finns detta som en anspråksregel hos den förlitande parten. I de flesta fall är det anspråksregeln som utfärdar ett anspråk med en typ som slutar med ”nameidentifier”|I Azure AD finns användaridentifieraren i Azure-portalen i programmets egenskaper för **Enkel inloggning** under rubriken **Användarattribut**.</br></br>Som standard används UPN.|Meddelas från IdP:n till appen som ett **NameID**-element i SAML-token.|
|Övriga anspråk som skickas till appen|Förutom användaridentifierare/**NameID** skickas ofta annan anspråksinformation från IdP:n till appen. Exempel på detta är förnamn, efternamn, e-postadress och grupper som användaren är medlem i.|I AD FS finns detta som övriga anspråksregler hos den förlitande parten.|I Azure AD finns det i Azure-portalen i programmets egenskaper för **Enkel inloggning** under rubriken **Användarattribut**. Välj **Visa** och redigera alla andra användarattribut.|Gäller inte|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Visa Azure AD som en identitetsprovider i en SaaS-app
Som en del av migreringen måste du konfigurera appen så att den pekar på Azure AD (i stället för den lokala identitetsprovidern). Det här avsnittet fokuserar på SaaS-appar som använder SAML-protokoll och inte anpassade LOB-appar. Men begreppen kan även användas på anpassade LOB-appar. 

På en högre nivå är det vissa viktiga delar som pekar en SaaS-app till Azure AD:
- Utfärdare av identitetsprovider: https&#58;//sts.windows.net/{tenant-id}/
- Inloggnings-URL för identitetsprovider: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- Utloggnings-URL för identitetsprovider: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Plats för federationsmetadata: https&#58;//login.windows.net/{tenant-id} /federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

Ersätt {tenant-id} med ditt klientorganisations-ID som finns i Azure-portalen under **Azure Active Directory** > **Egenskaper** som **Katalog-ID**. Ersätt {application-id} med ditt program-ID som finns under programmets egenskaper som **Program-ID**.

Följande tabell beskriver viktiga IdP-konfigurationselement vid konfiguration av inställningar för enkel inloggning i appen, samt deras värden eller platser inom AD FS och Azure AD. Tabellens referensram är SaaS-appen, som måste veta vart begäranden om autentisering ska skickas och hur mottagna token ska valideras.

|Konfigurationselement|Beskrivning|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>inloggning </br>URL|Inloggnings-URL för IdP:n ur appens perspektiv (dit användare omdirigeras för inloggningen).|AD FS-inloggningens URL är AD FS-federationstjänstens namn följt av ”/adfs/ls/”. Till exempel: https&#58;//fs.contoso.com/adfs/ls/|Motsvarande värde för Azure AD följer mönstret där {tenant-id} ersätts med ditt klientorganisations-ID. Det finns i Azure-portalen under **Azure Active Directory** > **Egenskaper** som **Katalog-ID**.</br></br>För appar som använder SAML-P-protokollet: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 </br></br>För appar som använder WS-Federation-protokollet: https&#58;//login.microsoftonline.com/{tenant-id}/wsfed|
|IdP </br>utloggning </br>URL|Utloggnings-URL för IdP:n ur appens perspektiv (dit användarna omdirigeras när de väljer att logga ut från appen).|För AD FS är utloggnings-URL:en antingen samma som inloggnings-URL:en eller samma URL med tillägget ”wa=wsignout1.0”. Till exempel: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Motsvarande värde för Azure AD beror på om appen har stöd för SAML 2.0-utloggning eller inte.</br></br>Om appen har stöd för SAML-utloggning följer värdet mönstret där värdet för {tenant-id} ersätts med klientorganisations-ID. Det finns i Azure-portalen under **Azure Active Directory** > **Egenskaper** som **Katalog-ID**: https&#58;//login.microsoftonline.com/{tenant-id}/saml2</br></br>Om appen inte har stöd för SAML-utloggning: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Token </br>signering </br>certifikat|IdP:n använder certifikatets privata nyckel till att signera utfärdade tokens. Den kontrollerar att token kom från samma IdP som appen är konfigurerad att ha förtroende för.|AD FS-certifikatet för tokensignering finns i AD FS-hanteringen under **Certifikat**.|I Azure AD finns certifikatet för tokensignering i Azure-portalen i programmets egenskaper för **Enkel inloggning** under rubriken **SAML-signeringscertifikat**. Därifrån kan du ladda ner certifikatet för uppladdning till appen.</br></br> Om programmet har fler än ett certifikat finns alla certifikat i federationsmetadatans XML-fil.|
|Identifierare/</br>”utfärdare”|Identifierare för IdP:n ur appens perspektiv (kallas ibland för ”Utfärdar-ID”).</br></br>I SAML-token visas värdet som elementet **Utfärdare**.|Identifieraren för AD FS är vanligen federationstjänstens identifierare i AD FS-hanteringen under **Tjänst** > **Redigera federationstjänstens egenskaper**. Till exempel: http&#58;//fs.contoso.com/adfs/services/trust|Motsvarande värde för Azure AD följer mönstret där {tenant-id} ersätts med ditt klientorganisations-ID. Det finns i Azure-portalen under **Azure Active Directory** > **Egenskaper** som **Katalog-ID**: https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>federation </br>metadata|Plats för IdP:ns offentligt tillgängliga federationsmetadata. (Federationsmetadata används av vissa appar som ett alternativ för administratören och konfigurerar URL:er, identifierare och certifikat för tokensignering individuellt.)|Hitta metadata-URL:en för AD FS-federation i AD FS-hanteringen under **Tjänst** > **Slutpunkter** > **Metadata** > **Typ: Federationsmetadata**. Till exempel: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Motsvarande värde för Azure AD följer mönstret https&#58;/ / login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml. Värdet för {TenantDomainName} ersätts med klientorganisationens namn i formatet ”contoso.onmicrosoft.com”. </br></br>Mer information finns i [Federationsmetadata](../develop/azure-ad-federation-metadata.md).

## <a name="migrating-saas-apps"></a>Migrera SaaS-appar
I dag migreras SaaS-appar från AD FS eller någon annan identitetsprovider manuellt till Azure AD. Om du behöver appspecifik vägledning kan du [titta i listan med självstudier om integrering av SaaS-appar i Marketplace](../saas-apps/tutorial-list.md).

I självstudierna om integrering förutsätts att du gör en integrering med grönt fält. Det finns några viktiga begrepp om migrering som du bör känna till när du planerar, utvärderar, konfigurerar och utför snabba lösningar för dina appar:  
- Vissa appar är enkla att migrera. Andra appar med mer komplexa krav, som t.ex. anpassade anspråk, kan kräva ytterligare konfiguration i Azure AD och/eller Azure AD Connect.
- I de vanligaste scenarierna behöver endast **NameID**-anspråk och andra vanliga anspråk för användaridentifierare anges för en app. För att avgöra om det krävs ytterligare anspråk, undersöker du vilka anspråk som du utfärdar från AD FS eller din identitetsprovider från tredje part.
- När du har bestämt att ytterligare anspråk krävs bör du se till att de är tillgängliga i Azure AD. Kontrollera Azure AD Connect-synkroniseringen för att garantera att attribut som krävs, exempelvis **samAccountName**, synkroniseras till Azure AD.
- När attributen är tillgängliga i Azure AD kan du lägga till regler för anspråksutfärdande i Azure AD för att inkludera dessa attribut som anspråk i utfärdade tokens. Detta görs i egenskaperna för **Enkel inloggning** i appen i Azure AD.

### <a name="assess-what-can-be-migrated"></a>Bedöma vad som kan migreras
SAML 2.0-program kan antingen integreras med Azure AD via Azure AD-programgalleriet i Marketplace eller som program som inte finns på Marketplace.  

Vissa konfigurationer kräver ytterligare åtgärder för att konfigureras i Azure AD och vissa konfigurationer stöds inte i nuläget. För att fastställa vilka som kan flyttas kan du ta en titt på varje apps nuvarande konfiguration. Mer specifikt söker du efter:
- Konfigurerade anspråksregler (utfärdande av transformeringsregler).
- SAML **NameID**-format och attribut.
- Utfärdade SAML-tokenversioner.
- Andra konfigurationer, till exempel utfärdande av auktoriseringsregler eller principer för åtkomstkontroll och Multi-Factor Authentication-regler (ytterligare autentisering).

#### <a name="what-can-be-migrated-today"></a>Vad som kan migreras idag
Appar som går att migrera enkelt i dag är bl.a. SAML 2.0-appar som använder standarduppsättningen med konfigurationselement och anspråk. Apparna kan bestå av:
- Användarens huvudnamn.
- E-postadress.
- Förnamn.
- Efternamn.
- Alternativa attribut som SAML **NameID**, inklusive e-postattribut för Azure AD, e-postprefix, anställnings-ID, tilläggsattribut 1–15 eller lokalt **SamAccountName**-attribut. Mer information finns i [Redigera NameIdentifier-anspråket](../develop/active-directory-saml-claims-customization.md).
- Anpassade anspråk. Information om anspråksmappningar som stöds finns i [Anspråksmappning i Azure Active Directory](../active-directory-claims-mapping.md) och [Anpassa anspråk som utfärdas i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Utöver anpassade anspråk och **NameID**-element, är de konfigurationer som kräver extra konfigurationssteg i Azure AD som en del av migreringen följande:
- Anpassad auktorisering eller Multi-Factor Authentication-regler i AD FS. Du kan konfigurera dem med hjälp av funktionen [Villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md).
- Appar med flera SAML-slutpunkter. Du konfigurerar dem i Azure AD med hjälp av PowerShell. (Den här funktionen är inte tillgänglig i portalen.)
- WS-Federation-appar, till exempel SharePoint-appar som kräver SAML version 1.1-tokens. Du måste konfigurera dem manuellt med hjälp av PowerShell.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Appar och konfigurationer som inte stöds i Azure AD i nuläget
Appar som kräver följande funktioner kan inte migreras idag. Ge feedback i kommentarsavsnittet om du har appar som kräver dessa funktioner.
- Protokollfunktioner:
    - Stöd för enkel utloggning i SAML för alla inloggade appar.
    - Stöd för WS-Trust ActAs-mönster.
    - SAML-artefaktmatchning.
    - Signaturverifiering av signerade SAML-begäranden. Lägg märke till att signerade begäranden accepteras, men signaturen är inte verifierad.
- Tokenfunktioner: 
    - SAML-tokenkryptering. 
    - SAML version 1.1-tokens i SAML-protokollsvar. 
- Anspråk i tokenfunktioner:
    - Utfärdande av lokala gruppnamn som anspråk.
    - Anspråk från andra lager än Azure AD.
    - Transformeringsregler för utfärdande av komplexa anspråk. Information om anspråksmappningar som stöds finns i [Anspråksmappning i Azure Active Directory](../active-directory-claims-mapping.md) och [Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).
    - Utfärdande av katalogtillägg som anspråk.
    - Anpassad specifikation av **NameID**-formatet.
    - Utfärdande av flervärdesattribut.

>[!NOTE]
>Azure AD utvecklas ständigt med nya funktioner i det här området. Vi uppdaterar den här artikeln regelbundet. 

### <a name="configure-azure-ad"></a>Konfigurera Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Konfigurera inställningar för enkel inloggning (SSO) för SaaS-appen

I Azure AD ska konfiguration av SAML-inloggningen (som krävs av din app) göras i egenskaperna för **Enkel inloggning** för appen under **Användarattribut**.

![Egenskaper för enkel inloggning med avsnittet ”Användarattribut”](media/migrate-adfs-apps-to-azure/migrate3.png)

Välj **Visa och redigera alla andra användarattribut** om du vill se attributen och skicka dem som anspråk i säkerhetstoken.

![Lista med attribut](media/migrate-adfs-apps-to-azure/migrate4.png)

Välj en specifik attributrad som ska redigeras eller klicka på **Lägg till attribut** för att lägga till ett nytt attribut.

![Fönstret ”Redigera attribut”](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Tilldela användare till appen
Du måste ge dina användare åtkomst för att de ska kunna logga in på en SaaS-app i Azure AD.

Om du vill tilldela användare i Azure AD-portalen går du till SaaS-appens sida och väljer **Användare och grupper** i sidofältet. För att lägga till en användare eller en grupp klickar du på **Lägg till användare** högst upp i fönstret. 

![Knappen ”Lägg användare” i ”Användare och grupper”](media/migrate-adfs-apps-to-azure/migrate6.png) 

![Fönstret ”Lägg till tilldelning”](media/migrate-adfs-apps-to-azure/migrate7.png)

För att kunna verifiera åtkomsten ska användarna se SaaS-appen i sin [åtkomstpanel](../user-help/active-directory-saas-access-panel-introduction.md) när de loggar in. Åtkomstpanelen finns på http://myapps.microsoft.com. Användaren i det här exemplet har tilldelats åtkomst till både Salesforce och ServiceNow.

![Exempel på åtkomstpanel med Salesforce- och ServiceNow-appar](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Konfigurera SaaS-appen
Snabbprocessen från lokal federation till Azure AD beror på om SaaS-appen du arbetar med stöder flera identitetsproviders. Här följer några vanliga frågor om stöd för flera IdP:er:

   **F: Vad betyder det att en app stöder flera IDP:er?**
    
   S: Med SaaS-appar som stöder flera IdP:er kan du ange all information om den nya IdP:n (i vårt fall Azure AD) innan du ändrar inloggningen. När konfigurationen är klar kan du ändra appens autentiseringskonfiguration så att den pekar på Azure AD.

   **F: Varför spelar det någon roll om SaaS-appen stöder flera IdP:er?**

   S: Om flera IdP:er inte stöds måste administratören göra ett kort avbrott i tjänsten eller ett underhållsavbrott då han/hon konfigurerar Azure AD som en apps nya IdP. Under avbrottet ska användarna få ett meddelande om att de inte kan logga in på sina konton.

   Om en app har stöd för flera IdP:er kan en ytterligare IdP konfigureras i förväg. Administratören kan sedan snabbt växla IdP i Azure.

   Om appen stöder flera IdP:er och du väljer att flera IdP:er samtidigt ska hantera autentisering för inloggning, kan användarna välja vilken IdP som ska autentiseras på deras inloggningssida.

#### <a name="example-support-for-multiple-idps"></a>Exempel: Stöd för flera IdP:er
I Salesforce finns exempelvis IdP-konfigurationen under **Inställningar** > **Företagsinställningar** > **Min domän** > **Autentiseringskonfiguration**.

![Avsnittet ”Autentiseringskonfiguration” i Salesforce-appen](media/migrate-adfs-apps-to-azure/migrate9.png)

Eftersom konfigurationen skapades tidigare i **Identitet** > **Enkel inloggning** ska du kunna ändra IdP:n för autentiseringskonfigurationen. Du kan till exempel ändra den från AD FS till Azure AD. 

![Välja Azure AD som autentiseringstjänst](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Valfritt: Konfigurera användaretablering i Azure AD
Om du vill att Azure AD ska hantera användaretablering direkt för SaaS-appen, kan du läsa mer i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md).

## <a name="next-steps"></a>Nästa steg

- [Hantera program med Azure Active Directory](what-is-application-management.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
- [Azure AD Connect-federation](../active-directory-aadconnectfed-whatis.md)
