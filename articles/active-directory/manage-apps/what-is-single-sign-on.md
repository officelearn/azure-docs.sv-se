---
title: Vad är programåtkomst och enkel inloggning med Azure Active Directory? | Microsoft Docs
description: Använda Azure Active Directory för att aktivera enkel inloggning till alla SaaS- och web program som ska användas för företag.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 1ac15ce8f8abf2b30b42f02b300a17448f86fc40
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052776"
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Vad är programåtkomst och enkel inloggning med Azure Active Directory?
Enkel inloggning innebär det att kunna komma åt alla program och resurser som du behöver för att göra affärer, genom att logga in med ett enda användarkonto. När du har loggat in, du kan komma åt alla program som du behöver utan att behöva autentisera dig (exempel: Skriv ett lösenord) en andra gång.

Många organisationer förlita sig på programvara som en tjänst (SaaS)-program som Office 365, Box och Salesforce för slutanvändarnas produktivitet. Historiskt sett har IT-personal måste individuellt skapa och uppdatera användarkonton i varje SaaS-program och användare måste komma ihåg ett lösenord för varje SaaS-program.

Azure Active Directory utökar den lokala Active Directory till molnet, där användaren kan använda sina primära organisationskonto för att inte bara logga in på sina domänanslutna enheter och företagets resurser, men även alla webb- och SaaS-program behövs för sitt arbete.

Så inte bara användare behöver inte hantera flera uppsättningar med användarnamn och lösenord, sina program åtkomst kan etableras eller tas bort automatiskt baserat på deras organisation gruppmedlemmar och deras status som en anställd. Azure Active Directory introducerar säkerhet och styrning åtkomstkontroller som gör det möjligt att centralt hantera användarnas åtkomst i SaaS-program.

Azure AD tillåter enkel integrering med många av dagens populära SaaS-program; Det ger identitets- och åtkomsthantering och gör att användarna kan enkel inloggning till program direkt, eller upptäcka och starta dem från en portal som Office 365 eller Azure AD-åtkomstpanelen.

Arkitekturen för integrering består av följande fyra huvudsakliga byggblocken:

* Enkel inloggning gör att användarna kan komma åt sina SaaS-program baserat på deras organisationskonto i Azure AD. Enkel inloggning är vad användarna kan autentisera till ett program med enkel organisationskontot.
* Etableringen av användare kan användaretablering och avetablering till målet SaaS baserat på ändringar som gjorts i Windows Server Active Directory och/eller Azure AD. Ett etablerade konto är det som ger en användare behörighet att använda ett program när de har autentiserat sig via enkel inloggning.
* Centraliserad hantering av programåtkomst programåtkomst för Azure portal kan enskild punkt SaaS och hantering, med möjlighet att delegera program åtkomst beslutsfattande och godkännanden för alla i organisationen
* Enhetlig rapportering och övervakning av användaraktivitet i Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Hur fungerar enkel inloggning med Azure Active Directory?
När användare loggar in till ett program, övergången till en autentiseringsprocess där de krävs för att bevisa att de är de säger att de är. Den här autentiseringsprocessen görs normalt genom att ange ett lösenord som lagras på programmet utan enkel inloggning, och användarna uppmanas att känna till lösenordet.

Azure AD stöder tre olika sätt att logga in på programmen:

* **Federerad enkel inloggning** gör det möjligt att omdirigera till Azure AD för autentisering av användare i stället för att fråga om ett eget lösenord. Federerad enkel inloggning har stöd för program som stöder protokoll, till exempel SAML 2.0, WS-Federation och OpenID Connect, och är det bästa möjliga läget för enkel inloggning.
* **Lösenordsbaserad enkel inloggning** möjliggör säker lagring av lösenord för programmet och spela upp med ett webbläsartillägg eller mobilapp. Lösenordsbaserad enkel inloggning använder den befintliga processen som tillhandahålls av programmet, men låter en administratör hantera lösenorden och kräver inte att användaren känner till lösenordet.
* **Länkad enkel inloggning** gör att Azure AD kan utnyttja alla befintliga enkel inloggning som har ställts in för programmet, men gör att programmen ska kopplas till de Office 365 eller Azure AD åtkomst till panelen portalerna och även aktiverar ytterligare rapportering i Azure AD när programmen det startas.

När en användare har autentiserats med ett program, måste de också har en kontopost som etablerats på programnivå som instruerar programmet var behörigheter och åtkomstnivå är i programmet. Etableringen av den här kontopost kan antingen automatiskt, eller när det kan ske manuellt av en administratör innan användaren får enkel inloggning.

 Mer information om dessa lägen för enkel inloggning och etablering nedan.

### <a name="federated-single-sign-on"></a>Federerad enkel inloggning
Federerad enkel inloggning kan användare i din organisation att logga in automatiskt till en tredje parts SaaS-program med Azure AD med hjälp av informationen om användarkontot från Azure AD.

I det här scenariot när du redan har loggats i Azure AD och du vill komma åt resurser som styrs av en tredje parts SaaS-program, eliminerar federation behovet av en användare autentiseras.

Azure AD har stöd för federerad enkel inloggning med program som stöder SAML 2.0, WS-Federation, eller OpenID connect-protokoll.

Se även: [hantera certifikat för federerad enkel inloggning](manage-certificates-for-federated-single-sign-on.md)

### <a name="password-based-single-sign-on"></a>Lösenordsbaserad enkel inloggning
Konfigurera lösenordsbaserad enkel inloggning kan användare i din organisation att logga in automatiskt till en tredje parts SaaS-program med Azure AD med hjälp av informationen om användarkontot från SaaS-program från tredje part. När du aktiverar den här funktionen kan Azure AD samlar in och lagrar informationen om användarkontot och relaterade lösenordet på ett säkert sätt.

Azure AD stöder lösenordsbaserad enkel inloggning för alla molnbaserade appar som har en HTML-baserad på inloggningssidan. Genom att använda ett anpassat webbläsare plugin-program kan AAD automatiserar inloggningsprocess via på ett säkert sätt hämta autentiseringsuppgifter för programmet, till exempel användarnamnet och lösenordet från katalogen och anger autentiseringsuppgifterna i program på inloggningssidan användarens räkning. Det finns två användningsfall:

1. **Administratören hanterar autentiseringsuppgifter** – administratörer kan skapa och hantera autentiseringsuppgifter för program och tilldela dessa autentiseringsuppgifter till användare eller grupper som behöver åtkomst till programmet. I dessa fall kan slutanvändaren behöver inte känner till autentiseringsuppgifterna, men fortfarande får enkel inloggning till programmet genom att klicka på den på sina åtkomstpaneler eller via en angiven länk. Den här processen gör det möjligt för både livscykelhantering av autentiseringsuppgifter som administratör, samt bekvämlighet för slutanvändare där de inte behöver komma ihåg eller hantera appspecifika lösenord. Autentiseringsuppgifterna som har dolts från användaren under automatisk inloggning; men de är tekniskt sett kan identifieras av användaren med web felsökningsverktyg och användare och administratörer bör följa samma säkerhetsprinciper som om autentiseringsuppgifterna exponerades direkt av användaren. Autentiseringsuppgifter för administratören är användbara när du anger kontoåtkomst som delas mellan många användare, till exempel sociala medier eller dokumentdelning program.
2. **Användare hanterar autentiseringsuppgifter** – administratörer kan tilldela program till användare eller grupper och tillåta att användarna anger sina egna autentiseringsuppgifter direkt vid åtkomst till programmet för första gången på sin åtkomstpanel. Detta skapar för att underlätta för slutanvändare där de inte behöver kontinuerligt ange appspecifika lösenord varje gång som de har åtkomst till programmet. Användare kan fortsätta att hantera sina lösenord genom att uppdatera eller ta bort dem efter behov. Det här användningsfallet kan också användas som en version bricka till administrativa hantering av autentiseringsuppgifter, där administratören kan ställa in nya autentiseringsuppgifter för programmet ett datum i framtiden utan att ändra app-upplevelse för åtkomst till slutanvändarens företagsidentitet.

I båda fallen autentiseringsuppgifter lagras i ett krypterat tillstånd i katalogen och skickas endast via HTTPS under processen för automatisk inloggning. Med lösenordsbaserad enkel inloggning för erbjuder Azure AD en lämplig identitetshanteringslösning för åtkomst för appar som inte kan stödja federation-protokoll.

Lösenordsbaserad SSO är beroende av ett webbläsartillägg på ett säkert sätt hämta program- och användarspecifik information från Azure AD och tillämpa den på tjänsten. De flesta tredje parts SaaS-program som stöds av Azure AD stöder denna funktion.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:
* Internet Explorer 11 – i Windows 7 eller senare
* Edge på Windows 10 Anniversary Edition eller senare 
* Chrome--På Windows 7 eller senare, och i Mac OS X eller senare
* Firefox 26.0 eller senare, på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare

### <a name="linked-single-sign-on"></a>Länkade enkel inloggning
När du konfigurerar enkel inloggning för ett program, innehåller Azure portal ett tredje alternativ för ”länkade enkel inloggning”. Det här alternativet kan bara en administratör skapa en länk till ett program och placera den på åtkomstpanelen för valda användare.

Om det finns ett program som är konfigurerad för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör exempelvis använda alternativet ”länkade enkel inloggning” för att skapa en länk till den på åtkomstpanelen. När användarna använder länken kan autentiseras med hjälp av Active Directory Federation Services 2.0 eller den befintliga enkla inloggning lösningen tillhandahålls av programmet.

### <a name="user-provisioning"></a>Etableringen av användare
Välj program aktiverar Azure AD för automatisk användaretablering och avetablering av konton i tredje parts SaaS-program från Azure Portal med din Windows Server Active Directory eller Azure AD-identitetsinformation. När en användare har behörighet i Azure AD för en av dessa program, kan ett konto skapas automatiskt (etablerade) i mål-SaaS-program.

När en användare tas bort eller deras informationen ändras i Azure AD, visas ändringarna också i SaaS-program. Det innebär att konfigurera automatiserade identitetslivcykelhantering gör att administratörer kan styra och tillhandahålla Automatisk etablering och avetablering från SaaS-program. I Azure AD aktiveras den här automatisering av identitetslivcykelhantering som etableringen av användare.

Mer information finns i [automatisk Användaretablering och avetablering för SaaS-program](../active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Kom igång med Azure AD-programgalleriet
Redo att sätta igång? Distribuera enkel inloggning mellan Azure AD och SaaS-program som din organisation använder, följer dessa riktlinjer.

### <a name="using-the-azure-ad-application-gallery"></a>Med hjälp av Azure AD-programgalleriet
Den [Azure Active Directory-Programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/) innehåller en lista över program som stöder en form av enkel inloggning med Azure Active Directory.

![Appgalleriet för Azure online](media/what-is-single-sign-on/onlineappgallery.png)

Här följer några tips för att hitta appar med vilka funktioner de stöder:

* Azure AD stöder automatisk etablering och avetablering för alla ”aktuella” appar i den [Azure Active Directory-Programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/).
* En lista över federerade program som specifikt stöder federerad enkel inloggning med ett protokoll, till exempel SAML, WS-Federation, eller OpenID Connect finns [här](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

När du har hittat ditt program, kan du komma igång genom att följa de stegvisa anvisningarna i appgalleriet och i Azure-portalen att aktivera enkel inloggning.

### <a name="application-not-in-the-gallery"></a>Program inte i galleriet?
Om ditt program inte finns i Azure AD-programgalleriet, finns följande alternativ:

* **Lägg till en ny app som du använder** -använda anpassad kategori i app-galleriet i Azure-portalen för att ansluta ett olistade program som din organisation använder. Du kan lägga till alla program som stöder SAML 2.0 som federerade app eller alla program som har en HTML-baserad på inloggningssidan som ett lösenord för SSO-app. Mer information finns i den här artikeln på [att lägga till ditt eget program](../application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Lägg till dina egna app som du utvecklar** – om du har utvecklat programmet själv, följer du instruktionerna i dokumentationen för Azure AD-utvecklare att implementera federerad enkel inloggning eller etablering med hjälp av Azure AD graph API. Mer information finns i följande källor:
  
  * [Autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Begär en appintegrering** -begära stöd för det program som du behöver med hjälp av den [Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Använda Azure Portal
Du kan använda Active Directory-tillägget i Azure-portalen för att konfigurera de program enkel inloggning. Du måste välja en katalog från Active Directory-avsnittet i portalen som ett första steg:

![](./media/what-is-single-sign-on/azuremgmtportal.png)

För att hantera dina SaaS-program från tredje part, kan du växla till fliken program i den valda katalogen. Den här vyn kan administratörer:

* Lägg till nya program från Azure AD-galleriet, samt appar som du utvecklar
* Ta bort integrerade program
* Hantera program som de redan har integrerat

Vanliga administrativa uppgifter för ett SaaS-program från tredje part är:

* Aktivera enkel inloggning med Azure AD, med hjälp av enkel inloggning med lösenord eller, om det finns för målet SaaS-federerad enkel inloggning
* Du kan också aktivera användaretablering för användaren etablering och avetablering (identitetslivcykelhantering)
* För program 
* Om etableringen av användare är aktiverad, att välja vilka användare har åtkomst till programmet

För gallery-appar som har stöd för federerad enkel inloggning måste configuration vanligtvis du ange fler konfigurationsinställningar som certifikat och metadata för att skapa ett federerat förtroende mellan app från tredje part och Azure AD. Guiden vägleder dig genom de och ger dig enkel åtkomst till SaaS programspecifika data och instruktioner.

Detta kräver att ge Azure AD-behörigheter för att hantera dina konton i SaaS-programmet för gallery-appar som har stöd för automatisk användaretablering. Minst måste du ange autentiseringsuppgifter för Azure AD ska användas vid autentisering via målprogrammet. Om ytterligare konfigurationsinställningar måste tillhandahållas beror på kraven för programmet.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Distribuera Azure AD-integrerade program till användare
Azure AD erbjuder anpassningsbara sätt att distribuera program för slutanvändare i din organisation:

* Azure AD-åtkomstpanelen
* Startprogrammet för Office 365
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilka metoder som du väljer att distribuera i din organisation är passar dig bäst.

### <a name="azure-ad-access-panel"></a>Azure AD-åtkomstpanelen
Åtkomstpanelen på https://myapps.microsoft.com är en webbaserad portal där en användare med ett organisationskonto i Azure Active Directory för att visa och starta molnbaserade program som de har beviljats åtkomst med Azure AD-administratör. Om du är en användare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), du kan också använda funktioner för hantering av självbetjäning via åtkomstpanelen.

![Azure AD-åtkomstpanelen](media/what-is-single-sign-on/azure-ad-access-panel.png)

Åtkomstpanelen är separat från Azure-portalen och kräver inte användarna måste ha en Azure-prenumeration eller Office 365-prenumeration.

Mer information om Azure AD-åtkomstpanelen finns i den [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Startprogrammet för Office 365
För organisationer som har distribuerat Office 365, visas även program som är tilldelade till användare via Azure AD i Office 365-portalen på https://portal.office.com/myapps. Detta gör det enkelt och praktiskt för användare i en organisation att starta sina appar utan att behöva använda en andra portal och är den rekommenderade app starta lösningen för organisationer som använder Office 365.

![](./media/what-is-single-sign-on/officeapphub.png)

Läs mer om startprogrammet för Office 365, [appen visas i Office 365-appstartaren](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar
Mest federerade program som stöder SAML 2.0, WS-Federation och OpenID connect också support möjligheten för användare att börja på programmet och sedan hämta loggat in via Azure AD genom automatisk omdirigering eller genom att klicka på en länk för inloggning. Detta kallas tjänstleverantör-initieras inloggnings- och mest federerade program i Azure AD-programgalleriet har stöd för den här (se dokumentationen länkad från appens konfigurationen för enkel inloggning för guiden i Azure-portalen för information).

![](./media/what-is-single-sign-on/workdaymobile.png)

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Inloggning direktlänkar för federerade, lösenordsbaserade eller befintliga appar
Azure AD stöder också enkel inloggning Direktlänkar till enskilda program som har stöd för lösenordsbaserad enkel inloggning, länkade enkel inloggning och någon form av federerad enkel inloggning.

Dessa länkar är särskilt utformad URL: er som skickar en användare via Azure AD logga in igen för ett visst program utan att användaren lanseringen dem från Azure AD åtkomst till panelen eller Office 365. Dessa enkel inloggnings-URL: er finns under fliken instrumentpanel i alla redan integrerade program i Active Directory-avsnittet i Azure-portalen som visas i skärmbilden nedan.

![](./media/what-is-single-sign-on/deeplink.png)

Dessa länkar kan kopieras och klistras in var du vill att ange en länk till det valda programmet. Det kan i ett e-postmeddelande eller i valfri anpassad webbaserad portal som du har konfigurerat för programåtkomst för användare. Här är ett exempel på en Azure AD direkt inloggnings-URL för enkel för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Liknar specifika URL: er för åtkomstpanelen, du kan också anpassa denna URL genom att lägga till en av de aktiva eller verifierade domänerna för din katalog myapps.microsoft.com-domän. Detta säkerställer att alla företagsprofilering har lästs in direkt på sidan logga in utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på någon av länkarna programspecifika visas de först finns i deras organisationens inloggningssida (förutsatt att de redan inte är inloggad) och efter inloggningen dirigeras om till appen utan att stoppa på åtkomstpanelen först. Om användaren saknar förutsättningar för att komma åt programmet, till exempel webbläsartillägget lösenordsbaserad enkel inloggning sedan uppmanas länken användaren att installera tillägget saknas. Webbadressen för länken förblir konstant om enkel inloggning för konfigurationen för programmet ändras.

Dessa länkar använder samma metoder för kontroll av åtkomst som åtkomstpanelen och Office 365 och endast de användare eller grupper som har tilldelats till programmet i Azure-portalen kommer att kunna autentisera. Alla användare som är obehörig visas ett meddelande som förklarar att de inte har beviljats åtkomst och får en länk för att läsa in panelen om du vill visa tillgängliga program som de har åtkomst.

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)
* [Introduktion till hantering av åtkomst till appar](what-is-access-management.md)
* [Jämför funktioner för att hantera externa identiteter i Azure AD](../active-directory-b2b-compare-b2c.md)


