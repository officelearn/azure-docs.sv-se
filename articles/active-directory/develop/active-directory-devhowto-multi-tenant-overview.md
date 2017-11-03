---
title: "Hur du skapar en app som kan logga in alla Azure AD-användare"
description: "Stegvisa kan anvisningar för att skapa ett program som logga in en användare från en Azure Active Directory-klient, även kallat ett program med flera innehavare."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b409aa762b60c6bed0ee26f4b9fa7c347d9eb997
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Hur du loggar in alla Azure Active Directory (AD)-användare med flera innehavare programmönster
Du kan konfigurera programmet att acceptera inloggningar från Azure AD-klient om du tillhandahåller en programvara som en tjänst för många organisationer.  I Azure AD kallas den här konfigurationen gör dina program med flera innehavare.  Användare i Azure AD-klient kommer att kunna logga in på ditt program efter principer för att använda sitt konto med ditt program.  

Om du har ett befintligt program som har ett eget konto eller andra typer av logga in från andra molntjänstleverantörer av har stöd för är att lägga till Azure AD-inloggning från en klient enkelt. Bara registrera din app, Lägg till kod logga in via OAuth2, OpenID Connect eller SAML och placera en ”logga In med Microsoft”-knappen i ditt program. Klicka på följande mer information om anpassning av ditt program.

[![Logga in knappen][AAD-Sign-In]][AAD-App-Branding]

Den här artikeln förutsätter att du redan är bekant med att skapa en enskild klient program för Azure AD.  Om du inte head tillbaka till den [developer guide webbsida] [ AAD-Dev-Guide] och prova en av våra snabbstarter!

Det finns fyra enkla steg för att konvertera programmet till en app för flera innehavare av Azure AD:

1. Uppdatera registreringen programmet så att flera innehavare
2. Uppdatera din kod för att skicka begäranden till den/Common slutpunkt 
3. Uppdatera din kod för att hantera flera utfärdaren värden
4. Förstå användare och admin medgivande och göra ändringar av lämplig kod

Nu ska vi titta på varje steg i detalj. Du kan även gå direkt till [den här listan med flera innehavare exempel][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Uppdatera registreringen för flera innehavare
Som standard är web app/API registreringar i Azure AD enda innehavare.  Du kan göra registreringen flera innehavare genom att söka efter ”flera innehavare” växel på egenskapssidan för programmet registreringen i den [Azure-portalen] [ AZURE-portal] och ange värdet ”Yes”.

Observera även, innan ett program kan göras med flera innehavare, Azure AD kräver App-ID-URI för programmet som ska vara globalt unika. URI för App-ID är ett sätt som ett program har identifierats i protokollmeddelanden.  Den är tillräcklig för URI: N App-ID är unikt i den klienten för en enskild klient-program.  För ett program med flera innehavare, måste den vara globalt unika så att Azure AD kan hitta programmet på alla klienter.  Globala unika tillämpas genom att kräva att App-ID-URI har ett värdnamn som matchar en verifierad domän till Azure AD-klient.  Till exempel om namnet på din klient har contoso.onmicrosoft.com sedan en giltig URI för App-ID är `https://contoso.onmicrosoft.com/myapp`.  Om din klient har en verifierad domän i `contoso.com`, och sedan en giltig URI för App-ID kan även vara `https://contoso.com/myapp`.  Om App-ID-URI inte följer detta mönster, ange ett program som misslyckas av flera innehavare.

-Klientregistreringar finns flera innehavare som standard.  Du behöver inte göra något för att göra en intern klient programmet registrering flera innehavare.

## <a name="update-your-code-to-send-requests-to-common"></a>Uppdatera din kod för att skicka begäranden till/Common
Inloggning begäranden skickas till klientens inloggning slutpunkt i en enskild klient-program. Till exempel skulle contoso.onmicrosoft.com slutpunkten vara för:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Förfrågningar som skickas till slutpunkten för en klient kan logga in användare (eller gäster) i den klienten till program i den klienten.  Med ett program med flera innehavare vet programmet inte direkt vilken klient som användaren är från, så du inte kan skicka förfrågningar till slutpunkten för en klient.  I stället skickas begäranden till en slutpunkt som multiplexes över alla Azure AD-klienter:

    https://login.microsoftonline.com/common

När Azure AD tar emot en begäran på på/Common slutpunkt, den användaren loggar in och följaktligen identifierar vilken klient som användaren är från.  Den/vanliga slutpunkt som fungerar med alla autentiseringsprotokollet som stöds av Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 och WS-Federation.

Logga in svaret sedan innehåller en token som representerar användaren.  Utfärdaren värdet i token som talar om ett program vilken klient som användaren är från.  När ett svar returneras från den/Common slutpunkt utfärdaren värdet i token motsvarar användarens klient.  Det är viktigt att Observera de/Common slutpunkt är inte en klient och är inte en utfärdare, är det bara en multiplexor.  När du använder/Common måste logiken i ditt program för att validera token uppdateras för att ta hänsyn till. 

Som tidigare nämnts kan bör program med flera klienter dessutom en konsekvent inloggningsupplevelse för användare, efter anpassning riktlinjer Azure AD-programmet. Klicka på följande mer information om anpassning av ditt program.

[![Logga in knappen][AAD-Sign-In]][AAD-App-Branding]

Låt oss ta en titt på användning av den/Common slutpunkt och implementeringen koden i detalj.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Uppdatera din kod för att hantera flera utfärdaren värden
Webbprogram och webb-API: er får och validera token från Azure AD.  

> [!NOTE]
> Inbyggda klientprogram begära och ta emot token från Azure AD, gör de det om du vill skicka dem till API: er, där de verifieras.  Interna program validera inte token och hantera dem som täckande.
> 
> 

Nu ska vi titta på hur ett program validerar token tas emot från Azure AD.  En enskild klient-program tar normalt en slutpunktsvärde som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

och använda den för att konstruera en URL för tjänstmetadata (i det här fallet OpenID Connect) som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Hämta två viktiga delar av information som används för att validera token: klienten signering nycklar och utfärdaren värde.  Varje Azure AD-klient har ett unikt utfärdaren värde i formatet:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

GUID-värde är där rename-säkert version av klient-ID för innehavaren.  Om du klickar på länken föregående metadata för `contoso.onmicrosoft.com`, du kan se den här utfärdaren värdet i dokumentet.

När en enskild klient program validerar token, kontrollerar signaturen för token mot Signeringsnycklar från metadata-dokument. Det här testet kan den och kontrollera att värdet utfärdaren i token matchar det som hittades i Metadatadokumentet.

Eftersom den/Common slutpunkten motsvarar inte en klient och är inte en utfärdare när du undersöker utfärdaren värdet i metadata för/vanliga har en mallbaserat URL i stället för ett faktiskt värde:

    https://sts.windows.net/{tenantid}/

Därför kan ett program med flera innehavare kan inte verifiera token genom att bara matchar utfärdaren värdet i metadata med den `issuer` värde i token.  Ett program med flera innehavare behov logik för att bestämma vilka utfärdaren värden är giltiga och är inte, baserat på klienten ID delen av utfärdaren värdet.  

Till exempel om ett program med flera innehavare kan bara logga in från specifika klienter som har registrerat dig för tjänsten, den måste kontrollera värdet utfärdaren eller `tid` anspråksvärde i token för att se till att klient sin lista över prenumeranter.  Om ett program med flera innehavare endast behandlar enskilda användare och inte göra någon åtkomst beslut baserat på klienter, kan den Ignorera utfärdaren värdet helt och hållet.

I exempel flera innehavare i den [relaterade innehåll](#related-content) avsnittet i slutet av den här artikeln utfärdaren validering är inaktiverat för att aktivera alla Azure AD-klient att logga in.

Nu ska vi titta på användarupplevelsen för användare som loggar in på program med flera klienter.

## <a name="understanding-user-and-admin-consent"></a>Förstå användare och admin-medgivande
Programmet måste vara representerad i användarens klient för en användare att logga in på ett program i Azure AD.  På så sätt kan organisationen exempelvis tillämpa unika principer när användare från deras klient loggar in till programmet.  För en enskild klient programmet är registreringen enkel; Det är det som händer när du registrerar programmet i den [Azure-portalen][AZURE-portal].

För ett program med flera innehavare bor registreringen för programmet i Azure AD-klient som används av utvecklaren.  När en användare från en annan klient loggar in på programmet för första gången, ber dem att godkänna de behörigheter som programmet har begärt Azure AD.  Om de accepterar och sedan en representation av programmet kallas en *tjänstens huvudnamn* skapas i användarens-klient och inloggningen kan fortsätta. En delegering skapas också i katalogen som innehåller användarens medgivande till programmet. Se [program och tjänstens huvudnamn objekt] [ AAD-App-SP-Objects] information om programmets program och ServicePrincipal objekt och hur de relaterar till varandra.

![Samtycka till en nivå-app][Consent-Single-Tier] 

Den här medgivande upplevelse påverkas av de behörigheter som programmet har begärt.  Azure AD stöder två typer av endast app- och delegera behörigheter:

* En delegerad behörighet ger ett program som kan fungera som en inloggad användare för en delmängd av sakerna som användaren kan göra.  Du kan till exempel ge ett program delegerad behörighet att läsa den inloggade användaren kalender.
* En app bara behörigheten direkt identiteten för programmet.  Du kan till exempel ge ett program appen endast behörighet att läsa en lista över användare i en klient oavsett vem som har loggat in till programmet.

Vissa behörigheter kan vara godkänt för av en vanlig användare, medan andra kräver en Innehavaradministratör medgivande. 

### <a name="admin-consent"></a>Admin-medgivande
Behörigheter endast appen kräver alltid en Innehavaradministratör medgivande.  Om ditt program begär en endast app-behörighet och en användare försöker logga in på programmet, visas ett felmeddelande om användaren inte kunna medgivande.

Vissa delegerade behörigheter kräver också en Innehavaradministratör medgivande.  Till exempel kräver möjligheten att skriva tillbaka till Azure AD som den inloggade användaren en Innehavaradministratör medgivande.  Som endast app-behörigheter, om en vanlig användare försöker logga in på ett program som kräver en delegerad behörighet som kräver att administratören medgivande får program ett fel.  Huruvida en behörighet kräver admin samtycke bestäms av utvecklaren som publicerats resursen och kan vara finns i dokumentationen för resursen.  Länkar till avsnitt som beskriver tillgängliga behörigheter för Azure AD Graph API och Microsoft Graph API finns i den [relaterade innehåll](#related-content) i den här artikeln.

Om programmet använder behörigheter som kräver godkännande av administratören, som du behöver ha en gest, till exempel en knapp eller en länk där administratören kan starta åtgärden.  Begäran som programmet skickar den här åtgärden är ett vanligt OAuth2/OpenID Connect auktoriseringsbegäran, men som också innehåller den `prompt=admin_consent` frågesträngparametern.  När administratören har godkänt och tjänstens huvudnamn skapas i kundens klient efterföljande inloggning förfrågningar behöver inte den `prompt=admin_consent` parameter. Eftersom administratören har valt de begärda behörigheterna accepteras, tillfrågas inga andra användare i klienten om samtycke från den tidpunkten och framåt.

Den `prompt=admin_consent` parameter kan även användas av program som begär behörighet som inte kräver godkännande av administratören. Detta görs när applicator kräver en upplevelse som där klient admin ”registrerar sig” en gång och ingen användarna tillfrågas om samtycke från den tidpunkten på.

Om ett program kräver godkännande av administratören och en administratör loggar in men `prompt=admin_consent` parametern skickas inte, administratören har samtycker till programmet **endast för sitt användarkonto**.  Vanliga användare är fortfarande inte att kunna logga in och samtycker till att programmet.  Den här funktionen är användbart om du vill ge innehavaradministratören möjlighet att utforska dina program innan andra användare åtkomst.

En Innehavaradministratör kan inaktivera möjligheten för vanliga användare att samtycka till program.  Om den här funktionen är inaktiverad krävs alltid admin medgivande för programmet som ska ställas in i klienten.  Om du vill testa programmet med vanlig inaktiverat tillstånd hittar du växeln konfiguration i Azure AD-klient konfigurationsavsnittet för den [Azure-portalen][AZURE-portal].

> [!NOTE]
> Vissa program vill en upplevelse där vanliga användare ska kunna medgivande från början och senare programmet kan omfatta administratören och be behörigheterna som kräver godkännande av administratören.  Det går inte att göra det med en enda programregistrering i Azure AD idag.  Kommande Azure AD Resource Manager distribution modellen slutpunkten kan program begär behörighet vid körning, i stället för närvarande registreringen, vilket gör det här scenariot.  Mer information finns i [Azure AD App Model Resource Manager-distributionsmodellen Utvecklarhandbok][AAD-V2-Dev-Guide].
> 
> 

### <a name="consent-and-multi-tier-applications"></a>Medgivande och flera nivåer program
Programmet kan ha flera nivåer, var och en representeras av dess egna registrering i Azure AD.  Till exempel ett enhetligt program som anropar ett webb-API eller ett webbprogram som anropar ett webb-API.  I båda dessa fall begär klienten (inbyggda appen eller webbprogram) behörighet att anropa resurs (webb-API).  För att klienten ska vara har samtyckt till en kund-klient, måste alla resurser som det begär behörighet redan finnas i kundens klient.  Om detta inte är uppfyllt, returneras ett fel som resursen måste läggas till först i Azure AD.

**Flera nivåer i en enskild klient**

Detta kan vara ett problem om tillämpningsprogrammet logiska består av två eller flera program registreringar, till exempel en separat klient och resurs.  Hur du skaffar resursen till kund-klient första?  Azure AD innehåller det här fallet genom att aktivera klient- och resource godkänt i ett enda steg. Användaren ser totalsumman av de behörigheter som begärs av både klienten och resursen på sidan medgivande.  Om du vill aktivera det här beteendet resursens appregistrering måste innehålla klientens App-ID som en `knownClientApplications` i sitt applikationsmanifest.  Exempel:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Den här egenskapen kan uppdateras via resursen [programmets manifest][AAD-App-Manifest]. Detta visas i en-klienten på flera nivåer anropa webb-API-exemplet i den [relaterade innehåll](#related-content) avsnittet i slutet av den här artikeln. Följande diagram ger en översikt av medgivande för en flernivåapp som registrerats i en enskild klient:

![Samtycka till flera nivåer kända-klientappen][Consent-Multi-Tier-Known-Client] 

**Flera nivåer i flera innehavare**

Liknande fall händer om olika nivåer av ett program som har registrerats i olika klienter.  Till exempel vara fallet för att skapa ett enhetligt klientprogram som Office 365 Exchange Online API-anropar.  Exchange Online tjänstens huvudnamn måste vara uppfyllda för att utveckla intern, och senare för det ursprungliga programmet att köras i en kund-klient.  I det här fallet måste utvecklaren och kunden köpa Exchange Online för tjänsten som ska skapas i sina klienter.  

Utvecklaren av API: N måste ge ett sätt för kunderna att godkänna programmet till sina kunder klienter om en API som skapats av en organisation än Microsoft. Den rekommenderade designen är 3 part utvecklare att bygga API: N så att den kan också fungera som en webbklient att implementera registrering:

1. Följ tidigare avsnitt för att säkerställa API implementerar flera innehavare registrering/kod programkrav
2. Förutom exponera scope och roller för API: er, se till att registreringen finns i ”logga in och Läs användarprofil” Azure AD-behörigheten (som standard)
3. Implementera en logga-i/sign-upp sida i Webbklient, efter den [admin medgivande](#admin-consent) vägledning nämnts tidigare 
4. När användaren godkänner till programmet, service principal och samtycke delegering kopplingarna i klientorganisationen och det ursprungliga programmet kan hämta token för API: et

I följande diagram ger en översikt över medgivande för en flernivåapp som registrerats i olika klienter:

![Samtycka till flerparti flernivåapp][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Återkalla medgivande
Användare och administratörer kan upphäva ditt medgivande till ditt program när som helst:

* Användarna återkallar åtkomst till enskilda program genom att ta bort dem från sina [åtkomst panelen program] [ AAD-Access-Panel] lista.
* Administratörer återkalla åtkomst till program genom att ta bort dem från Azure AD med hjälp av Azure AD management avsnitt i den [Azure-portalen][AZURE-portal].

Om en administratör samtycker till ett program för alla användare i en klient, kan användare kan inte återkalla åtkomst individuellt.  Endast administratören kan återkalla åtkomst och endast för hela programmet.

### <a name="consent-and-protocol-support"></a>Medgivande och protokollstöd
Medgivande stöds i Azure AD via OAuth OpenID Connect WS-Federation och SAML-protokoll.  Protokollen SAML och WS-Federation stöder inte den `prompt=admin_consent` parameter, så admin medgivande är endast möjligt via OAuth och OpenID Connect.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Program med flera klienter och cachelagring åtkomst-token
Program med flera klienter kan också hämta åtkomsttoken att anropa API: er som skyddas av Azure AD.  Ett vanligt fel när du använder Active Directory Authentication Library (ADAL) med ett program med flera innehavare, är att först begära en token för en användare som använder/Common, ett svar och sedan begära en efterföljande token för samma användare också använder/Common.  Eftersom svaret från Azure AD inte kommer från en klient/vanliga, ADAL cachelagrar token som från klienten. Efterföljande anrop till/Common att få en åtkomsttoken för användaren missar cache-post och användaren uppmanas att logga in igen.  Kontrollera följande anrop för en redan inloggad användare görs till klientens slutpunkten för att undvika saknas i cacheminnet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du skapar ett program som kan logga in en användare från en Azure Active Directory-klient. Du kan också uppdatera programmet API: er som exponeras av Microsoft-resurser, t.ex. Office 365 för att aktivera enkel inloggning mellan din app och Azure Active Directory. Så kan du erbjuda en anpassad upplevelse i ditt program, till exempel visar kontextuella information till användare som deras profilbild eller deras nästa möte i kalendern. Mer information om API-anrop till Azure Active Directory och Office 365-tjänster som Exchange, SharePoint, OneDrive, OneNote, Planner, Excel och mer finns: [Microsoft Graph API][MSFT-Graph-overview].


## <a name="related-content"></a>Relaterat innehåll
* [Exempel på program för flera innehavare][AAD-Samples-MT]
* [Riktlinjer för varumärkesanpassning för program][AAD-App-Branding]
* [Azure AD-Guide för utvecklare][AAD-Dev-Guide]
* [Program och tjänstens huvudnamn objekt][AAD-App-SP-Objects]
* [Integrera program med Azure Active Directory][AAD-Integrating-Apps]
* [Översikt över medgivande Framework][AAD-Consent-Overview]
* [Behörighetsomfattningen för Microsoft Graph API][MSFT-Graph-permision-scopes]
* [Azure AD Graph API Behörighetsomfattningen][AAD-Graph-Perm-Scopes]

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














