---
title: "Förstå Azure Active Directorys programmanifest | Microsoft Docs"
description: "Detaljerad täckning av Azure Active Directory-programmanifestet som representerar ett programs identitet konfiguration i en Azure AD-klient och används för att underlätta OAuth auktorisering, medgivande upplevelse och mycket mer."
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Förstå Azure Active Directory-programmanifestet
Program som integreras med Azure Active Directory (AD) måste vara registrerad med en Azure AD-klient som tillhandahåller en permanent identitet konfiguration för programmet. Den här konfigurationen samråd vid körning, aktivera scenarier som gör att ett program för att flytta ut och broker autentisering/auktorisering via Azure AD. Mer information om Azure AD-programmodell finns i [att lägga till, uppdatera och ta bort ett program] [ ADD-UPD-RMV-APP] artikel.

## <a name="updating-an-applications-identity-configuration"></a>Uppdatera konfigurationen för ett program identitet
Det finns faktiskt flera alternativ för uppdatering av egenskaper på identity-konfigurationen för ett program där olika funktioner och frihetsgrader problem, inklusive följande:

* Den  **[Azure portal] [ AZURE-PORTAL] webbanvändargränssnitt** kan du uppdatera de vanligaste egenskaperna för ett program. Detta är det snabbaste och minst fel felbenägna sättet att uppdatera egenskaperna för ditt program, men ger inte dig fullständig åtkomst till alla egenskaper, t.ex. följande två metoder.
* För mer avancerade scenarier där du behöver uppdatera egenskaper som inte exponeras i den klassiska Azure-portalen, kan du ändra den **programmanifestet**. Detta är fokus för den här artikeln som beskrivs i detalj i nästa avsnitt.
* Det är också möjligt att **skriva ett program som använder den [Graph API] [ GRAPH-API]**  att uppdatera ditt program som kräver mest arbete. Detta kan vara ett bra alternativ om, om du skriver hanteringsprogramvara eller uppdatera programegenskaper regelbundet i obevakat läge.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Med hjälp av applikationsmanifestet för att uppdatera konfigurationen för ett program identitet
Via den [Azure-portalen][AZURE-PORTAL], du kan hantera identitet programkonfigurationen genom att uppdatera applikationsmanifestet med redigeraren infogade manifestet. Du kan också hämta och överföra programmanifestet som en JSON-fil. Inga faktiska filen lagras i katalogen. Programmanifestet är bara en HTTP GET-åtgärd på Azure AD Graph API-program entiteten och överföringen är en korrigering av HTTP-åtgärd på entiteten för programmet.

Därför för att förstå format och egenskaperna för programmanifestet måste du referera Graph API [programmet entiteten] [ APPLICATION-ENTITY] dokumentation. Exempel på uppdateringar som kan utföras om programmets manifest överför:

* **Deklarera behörighetsomfattningen (oauth2Permissions)** visas av ditt webb-API. I avsnittet ”exponera API: er till andra webbprogram” i [integrera program med Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] information om hur du implementerar personifiering med hjälp av oauth2Permissions delegerad behörighet omfång. Som tidigare nämnts programmet Entitetsegenskaper dokumenteras i Graph API [entitetstyper och komplexa typen] [ APPLICATION-ENTITY] referensartikeln, inklusive egenskapen oauth2Permissions som är en mängd av typen [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
* **Deklarera programroller (appRoles) som exponeras av din app**. Programmet entitetens appRoles egenskapen är en mängd av typen [AppRole][APPLICATION-ENTITY-APP-ROLE]. Finns det [rollbaserad åtkomstkontroll i molnprogram med Azure AD] [ RBAC-CLOUD-APPS-AZUREAD] artikel exempel implementering.
* **Deklarera kända klienten program (knownClientApplications)**, vilket gör att du kan koppla logiskt medgivande på de angivna klient program i resursen/webb-API.
* **Begäran om Azure AD för att utfärda medlemskap gruppanspråk** för den inloggade användaren (groupMembershipClaims).  Detta kan också konfigureras för att utfärda anspråk om användarens directory roller medlemskap. Finns det [auktorisering i molnprogram med hjälp av AD-grupper] [ AAD-GROUPS-FOR-AUTHORIZATION] artikel exempel implementering.
* **Ditt program stöder OAuth 2.0 Implicit bevilja** flöden (oauth2AllowImplicitFlow). Den här typen av bevilja flödet används med inbäddade JavaScript-webbsidor eller enda sidan program (SPA). Mer information om implicit auktorisering beviljande finns [förstå OAuth2 implicit bevilja flödet i Azure Active Directory][IMPLICIT-GRANT].
* **Aktivera användning av X509 certifikat som den hemliga nyckeln** (keyCredentials). Finns det [skapa tjänsten och daemon appar i Office 365] [ O365-SERVICE-DAEMON-APPS] och [Developer's guide för autentisering med Azure Resource Manager API] [ DEV-GUIDE-TO-AUTH-WITH-ARM] artiklar för exempel på implementering.
* **Lägg till en ny App-ID URI** för programmet (identifierURIs[]). URI: er för App-ID används för att unikt identifiera ett program i sin Azure AD-klient (eller över flera Azure AD-innehavare för scenarier med flera innehavare när kvalificerat via verifierad domän). De används när du begär behörighet till ett resursprogram eller skaffa en åtkomst-token för en resursprogram. När du uppdaterar det här elementet görs samma uppdatering till motsvarande tjänstens huvudnamn servicePrincipalNames [] samling, som finns i programmets hem-klient.

Applikationsmanifestet innehåller också ett bra sätt att spåra status för programmet registreringen. Eftersom den är tillgänglig i JSON-format kan filen representation kontrolleras i source-kontroll, tillsammans med ditt programs källkod.

## <a name="step-by-step-example"></a>Steg för steg-exempel
Nu kan du gå igenom de steg som krävs för att uppdatera ditt programs identitet konfigurationen med hjälp av programmanifestet. Vi betona något av föregående exempel visar hur du deklarera ett nytt behörighet scope på en resursprogram:

1. Logga in på [Azure Portal][AZURE-PORTAL].
2. När du har autentiserats, väljer du din Azure AD-klient genom att välja den i det övre högra hörnet på sidan.
3. Välj **Azure Active Directory** tillägget från den vänstra navigeringsfönstret och klicka på **App registreringar**.
4. Sök efter programmet som du vill uppdatera i listan och klicka på den.
5. Program-sidan klickar du på **Manifest** att öppna redigeraren infogade manifestet. 
6. Du kan redigera manifestet med den här redigeraren direkt. Observera att manifestet följer schemat för den [programmet entiteten] [ APPLICATION-ENTITY] som vi nämnt tidigare: till exempel antar vi vill implementera/exponera en ny behörighet som kallas ”Employees.Read.All” på vår resursprogram (API), bara och Lägg till ett nytt/sekund element samlingen oauth2Permissions ie:
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    Posten måste vara unikt och därför måste du generera en ny globalt unikt ID (GUID) för den `"id"` egenskapen. I det här fallet eftersom vi har angetts `"type": "User"`, den här behörigheten kan vara godkänt för med ett konto som autentiseras av Azure AD-klient där resursen/API-program är registrerat. Detta ger klienten programmet behörighet att komma åt den för kontots räkning. De beskrivning och visa namnet strängarna används under medgivande och för att visas i Azure-portalen.
6. När du är klar uppdaterar manifestet klickar du på **spara** spara manifestet.  
   
Nu när manifestet sparas, kan du ge en registrerad ansökan klientåtkomst till den nya behörigheten vi lades till ovan. Nu kan du använda Azure portal Webbgränssnittet i stället för att redigera klienten programmanifestet:  

1. Först gå till den **inställningar** klickar du på bladet av klientprogram som du vill lägga till åtkomst till den nya API: T **nödvändiga behörigheter** och välj **väljer en API**.
2. Sedan visas med en lista över registrerade resursprogram-API: er i klienten. Klicka på resursprogram att välja den eller Skriv namnet på programmet sökrutan. När du har hittat programmet, klickar du på **Välj**.  
3. Detta tar du det **Välj behörigheter** sidan som visar listan med behörigheter för program och delegerade behörigheter för resursprogrammet. Välj ny behörighet för att lägga till den begärda listan för klientens behörigheter. Den här nya behörigheten lagras i client-program identitet konfigurationen i egenskapen ”requiredResourceAccess” samling.


Det var allt. Nu körs programmen med sina nya identity-konfigurationen.

## <a name="next-steps"></a>Nästa steg
* Mer information om förhållandet mellan program och tjänstens huvudnamn objekt i ett program finns [program och tjänstens huvudnamn objekt i Azure AD][AAD-APP-OBJECTS].
* Finns det [Azure AD-utvecklare ordlista] [ AAD-DEVELOPER-GLOSSARY] definitioner av vissa grundbegrepp för utvecklare i Azure Active Directory (AD).

Använd kommentaravsnittet nedan för att ge feedback och hjälp oss att förfina och utforma innehållet.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

