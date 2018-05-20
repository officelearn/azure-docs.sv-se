---
title: Hur och varför program läggs till i Azure Active Directory
description: Vad innebär det för ett program som ska läggas till Azure AD och hur de får det?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: c9ebfcba59e3f46fb30f4cd2402ec4ebb606f6d0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hur och varför program läggs till Azure AD
Det finns två representationer av program i Azure AD: 
* [Programobjekt](active-directory-application-objects.md#application-object) – även om det finns [undantag](#notes-and-exceptions), dessa kan betraktas som definitionen av ett program.
* [Tjänsten säkerhetsobjekt](active-directory-application-objects.md#service-principal-object) -dessa kan betraktas som en instans av ett program. Tjänstens huvudnamn vanligtvis referera ett programobjekt och objekt för ett program kan refereras av flera tjänstens huvudnamn i kataloger.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Vad är programobjekt och där de kommer från?
[Programobjekt](active-directory-application-objects.md#application-object) (som du kan hantera i Azure-portalen via den [App registreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) upplevelse) beskriver programmet till Azure AD och kan ses definitionen av programmet, så att den tjänsten vet hur man utfärdar token till programmet baserat på dess inställningar. Programobjektet kommer endast finnas i sin hemkatalog, även om det är ett program för flera innehavare som stöder tjänstens huvudnamn i andra kataloger. Programobjektet kan vara något av följande (som också som ytterligare information som inte anges här):
* Namn, logotyp och utgivare
* Svars-URL:er
* Hemligheter (symmetrisk och/eller asymmetriska nycklar används för att autentisera programmet)
* API-beroenden (OAuth)
* Publicerade API: er eller resurser/Omfattningarna (OAuth)
* Appen roller (RBAC)
* Metadata för SSO och konfiguration
* Användaretablering metadata och konfiguration
* Metadata för proxy och konfiguration

Programobjekt kan skapas med flera sökvägar, inklusive:
* Programmet registreringar i Azure-portalen
* Skapa ett nytt program med Visual Studio och konfigurera den att använda Azure AD-autentisering
* När en administratör lägger till ett program från appgalleriet (skapas också ett huvudnamn för tjänsten)
* Använda Microsoft Graph API, Azure AD Graph API och PowerShell för att skapa ett nytt program
* Många andra inklusive olika developer upplevelser i Azure och API explorer upplevelser i developer Center

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Vad är tjänstens huvudnamn och där de kommer från?
[Tjänsten säkerhetsobjekt](active-directory-application-objects.md#service-principal-object) (som du kan hantera via den [företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) upplevelse) är vad faktiskt styr ett program som ansluter till Azure AD och kan ses instans av programmet i din katalog. Den kan ha högst en programobjektet (som är registrerade i en ”” arbetskatalog) och en eller flera service principal-objekt som representerar instanser av programmet i alla kataloger som det fungerar för alla angivna program. 

Tjänstens huvudnamn kan inkludera följande:

* En referens till ett programobjekt via program-ID-egenskap
* Posterna för lokala användare och grupp program-rolltilldelningar
* Posterna för lokala användare och admin behörigheterna för programmet
  * Till exempel: behörigheten för programmet att få åtkomst till e-post för en viss användare
* Posterna för lokala principer, inklusive principen för villkorlig åtkomst
* Posterna för alternativa lokala inställningar för ett program
  * Regler för omvandling av anspråk
  * Attributmappning (användaretablering)
  * Roller för Directory-specifika appen (om programmet stöder anpassade roller)
  * Directory-specifika namn eller logotyp

T.ex. programobjekt, kan du även skapa tjänstens huvudnamn via flera sökvägar, inklusive:

* När användare loggar in på ett program från tredje part integrerade med Azure AD
  * Under inloggning användare uppmanas att ge behörighet till programmet åtkomst till deras profil och andra behörigheter. Den första personen att ge medgivande gör ett huvudnamn för tjänsten som representerar programmet som ska läggas till i katalogen.
* När användare loggar in på Microsoft online services som [Office 365](http://products.office.com/)
  * När du prenumererar på Office 365 eller starta en utvärderingsversion, skapas en eller flera tjänstens huvudnamn i katalogen som representerar olika tjänster som används för att leverera alla funktioner som är associerade med Office 365.
  * Vissa Office 365-tjänster som SharePoint skapa tjänstens huvudnamn med jämna mellanrum för att säker kommunikation mellan komponenter, inklusive arbetsflöden.
* När en administratör lägger till ett program från appgalleriet (skapas också en underliggande app-objektet)
* Lägg till ett program att använda den [Azure AD Application Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Ansluta ett program för enkel inloggning med SAML eller lösenord enkel inloggning (SSO)
* Genom att programmera via Azure AD Graph API eller PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hur är programobjekt och tjänstens huvudnamn relaterade till varandra?
Ett program har en programobjektet i sin hemkatalog som refereras av en eller flera tjänstens huvudnamn i alla kataloger där den används (inklusive programmets arbetskatalog).
![Ett diagram som illustrerar hur programobjekt och tjänstens huvudnamn interagera med varandra och Azure AD-instanser.][apps_service_principals_directory]

I föregående diagram Microsoft har två kataloger internt (visas till vänster) som används för att publicera program:

* En för Microsoft Apps (Microsoft services directory)
* En för förintegrerade tredjepartsprogram (App-galleriet katalog)

Utgivare/programleverantörer som integreras med Azure AD måste ha en publishing katalog (visas till höger som ”vissa SaaS Directory”).

Program som du lägger till dig själv (visas i form av **App (er)** i diagrammet) inkluderar:

* Appar som du har utvecklat (integrerat med Azure AD)
* Appar som du har anslutit för enkel inloggning
* Appar som du har publicerat med hjälp av Azure AD application proxy

### <a name="notes-and-exceptions"></a>Anteckningar och undantag
* Inte alla tjänstens huvudnamn refererar till ett programobjekt. När Azure AD har ursprungligen skapats de tjänster som tillhandahålls av program har mer begränsad och tjänstens huvudnamn var tillräckligt för att upprätta en Programidentitet. Den ursprungliga tjänsten huvudnamn var närmare i formen för Windows Server Active Directory-tjänstkontot. Därför är det fortfarande möjligt att skapa tjänstens huvudnamn via olika sökvägar, till exempel med hjälp av Azure AD PowerShell, utan att först skapa ett programobjekt. Azure AD Graph API kräver ett programobjekt innan du skapar en tjänst huvudnamn.
* Inte alla de uppgifter som beskrivs ovan är för närvarande exponerad programmässigt. Följande är bara tillgängliga i Användargränssnittet:
  * Regler för omvandling av anspråk
  * Attributmappning (användaretablering)
* Mer detaljerad information om tjänstens huvudnamn och programobjekt finns i dokumentationen för Azure AD Graph REST API-referens:
  * [Programmet](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Tjänstens huvudnamn](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Varför program integreras med Azure AD?
Program läggs till Azure AD för att använda en eller flera av de tjänster som den tillhandahåller inklusive:

* Autentisering och auktorisering
* Autentisering och auktorisering
* Enkel inloggning med federation eller lösenord
* Användaretablering och synkronisering
* Rollbaserad åtkomstkontroll - Använd katalogen för att definiera roller för programmet för att utföra roller baserat auktoriseringskontroller i ett program
* Auktoriseringstjänster för OAuth - används av Office 365 och andra Microsoft-program för att auktorisera åtkomst till API: er/resurser
* Programpublicering och proxy - publicera ett program från ett privat nätverk till internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Vem har behörighet att lägga till program till Azure AD-instans?
Det finns vissa uppgifter som att endast globala administratörer kan göra (till exempel lägga till program från appgalleriet och konfigurera ett program att använda Application Proxy) som standard alla användare i din katalog har behörighet att registrera program objekt som de är utvecklar och gottfinnande över vilka program som de resursen/ge åtkomst till sina organisationsdata via medgivande. Om en person är den första användaren i din katalog att logga in till ett program och ge ditt medgivande, som skapar ett huvudnamn för tjänsten i din klient; Annars lagras medgivande bevilja information på den befintliga tjänsten huvudnamn.

Att tillåta användare att registrera och samtycker till att program kan ursprungligen ljud om, men tänk på följande:


* Program har kunnat utnyttja Windows Server Active Directory för autentisering av användare för många år utan att programmet som ska registreras eller registrerats i katalogen. Organisationen kommer nu har bättre synlighet till exakt hur många program använder katalogen och för vilket syfte.
* Dessa ansvarsområden för användare att delegera negeras behovet av en admin-driven appregistrering och publiceringsprocessen. Med Active Directory Federation Services (ADFS) och det var sannolikt att en administratör måste lägga till ett program som en förlitande part för utvecklarens. Utvecklare kan nu självbetjäning.
* Användare som loggar in program med deras organisation konton för affärsändamål är bra. Om de senare lämnar organisationen kommer de automatiskt förlora åtkomsten till sitt konto i program som de använder.
* Med en post på vilka data delades som programmet är bra. Data är transportera än någonsin och det är praktiskt att ha en tydlig post på som delas vilka data med vilka program.
* API-ägare som använder Azure AD för OAuth bestämma exakt vilka behörigheter användarna ska kunna bevilja till program och vilka behörigheter måste en administratör för att godkänna. Endast administratörer kan samtycka till större scope och större behörighet när tillstånd är begränsad till användarnas egna data och funktioner.
* När en användare lägger till eller låter ett program att komma åt sina data, granskas händelsen så att du kan visa granskningsrapporter i Azure-portalen för att avgöra hur ett program har lagts till i katalogen.

Om du vill förhindra att användare i din katalog från registrera program och logga in på program utan administratörsgodkännande finns det två inställningar som du kan ändra om du vill inaktivera dessa funktioner:

* För att förhindra användare från principer för program på egen hand:
  1. I Azure-portalen går du till den [användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) avsnitt under företagsprogram.
  2. Ändra **användare kan samtycker till att appar kommer åt företagsdata åt** till **nr**. 
     *Observera att om du vill inaktivera användargodkännande administratör kommer att behöva samtycker till att alla nya program som en användare behöver använda.*
* Att förhindra att användare registrerar sina egna program:
  1. I Azure-portalen går du till den [användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) avsnitt under Azure Active Directory
  2. Ändra **användarna kan registrera program** till **nr**.

> [!NOTE]
> Microsoft själva använder standardkonfigurationen med användare kan registrera program och samtycka till program på egen hand.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

