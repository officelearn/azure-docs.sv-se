---
title: Direkt federation med en identitetsprovider för B2B - Azure AD
description: Direkt federerar med en SAML- eller WS-Fed-identitetsleverantör så att gästerna kan logga in på dina Azure AD-appar
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050885"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Direkt federation med AD FS och tredjepartsleverantörer för gästanvändare (förhandsversion)
|     |
| --- |
| Direktfederation är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

I den här artikeln beskrivs hur du ställer in direkt federation med en annan organisation för B2B-samarbete. Du kan ställa in direkt federation med alla organisationer vars identitetsprovider (IdP) stöder SAML 2.0- eller WS-Fed-protokollet.
När du konfigurerar direkt federation med en partners IdP kan nya gästanvändare från den domänen använda sitt eget IdP-hanterade organisationskonto för att logga in på din Azure AD-klientorganisation och börja samarbeta med dig. Gästanvändaren behöver inte skapa ett separat Azure AD-konto.
> [!NOTE]
> Direktfederationsgästanvändare måste logga in med `https://myapps.microsoft.com/?tenantid=<tenant id>` en `https://portal.azure.com/<tenant id>`länk som innehåller klientkontexten (till exempel eller , eller när det gäller en verifierad domän, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Direkta länkar till program och resurser fungerar också så länge de innehåller klientkontexten. Direktfederationsanvändare kan för närvarande inte logga in med vanliga slutpunkter som inte har någon klientkontext. Om du `https://myapps.microsoft.com`till `https://portal.azure.com`exempel `https://teams.microsoft.com` använder , eller resulterar det i ett fel.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>När autentiseras en gästanvändare med direkt federation?
När du har konfigurerat direkt federation med en organisation autentiseras alla nya gästanvändare som du bjuder in med direkt federation. Det är viktigt att notera att inrättandet av direkt federation inte ändrar autentiseringsmetoden för gästanvändare som redan har löst in en inbjudan från dig. Här följer några exempel:
 - Om gästanvändare redan har löst in inbjudningar från dig och du sedan har konfigurerat direkt federation med sin organisation, fortsätter gästanvändarna att använda samma autentiseringsmetod som de använde innan du konfigurerade direkt federation.
 - Om du konfigurerar direkt federation med en partnerorganisation och bjuder in gästanvändare, och sedan partnerorganisationen senare flyttar till Azure AD, fortsätter gästanvändarna som redan har löst in inbjudningar att använda direkt federation, så länge som den direkta federationsprincipen i din klientorganisation finns.
 - Om du tar bort direkt federation med en partnerorganisation kan inte alla gästanvändare som använder direktfederation logga in.

I något av dessa scenarier kan du uppdatera en gästanvändares autentiseringsmetod genom att ta bort gästanvändarkontot från katalogen och skicka in dem igen.

Direkt federation är knuten till domännamnsrymd, till exempel contoso.com och fabrikam.com. När organisationer upprättar en direkt federationskonfiguration med AD FS eller ett tredjeparts-IDP associerar de en eller flera domännamnsrymden till dessa IDPs. 

## <a name="end-user-experience"></a>Upplevelse för slutanvändaren 
Med direkt federation loggar gästanvändare in på din Azure AD-klient med sitt eget organisationskonto. När de har åtkomst till delade resurser och uppmanas att logga in omdirigeras direktfederationsanvändare till sin IdP. Efter lyckad inloggning returneras de till Azure AD för att komma åt resurser. Direkt federationsanvändares uppdateringstoken är giltiga i 12 timmar, [standardlängden för vidaregående uppdateringstoken](../develop/active-directory-configurable-token-lifetimes.md#exceptions) i Azure AD. Om den federerade IDP har SSO aktiverat kommer användaren att uppleva SSO och kommer inte att se någon inloggningsfråga efter första autentiseringen.

## <a name="limitations"></a>Begränsningar

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-verifierade domäner i Azure AD
Domänen som du vill federera med får ***inte*** DNS-verifieras i Azure AD. Du får konfigurera direkt federation med ohanterade (e-postverifierade eller "virala") Azure AD-klienter eftersom de inte är DNS-verifierade.

### <a name="authentication-url"></a>URL för autentisering
Direkt federation är endast tillåtet för principer där autentiserings-URL:ens domän matchar måldomänen, eller där autentiserings-URL:en är en av dessa tillåtna identitetsleverantörer (den här listan kan komma att ändras):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

När du till exempel konfigurerar direkt federation för `https://fabrikam.com/adfs` **fabrikam.com**skickar autentiserings-URL:en valideringen. En värd i samma domän kommer `https://sts.fabrikam.com/adfs`också att passera, till exempel . Autentiserings-URL:en `https://fabrikamconglomerate.com/adfs` eller `https://fabrikam.com.uk/adfs` för samma domän går dock inte igenom.

### <a name="signing-certificate-renewal"></a>Signera förnyelse av certifikat
Om du anger metadata-URL:en i identitetsproviderns inställningar förnyas signeringscertifikatet automatiskt när det upphör att gälla. Men om certifikatet roteras av någon anledning före förfallodatumet, eller om du inte tillhandahåller en metadata-URL, kommer Azure AD inte att kunna förnya det. I det här fallet måste du uppdatera signeringscertifikatet manuellt.

### <a name="limit-on-federation-relationships"></a>Begränsa federationsrelationer
För närvarande stöds högst 1 000 federationsrelationer. Denna gräns omfattar både [interna federationer](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) och direkta förbund.

### <a name="limit-on-multiple-domains"></a>Begränsa flera domäner
Vi stöder för närvarande inte direkt federation med flera domäner från samma klient.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kan jag ställa in direkt federation med en domän för vilken en ohanterat (e-postverifierat) klient finns? 
Ja. Om domänen inte har verifierats och klienten inte har genomgått ett [administratörsövertagande](../users-groups-roles/domains-admin-takeover.md)kan du konfigurera direkt federation med den domänen. Ohanterade eller e-postverifierade klienter skapas när en användare löser in en B2B-inbjudan eller utför en självbetjäningsregistrering för Azure AD med hjälp av en domän som för närvarande inte finns. Du kan ställa in direkt federation med dessa domäner. Om du försöker konfigurera direkt federation med en DNS-verifierad domän, antingen i Azure-portalen eller via PowerShell, visas ett fel.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Om direkt federation och e-post engångslösenkod autentisering är båda aktiverade, vilken metod har företräde?
När direkt federation upprättas med en partnerorganisation har den företräde framför e-postautentisering med engångslösenord för nya gästanvändare från den organisationen. Om en gästanvändare löste in en inbjudan med engångslösenkodsautentisering innan du konfigurerade direkt federation fortsätter de att använda engångsautentisering av lösenkod. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Tar direkt federationen upp inloggningsproblem på grund av en delvis synkroniserad hyresrätt?
Nej, [funktionen för engångslösenord för e-post](one-time-passcode.md) ska användas i det här scenariot. En "delvis synkroniserad hyresrätt" refererar till en partner Azure AD-klient där lokala användaridentiteter inte är helt synkroniserade med molnet. En gäst vars identitet ännu inte finns i molnet men som försöker lösa in din B2B-inbjudan kan inte logga in. Engångslösenordsfunktionen gör det möjligt för den här gästen att logga in. Den direkta federationsfunktionen behandlar scenarier där gästen har ett eget IdP-hanterat organisationskonto, men organisationen har ingen Azure AD-närvaro alls.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Steg 1: Konfigurera partnerorganisationens identitetsprovider
Först måste din partnerorganisation konfigurera sin identitetsprovider med de anspråk som krävs och förlitande part förtroenden. 

> [!NOTE]
> För att illustrera hur du konfigurerar en identitetsprovider för direkt federation använder vi AD FS (Active Directory Federation Services) som exempel. Se artikeln [Konfigurera direkt federation med AD FS](direct-federation-adfs.md), som ger exempel på hur du konfigurerar AD FS som en SAML 2.0- eller WS-Fed-identitetsprovider som förberedelse för direkt federation.

### <a name="saml-20-configuration"></a>SAML 2.0-konfiguration

Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder SAML-protokollet med specifika krav som anges nedan. Mer information om hur du konfigurerar ett förtroende mellan din SAML-identitetsprovider och Azure AD finns i [Använda en SAML 2.0 Identity Provider (IdP) för enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Måldomänen för direkt federation får inte DNS-verifieras på Azure AD. URL-domänen för autentisering måste matcha måldomänen eller vara domänen för en tillåten identitetsprovider. Mer information finns i avsnittet [Begränsningar.](#limitations) 

#### <a name="required-saml-20-attributes-and-claims"></a>Obligatoriska SAML 2.0-attribut och anspråk
Följande tabeller visar krav på specifika attribut och anspråk som måste konfigureras hos identitetsprovidern från tredje part. För att konfigurera direkt federation måste följande attribut tas emot i SAML 2.0-svaret från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till XML-filen för onlinesäkerhetstokentjänsten eller genom att ange dem manuellt.

Obligatoriska attribut för SAML 2.0-svaret från IdP:

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Emittenten URI för partner-IdP, till exempel`http://www.example.com/exk10l6w90DHM0yi...`         |


Obligatoriska anspråk för SAML 2.0-token som utfärdats av IdP:

|Attribut  |Värde  |
|---------|---------|
|NameID-format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed-konfiguration 
Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder WS-Fed-protokollet med vissa specifika krav som anges nedan. För närvarande har de två WS-Fed-leverantörerna testats för kompatibilitet med Azure AD inkluderar AD FS och Shibboleth. Mer information om hur du upprättar ett förtroende för en förlitande part mellan en WS-Fed-kompatibel provider med Azure AD finns i "STS Integration Paper using WS Protocols" som finns i [Kompatibilitetsdokumenten för Azure AD Identity Provider](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Måldomänen för direkt federation får inte DNS-verifieras på Azure AD. URL-domänen för autentisering måste matcha antingen måldomänen eller domänen för en tillåten identitetsprovider. Mer information finns i avsnittet [Begränsningar.](#limitations) 

#### <a name="required-ws-fed-attributes-and-claims"></a>Obligatoriska WS-Fed-attribut och anspråk

Följande tabeller visar krav på specifika attribut och anspråk som måste konfigureras hos WS-Fed-identitetsprovidern från tredje part. För att konfigurera direkt federation måste följande attribut tas emot i WS-Fed-meddelandet från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till XML-filen för onlinesäkerhetstokentjänsten eller genom att ange dem manuellt.

Obligatoriska attribut i WS-Fed-meddelandet från IdP:
 
|Attribut  |Värde  |
|---------|---------|
|PassivRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Emittenten URI för partner-IdP, till exempel`http://www.example.com/exk10l6w90DHM0yi...`         |

Obligatoriska anspråk för WS-Fed-token som utfärdats av IdP:

|Attribut  |Värde  |
|---------|---------|
|OföränderligtID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Steg 2: Konfigurera direkt federation i Azure AD 
Därefter konfigurerar du federation med identitetsprovidern som konfigurerats i steg 1 i Azure AD. Du kan använda antingen Azure AD-portalen eller PowerShell. Det kan ta 5-10 minuter innan den direkta federationspolicyn träder i kraft. Försök inte lösa in en inbjudan för den direkta federationsdomänen under den här tiden. Följande attribut krävs:
- Emittent URI av partner-IdP
- Slutpunkt för passiv autentisering av partner-IdP (endast https stöds)
- Certifikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Så här konfigurerar du direktfederation i Azure AD-portalen

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **Organisationsrelationer**.
3. Välj **Identitetsleverantörer**och välj sedan **Ny SAML/WS-Fed IdP**.

    ![Skärmbild som visar knappen för att lägga till ett nytt SAML- eller WS-Fed-IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. På sidan **Ny SAML/WS-Fed IdP** väljer du **SAML** eller **WS-FED**under **Identity provider-protokoll**.

    ![Skärmbild som visar knappen parsa på SAML- eller WS-Fed-IdP-sidan](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Ange din partnerorganisations domännamn, som kommer att vara måldomännamnet för direkt federation
6. Du kan ladda upp en metadatafil för att fylla i metadatainformation. Om du väljer att mata in metadata manuellt anger du följande information:
   - Domännamn för partner-IDP
   - Enhets-ID för partner-ID
   - Slutpunkt för passiv requestor för partner-IdP
   - Certifikat
   > [!NOTE]
   > Metadata URL är valfritt, men vi rekommenderar det starkt. Om du anger metadata-URL:en kan Azure AD automatiskt förnya signeringscertifikatet när det upphör att gälla. Om certifikatet roteras av någon anledning före förfallodatumet eller om du inte anger en metadata-URL, kan Azure AD inte förnya det. I det här fallet måste du uppdatera signeringscertifikatet manuellt.

7. Välj **Spara**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Så här konfigurerar du direktfederation i Azure AD med PowerShell

1. Installera den senaste versionen av Azure AD PowerShell for Graph-modulen[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Om du behöver detaljerade steg innehåller snabbstarten för att lägga till en gästanvändare avsnittet [Installera den senaste AzureADPreview-modulen](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
1. Logga in med det hanterade globala administratörskontot vid inloggningsprompten. 
2. Kör följande kommandon och ersätt värdena från federationsmetadatafilen. För AD FS Server och Okta är federationsfilen federationmetadata.xml, till exempel: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Steg 3: Testa direkt federation i Azure AD
Testa nu din direktfederationsinställning genom att bjuda in en ny B2B-gästanvändare. Mer information finns [i Lägga till Azure AD B2B-samarbetsanvändare i Azure-portalen](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hur redigerar jag en direkt federationsrelation?

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **Organisationsrelationer**.
3. Välj **Identitetsleverantörer**
4. Välj leverantören under **SAML/WS-Fed-identitetsleverantörer.**
5. Uppdatera värdena i informationsfönstret för identitetsprovidern.
6. Välj **Spara**.


## <a name="how-do-i-remove-direct-federation"></a>Hur tar jag bort direkt federation?
Du kan ta bort din direktfederationsinställning. Om du gör det kan inte direktfederationsgästanvändare som redan har löst in sina inbjudningar logga in. Men du kan ge dem tillgång till dina resurser igen genom att ta bort dem från katalogen och skicka in dem igen. Så här tar du bort direkt federation med en identitetsprovider i Azure AD-portalen:

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **Organisationsrelationer**.
3. Välj **Identitetsleverantörer**.
4. Välj identitetsprovidern och välj sedan **Ta bort**. 
5. Välj **Ja** för att bekräfta borttagningen. 

Så här tar du bort direkt federation med en identitetsprovider med PowerShell:
1. Installera den senaste versionen av Azure AD PowerShell for Graph-modulen[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
3. Logga in med det hanterade globala administratörskontot vid inloggningsprompten. 
4. Ange följande kommando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
