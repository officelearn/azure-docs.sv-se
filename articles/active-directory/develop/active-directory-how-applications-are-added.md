---
title: Hur och varför program läggs till i Azure Active Directory
description: Vad innebär det för ett program som ska läggas till Azure AD och hur kommer de dit?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.openlocfilehash: 2b25f2da7c512b58133a58ad07e5efedc5f79c95
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077694"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hur och varför program läggs till Azure AD

Det finns två representationer av program i Azure AD: 
* [Programobjekt](app-objects-and-service-principals.md#application-object) – även om det finns [undantag](#notes-and-exceptions), programobjekt kan ses definitionen av ett program.
* [Tjänsthuvudnamn](app-objects-and-service-principals.md#service-principal-object) -kan betraktas som en instans av ett program. Tjänstens huvudnamn Allmänt refererar ett programobjekt och ett programobjekt kan refereras av flera tjänstens huvudnamn i kataloger.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Vad är programobjekt och de varifrån kommer?
Du kan hantera [programobjekt](app-objects-and-service-principals.md#application-object) i Azure-portalen via den [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) upplevelse. Programobjekt beskriver programmet till Azure AD och kan ses definitionen av programmet, vilket gör att tjänsten att se hur du utfärda token till appen baserat på dess inställningar. Programobjektet finns bara i sin hemkatalog, även om det är ett program för flera innehavare som stöd för tjänstens huvudnamn i andra kataloger. Programobjektet kan vara något av följande (som även följande ytterligare information som inte anges här):
* Namn, logotyp och utgivare
* Svars-URL:er
* Hemligheter (symmetriska och/eller asymmetriska nycklar används för att autentisera programmet)
* API-beroenden (OAuth)
* Publicerade API: er/resurser/områden (OAuth)
* Roller (RBAC)
* Metadata för enkel inloggning och konfiguration
* Användaretablering metadata och konfiguration
* Proxy-metadata och konfiguration

Programobjekt kan skapas via flera sökvägar, inklusive:
* Programregistreringar i Azure portal
* Skapa ett nytt program med Visual Studio och konfigurera den att använda Azure AD-autentisering
* När en administratör lägger till ett program från appgalleriet (som ska också skapa ett tjänstobjekt)
* Med Microsoft Graph API, Azure AD Graph API eller PowerShell till att skapa ett nytt program
* Många andra inklusive olika utvecklarfunktioner i Azure och i API: et explorer upplevelser i utvecklarcenter

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Vad är tjänstens huvudnamn och de varifrån kommer?
Du kan hantera [tjänsthuvudnamn](app-objects-and-service-principals.md#service-principal-object) i Azure-portalen via den [företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) upplevelse. Tjänstens huvudnamn är vad styr ett program som ansluter till Azure AD och kan ses instans av programmet i din katalog. Det kan ha högst en programobjektet (som är registrerad i en ”” arbetskatalog) och en eller flera objekt för tjänstens huvudnamn som representerar instanser av programmet i alla kataloger som fungerar för alla angivna program. 

Tjänstens huvudnamn kan innefatta:

* En referens till ett programobjekt via programegenskapen-ID
* Poster för lokala användare och programrolltilldelningar för grupp
* Poster för lokala användare och administratör behörigheterna för programmet
  * Till exempel: behörighet för programmet åt en viss användares e-post
* Poster i lokala principer, inklusive principen för villkorlig åtkomst
* Poster i alternativa lokala inställningar för ett program
  * Regler för omvandling av anspråk
  * Attributmappningar (etableringen av användare)
  * Directory-specifika approller (om programmet stöder anpassade roller)
  * Directory-specifika namn eller logotyp

Som programobjekt, kan tjänstens huvudnamn skapas via flera sökvägar, inklusive:

* När användare loggar in på ett program från tredje part integrerad med Azure AD
  * Under inloggning uppmanas användarna att ge behörighet till programmet för att få åtkomst till deras profil och andra behörigheter. Den första personen som samtycka orsakar ett huvudnamn för tjänsten som representerar programmet som ska läggas till katalogen.
* När användare loggar in på Microsoft online services som [Office 365](https://products.office.com/)
  * När du prenumererar på Office 365 eller starta en utvärderingsversion, skapas en eller flera tjänstens huvudnamn i katalogen som representerar de olika tjänsterna som används för att leverera alla funktioner som är associerade med Office 365.
  * Vissa Office 365-tjänster som SharePoint skapa tjänstens huvudnamn med jämna mellanrum att tillåta säker kommunikation mellan komponenter, inklusive arbetsflöden.
* När en administratör lägger till ett program från appgalleriet (det skapas också en underliggande app-objekt)
* Lägg till ett program att använda den [Azure AD Application Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Ansluta ett program för enkel inloggning om hur du använder SAML eller ett lösenord med enkel inloggning (SSO)
* Programmässigt via Azure AD Graph API eller PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hur är programobjekt och tjänstens huvudnamn relaterade till varandra?
Ett program har ett programobjekt i sin hemkatalog som refereras av en eller flera tjänsthuvudnamn i var och en av de kataloger som där den fungerar (inklusive programmets arbetskatalog).
![Ett diagram som illustrerar hur programobjekt och tjänstens huvudnamn ska interagera med varandra och Azure AD-instanser.][apps_service_principals_directory]

I det föregående diagrammet Microsoft har två kataloger internt (visas till vänster) som används för att publicera program:

* En för Microsoft Apps (Microsoft services-katalog)
* En för förintegrerade tredjepartsprogram (App-galleriet directory)

Programmet utgivare/leverantörer som integreras med Azure AD måste ha en publishing katalog (visas till höger som ”vissa SaaS Directory”).

Program som du lägger till dig själv (som **App (själv)** i diagrammet) inkluderar:

* Appar som du har utvecklat (integrerad med Azure AD)
* Appar som du har anslutit för enkel inloggning
* Appar som du publicerat med hjälp av Azure AD-programproxyn

### <a name="notes-and-exceptions"></a>Viktig information och undantag
* Inte alla tjänsthuvudnamn refererar till ett programobjekt. När Azure AD har ursprungligen skapats de tjänster som tillhandahålls till program var mer begränsade och tjänstens huvudnamn har tillräckliga för att upprätta en Programidentitet. Ursprungliga tjänstens huvudnamn var närmare i formen för Windows Server Active Directory-tjänstkontot. Därför är det fortfarande möjligt att skapa tjänstens huvudnamn via olika vägar, till exempel med Azure AD PowerShell, utan att först skapa ett programobjekt. Azure AD Graph API kräver ett programobjekt innan du skapar ett tjänstens huvudnamn.
* Inte alla de uppgifter som beskrivs ovan är för närvarande visas programmässigt. Följande är endast tillgängliga i Användargränssnittet:
  * Regler för omvandling av anspråk
  * Attributmappningar (etableringen av användare)
* Mer detaljerad information om tjänstens huvudnamn och programobjekt finns i dokumentationen för Azure AD Graph REST API-referens:
  * [Programmet](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Tjänstens huvudnamn](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Varför program integreras med Azure AD?
Program läggs till Azure AD för att använda en eller flera av de tjänster, bland annat:

* Program-autentisering och auktorisering
* Användarautentisering och auktorisering
* Enkel inloggning med federation eller lösenord
* Etableringen av användare och synkronisering
* Rollbaserad åtkomstkontroll – Använd katalogen för att definiera roller för programmet för att utföra rollbaserad auktorisering kontrollerar i ett program
* Auktoriseringstjänster för OAuth - används av Office 365 och andra Microsoft-program för att auktorisera åtkomst till API: er/resurser
* Programpublicering och proxy - publicera ett program från ett privat nätverk till internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Vem har behörighet att lägga till program i min Azure AD-instans?
Det finns några uppgifter som att endast globala administratörer kan utföra (till exempel lägga till program från appgalleriet och konfigurera ett program att använda Application Proxy) som standard alla användare i katalogen har behörighet att registrera program objekt som de är utveckla och gottfinnande över vilka program som de resurs/ge åtkomst till sin organisations data via ditt medgivande. Om en person är den första användaren i din katalog för att logga in till ett program och ge ditt medgivande, som skapar ett huvudnamn för tjänsten i din klient; i annat fall lagras medgivande bevilja information på den befintliga tjänsten huvudnamn.

Så att användarna kan registrera och samtycka till program kan ursprungligen ljud om, men tänk på följande:


* Program har kunnat använda Windows Server Active Directory för autentisering under många år utan att programmet registreras eller läggas till i katalogen. Organisationen kommer nu har förbättrat synlighet till exakt hur många program använder katalogen och i vilket syfte.
* Dessa uppgifter för användare att delegera eliminerar behovet av en administratör-drivna programregistrering och publiceringsprocessen. Med Active Directory Federation Services (ADFS) och det var sannolikt att en administratör måste lägga till ett program som en förlitande part för utvecklarna. Nu kan utvecklare självbetjäning.
* Användare som loggar in till program med hjälp av sina organisationskonton för företag är bra. Om de senare lämnar organisationen förlorar de automatiskt åtkomst till sitt konto i programmet som de använder.
* Att ha koll på vilka data som har delats med vilket program som är bra. Data är transportera än någonsin och det är praktiskt att ha en tydlig post med som delas vilka data med vilka program.
* API-ägare som använder Azure AD för OAuth bestämma exakt vilka behörigheter som användarna ska kunna bevilja till program och vilka behörigheter kräver en administratör att godkänna. Endast administratörer kan godkänna större omfång och mer betydande behörigheter, även om användarens medgivande begränsas till användarnas egna data och funktioner.
* När en användare lägger till eller gör att ett program för att komma åt sina data, kan händelsen granskas så att du kan visa granskningsrapporter i Azure-portalen för att avgöra hur ett program har lagts till i katalogen.

Om du vill förhindra att användare i din katalog från att registrera program och logga in i program utan administratörsgodkännande finns det två inställningar som du kan ändra om du vill inaktivera dessa funktioner:

* Att hindra användare från principer för program på egen hand:
  1. I Azure-portalen går du till den [användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) avsnittet under företagsprogram.
  2. Ändra **användare kan godkänna att appar får åtkomst till företagets data å deras vägnar** till **nr**. 
      > [!NOTE]
      > Om du vill stänga av användarens medgivande kan en administratör krävas samtycker till att alla nya program som en användare behöver använda.    
* Att hindra användare från att registrera sina egna program:
  1. I Azure-portalen går du till den [användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) avsnittet under Azure Active Directory
  2. Ändra **användare kan registrera program** till **nr**.

> [!NOTE]
> Microsoft själva använder standardkonfigurationen med användare kan registrera program och samtycka till program på egen hand.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

