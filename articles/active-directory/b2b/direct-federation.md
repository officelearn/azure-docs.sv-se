---
title: Ställ in direkt federation med en identitetsprovider för B2B - Azure Active Directory | Microsoft Docs
description: Federera med en identitetsprovider SAML eller WS-Fed direkt så att gäster kan logga in på din Azure AD-appar
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4dadc68e78fbaa979751d5bcd04ef481c3ab886
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544353"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Direct federation med AD FS och tredjepartsleverantörer för gästanvändare (förhandsgranskning)
|     |
| --- |
| Direct federation är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Den här artikeln beskriver hur du ställer in direkt federation med en annan organisation för B2B-samarbete. Du kan ställa in direkt federation med en organisation vars identitetsprovider (IdP) stöder SAML 2.0 eller WS-Fed-protokollet.
När du ställer in direkt federation med en partner IdP kan nya gästanvändare från domänen använda sina egna IdP-hanterade organisationskonto för att logga in på Azure AD-klienten och börja samarbeta med dig. Det finns inget behov av gästanvändaren att skapa en separat Azure AD-konto.
> [!NOTE]
> Dirigera federation gästanvändare måste logga in med en länk som innehåller klient-kontext (till exempel `https://myapps.microsoft.com/?tenantid=<tenant id>` eller `https://portal.azure.com/<tenant id>`, eller när det gäller en verifierad domän `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Direktlänkar till program och resurser fungerar också så länge som de innehåller klient-kontext. Direct federation användare är för närvarande inte att logga in med vanliga slutpunkter som har ingen klient-kontext. Till exempel `https://myapps.microsoft.com`, `https://portal.azure.com`, eller `https://teams.microsoft.com` resulterar i ett fel.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>När autentiseras en gästanvändare med direct federation?
När du har konfigurerat direkt federation med en organisation kan autentiseras alla nya gästanvändare du bjuder in med hjälp av direkt federation. Det är viktigt att Observera att ställa in direkt federation inte ändra autentiseringsmetod för gästanvändare som har redan utnyttjat en inbjudan från dig. Här följer några exempel:
 - Om gästanvändare har redan utnyttjat inbjudningar från dig och du därefter konfigurera direkt federation med deras organisation, kommer dessa gästanvändare fortsätta att använda samma autentiseringsmetoden som de används innan du konfigurerar direkt federation.
 - Om du ställer in direkt federation med en partnerorganisation och bjuda in gästanvändare och därefter partnerorganisationen senare flyttas till Azure AD, fortsätter gästanvändare som har redan utnyttjat inbjudningar att använda direct federation, så länge direkt Det finns Federation-princip i din klient.
 - Om du tar bort direkt federation med en partnerorganisation går alla gästanvändare som använder direkt federation inte att logga in.

Du kan uppdatera en gästanvändare autentiseringsmetod genom att ta bort användarkontot från din katalog och reinviting dem i något av dessa scenarier.

Direct federation är kopplad till namnområden för domäner, till exempel contoso.com och fabrikam.com. När en konfiguration för direct federation med AD FS eller en tredje parts IDP: N, associera organisationer namnområden för en eller flera domäner till dessa IDP: er. 

## <a name="end-user-experience"></a>Slutanvändarens upplevelse 
Med direkt federation gästanvändare logga in på Azure AD-klienten med sina egna organisationskonto. När de har åtkomst till delade resurser och uppmanas att logga in dirigeras direkt federation om till deras IDP: N. Efter inloggningen lyckas, kommer de tillbaka till Azure AD för att få åtkomst till resurser. Dirigera federation användarnas uppdateringstoken är giltiga i 12 timmar den [standard längden för genomströmning uppdateringstoken](../develop/active-directory-configurable-token-lifetimes.md#exceptions) i Azure AD. Om federerade IDP: N har enkel inloggning aktiverat kan användaren får enkel inloggning och ser inte någon inloggning visas efter den inledande autentiseringen.

## <a name="limitations"></a>Begränsningar

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-kontrollerade domäner i Azure AD
Dirigera federation tillåts endast för domäner som är ***inte*** DNS verifierats i Azure AD. Direct federation är tillåtna för ohanterade (e-postkontrollerad eller ”viral”) Azure AD-klienter eftersom de inte är DNS-verifieras.
### <a name="authentication-url"></a>Autentiserings-URL
Direct federation tillåts endast för principer där autentisering URL-domän matchar måldomänen eller där autentiserings-URL är en av de här tillåtna identitetsleverantörer (den här listan kan komma att ändras):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Till exempel när du konfigurerar direkt federation för **fabrikam.com**, autentiserings-URL `https://fabrikam.com/adfs` skickar verifieringen. En värd i samma domän kommer även skicka, till exempel `https://sts.fabrikam.com/adfs`. Dock autentiserings-URL `https://fabrikamconglomerate.com/adfs` eller `https://fabrikam.com.uk/adfs` för samma domän som inte skickar.

### <a name="signing-certificate-renewal"></a>Signering certifikatförnyelse
Om du anger metadata-URL i providern Identitetsinställningar förnyas automatiskt Azure AD signeringscertifikatet när den upphör att gälla. Men går om certifikatet har roterats av någon anledning innan den upphör att gälla, eller om du inte anger en URL för metadata, Azure AD inte att förnya den. I det här fallet måste du uppdatera signeringscertifikatet manuellt.
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="can-i-set-up-direct-federation-with-an-unmanaged-email-verified-tenant"></a>Kan jag konfigurera direkt federation med en ohanterad klient (e-postkontrollerad)? 
Ja. Om domänen inte har verifierats och klienten inte har genomgått en [adminövertagande](../users-groups-roles/domains-admin-takeover.md), du kan ställa in direkt federation. Ohanterade eller e-postkontrollerad klienter skapas när en användare redeems en B2B-inbjudan eller utför en självbetjäningsregistrering för Azure AD med hjälp av en domän som för närvarande inte finns. Du kan ställa in direkt federation med dessa domäner. Om du försöker konfigurera direkt federation med en DNS-verifierad domän i Azure portal eller via PowerShell, visas ett fel.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Om både direct federation och e-post engångskod verifiering aktiveras vilken metod du företräde?
När en direkt federation upprättas med en partnerorganisation, har företräde framför e-engångskod autentisering för nya gästanvändare från organisationen. Om en gästanvändare in inbjudan med engångskod autentisering innan du konfigurerar direkt federation, fortsätter de att använda autentisering med engångslösenord. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Dirigera federation adress inloggningsproblem på grund av en delvis synkroniserade innehavare?
Nej, den [e-engångskod](one-time-passcode.md) funktionen ska användas i det här scenariot. En ”delvis synkroniserad innehavare” som refererar till en partner Azure AD-klient där lokala användaridentiteter fullständigt inte synkroniserats till molnet. Gäst vars identitet ännu inte finns i molnet men som försöker lösa in dina B2B-inbjudan kommer inte att kunna logga in. Funktionen engångskod skulle låta den här gästen för att logga in. Direct federation-funktionen behandlar scenarier där gästen har sina egna IdP-hanterade organisationskonto, men organisationen har inga Azure AD-närvaro alls.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Steg 1: Konfigurera den partnerorganisation identitetsprovider
Partnerorganisationen måste först konfigurera sina identitetsprovider med begärda anspråk och förtroenden för förlitande part. 

> [!NOTE]
> För att visa hur du konfigurerar en identitetsprovider för direkta federation kan använder vi Active Directory Federation Services (AD FS) som ett exempel. Finns i artikeln [konfigurera direkt federation med AD FS](direct-federation-adfs.md), vilket ger exempel på hur du konfigurerar AD FS som en SAML 2.0 eller WS-Fed identitetsprovider som förberedelse inför direkt federation.

### <a name="saml-20-configuration"></a>SAML 2.0-konfiguration

Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder SAML-protokoll med specifika krav som anges nedan. Mer information om hur du konfigurerar ett förtroende mellan din SAML-identitetsprovider och Azure AD finns i [använda en SAML 2.0-identitetsprovider (IdP) för enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Obs måldomänen för direkta federation får inte vara DNS-verifierats i Azure AD. URL-domän för autentisering måste matcha måldomänen eller så måste vara domänen för en identitetsprovider som är tillåtna. Se den [begränsningar](#limitations) information. 

#### <a name="required-saml-20-attributes-and-claims"></a>Obligatoriska attribut som SAML 2.0 och anspråk
I följande tabeller visas kraven för specifika attribut och anspråk som måste konfigureras på tredjeparts identitetsprovider. Om du vill konfigurera direkt federation tas följande attribut emot i SAML 2.0-svar från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till online säkerhetstokentjänsten XML-fil eller genom att ange dem manuellt.

Obligatoriska attribut för SAML 2.0-svar från IdP:

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IDP: N, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |


Begärda anspråk för SAML 2.0-token som utfärdats av IDP: N:

|Attribut  |Värde  |
|---------|---------|
|NameID-Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfiguration 
Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder protokollet WS-Fed med vissa krav som anges ovan. För närvarande har två WS-Fed providrar testats för kompatibilitet med Azure AD inkluderar AD FS och Shibboleth. Mer information om hur du upprättar ett förlitande partsförtroende mellan en kompatibel WS-Fed-provider med Azure AD finns i ”STS-Integration med WS-protokoll” tillgängliga i den [Docs Provider-kompatibilitet i Azure AD Identity](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Måldomänen för direkta federation får inte vara DNS-verifierats i Azure AD. URL-domän för autentisering måste matcha måldomänen eller domänen för en identitetsprovider som är tillåtna. Se den [begränsningar](#limitations) information. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Obligatoriska WS-Fed attribut och anspråk

I följande tabeller visas kraven för specifika attribut och anspråk som måste konfigureras på tredje parts WS-Fed identitetsprovidern. Om du vill konfigurera direkt federation tas följande attribut emot i WS-Fed meddelandet från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till online säkerhetstokentjänsten XML-fil eller genom att ange dem manuellt.

Obligatoriska attribut i WS-Fed meddelandet från IdP:
 
|Attribut  |Värde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IDP: N, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Begärda anspråk för WS-Fed token som utfärdats av IDP: N:

|Attribut  |Värde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Steg 2: Konfigurera direkt federation i Azure AD 
Därefter får du konfigurera federation med den identitetsprovider som är konfigurerade i steg 1 i Azure AD. Du kan använda antingen Azure AD-portalen eller PowerShell. Det kan ta 5 – 10 minuter innan principen direkt federation träder i kraft. Under denna tid försök inte att lösa in inbjudan för direkta federation-domän. Det krävs följande attribut:
- Utfärdar-URI för partner IdP
- Passiv autentisering slutpunkten för partner IDP: N (endast https stöds)
- Certifikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Konfigurera federation direkt i Azure AD-portalen

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisationens relationer**.
3. Välj **identitetsprovidrar**, och välj sedan **nya SAML/WS-Fed IdP**.

    ![Skärmbild som visar knappen för att lägga till en ny SAML eller WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. På den **nya SAML/WS-Fed IdP** sidan under **identitet protokoll**väljer **SAML** eller **WS-FED**.

    ![Skärmbild som visar parsa knappen på sidan SAML eller WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Ange ditt partnerföretag domännamn, som är mål domännamnet för direkta federation
6. Du kan överföra en metadatafil för att fylla i metadata-information. Om du väljer att ange metadata manuellt måste du ange följande information:
   - Domännamnet för partner IdP
   - Entitets-ID för partner IdP
   - Passiva Begärarens slutpunkt för partner IdP
   - Certifikat
   > [!NOTE]
   > Metadata-URL är valfritt, men vi rekommenderar den. Om du anger metadata-URL kan kan Azure AD förnyas automatiskt signeringscertifikatet när den upphör att gälla. Om certifikatet har roterats av någon anledning innan den upphör att gälla eller om du inte anger en URL för metadata, går Azure AD inte att förnya den. I det här fallet måste du uppdatera signeringscertifikatet manuellt.

7. Välj **Spara**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Att konfigurera direkt federation i Azure AD med hjälp av PowerShell

1. Installera den senaste versionen av Azure AD PowerShell för Graph-modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Om du behöver detaljerade anvisningar Snabbstart för att lägga till en gästanvändare innehåller avsnittet [installera den senaste modulen AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
1. Logga in med det hanterade kontot för Global administratör i Kommandotolken logga in. 
2. Kör följande kommandon och Ersätt värdena från filen federation metadata. För AD FS-servern och Okta federation filen är federationmetadata.xml, till exempel: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Steg 3: Testa direct federation i Azure AD
Testa nu direkt federation inställningen genom att bjuda in en ny B2B-gästanvändare. Mer information finns i [användare i Lägg till Azure AD B2B-samarbetet i Azure-portalen](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hur gör jag för att redigera en direkt federationsrelation?

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisationens relationer**.
3. Välj **identitetsprovidrar**
4. Under **SAML/WS-Fed identitetsleverantörer**, väljer du en provider.
5. Uppdatera värden i informationsfönstret för identitets-provider.
6. Välj **Spara**.


## <a name="how-do-i-remove-direct-federation"></a>Hur tar jag bort direkt federation?
Du kan ta bort din direkt federation-konfiguration. Om du gör inte direkt federation gästanvändare som har redan utnyttjat sina inbjudningar att logga in. Men du kan ge dem åtkomst till dina resurser igen genom att ta bort dem från katalogen och reinviting dem. Ta bort direkt federation med en identitetsprovider i Azure AD-portalen:

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisationens relationer**.
3. Välj **identitetsprovidrar**.
4. Välj identitetsprovidern och välj sedan **ta bort**. 
5. Välj **Ja** att bekräfta borttagningen. 

Ta bort direkt federation med en identitetsprovider med hjälp av PowerShell:
1. Installera den senaste versionen av Azure AD PowerShell för Graph-modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
3. Logga in med det hanterade kontot för Global administratör i Kommandotolken logga in. 
4. Ange följande kommando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
