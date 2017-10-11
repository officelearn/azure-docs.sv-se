---
title: "Hur program läggs till i Azure Active Directory."
description: "Den här artikeln beskriver hur program läggs till en instans av Azure Active Directory."
services: active-directory
documentationcenter: 
author: shoatman
manager: kbrint
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
ms.custom: aaddev
ms.openlocfilehash: 6ffcfcb7ed071a12b0b3495ad534fd00f6d6ad99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hur och varför program läggs till Azure AD
Något av det ursprungligen puzzling när du visar en lista över program i din instans av Azure Active Directory för att förstå var program kommer ifrån och varför de finns.  Den här artikeln ger en översiktlig överblick över hur program visas i katalogen och förse dig med kontext som hjälper dig att förstå hur ett program kommer att finnas i katalogen.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Vilka tjänster som ger Azure AD till program?
Program läggs till Azure AD för att använda en eller flera av de tjänster som den innehåller.  Tjänsterna omfattar:

* App-autentisering och auktorisering
* Användarautentisering och auktorisering
* Enkel inloggning (SSO) använder federation eller lösenord
* Användaretablering & synkronisering
* Rollbaserad åtkomstkontroll; Använd katalogen för att definiera roller för programmet för att utföra roller baserat auktoriseringskontroller i en app.
* oAuth-auktoriseringstjänster (används av Office 365 och andra Microsoft-appar för att bevilja åtkomst till API: er/resurser.)
* Programpublicering & proxy; Publicera en app från ett privat nätverk till internet

## <a name="how-are-applications-represented-in-the-directory"></a>Hur representeras program i katalogen?
Program som representeras i Azure AD-objekt 2: programobjekt och ett service principal-objekt.  En program-objekt registrerats i ett ”hem” / ”ägare” eller ”publicera” katalog och en eller flera service principal-objekt som representerar programmet i alla kataloger som det fungerar.  

Programobjektet beskriver appen till Azure AD (flera innehavare-tjänsten) och får innehålla något av följande: (*Observera*: Detta är inte en fullständig förteckning.)

* Namn, logotyp och utgivare
* Hemligheter (symmetrisk och/eller asymmetriska nycklar används för att autentisera appen)
* API-beroenden (oAuth)
* API: er eller resurser/Omfattningarna publicerade (oAuth)
* Appen roller (RBAC)
* Metadata för SSO och konfiguration (SSO)
* Användaretablering metadata och konfiguration
* Metadata för proxy och konfiguration

Tjänstens huvudnamn är en post för varje katalog där programmet fungerar inklusive sin hemkatalog programmet.  Tjänstens huvudnamn:

* Refererar tillbaka till ett programobjekt via egenskapen app-id
* Innehåller lokala användare och grupp app-rolltilldelningar
* Registrerar lokala användar- och admin behörigheter beviljas för appen
  * Till exempel: behörigheten för programmet för att komma åt en viss användare e-post
* Poster lokala principer, inklusive principen för villkorlig åtkomst
* Poster lokala alternativa lokala inställningar för en app
  * Regler för omvandling av anspråk
  * Attributmappning (användaretablering)
  * Klient specifik app roller (om appen har stöd för anpassade roller)
  * Namn/logotyp

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Ett diagram över programobjekt och tjänstens huvudnamn i kataloger
![Ett diagram som illustrerar hur programmet objekt och tjänsten säkerhetsobjekt befintliga med Azure AD-instanser.][apps_service_principals_directory]

Som du ser i diagrammet ovan.  Microsoft har två kataloger internt (på vänster) används för att publicera program.

* En för Microsoft Apps (Microsoft services directory)
* En för appar förintegrerade med 3 part (App-galleriet katalog)

Utgivare/programleverantörer som integreras med Azure AD måste ha en publishing katalog.  (Vissa SAAS Directory).

Program som du lägger till dig själv innehåller:

* Appar som du har utvecklat (integrerat med AAD)
* Appar som du har anslutit för enkel inloggning
* Appar som du har publicerat med hjälp av Azure AD-programproxy.

### <a name="a-couple-of-notes-and-exceptions"></a>Några anteckningar och undantag
* Inte alla tjänstens huvudnamn peka tillbaka programobjekt.  Huh? När Azure AD har ursprungligen skapats de tjänster som tillhandahålls av program var mycket mer begränsade och tjänstens huvudnamn var tillräckligt för att upprätta en app-identitet.  Den ursprungliga tjänsten huvudnamn var närmare i formen för Windows Server Active Directory-tjänstkontot.  Därför är det fortfarande möjligt att skapa tjänstens huvudnamn med hjälp av Azure AD PowerShell utan att först skapa ett programobjekt.  Graph API kräver ett app-objekt innan du skapar en tjänst huvudnamn.
* Inte alla de uppgifter som beskrivs ovan är för närvarande exponerad programmässigt.  Följande är bara tillgängliga i Användargränssnittet:
  * Regler för omvandling av anspråk
  * Attributmappning (användaretablering)
* Mer detaljerad information om tjänstens huvudnamn och programobjekt finns information i dokumentationen för Azure AD Graph REST API-referens.  *Tipset*: I Azure AD Graph API-dokumentationen är det närmaste bör en Schemareferens för Azure AD som är tillgänglig.  
  * [Programmet](https://msdn.microsoft.com/library/azure/dn151677.aspx)
  * [Tjänstens huvudnamn](https://msdn.microsoft.com/library/azure/dn194452.aspx)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Hur läggs appar till Azure AD-instans?
Det finns många sätt en app kan läggas till Azure AD:

* Lägg till en app från den [Azure Active Directory App-galleriet](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Logga in/till en 3 part App integrerat med Azure Active Directory (till exempel: [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * Vid inloggning upp eller användare uppmanas att ge behörighet till appen åtkomst till deras profil och andra behörigheter.  Den första personen att ge medgivande gör ett huvudnamn för tjänsten som representerar app som ska läggas till i katalogen.
* Logga in/till Microsofts onlinetjänster som [Office 365](http://products.office.com/)
  * När du prenumererar på Office 365 eller starta en utvärderingsversion av en eller flera tjänstens huvudnamn skapas i katalogen som representerar olika tjänster som används för att leverera alla funktioner som är associerade med Office 365.
  * Vissa Office 365-tjänster som SharePoint skapa tjänstens huvudnamn på ett pågående sätt att säker kommunikation mellan komponenter, inklusive arbetsflöden.
* Lägg till en app som du utvecklar i Azure-hanteringsportalen ser: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Lägg till en app som du utvecklar med Visual Studio finns:
  * [ASP.Net-autentiseringsmetoder](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [Anslutna tjänster](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Lägg till en app ska använda för att använda den [Azure AD Application Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Anslut en app för enkel inloggning med SAML eller lösenord för inloggning
* Många andra inklusive olika developer upplevelser i Azure och/i API explorer upplevelser i developer Center

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Vem har behörighet att lägga till program till Azure AD-instans?
Endast globala administratörer kan:

* Lägga till appar från appgalleriet för Azure AD (förintegrerade 3 Partsappar)
* Publicera en app med Azure AD Application Proxy

Alla användare i din katalog har behörighet att lägga till program som de utvecklar och gottfinnande över vilka program som de resursen/ge åtkomst till sina organisationsdata.  *Kom ihåg användarinloggning upp/i att en app och bevilja behörigheter kan resultera i ett huvudnamn för tjänsten håller på att skapas.*

Det här kan ursprungligen ljud om, men behåll följande i åtanke:

* Appar har kunnat utnyttja Windows Server Active Directory för autentisering av användare för många år utan att programmet ska vara registrerad/registrerats i katalogen.  Organisationen kommer nu har bättre synlighet exakt hur många appar med katalog- och what för.
* Inget behov av administratören drivs appen publicering/registreringsprocessen.  Det var sannolikt att en administratör måste lägga till en app som en förlitande part för utvecklare med Active Directory Federation Services.  Utvecklare kan nu självbetjäning.
* Användare som loggar in/upp till appar med hjälp av deras organisation konton för affärsändamål är bra.  Om de senare lämnar organisationen förlorar de åtkomsten till sitt konto i program som de använder.
* Med en post på vilka data delades som programmet är bra.  Data är transportera än någonsin och ha en tydlig post på som delas vilka data med vilka program som kan användas.
* Appar som använder Azure AD för oAuth bestämma exakt vilka behörigheter som användarna kan tilldela till program och vilka behörigheter måste en administratör för att godkänna.  Det ska gå utan säger att endast administratörer kan samtycka till större scope och större behörighet.
* Användare lägger till, så att appar åtkomst till sina data är granskade händelser så att du kan visa granskningsrapporter i Azure Management portal för att avgöra hur en app har lagts till i katalogen.

**Obs:** *Microsoft själva har fungerat använder standardkonfigurationen för många månader nu.*

Med alla som säger är det möjligt att förhindra att användare i din katalog lägga till program och från utöva gottfinnande över vilka uppgifter de dela med program genom att ändra katalogkonfiguration i Azure-hanteringsportalen.  Följande konfiguration kan användas inom Azure Management portal på fliken för din katalog ”konfigurera”.

![En skärmbild av Användargränssnittet för att konfigurera integrerad app-inställningar][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
Läs mer om hur du lägger till Azure AD-program och hur du konfigurerar tjänster för appar.

* Utvecklare: [Lär dig hur du integrerar ett program med AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Utvecklare: [granska exempelkod för appar som är integrerad med Azure Active Directory på GitHub](https://github.com/AzureADSamples)
* Utvecklare och IT-proffs: [granska REST API-dokumentation för Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* IT-proffs: [beskrivs hur du använder Azure Active Directory redan integrerade program från galleriet App](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* IT-proffs: [hittar självstudier för att konfigurera specifika förintegrerade appar](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* IT-proffs: [Lär dig hur du publicerar en app med Azure Active Directory Application Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Se även
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
