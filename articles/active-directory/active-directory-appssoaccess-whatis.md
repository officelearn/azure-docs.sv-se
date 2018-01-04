---
title: "Vad är programåtkomst och enkel inloggning med Azure Active Directory? | Microsoft Docs"
description: "Använd Azure Active Directory för att aktivera enkel inloggning till alla SaaS och webb-program som du behöver för företag."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 75d1a3fd-b3c5-4495-a5c8-c4c24145ff00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 4fd80e147f3f24676bde77a1aa4e9f29432f544d
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Vad är programåtkomst och enkel inloggning med Azure Active Directory?
Enkel inloggning innebär att kunna komma åt alla program och resurser som du behöver göra affärer, genom att logga in bara en gång med ett enda användarkonto. När du är inloggad du har åtkomst till alla program som du behöver, utan som krävs för att autentisera (t.ex. Ange ett lösenord) en andra gång.

Många organisationer förlita sig på programvara som en tjänst (SaaS)-program, till exempel Office 365, rutan och Salesforce för slutanvändarens produktivitet. Tidigare IT-personal måste individuellt skapa och uppdatera användarkonton i varje SaaS-program och användarna behöver ett lösenord för varje SaaS-program.

Azure Active Directory utökar lokala Active Directory till molnet, så att användarna kan använda sina primära organisationskonto inte bara logga in på sina domänanslutna enheter och företagets resurser, men även alla webb- och SaaS-program behövs för sitt jobb.

Därför inte bara användare behöver inte hantera flera uppsättningar av användarnamn och lösenord, sina program åtkomst kan etableras eller tas bort automatiskt baserat på deras organisationsmedlemmar och deras status som ett anställda. Azure Active Directory introducerar säkerhets- och styrning-kontroller att centralt hantera användarnas åtkomst i SaaS-program.

Azure AD möjliggör enkel integrering till många av dagens populära SaaS-program. Det tillhandahåller identitets- och åtkomsthantering och gör det möjligt att enkel inloggning till program direkt, eller att upptäcka och starta dem från en portal, till exempel Office 365 eller Azure AD-åtkomstpanelen.

Arkitekturen för integrering består av följande fyra huvudsakliga byggblock:

* Ger användare åtkomst till sina SaaS-program baserat på deras organisationens konto i Azure AD för enkel inloggning. Enkel inloggning är vad användarna kan autentisera till ett program med enkel organisationskontot.
* Användaretablering kan användaretablering och avetablering på målet SaaS baserat på ändringar i Windows Server Active Directory och/eller Azure AD. Ett etablerade konto är vad innebär att användaren kan ha behörighet att använda ett program när de har autentiserats via enkel inloggning.
* Centraliserad åtkomst programhantering i Azure-hanteringsportalen möjliggör enskild punkt SaaS programåtkomst och hantering, med möjlighet att delegera programmet åtkomst beslutsfattande och godkännanden vem som helst i organisationen
* Enhetlig rapportering och övervakning av användaraktivitet i Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Hur fungerar enkel inloggning med Azure Active Directory?
När en användare ”” till ett program, går genom en autentiseringsprocess där de krävs för att bevisa att de är de som de säger att de är. Detta görs normalt genom att ange ett lösenord som lagras på programmet utan enkel inloggning, och användaren behöver känna till lösenordet.

Azure AD stöder tre olika sätt att logga in på program:

* **Federerad enkel inloggning** gör det möjligt att omdirigera till Azure AD för autentisering av användare i stället för att fråga efter eget lösenord. Detta stöds för program som stöd för protokoll, till exempel SAML 2.0, WS-Federation eller OpenID Connect och är bra läget för enkel inloggning.
* **Lösenordsbaserade enkel inloggning** möjliggör säker lagring av lösenord för program- och replay-med hjälp av en webbläsare webbtillägg eller mobilapp. Detta utnyttjar den befintliga inloggningsprocessen tillhandahålls av programmet, men kan administratören hantera lösenord och kräver inte att användaren känner till lösenordet.
* **Befintliga enkel inloggning** aktiverar Azure AD för att utnyttja alla befintliga enkel inloggning som har ställts in för programmet, men gör att programmen kan länkas till på Office 365 eller Azure AD åtkomst panelen portalerna och kan också ytterligare rapportering i Azure AD när programmen det startas.

När en användare har autentiserats med ett program, de måste också ha en kontoposten etablerad på det program som talar om programmet där det behörigheter och åtkomstnivå är i programmet. Etableringen av den här kontoposten kan antingen ske automatiskt eller det kan ske manuellt av en administratör innan användaren får åtkomst för enkel inloggning.

 Mer information om dessa lägen för enkel inloggning och etablerar nedan.

### <a name="federated-single-sign-on"></a>Federerad enkel inloggning
Gör att användare i din organisation kan loggas in automatiskt till en tredje parts SaaS-program med Azure AD med hjälp av informationen om användarkontot från Azure AD federerad enkel inloggning.

I det här scenariot när du redan har loggats i Azure AD och du vill komma åt resurser som styrs av en tredje parts SaaS-program eliminerar federation behovet av att en användare autentiseras igen.

Azure AD har stöd för federerad enkel inloggning med program som stöder SAML 2.0, WS-Federation, eller OpenID connect protokoll.

Se även: [hantera certifikat för federerad enkel inloggning](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Lösenordsbaserade enkel inloggning
Konfigurera lösenordsbaserade enkel inloggning gör att användare i din organisation kan loggas in automatiskt till en tredje parts SaaS-program med Azure AD med hjälp av informationen om användarkontot från SaaS-program från tredje part. När du aktiverar den här funktionen Azure AD samlar in och lagrar informationen om användarkontot och relaterade lösenordet på ett säkert sätt.

Azure AD stöder lösenordsbaserad enkel inloggning på för molnbaserade appar som har en HTML-baserad inloggningssidan. Genom att använda ett anpassat webbläsare plugin-program kan AAD automatiserar en användares inloggning i processen via på ett säkert sätt hämta autentiseringsuppgifter, till exempel användarnamn och lösenord från katalogen och anger autentiseringsuppgifterna i programmets inloggningssidan för användarens räkning . Det finns två användningsområden:

1. **Administratören hanterar autentiseringsuppgifter** – administratörer kan skapa och hantera autentiseringsuppgifter och tilldelar dessa behörigheter till användare eller grupper som behöver åtkomst till programmet. I dessa fall måste slutanvändaren behöver inte känner till autentiseringsuppgifterna, men fortfarande får enkel inloggning åtkomst till programmet genom att klicka på den i deras åtkomstpanelen eller via en angiven länk. På så sätt kan både livscykelhantering av autentiseringsuppgifter som administratör, samt bekvämlighet för slutanvändare där de inte behöver komma ihåg eller hantera app-specifik lösenord. Autentiseringsuppgifterna som har dolts från användaren vid automatisk inloggning i process. men de är tekniskt sett kan upptäckas av användaren med hjälp av web felsökningsverktyg och användare och administratörer bör följa samma säkerhetsprinciper som om autentiseringsuppgifterna som presenteras direkt av användaren. Autentiseringsuppgifter för administratören är mycket användbara när ge kontot tillgång som delas av många användare, till exempel sociala medier eller dokumentdelning program.
2. **Användaren hanterar autentiseringsuppgifter** – administratörer kan tilldela program till användare eller grupper och tillåta att användarna anger sina egna autentiseringsuppgifter direkt vid åtkomst till programmet för första gången i sina åtkomstpanelen. Detta skapar i syfte att underlätta för slutanvändare där de inte behöver kontinuerligt ange app-specifik lösenord varje gång som de har åtkomst till programmet. Den här användningsfall kan också användas som en version bricka administrativa hanteringen av de autentiseringsuppgifter som administratören kan där ange nya autentiseringsuppgifter för programmet senare utan att ändra app åtkomst upplevelse för slutanvändaren.

I båda fallen autentiseringsuppgifter lagras i ett krypterat tillstånd i katalogen och skickas endast över HTTPS under processen för automatisk inloggning. Azure AD erbjuder med lösenordsbaserade enkel inloggning på en smidig åtkomstlösning för Identitetshantering för appar som inte kan stödja federation protokoll.

Lösenordsbaserade SSO är beroende av ett webbläsartillägg på ett säkert sätt hämta program- och informationen från Azure AD och tillämpa det på tjänsten. De flesta SaaS tredjepartsprogram som stöds av Azure AD stöder denna funktion.

Användarens webbläsare kan vara för lösenordsbaserad enkel inloggning:
* Internet Explorer 8, 9, 10, 11--på Windows 7 eller senare
* Kanten på Windows 10 årsdagar Edition eller senare 
* Chrome--På Windows 7 eller senare, och i MacOS X eller senare
* Firefox 26.0 eller senare--på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare

### <a name="existing-single-sign-on"></a>Befintliga enkel inloggning
När du konfigurerar enkel inloggning för ett program innehåller ett tredje alternativ för ”befintliga enkel inloggning” Azure-hanteringsportalen. Det här alternativet kan bara en administratör skapa en länk till ett program och placera den på åtkomstpanelen för valda användare.

Om det finns ett program som har konfigurerats för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör till exempel använda alternativet ”befintliga enkel inloggning” så här skapar du en länk till den på åtkomstpanelen. När användare har åtkomst till länken autentiseras med hjälp av Active Directory Federation Services 2.0 eller den befintliga enkla inloggning lösningen tillhandahålls av programmet.

### <a name="user-provisioning"></a>Användaretablering
Azure AD gör automatisk användaretablering och avetablering för konton i tredje parts SaaS-program från inom Azure-hanteringsportalen, med information om din Windows Server Active Directory eller Azure AD identitet för utvalda program. När en användare ges behörighet i Azure AD för något av dessa program, kan ett konto skapas automatiskt (etablerade) i målet SaaS-program.

När en användare tas bort eller information om deras ändringar i Azure AD, återspeglas ändringarna i SaaS-program. Detta innebär konfigurera automatisk identitet livscykelhantering gör att administratörer kan styra och tillhandahålla Automatisk etablering och avetablering från SaaS-program. Denna automatisering av Identitetshantering livscykel är aktiverat som användaretablering i Azure AD.

Läs mer i [automatisk Användaretablering och avetablering för SaaS-program](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Kom igång med Azure AD application gallery
Redo att sätta igång? Att distribuera enkel inloggning mellan Azure AD och SaaS-program som används i din organisation, Följ dessa riktlinjer.

### <a name="using-the-azure-ad-application-gallery"></a>Med Azure AD application gallery
Den [Azure Active Directory-Programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/) innehåller en lista över program som stöder en form av enkel inloggning med Azure Active Directory.

![][1]

Här följer några tips för att hitta appar med vilka funktioner som de stöder:

* Azure AD stöder automatisk etablering och avetablering för alla ”aktuell” appar i den [Azure Active Directory-Programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/).
* En lista över federerade program som uttryckligen har stöd för federerad enkel inloggning med ett protokoll, till exempel SAML WS-Federation, eller OpenID Connect finns [här](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

När du har hittat ditt program, kan du sätta igång genom att följa instruktionerna visas i appgalleriet och i Azures hanteringsportal för att aktivera enkel inloggning.

### <a name="application-not-in-the-gallery"></a>Programmet inte i galleriet?
Om programmet inte hittas i Azure AD application gallery, finns följande alternativ:

* **Lägg till en ny app som du använder** -använder anpassad kategori i appgalleriet i Azure-hanteringsportalen för att ansluta ett olistade program som använder din organisation. Du kan lägga till alla program som stöder SAML 2.0 som en federerad app eller alla program som har en HTML-baserad-inloggningssida som lösenord SSO app. Mer information finns i den här artikeln på [lägga till egna program](application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Lägg till dina egna app som du utvecklar** - om du har utvecklat programmet själv, Följ riktlinjerna i Azure AD-dokumentation för utvecklare att implementera federerad enkel inloggning eller etablering med hjälp av Azure AD graph API. Mer information finns i följande resurser:
  
  * [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Begär en appintegrering** -begär support för programmet som du behöver använda den [Azure AD Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-management-portal"></a>Med hjälp av Azure-hanteringsportalen
Du kan använda Active Directory-tillägget i Azure-hanteringsportalen för att konfigurera de program enkel inloggning. Du måste välja en katalog från Active Directory-avsnittet i portalen som ett första steg:

![][2]

Om du vill hantera din SaaS-program från tredje part, kan du växla till fliken program i den valda katalogen. Den här vyn kan administratörer:

* Lägga till nya program från Azure AD-galleriet, samt appar som du utvecklar
* Ta bort integrerade program
* Hantera program som de redan har integrerat

Vanliga administrativa uppgifter för ett SaaS-program från tredje part är:

* Aktivera enkel inloggning med Azure AD med lösenord SSO eller, om det finns för målet SaaS federerad enkel inloggning
* Du kan också aktivera användaretablering för användaren etablering och avetablering (livscykelhantering identitet)
* För program där användaretablering är aktiverat, välja vilka användare som har åtkomst till programmet

Galleri för appar som har stöd för federerad enkel inloggning måste configuration vanligtvis du ange ytterligare konfigurationsinställningar som t.ex certifikat och metadata för att skapa ett federerat förtroende mellan appar från tredje part och Azure AD. Guiden vägleder dig igenom informationen och ger enkel åtkomst till specifika data för SaaS-program och anvisningar.

För galleriet appar som stöder automatisk användaretablering, kräver detta att ge Azure AD-behörigheter för att hantera dina konton i SaaS-program. Som minimum måste du ange autentiseringsuppgifter för Azure AD ska använda vid autentisering via till målprogrammet. Om ytterligare konfigurationsinställningar måste tillhandahållas beror på kraven för programmet.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Distribuera Azure AD-integrerade program till användare
Azure AD innehåller flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation:

* Azure AD-åtkomstpanelen
* Startprogrammet för Office 365
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilken metod du vill distribuera i din organisation är så önskar.

### <a name="azure-ad-access-panel"></a>Azure AD-åtkomstpanelen
Åtkomstpanelen på https://myapps.microsoft.com är en webbaserad portal som gör att en användare med ett organisationskonto i Azure Active Directory för att visa och starta molnbaserade program som de har beviljats åtkomst av Azure AD-administratör . Om du är en slutanvändare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), du kan också använda självbetjäning hanteringsmöjligheter till åtkomstpanelen.

![][3]

Åtkomstpanelen är separat från Azure-hanteringsportalen och kräver inte användarna måste ha en Azure-prenumeration eller Office 365-prenumeration.

Mer information om Azure AD-åtkomstpanelen finns i [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Startprogrammet för Office 365
Program som tilldelats till användare via Azure AD visas också i Office 365-portalen på https://portal.office.com/myapps för organisationer som har distribuerat Office 365. Detta gör det enkelt och för användare i en organisation att starta sina appar utan att behöva använda andra portal och är den rekommenderade app startar lösningen för organisationer som använder Office 365.

![][4]

Mer information om startprogrammet för Office 365 finns [har appen visas i Office 365 app starta](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar
Mest federerade program som stöder SAML 2.0, WS-Federation eller OpenID ansluta också stöd för möjligheten för användare att starta i programmet och sedan hämta loggat in via Azure AD genom automatisk omdirigering eller genom att klicka på en länk för inloggning. Detta kallas tjänstleverantör-initieras inloggning och mest federerade program i Azure AD application gallery stöder denna (se dokumentationen som länkas från appens enkel inloggning konfigurationsguiden i Azures hanteringsportal för mer information).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Inloggning direktlänkar för federerade, lösenordsbaserade eller befintliga appar
Azure AD stöder också enkel inloggning Direktlänkar till enskilda program som stöder lösenordsbaserad enkel inloggning, befintlig enkel inloggning och någon form av federerad enkel inloggning.

Dessa länkar är särskilt utformad URL: er som skickar en användare via Azure AD-inloggning i processen för ett visst program utan att användaren starta dem från Azure AD-åtkomstpanelen eller Office 365. Enkel inloggning webbadresserna finns under fliken instrumentpanel i alla redan integrerade program i Active Directory-avsnittet i Azure-hanteringsportalen, som visas i skärmbilden nedan.

![][6]

Dessa länkar kan kopieras och klistras in var som helst vill du ange en länk till det valda programmet. Detta kan bero på ett e-postmeddelande eller i alla anpassade webbaserad portal som du har skapat för programåtkomst för användaren. Här är ett exempel på en Azure AD direkt enkel inloggnings-URL för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Liknar organisation-specifika URL: er för åtkomstpanelen, du kan anpassa ytterligare URL: en genom att lägga till en aktiv eller verifierade domäner för din katalog myapps.microsoft.com-domän. Detta säkerställer att alla organisationens företagsanpassning har lästs in direkt på sidan logga in utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på en av länkarna programspecifika data, de först finns i deras organisationens inloggningssida (förutsatt att de inte är redan inloggad) och efter inloggning omdirigeras till appen utan att först stoppa på åtkomstpanelen. Om användaren saknar förutsättningar för att komma åt programmet, till exempel webbläsartillägget lösenordsbaserade enkel inloggning sedan uppmanas länken användaren att installera tillägget saknas. Länk-URL förblir konstant om enkel inloggning konfigurationen för programmet ändras.

Dessa länkar använder samma access control mekanismer som åtkomstpanelen och Office 365 och endast de användare eller grupper som har tilldelats till programmet i Azure-hanteringsportalen kommer att kunna autentisera. Alla användare som inte är auktoriserad visas ett meddelande om att de inte har beviljats åtkomst och ges en länk för att läsa in åtkomstpanelen om du vill visa tillgängliga program som de har åtkomst.

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hitta ej sanktionerad molnprogram med Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Introduktion till att hantera åtkomst till appar](active-directory-managing-access-to-apps.md)
* [Jämförelse av funktioner för att hantera externa identiteter i Azure AD](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
