---
title: Skapa appar som loggar in Azure AD-användare
titleSuffix: Microsoft identity platform
description: Visar hur du skapar ett program med flera innehavare som kan logga in en användare från valfri Azure Active Directory-klientorganisation.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: f22ecb13284eaf6fb2a833791b5563351ca19147
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884094"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Så här loggar du in alla Azure Active Directory-användare med programmönstret för flera innehavare

Om du erbjuder ett SaaS-program (Software as a Service) till många organisationer kan du konfigurera programmet så att det accepterar inloggningar från alla Azure Active Directory -klienter (Azure AD). Den här konfigurationen kallas *att göra ditt program flera innehavare*. Användare i alla Azure AD-klienter kan logga in på ditt program efter att ha samtyckt till att använda sitt konto med ditt program.

Om du har ett befintligt program som har ett eget kontosystem, eller stöder andra typer av inloggningar från andra molnleverantörer, är det enkelt att lägga till Azure AD-inloggning från valfri klientorganisation. Registrera bara din app, lägg till inloggningskod via OAuth2, OpenID Connect eller SAML och sätt en ["Logga in med Microsoft"-knappen][AAD-App-Branding] i ditt program.

> [!NOTE]
> Den här artikeln förutsätter att du redan är bekant med att skapa ett enda klientprogram för Azure AD. Om du inte är det, börja med en av snabbstarterna på [utvecklarguidens hemsida][AAD-Dev-Guide].

Det finns fyra enkla steg för att konvertera ditt program till en Azure AD-app för flera innehavare:

1. [Uppdatera din programregistrering så att den blir flera innehavare](#update-registration-to-be-multi-tenant)
2. [Uppdatera koden för att skicka begäranden till slutpunkten /common](#update-your-code-to-send-requests-to-common)
3. [Uppdatera koden för att hantera flera utfärdarvärden](#update-your-code-to-handle-multiple-issuer-values)
4. [Förstå användarens och administratörens samtycke och gör lämpliga kodändringar](#understand-user-and-admin-consent)

Låt oss titta på varje steg i detalj. Du kan också hoppa direkt till exemplet [Skapa ett SaaS-webbprogram för flera innehavare som anropar Microsoft Graph med Azure AD och OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Uppdatera registreringen för att vara flera innehavare

Som standard är webbapp/API-registreringar i Azure AD en enda klient. Du kan göra registreringen flera innehavare genom att hitta knappen **Konton som stöds** i **autentiseringsfönstret** för programregistreringen i [Azure-portalen][AZURE-portal] och ange den för **konton i valfri organisationskatalog**.

Innan ett program kan göras flera innehavare kräver Azure AD app-ID-URI för programmet att vara globalt unikt. App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. För ett program för en enskild klientorganisation räcker det att app-ID-URI är unikt i den klientorganisationen. För ett program för flera klientorganisationer måste den vara globalt unikt så att Azure AD kan hitta programmet bland alla klientorganisationer. Global unikhet framtvingas genom att det krävs att app-ID-URI har ett värdnamn som matchar en verifierad domän i Azure AD-klientorganisationen.

Som standard har appar som skapats via Azure-portalen en globalt unik App ID URI-uppsättning när du skapar appar, men du kan ändra det här värdet. Om namnet på din klientorganisation till exempel var contoso.onmicrosoft.com skulle en giltig `https://contoso.onmicrosoft.com/myapp`App ID URI vara . Om din klient hade `contoso.com`en verifierad domän av , då `https://contoso.com/myapp`en giltig App ID URI skulle också vara . Om App-ID-URI inte följer detta mönster misslyckas konfigurationen av ett program som ett program för flera klientorganisationer.

> [!NOTE]
> Inbyggda klientregistreringar samt [Microsoft-identitetsplattformsprogram](./active-directory-appmodel-v2-overview.md) är som standard flera innehavare. Du behöver inte vidta några åtgärder för att göra dessa programregistreringar flera innehavare.

## <a name="update-your-code-to-send-requests-to-common"></a>Uppdatera koden för att skicka förfrågningar till /common

I ett enda klientprogram skickas inloggningsbegäranden till klientens inloggningsslutpunkt. Till exempel för contoso.onmicrosoft.com slutpunkten skulle vara: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Begäranden som skickas till en klients slutpunkt kan logga in användare (eller gäster) i klienten till program i den klienten.

Med ett program med flera innehavare vet programmet inte på framsidan vilken klientorganisation användaren kommer från, så du kan inte skicka begäranden till en klients slutpunkt. I stället skickas begäranden till en slutpunkt som multiplexerar i alla Azure AD-klienter:`https://login.microsoftonline.com/common`

När Microsofts identitetsplattform tar emot en begäran på /common-slutpunkten signerar den användaren i och identifierar därför vilken klientorganisation användaren kommer ifrån. Den /common slutpunkten fungerar med alla autentiseringsprotokoll som stöds av Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 och WS-Federation.

Inloggningssvaret till programmet innehåller sedan en token som representerar användaren. Utfärdarvärdet i token talar om för ett program vilken klient användaren kommer från. När ett svar returneras från den /common slutpunkten motsvarar utfärdarvärdet i token användarens klientorganisation.

> [!IMPORTANT]
> Den /common slutpunkten är inte en klient och är inte en utfärdare, det är bara en multiplexer. När du använder /common måste logiken i ditt program för att validera token uppdateras för att ta hänsyn till detta.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Uppdatera koden för att hantera flera utfärdarvärden

Webbprogram och webb-API:er får och validerar token från Microsofts identitetsplattform.

> [!NOTE]
> Medan inbyggda klientprogram begär och tar emot token från Microsofts identitetsplattform, gör de det för att skicka dem till API:er, där de valideras. Inbyggda program validerar inte token och måste behandla dem som ogenomskinliga.

Låt oss titta på hur ett program validerar token som tas emot från Microsoft identity platform. Ett enda klientprogram tar normalt ett slutpunktsvärde som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

och använder den för att konstruera en metadata-URL (i det här fallet OpenID Connect) som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

för att hämta två kritiska delar av information som används för att validera token: klientens signeringsnycklar och utfärdarvärde. Varje Azure AD-klient har ett unikt utfärdarvärde för formuläret:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

där GUID-värdet är den namnbytessäkra versionen av klient-ID för klienten. Om du väljer länken för `contoso.onmicrosoft.com`föregående metadata för kan du se det här utfärdarvärdet i dokumentet.

När ett enda klientprogram validerar en token kontrollerar det tokens signatur mot signeringsnycklarna från metadatadokumentet. Det här testet gör det möjligt att se till att utfärdarvärdet i token matchar det som hittades i metadatadokumentet.

Eftersom /common-slutpunkten inte motsvarar en klient och inte är en utfärdare, har den en malladress i stället för ett faktiskt värde när du undersöker utfärdarvärdet i metadata för /common:

    https://sts.windows.net/{tenantid}/

Därför kan ett program med flera innehavare inte validera token bara genom `issuer` att matcha utfärdarvärdet i metadata med värdet i token. Ett program med flera innehavare behöver logik för att avgöra vilka utfärdarvärden som är giltiga och vilka som inte baseras på klient-ID-delen av utfärdarvärdet. 

Om ett program med flera innehavare till exempel bara tillåter inloggning från specifika klienter som har registrerat sig `tid` för sin tjänst, måste det kontrollera antingen utfärdarvärdet eller anspråksvärdet i token för att se till att klienten finns i deras lista över prenumeranter. Om ett program med flera innehavare endast behandlar enskilda personer och inte fattar några åtkomstbeslut baserat på klienter, kan det ignorera utfärdarvärdet helt och hållet.

I [exemplen för flera innehavare][AAD-Samples-MT]inaktiveras utfärdarvalidering för att alla Azure AD-klienter ska kunna logga in.

## <a name="understand-user-and-admin-consent"></a>Förstå användarens och administratörens medgivande

För att en användare ska kunna logga in på ett program i Azure AD måste programmet representeras i användarens klientorganisation. Detta gör det möjligt för organisationen att göra saker som att tillämpa unika principer när användare från deras klient logga in på programmet. För ett enda klientprogram är den här registreringen enkel. Det är den som händer när du registrerar programmet i [Azure-portalen][AZURE-portal].

För ett program med flera innehavare finns den första registreringen för programmet i Azure AD-klienten som används av utvecklaren. När en användare från en annan klient loggar in på programmet för första gången ber Azure AD dem att godkänna de behörigheter som begärs av programmet. Om de samtycker skapas en representation av programmet som kallas *tjänsthuvudnamn* i användarens klientorganisation och inloggningen kan fortsätta. En delegering skapas också i katalogen som registrerar användarens samtycke till programmet. Mer information om programmets Application and ServicePrincipal-objekt och hur de relaterar till varandra finns [i Programobjekt och huvudobjekt][AAD-App-SP-Objects]för tjänsten .

![Illustrerar samtycke till ennivåapp][Consent-Single-Tier]

Den här medgivandeupplevelsen påverkas av de behörigheter som begärs av programmet. Microsofts identitetsplattform stöder två typer av behörigheter, endast appar och delegerade.

* En delegerad behörighet ger ett program möjlighet att fungera som en inloggad användare för en delmängd av de saker som användaren kan göra. Du kan till exempel ge ett program delegerad behörighet att läsa den inloggade användarens kalender.
* En behörighet endast för appar beviljas direkt till programmets identitet. Du kan till exempel ge ett program behörighet endast för appen att läsa listan över användare i en klient, oavsett vem som är inloggad på programmet.

Vissa behörigheter kan godkännas av en vanlig användare, medan andra kräver en klientadministratörs medgivande. 

### <a name="admin-consent"></a>Administratörsmedgivande

Appspecifika behörigheter kräver alltid medgivande av en klientadministratör. Om ditt program begär en behörighet endast för appar och en användare försöker logga in på programmet visas ett felmeddelande om att användaren inte kan medgivande.

Vissa delegerade behörigheter kräver också en klientadministratörs medgivande. Möjligheten att skriva tillbaka till Azure AD som inloggad användare kräver till exempel en klientadministratörs medgivande. Precis som behörigheter endast för appar, får ditt program ett fel om en vanlig användare försöker logga in på ett program som begär en delegerad behörighet som kräver administratörsmedgivande. Om en behörighet kräver administratörsgodkännande bestäms av utvecklaren som publicerade resursen och finns i dokumentationen för resursen. Behörighetsdokumentationen för [Microsoft Graph API][MSFT-Graph-permission-scopes] anger vilka behörigheter som kräver administratörsgodkännande.

Om ditt program använder behörigheter som kräver administratörsmedgivande måste du ha en gest, till exempel en knapp eller länk där administratören kan initiera åtgärden. Begäran som programmet skickar för den här åtgärden är den vanliga OAuth2/OpenID Connect-auktoriseringsbegäran som även innehåller `prompt=admin_consent` frågesträngparametern. När administratören har samtyckt och tjänstens huvudnamn har skapats i kundens klientorganisation behöver efterföljande inloggningsbegäranden inte parametern. `prompt=admin_consent` Eftersom administratören har bestämt att de begärda behörigheterna är godtagbara uppmanas inga andra användare i klienten att godkännas från den punkten.

En klientadministratör kan inaktivera möjligheten för vanliga användare att samtycka till program. Om den här funktionen har inaktiverats krävs alltid administratörens godkännande för program som ska användas i klienten. Om du vill testa ditt program med slutanvändarens medgivande inaktiverat kan du hitta konfigurationsväxeln i [Azure-portalen][AZURE-portal] i avsnittet **[Användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** under **Enterprise-program**.

Parametern `prompt=admin_consent` kan också användas av program som begär behörigheter som inte kräver administratörsgodkännande. Ett exempel på när detta skulle användas är om programmet kräver en upplevelse där klientadministratören "registrerar sig" en gång, och inga andra användare uppmanas att ge sitt samtycke från den tidpunkten.

Om ett program kräver administratörsmedgivande `prompt=admin_consent` och en administratör loggar in utan att parametern skickas, när administratören har gett sitt samtycke till programmet gäller det **endast för deras användarkonto**. Vanliga användare kommer fortfarande inte att kunna logga in eller samtycka till programmet. Den här funktionen är användbar om du vill ge klientadministratören möjlighet att utforska ditt program innan du tillåter andra användare åtkomst.

> [!NOTE]
> Vissa program vill ha en upplevelse där vanliga användare kan medgivande från början, och senare kan programmet involvera administratören och begära behörigheter som kräver administratörsgodkännande. Det finns inget sätt att göra detta med en v1.0-programregistrering i Azure AD idag. Med hjälp av slutpunkten för Microsoft Identity Platform (v2.0) kan program dock begära behörigheter vid körning i stället för vid registreringstid, vilket aktiverar det här scenariot. Mer information finns i [Slutpunkten för Microsoft identity platform][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Samtycke och flernivåprogram

Ditt program kan ha flera nivåer, var och en representeras av sin egen registrering i Azure AD. Till exempel ett inbyggt program som anropar ett webb-API eller ett webbprogram som anropar ett webb-API. I båda dessa fall begär klienten (inbyggd app eller webbapp) behörigheter för att anropa resursen (webb-API). För att klienten ska kunna godkännas till en kunds klientorganisation måste alla resurser som den begär behörigheter till redan finns i kundens klientorganisation. Om det här villkoret inte uppfylls returnerar Azure AD ett fel som resursen måste läggas till först.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Flera nivåer i en enda klient

Detta kan vara ett problem om ditt logiska program består av två eller flera programregistreringar, till exempel en separat klient och resurs. Hur får du resursen till kundens klient först? Azure AD täcker det här fallet genom att aktivera klient och resurs som ska godkännas i ett enda steg. Användaren ser summan av de behörigheter som begärs av både klienten och resursen på medgivandesidan. För att aktivera det här beteendet måste resursens programregistrering `knownClientApplications` inkludera klientens app-ID som ett i [dess programmanifest][AAD-App-Manifest]. Ett exempel:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Detta visas i ett internt webb-API-exempel på flera nivåer i avsnittet [Relaterat innehåll](#related-content) i slutet av den här artikeln. Följande diagram ger en översikt över medgivande för en app på flera nivåer som är registrerad i en enda klient.

![Illustrerar samtycke till flernivåberöjarklientapp][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Flera nivåer i flera klienter

Ett liknande ärende inträffar om de olika nivåerna i ett program registreras i olika klienter. Tänk dig till exempel fallet med att skapa ett inbyggt klientprogram som anropar Office 365 Exchange Online API. Om du vill utveckla det inbyggda programmet och senare för att det inbyggda programmet ska köras i en kunds klientorganisation måste huvudmannen för Exchange Online-tjänsten finnas. I det här fallet måste utvecklaren och kunden köpa Exchange Online för att tjänstens huvudnamn ska skapas i deras klienter.

Om det är ett API som skapats av en annan organisation än Microsoft måste utvecklaren av API:et tillhandahålla ett sätt för sina kunder att godkänna programmet i sina kunders klienter. Den rekommenderade designen är för tredjepartsutvecklaren att bygga API: et så att det också kan fungera som en webbklient för att implementera registrering. Gör så här:

1. Följ de tidigare avsnitten för att säkerställa att API:et implementerar kraven för registrering/kod för flera innehavare.
2. Förutom att exponera API:ets scope/roller kontrollerar du att registreringen innehåller behörigheten "Logga in och läs användarprofil" (tillhandahålls som standard).
3. Implementera en inloggnings-/registreringssida i webbklienten och följ [administratörsmedgivandevägledningen.](#admin-consent)
4. När användaren har medgivandet till programmet skapas tjänstens huvudnamn och delegeringslänkar för godkännande i deras klientorganisation och det inbyggda programmet kan hämta token för API:et.

Följande diagram ger en översikt över medgivande för en app på flera nivåer som är registrerad i olika klienter.

![Illustrerar samtycke till flernivåapp med flera parter][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Återkalla samtycke

Användare och administratörer kan när som helst återkalla samtycke till ditt program:

* Användare återkallar åtkomsten till enskilda program genom att ta bort dem från listan Program i [Åtkomstpanelen.][AAD-Access-Panel]
* Administratörer återkallar åtkomsten till program genom att ta bort dem med hjälp av avsnittet [Enterprise-program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) i [Azure-portalen][AZURE-portal].

Om en administratör godkänner ett program för alla användare i en klient kan användare inte återkalla åtkomsten individuellt. Endast administratören kan återkalla åtkomsten och endast för hela programmet.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Program för flera innehavare och cachelagring av åtkomsttoken

Program med flera innehavare kan också få åtkomsttoken för att anropa API:er som skyddas av Azure AD. Ett vanligt fel när du använder Active Directory Authentication Library (ADAL) med ett program med flera innehavare är att först begära en token för en användare som använder /common, få ett svar och sedan begära en efterföljande token för samma användare som också använder /common. Eftersom svaret från Azure AD kommer från en klient, inte /common, cachelagrar ADAL token som från klienten. Det efterföljande anropet till /common för att hämta en åtkomsttoken för användaren missar cacheposten och användaren uppmanas att logga in igen. För att undvika att cachen saknas kontrollerar du att efterföljande anrop för en redan inloggad användare görs till klientens slutpunkt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar ett program som kan logga in en användare från valfri Azure AD-klientorganisation. När du har aktiverat SSO (Single Sign-On) mellan din app och Azure AD kan du även uppdatera programmet för att komma åt API:er som exponeras av Microsoft-resurser som Office 365. På så sätt kan du erbjuda en personlig upplevelse i ditt program, till exempel visa kontextuell information för användarna, till exempel deras profilbild eller nästa avtalade tider i kalendern. Mer information om hur du gör API-anrop till Azure AD- och Office 365-tjänster som Exchange, SharePoint, OneDrive, OneNote med mera finns i [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Relaterat innehåll

* [Programexempel för flera innehavare](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Varumärkesriktlinjer för program][AAD-App-Branding]
* [Programobjekt och huvudobjekt för tjänsten][AAD-App-SP-Objects]
* [Integrera program med Azure Active Directory][AAD-Integrating-Apps]
* [Översikt över ramverket för samtycke][AAD-Consent-Overview]
* [Behörighetsomfattningar för Microsoft Graph-API][MSFT-Graph-permission-scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://docs.microsoft.com/samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
