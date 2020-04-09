---
title: Hur och varför program läggs till i Azure AD
titleSuffix: Microsoft identity platform
description: Vad innebär det för ett program som ska läggas till i Azure AD och hur kommer de dit?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 01ea22af472877abe34236ec82a7750eccfcdfb9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884281"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hur och varför program läggs till i Azure AD

Det finns två representationer av program i Azure AD:

* [Programobjekt](app-objects-and-service-principals.md#application-object) - Även om det finns [undantag](#notes-and-exceptions)kan programobjekt betraktas som definitionen av ett program.
* [Tjänsthuvudnamn](app-objects-and-service-principals.md#service-principal-object) - Kan betraktas som en instans av ett program. Tjänstobjekt refererar i allmänhet till ett programobjekt och ett programobjekt kan refereras av flera tjänsthuvudnamn över kataloger.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Vad är programobjekt och var kommer de ifrån?

Du kan hantera [programobjekt](app-objects-and-service-principals.md#application-object) i Azure-portalen via [appregistreringsupplevelsen.](https://aka.ms/appregistrations) Programobjekt beskriver programmet till Azure AD och kan betraktas som definitionen av programmet, vilket gör att tjänsten vet hur token ska utfärdas till programmet baserat på dess inställningar. Programobjektet finns bara i sin arbetskatalog, även om det är ett program med flera innehavare som stöder tjänsthuvudnamn i andra kataloger. Ansökan objektet kan innehålla något av följande (samt ytterligare information som inte nämns här):

* Namn, logotyp och utgivare
* Omdirigera URI:er
* Hemligheter (symmetriska och/eller asymmetriska nycklar som används för att autentisera programmet)
* API-beroenden (OAuth)
* Publicerade API:er/resurser/scope (OAuth)
* Approller (RBAC)
* SSO-metadata och konfiguration
* Användaretablering av metadata och konfiguration
* Proxymetadata och konfiguration

Programobjekt kan skapas via flera vägar, inklusive:

* Programregistreringar i Azure-portalen
* Skapa ett nytt program med Visual Studio och konfigurera det för att använda Azure AD-autentisering
* När en administratör lägger till ett program från appgalleriet (som också skapar ett tjänsthuvudnamn)
* Använda Microsoft Graph API eller PowerShell för att skapa ett nytt program
* Många andra, inklusive olika utvecklarupplevelser i Azure och i API Explorer-upplevelser på olika utvecklarcenter

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Vad är tjänstehuvudmän och var kommer de ifrån?

Du kan hantera [tjänsthuvudnamn](app-objects-and-service-principals.md#service-principal-object) i Azure-portalen via [Enterprise Applications-upplevelsen.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Tjänsthuvudnamn är det som styr ett program som ansluter till Azure AD och kan betraktas som instansen av programmet i katalogen. För ett visst program kan den ha högst ett programobjekt (som är registrerat i en "hemkatalog" och ett eller flera huvudobjekt för tjänsten som representerar instanser av programmet i varje katalog där det fungerar. 

Tjänstens huvudnamn kan omfatta:

* En referens tillbaka till ett programobjekt via egenskapen program-ID
* Poster för lokala användar- och gruppprogramrolltilldelningar
* Register över lokala användar- och administratörsbehörigheter som beviljats programmet
  * Till exempel: behörighet för programmet att komma åt en viss användares e-post
* Register över lokala principer, inklusive principen villkorlig åtkomst
* Register över alternativa lokala inställningar för ett program
  * Regler för anspråkstransformation
  * Attributmappningar (etablering av användare)
  * Katalogspecifika approller (om programmet stöder anpassade roller)
  * Katalogspecifikt namn eller logotyp

Precis som programobjekt kan tjänstobjekt också skapas via flera vägar, inklusive:

* När användare loggar in på ett program från tredje part som är integrerat med Azure AD
  * Under inloggningen uppmanas användarna att ge programmet behörighet att komma åt sin profil och andra behörigheter. Den första personen som ger sitt samtycke gör att ett tjänsthuvudnamn som representerar programmet läggs till i katalogen.
* När användare loggar in på Microsofts onlinetjänster som [Office 365](https://products.office.com/)
  * När du prenumererar på Office 365 eller påbörjar en utvärderingsversion skapas ett eller flera tjänsthuvudnamn i katalogen som representerar de olika tjänster som används för att leverera alla funktioner som är associerade med Office 365.
  * Vissa Office 365-tjänster som SharePoint skapar tjänsthuvudnamn kontinuerligt för att möjliggöra säker kommunikation mellan komponenter, inklusive arbetsflöden.
* När en administratör lägger till ett program från appgalleriet (detta skapar också ett underliggande appobjekt)
* Lägga till ett program för att använda [Azure AD Application Proxy](/azure/active-directory/manage-apps/application-proxy)
* Anslut ett program för enkel inloggning med SAML eller lösenord enkel inloggning (SSO)
* Programmässigt via Microsoft Graph API eller PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hur är programobjekt och tjänsthuvudnamn relaterade till varandra?

Ett program har ett programobjekt i sin hemkatalog som refereras av ett eller flera tjänsthuvudnamn i var och en av katalogerna där det är verksamt (inklusive programmets arbetskatalog).

![Visar relation mellan appobjekt och tjänsthuvudnamn][apps_service_principals_directory]

I föregående diagram underhåller Microsoft två kataloger internt (visas till vänster) som används för att publicera program:

* En för Microsoft Apps (Microsoft-tjänstkatalog)
* En för förintegrerade program från tredje part (Appgallerikatalog)

Programutgivare/leverantörer som integreras med Azure AD måste ha en publiceringskatalog (visas till höger som "Some SaaS Directory").

Program som du lägger till själv (representerade som **App (din)** i diagrammet) inkluderar:

* Appar som du har utvecklat (integrerade med Azure AD)
* Appar som du har anslutit för enkel inloggning
* Appar som du publicerade med Azure AD-programproxyn

### <a name="notes-and-exceptions"></a>Anteckningar och undantag

* Alla tjänsthuvudnamn pekar inte tillbaka till ett programobjekt. När Azure AD ursprungligen byggdes var tjänsterna som tillhandahölls till program mer begränsade och tjänstens huvudnamn var tillräckligt för att upprätta en programidentitet. Det ursprungliga tjänstens huvudnamn var närmare form till Active Directory-tjänstkontot för Windows Server. Därför är det fortfarande möjligt att skapa tjänsthuvudnamn via olika vägar, till exempel att använda Azure AD PowerShell, utan att först skapa ett programobjekt. Microsoft Graph API kräver ett programobjekt innan du skapar ett tjänsthuvudnamn.
* Inte all information som beskrivs ovan är för närvarande exponeras programmässigt. Följande är endast tillgängliga i användargränssnittet:
  * Regler för anspråkstransformation
  * Attributmappningar (etablering av användare)
* Mer detaljerad information om tjänstens huvudnamn och programobjekt finns i referensdokumentationen för Microsoft Graph API:
  * [Program](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)
  * [Tjänstens huvudman](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Varför integreras program med Azure AD?

Program läggs till i Azure AD för att utnyttja en eller flera av de tjänster som de tillhandahåller, inklusive:

* Autentisering och auktorisering av program
* Användarautentisering och auktorisering
* SSO med federation eller lösenord
* Etablering och synkronisering av användare
* Rollbaserad åtkomstkontroll - Använd katalogen för att definiera programroller för att utföra rollbaserade auktoriseringskontroller i ett program
* OAuth-auktoriseringstjänster – används av Office 365 och andra Microsoft-program för att auktorisera åtkomst till API:er/resurser
* Programpublicering och proxy - Publicera ett program från ett privat nätverk till internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Vem har behörighet att lägga till program i min Azure AD-instans?

Även om det finns vissa uppgifter som endast globala administratörer kan göra (till exempel lägga till program från appgalleriet och konfigurera ett program för att använda programproxy) som standard har alla användare i katalogen behörighet att registrera programobjekt som de utvecklar och diskretion över vilka program de delar/ger åtkomst till sina organisationsdata via samtycke. Om en person är den första användaren i din katalog som loggar in på en ansökan och beviljar samtycke, skapar det ett tjänsthuvudnamn i din klient. I annat fall kommer informationen om bidragsbeviljande för samtycke att lagras på den befintliga tjänstens huvudman.

Att tillåta användare att registrera sig och samtycka till ansökningar kan inledningsvis låta om, men ha följande i åtanke:


* Program har kunnat utnyttja Windows Server Active Directory för användarautentisering i många år utan att programmet krävs för att registreras eller registreras i katalogen. Nu organisationen kommer att ha bättre synlighet för exakt hur många program som använder katalogen och för vilket ändamål.
* Delegera dessa ansvarsområden till användare förnekar behovet av en administratörsdriven programregistrering och publiceringsprocess. Med Active Directory Federation Services (ADFS) var det troligt att en administratör var tvungen att lägga till ett program som en förlitande part på uppdrag av sina utvecklare. Nu utvecklare kan självbetjäning.
* Användare som loggar in på program som använder sina organisationskonton i affärssyfte är bra. Om de sedan lämnar organisationen förlorar de automatiskt åtkomsten till sitt konto i det program de använde.
* Att ha en förteckning över vilka data som delades med vilket program är bra. Data är mer transportabel än någonsin och det är användbart att ha en tydlig post om vem som delade vilka data som program.
* API-ägare som använder Azure AD för OAuth bestämmer exakt vilka behörigheter användare kan bevilja till program och vilka behörigheter som kräver att en administratör godkänner. Endast administratörer kan samtycka till större scope och mer betydande behörigheter, medan användarens medgivande är begränsat till användarnas egna data och funktioner.
* När en användare lägger till eller tillåter ett program att komma åt sina data kan händelsen granskas så att du kan visa granskningsrapporterna i Azure-portalen för att avgöra hur ett program har lagts till i katalogen.

Om du fortfarande vill förhindra att användare i katalogen registrerar program och loggar in på program utan administratörsgodkännande finns det två inställningar som du kan ändra för att inaktivera dessa funktioner:

* Så här förhindrar du att användare samtycker till program för egen räkning:
  1. Gå till avsnittet [Användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) i Azure-portalen under Enterprise-program.
  2. Ändra **användare kan samtycka till appar som använder företagsdata för deras räkning** till **Nej**.
     
     > [!NOTE]
     > Om du bestämmer dig för att inaktivera användarens medgivande måste en administratör godkänna alla nya program som en användare behöver använda.

* Så här hindrar du användare från att registrera sina egna program:
  1. Gå till avsnittet [Användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) i Azure Active Directory
  2. Ändra **användare kan registrera program** till **Nr**.

> [!NOTE]
> Microsoft använder själva standardkonfigurationen med användare som kan registrera program och samtycka till program för egen räkning.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
