---
title: Hur du skapar en app som kan logga in alla Azure AD-användare
description: Visar hur du skapar ett program med flera innehavare som kan logga in en användare från en Azure Active Directory-klient.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu, elisol
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44feaecd42a8c3ce0ac0c712aa27b2480fd2a486
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806937"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Anvisningar: Logga in alla Azure Active Directory-användare med programmönstret för flera innehavare

Om du erbjuder en programvara som en tjänst (SaaS) till många organisationer, kan du konfigurera ditt program att godkänna inloggningar från alla Azure Active Directory (Azure AD)-klient. Den här konfigurationen kallas *gör ditt program flera innehavare*. Användare i alla Azure AD-klient kommer att kunna logga in till programmet efter medgivandedialogen använda sitt konto med ditt program. 

Om du har ett befintligt program som har ett eget konto eller har stöd för andra typer av inloggningar från andra molnleverantörer, är att lägga till Azure AD-inloggningen från en klient enkelt. Registrera din app, Lägg till inloggning kod via OAuth2, OpenID Connect eller SAML och sätta bara en [knappen ”Logga in med Microsoft”] [ AAD-App-Branding] i ditt program.

> [!NOTE] 
> Den här artikeln förutsätter att du redan är bekant med att skapa en enskild klient-program för Azure AD. Om du inte starta med någon av Snabbstart på den [developer guide startsidan][AAD-Dev-Guide].

Det finns fyra enkla steg för att konvertera ditt program i en app för flera innehavare av Azure AD:

1. [Uppdatera din programregistrering om du vill att flera innehavare](#update-registration-to-be-multi-tenant)
2. [Uppdatera koden för att skicka begäranden till den/Common slutpunkt](#update-your-code-to-send-requests-to-common)
3. [Uppdatera koden för att hantera flera utfärdarvärden](#update-your-code-to-handle-multiple-issuer-values)
4. [Förstå användare och administratör medgivande och göra lämplig kodändringar](#understand-user-and-admin-consent)

Låt oss titta på varje steg i detalj. Du kan även gå direkt till [den här listan med flera innehavare exempel][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Uppdatera registrering om du vill att flera innehavare

Som standard är enskild klient i web app/API registreringar i Azure AD. Du kan göra din registrering för flera innehavare genom att söka efter den **flera innehavare** växla den **egenskaper** rutan i din programregistrering i den [Azure-portalen] [ AZURE-portal] och ange värdet till **Ja**.

Innan ett program kan göras med flera innehavare, kräver Azure AD App-ID-URI för programmet som ska vara globalt unikt. App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. För ett program för en enskild klientorganisation räcker det att app-ID-URI är unikt i den klientorganisationen. För ett program för flera klientorganisationer måste den vara globalt unikt så att Azure AD kan hitta programmet bland alla klientorganisationer. Global unikhet framtvingas genom att det krävs att app-ID-URI har ett värdnamn som matchar en verifierad domän i Azure AD-klientorganisationen. 

Appar som har skapats via Azure-portalen har ett globalt unikt Appidentitets-URI på skapa appar som standard, men du kan ändra det här värdet. Till exempel om namnet på din klient har contoso.onmicrosoft.com och sedan en giltig URI för App-ID blir `https://contoso.onmicrosoft.com/myapp`. Om din klientorganisation har en verifierad domän för `contoso.com`, och sedan en giltig App-ID: T URI vore `https://contoso.com/myapp`. Om App-ID-URI inte följer detta mönster misslyckas konfigurationen av ett program som ett program för flera klientorganisationer.

> [!NOTE] 
> Inbyggd klientregistreringar samt [v2.0 program](./active-directory-appmodel-v2-overview.md) flera klientorganisationer som standard. Du behöver inte vidta några åtgärder för att göra dessa programregistreringar flera innehavare.

## <a name="update-your-code-to-send-requests-to-common"></a>Uppdatera koden för att skicka begäranden till/Common

Logga in begäranden skickas till klientens inloggning slutpunkt i en enda klient-program. Till exempel för contoso.onmicrosoft.com slutpunkten skulle vara: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Begäranden som skickas till slutpunkten för en klient kan logga in användare (eller gäster) i den klienten till program i den klienten. 

Med ett program för flera innehavare vet programmet inte direkt vad klient som användaren är från, så att du inte kan skicka begäranden till slutpunkten för en klient. Istället skickas begäranden till en slutpunkt som flerfaldigar över alla Azure AD-klienter: `https://login.microsoftonline.com/common`

När Azure AD tar emot en begäran på den/Common slutpunkten, det loggar du in och, följaktligen identifierar vilken klientorganisation som användaren är från. Den/vanliga slutpunkt som fungerar med alla protokoll för autentisering som stöds av Azure AD:  OpenID Connect, OAuth 2.0, SAML 2.0 och WS-Federation.

Logga in-svaret till programmet sedan innehåller en token som representerar användaren. Utfärdarvärdet i token visar ett program vad användaren är från-klient. När ett svar returneras från den/Common slutpunkt, utfärdarvärdet i token motsvarar användarens klient. 

> [!IMPORTANT]
> Den/det är bara en multiplexor vanliga slutpunkt är inte en klient och är inte en utfärdare,. När du använder/Common måste logiken i din app för att validera token uppdateras för att ta hänsyn till. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Uppdatera koden för att hantera flera utfärdarvärden

Webbprogram och webb-API: er får och validera token från Azure AD. 

> [!NOTE]
> Även om interna klientprogram begära och ta emot token från Azure AD, gör de för att skicka dem till API: er, där de verifieras. Interna program validera inte token och hantera dem som täckande.

Nu ska vi titta på hur ett program verifierar token som tas emot från Azure AD. En enda klient-programmet tar normalt en slutpunktsvärdet som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

och används för att konstruera metadata-URL (i det här fallet OpenID Connect) som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

ladda ned två viktiga uppgifter som används för att validera token: klienten signering nycklar och utfärdarvärdet. Varje Azure AD-klient har en unik utfärdarvärdet i formatet:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

GUID-värdet är där rename-safe-versionen av klient-ID för klienten. Om du väljer länken ovan metadata för `contoso.onmicrosoft.com`, du kan se den här utfärdarvärdet i dokumentet.

När en enskild klient program validerar en token, kontrollerar signaturen för token mot signeringsnycklarna från metadatadokument. Det här testet kan det se till att utfärdarvärdet i token matchar det som hittades i Metadatadokumentet.

Eftersom den/vanliga slutpunkt motsvarar inte en klient och är inte en utfärdare när du undersöker utfärdarvärdet i metadata för/vanliga den har en mallbaserad URL i stället för ett faktiskt värde:

    https://sts.windows.net/{tenantid}/

Därför kan ett program med flera innehavare kan inte verifiera token bara genom att matcha utfärdarvärdet i metadata med den `issuer` värdet i token. Ett program med flera innehavare måste logik för att avgöra vilka utfärdarvärden är giltiga och som inte är baserade på klient-ID delen av utfärdarvärdet. 

Till exempel om ett program med flera innehavare kan bara logga in från specifika klienter som har registrerat sig för service, den måste kontrollerar du antingen utfärdarvärdet eller `tid` anspråksvärde i token för att se till att klienten är i sin lista över prenumeranter. Om ett program med flera innehavare endast behandlar enskilda användare och gör inte några åtkomst beslut baserat på klienter, kan den Ignorera utfärdarvärdet helt och hållet.

I den [flera innehavare exempel][AAD-Samples-MT], utfärdare validering är inaktiverat för att aktivera alla Azure AD-klient att logga in.

## <a name="understand-user-and-admin-consent"></a>Förstå användare och administratör medgivande

För en användare att logga in på ett program i Azure AD, måste programmet vara representerad i användarens klientorganisation. På så sätt kan organisationen för att exempelvis tillämpa unika principer när användare från deras klient loggar in till programmet. Denna registrering är enkel; för en enskild klient-program Det är det som händer när du registrerar program i den [Azure-portalen][AZURE-portal].

För ett program med flera innehavare finns den första registreringen för programmet i Azure AD-klient som används av utvecklaren. När en användare från en annan klient loggar in till programmet för första gången ber dem att godkänna de behörigheter som programmet har begärt i Azure AD. Om de godkänner och sedan en representation av programmet kallas en *tjänstens huvudnamn* skapas i användarens klientorganisation och logga in kan fortsätta. En delegering skapas också i katalogen som innehåller användarens medgivande till programmet. Mer information om programmets program- och ServicePrincipal-objekt, och hur de relaterar till varandra finns [programobjekt och tjänstobjekt][AAD-App-SP-Objects].

![Godkänna en nivå app][Consent-Single-Tier] 

Den här samtycke upplevelsen påverkas av de behörigheter som begärdes av programmet. Azure AD stöder två typer av appspecifika och delegerade behörigheter.

* En delegerad behörighet ger ett program möjligheten att fungera som en inloggad användare för en delmängd av sakerna du kan göra. Du kan till exempel ge ett program delegerad behörighet att läsa den inloggade användaren kalender.
* En appspecifik behörighet beviljas direkt till identiteten för programmet. Du kan till exempel ge ett program appspecifik behörighet att läsa en lista över användare i en klient, oavsett vem som är inloggad i programmet.

Vissa behörigheter kan vara godkänts av en vanlig användare, medan andra kräver en Innehavaradministratör medgivande. 

### <a name="admin-consent"></a>Administratörsmedgivande

Appspecifika behörigheter kräver alltid medgivande av en klientadministratör. Om ditt program begär en appspecifik behörighet och en användare försöker logga in på programmet, visas ett felmeddelande visas om användaren inte kan godkänna.

Vissa delegerade behörigheter kräver också en Innehavaradministratör godkännande. Till exempel kräver möjligheten att skriva tillbaka till Azure AD som den inloggade användaren en Innehavaradministratör medgivande. Som appspecifika behörigheter får ditt program ett fel om en vanlig användare försöker logga in till ett program som begär en delegerad behörighet som kräver administratörens godkännande. Om en behörighet kräver administratörens godkännande bestäms av utvecklaren som publiceras till resursen och finns i dokumentationen för resursen. I dokumentationen för behörigheter för den [Azure AD Graph API] [ AAD-Graph-Perm-Scopes] och [Microsoft Graph API] [ MSFT-Graph-permission-scopes] anger vilka behörigheter kräver administratör ditt medgivande.

Om programmet använder behörigheter som kräver administratörens godkännande, måste du ha en gest, till exempel en knapp eller länk där administratören kan starta åtgärden. Den begäran som programmet skickar för den här åtgärden är vanligt OAuth2/OpenID Connect auktoriseringsbegäran som även innehåller den `prompt=admin_consent` frågesträngparametern. När administratören har godkänt och tjänstens huvudnamn har skapats i kundens klient, efterföljande inloggningsförfrågningar behöver inte den `prompt=admin_consent` parametern. Eftersom administratören har valt behörigheterna som krävs är godtagbara, tillfrågas inga andra användare i klienten om samtycke från den tidpunkten och framåt.

En klientadministratör kan inaktivera möjligheten för vanliga användare att samtycka till program. Om den här funktionen har inaktiverats krävs alltid administratörens godkännande för program som ska användas i klienten. Om du vill testa programmet med slutanvändarens medgivande inaktiverad, du kan hitta configuration-växeln i den [Azure-portalen] [ AZURE-portal] i den **[användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** avsnittet **företagsprogram**.

Den `prompt=admin_consent` parametern kan även användas av program som begär behörighet som inte kräver administratörens godkännande. Ett exempel på när det skulle användas är om programmet kräver en upplevelse där administratör ”registrerar sig” en tid och utan att andra användare tillfrågas om samtycke från den punkten på.

Om ett program kräver administratörens godkännande och en administratör loggar in utan den `prompt=admin_consent` parameter som skickas när administratören godkänner har programmet gäller den **endast för sitt användarkonto**. Vanliga användare kommer fortfarande inte att kunna logga in eller godkänna programmet. Den här funktionen är användbart om du vill ge klientadministratören möjlighet att utforska ditt program innan andra användare åtkomst.

> [!NOTE]
> Vissa program vill ha en upplevelse där vanliga användare kan godkänna först, och senare programmet kan omfatta de behörigheter som administratören och be som kräver administratörens godkännande. Det finns inget sätt att göra det med en v1.0 programregistrering i Azure AD i nuläget; med hjälp av v2.0-slutpunkten kan dock program begär behörighet vid körning i stället för vid tidpunkten för registrering, vilket gör att det här scenariot. Mer information finns i den [v2.0-slutpunkten][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Medgivande och flera nivåer program

Programmet kan ha flera nivåer, var och en representeras av en egen registrering i Azure AD. Till exempel ett internt program som anropar ett webb-API eller ett webbprogram som anropar ett webb-API. I båda dessa fall kan begär klienten (inbyggd app- eller webbapp) behörighet att anropa resource (webb-API). För att klienten ska vara har samtyckt till en kunds klient, måste alla resurser som det begär behörigheter redan finnas i kundens klient. Om det här villkoret inte uppfylls, returneras ett fel som att resursen måste läggas till först i Azure AD.

**Flera nivåer i en enda klient**

Detta kan vara ett problem om logiska programmet består av två eller flera programregistreringar, till exempel en separat klient och resursen. Hur du får resursen till kundens klient första? Azure AD täcker det här fallet genom att aktivera klienten och resurs för att vara samtyckt i ett enda steg. Användaren ser totalt antalet de behörigheter som begärdes av både klient- och resursen på sidan medgivande. Om du vill aktivera det här beteendet resursens programregistrering måste innehålla klientapp-ID som en `knownClientApplications` i dess [programmanifestet][AAD-App-Manifest]. Exempel:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Detta visas i en intern klient på flera nivåer anropa webb-API-exemplet den [relaterat innehåll](#related-content) i slutet av den här artikeln. Följande diagram innehåller en översikt över tillstånd för ett flerskiktat program som har registrerats i en enda klient.

![Godkänna flera nivåer kända klientappar][Consent-Multi-Tier-Known-Client] 

**Flera nivåer i flera klienter**

Ett liknande fall sker om de olika nivåerna av ett program som har registrerats i olika klienter. Till exempel vara fallet för att skapa ett internt klientprogram som anropar ett Office 365 Exchange Online-API. Om du vill utveckla interna programmet och senare att köra i en kunds klient för internt program, måste Exchange Online tjänstens huvudnamn finnas. I det här fallet måste developer och kunden köpa Exchange Online för tjänstens huvudnamn ska kunna skapas i sina klienter. 

När det gäller ett API som skapats av ett annat företag än Microsoft, måste utvecklaren av API: et gör det möjligt för kunderna att godkänna programmet till sina kunders klienter. Det är den rekommenderade designen för utvecklare att bygga API: et så att den kan också fungera som en webbklient för att implementera registreringen från tredje part. Gör så här:

1. Följ de tidigare avsnitt för att säkerställa att API: et implementerar program med flera innehavare/Registreringskod kraven.
2. Förutom att exponera API: er scope/roller kan du kontrollera registreringen ingår i ”logga in och läsa användarprofil” Azure AD-behörighet (ingår som standard).
3. Implementera en inloggning-i/registrering sida i webbklienten och följ de [administratörsmedgivande](#admin-consent) vägledning.
4. När användaren godkänner till programmet, tjänstens huvudnamn och medgivande delegering kopplingarna i deras klienter och internt program kan hämta token för API: et.

Följande diagram innehåller en översikt över tillstånd för ett flerskiktat program som är registrerade i olika klienter.

![Godkänna flerparti flernivåapp][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Återkalla medgivande

Användare och administratörer kan återkalla medgivande till att ditt program när som helst:

* Användare återkalla åtkomst till enskilda program genom att ta bort dem från sina [Åtkomstpanelsappar] [ AAD-Access-Panel] lista.
* Administratörer återkalla åtkomst till program genom att ta bort dem från Azure AD via den [företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) delen av den [Azure-portalen][AZURE-portal].

Om en administratör godkänner ett program för alla användare i en klient, kan användare kan inte återkalla åtkomsten individuellt. Endast administratören kan återkalla åtkomsten och endast för hela programmet.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Program för flera innehavare och cachelagring av åtkomsttoken

Program för flera innehavare kan också hämta åtkomsttoken för att anropa API: er som skyddas av Azure AD. Ett vanligt fel när du använder Active Directory Authentication Library (ADAL) med ett program med flera innehavare är att först begära en token för en användare som använder/Common, ta emot svaret och sedan begära en efterföljande token för den samma användare som också använder/Common. Eftersom svaret från Azure AD inte kommer från en klient/vanliga, ADAL cachelagrar token kommer från klienten. Efterföljande anrop till/Common att hämta en åtkomsttoken för användaren missar cache-post och användaren uppmanas att logga in igen. Kontrollera att efterföljande anrop för en redan inloggad användare görs till klientens slutpunkt för att undvika saknas i cachen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skapar en App som kan logga in en användare från alla Azure AD-klient. Du kan också uppdatera ditt program för att få åtkomst till API: er som exponeras av Microsoft-resurser som Office 365 efter att aktivera enkel inloggning (SSO) mellan din app och Azure AD. På så sätt kan du erbjuda personligt anpassade upplevelser i dina program, till exempel som visar sammanhangsbaserad information till användare, t.ex. deras profilbild eller deras nästa avtalad tid i kalendern. Mer information om att göra API-anrop till Azure AD och Office 365-tjänster som Exchange, SharePoint, OneDrive, OneNote och mer, gå till [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Relaterat innehåll

* [Exempel för program med flera innehavare][AAD-Samples-MT]
* [Riktlinjer för varumärkesanpassning för program][AAD-App-Branding]
* [Programobjekt och tjänstobjekt][AAD-App-SP-Objects]
* [Integrera program med Azure Active Directory][AAD-Integrating-Apps]
* [Översikt över ramverket för medgivande][AAD-Consent-Overview]
* [Behörighetsomfattning för Microsoft Graph API][MSFT-Graph-permission-scopes]
* [Behörighetsomfattning för Azure AD Graph API][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
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
