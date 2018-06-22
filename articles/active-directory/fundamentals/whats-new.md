---
title: Nyheter Viktig information för Azure AD | Microsoft Docs
description: Läs mer om nyheterna i Azure Active Directory (Azure AD), till exempel senaste viktig information, kända problem, felkorrigeringar, föråldrade funktioner och kommande ändringar.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: f5c2f8901db5fbeacbd9cc79a03c6dad63842ac9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268159"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

> Hålla dig uppdaterad med vad är nytt i Azure Active Directory (AD Azure) genom att prenumerera på den [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [feed](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).

Azure AD tar emot förbättringar kontinuerligt. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

-   De senaste versionerna
-   Kända problem
-   Felkorrigeringar
-   Föråldrade funktioner
-   Planer för ändringar

Den här sidan uppdateras varje månad, så kontrollera igen den regelbundet.

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändringsmeddelande: säkerhetskorrigering till flödet delegerad behörighet för appar som använder Azure AD aktivitet loggar API

**Typ:** ändringsplan  
**Tjänstekategori:** övervakning och rapportering  
**Produkten kapaciteten:** rapportering

På grund av våra bättre säkerhet tvingande som vi har fått att göra en ändring av behörigheter för appar som använder ett flöde delegerad behörighet att komma åt [Azure AD aktivitet loggar API: erna](https://aka.ms/aadreportsapi). Den här ändringen kommer att ske av **26 juni 2018**.

Följ stegen nedan för att se till att appen inte bryta när ändringen sker om någon av dina appar använder Azure AD aktivitet Logga API: er.

**Uppdatera app-behörigheter**

1.  Logga in på Azure Portal.

2.  På den vänstra navigeringsfält för, väljer **Azure Active Directory**, och välj sedan **App registreringar**.

3.  Välj din app som använder Azure AD aktivitet loggar API, Välj **inställningar**, och klicka sedan på den **inställningar** bladet väljer **nödvändiga behörigheter**.

4.  I den **nödvändiga behörigheter** bladet väljer den **Windows Azure Active Directory** API.

5.  I den **delegerad behörighet** område i den **Aktivera åtkomst** bladet markerar du kryssrutan bredvid **Läs directory** data och välj sedan **spara**.

    Du måste gå tillbaka till den **nödvändiga behörigheter** bladet.

    ![Alternativet för delegerade behörigheter läsa directory](./media/whats-new/app-registration-delegate-read-directory.png)

6.  Välj **bevilja behörigheter**, och välj sedan **Ja**.
    
    >[!Note]
    >Du måste vara en Global administratör för att ge behörigheter till appen.

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>Ändringar av ExpressRoute-support

**Typ:** ändringsplan  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** plattform  

Programvara som en tjänst som erbjuder som Azure Active Directory (AD Azure) är utformade att fungera bäst genom att gå direkt via Internet, utan att ExpressRoute eller någon annan privat VPN-tunnlar. Därför på **1 augusti 2018**, vi stoppas stöder ExpressRoute för Azure AD-tjänster med hjälp av offentlig Azure-peering och Azure communities i Microsoft-peering. Alla tjänster som påverkas av den här ändringen märker Azure AD-trafik rörliga gradvis från ExpressRoute till Internet.

Medan vi ändrar vår support vi också vet att det är fortfarande situationer där du kan behöva använda en särskild uppsättning kretsar för autentiseringstrafik. Därför fortfarande Azure AD stöder per klient IP-adressintervall begränsningar med ExpressRoute och tjänster redan på Microsoft-peering ”andra Office 365 onlinetjänster”-communityn. Om dina tjänster påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du är på offentlig Azure-peering.** Du måste flytta till Microsoft-peering och registrera dig för den **andra Office 365 onlinetjänster (12076:5100)** community. Mer information om hur du flyttar från offentlig Azure-peering för Microsoft-peering finns i [flytta en offentlig peering till Microsoft-peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) artikel.

- **Om du använder Microsoft-peering.** Du måste registrera dig för den **andra Office 365 Online-tjänsten (12076:5100)** community. Mer information om krav för routning finns i [stöd för BGP communities avsnittet](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) i ExpressRoute routning krav artikel.

Om du måste fortsätta att använda dedicerade kretsar, behöver du tala med din Microsoft Account team om hur du hämtar tillstånd att använda den **andra Office 365 Online-tjänsten (12076:5100)** community. MS Office-hanterade-granskningsnämnd ska kontrollera om du behöver dessa kretsar och kontrollera att du förstår följderna av att hålla dem tekniska. Obehörig prenumerationer försök att skapa filter för routning för Office 365 får ett felmeddelande. 
 
---


### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API: er för administrativa scenarier för villkor för användning

**Typ:** ny funktion  
**Tjänstekategori:** användningsvillkoren  
**Produkten kapaciteten:** Developer-upplevelse
 

 Vi har lagt till Microsoft Graph API för administrationsåtgärden av Azure AD användningsvillkoren. Du kan skapa, uppdatera, ta bort objektet användningsvillkoren.

---
 


### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägg till slutpunkt för flera innehavare av Azure AD som en identitetsleverantör i Azure AD B2C

**Typ:** ny funktion  
**Tjänstekategori:** B2C - identitetshanteringen  
**Produkten kapaciteten:** B2B/B2C
 

Anpassade principer kan, du nu använda vanliga slutpunkten Azure AD som en identitetsleverantör i Azure AD B2C. På så sätt kan du ha en enda åtkomstpunkt för alla Azure AD-användare som loggar in dina program. Mer information finns i [Azure Active Directory B2C: Tillåt användare att logga in på en identitetsleverantör för flera innehavare Azure AD med anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

 

---


### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använda interna URL: er för att komma åt appar från var som helst med vår Mina appar inloggning tillägget och Azure AD Application Proxy

**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning
 

Användare kan nu komma åt program via interna URL: er även när utanför företagets nätverk genom att använda Mina appar säker inloggning tillägget för Azure AD. Detta fungerar med alla program som du har publicerat med Azure AD Application Proxy på valfri webbläsare som också har åtkomstpanelen webbläsartillägget installerad. URL: en omdirigering funktioner aktiveras automatiskt när en användare loggar in på tillägget. Tillägget är tillgänglig för hämtning på [kant](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), och [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).
 

---
 


### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggningar aktivitetsloggar

**Typ:** ny funktion  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** övervakning och rapportering
 

Med introduktionen av ett fält kallas **Klientappen** i aktivitetsloggar inloggning kunder nu se användare som använder äldre autentiseringar. Kunder kommer att kunna komma åt informationen med hjälp av inloggningar MS Graph API eller via inloggningssidan loggar aktivitet i Azure AD-portalen där du kan använda den **Klientappen** kontrollen att filtrera på äldre autentiseringar. Läs i dokumentationen för mer information.
 

---
 

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Data i Europa för Europa kunder

**Typ:** ny funktion  
**Tjänstekategori:** andra  
**Produkten kapaciteten:** GoLocal
 

Kunder i Europa kräver att data ska vara i Europa och replikeras inte utanför Europeiska Datacenter möte sekretess-och Europeiska lagstiftning. Detta [artikel](https://go.microsoft.com/fwlink/?linkid=872328) innehåller specifik information om vilka identitetsinformation lagras i Europa och ger information om information som lagras utanför Europeiska datacenter. 

 

---
 

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Ny användare etablering app integrering med SaaS - maj 2018

**Typ:** ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapaciteten:** 3 part-integrering
 

Azure AD kan du automatisera genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Vi har lagt till användare som har stöd för följande program i appgalleriet för Azure AD-etablering för maj 2018:

- [BlueJeans](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Hörnstenarna OnDemand](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

En lista över alla program som stöder användaretablering i Azure AD-galleriet, se [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

 

---
 

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-åtkomst granskning av grupper och app-åtkomst ger nu återkommande granskningar

**Typ:** ny funktion  
**Tjänstekategori:** åtkomst granskningar  
**Produkten kapaciteten:** styrning
 

Åtkomst går igenom grupper och appar finns nu vanligtvis som en del av Azure AD Premium P2.  Administratörer kommer att kunna konfigurera åtkomst granskning av gruppmedlemskap och programmet tilldelningar för att återkomma automatiskt med jämna mellanrum, t.ex.
 

---
 

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD aktivitetsloggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** ny funktion  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** övervakning och rapportering
 

Azure AD aktivitetsloggar, vilket innefattar inloggningar och granskningsloggar, är nu tillgängliga via MS Graph. Vi har exponeras 2 slutpunkterna via MS Graph att komma åt dessa loggar. Läs vår [dokument](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) för programmatisk åtkomst till Azure AD Reporting API: er för att komma igång. 
 

---
 



### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B-inlösning får och lämna en organisation

**Typ:** ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapaciteten:** B2B/B2C
 

**Precis i tid inlösning:** när du delar en resurs med en gästanvändare med B2B-API – du behöver inte skicka ett e-postmeddelande med särskilda inbjudan. I de flesta fall gästanvändaren kan bara komma åt resursen och kommer att gå igenom inlösning upplevelsen precis i tid. Ingen större inverkan på grund av saknade e-postmeddelanden. Inga fler ber användarna gäst ”gjorde du klickar på länken inlösning systemet skickat till dig”?. Det innebär när SPO använder inbjudan manager – grumligt bifogade filer kan ha samma kanoniska URL för alla användare – interna och externa – alla med inlösning status.

**Moderna inlösning upplevelse:** inga fler dela skärmen inlösning startsida. Användarna ser en modern medgivande erfarenhet av bjuda in organisationens sekretesspolicy, precis som de gör för 3 partsappar.


**Gästanvändare kan lämna organisationen:** när en användare relation med en organisations är över, de kan självbetjäning lämnar organisationen. Inga fler anrop bjuda in org admin ”tas bort”, inga fler höjer supportärenden.
 

---
 


### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar tillgängliga i galleriet för Azure AD App - maj 2018

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** 3 part-integrering
 

I maj 2018 har vi lagt till stöd för följande 18 nya appar i vår App galleriet med Federation:

 

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), [Infogix Data3Sixty Govern](), [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

 

Du kan också hitta i dokumentationen för alla program [här](https://aka.ms/appstutorial).

Visar en lista över ditt program i appgalleriet för Azure AD finns i [visa ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing). 

 

---
 

### <a name="deployment-plans---guidance-to-get-you-started-with-azure-active-directory"></a>Distribution planer - vägledning för att komma igång med Azure Active Directory

**Typ:** ny funktion  
**Tjänstekategori:** andra  
**Produkten kapaciteten:** Directory
 

Söker efter steg-för-steg-vägledning för att distribuera (SSPR SSO, CA, App-Proxy, Användaretablering, ADFS till Tereftalsyra eller AD FS till PHS?). Vi har distribution planer för att!

Gå till [Aka.ms/DeploymentPlans](http://Aka.ms/DeploymentPlans) där du kan komma åt slutpunkt till slutpunkt guider för att starta hämta värdet från dessa funktioner.



- [Hur kan jag få distribution planer](http://Aka.ms/DeploymentPlans)? 

- Du kan ge feedback [här](http://aka.ms/DeploymentPlanFeedback)

- Har du några frågor? Nå ut till: [IDGitDeploy@microsoft.com](mailto:IDGitDeploy@microsoft.com)



     

 

---
 

### <a name="enterprise-applications-search---load-more-apps"></a>Företagsprogram Sök - läsa in flera program

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** enkel inloggning
 

Svårt att hitta dina program / service säkerhetsobjekt? Vi har lagt till möjligheten att läsa in fler program i din företagsprogram listan med alla program. Som standard visar vi 20 program. Nu kan du klicka på läsa in mer att visa fler program. 
 

---
 


### <a name="public-preview-of-new-and-improved-sign-ins-user-experience-in-azure-portal"></a>Förhandsversion av nya och förbättrade inloggningar användaren upplevelse i Azure Portal

**Typ:** ändrade funktionen  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** övervakning och rapportering
 

Vi kan glad över att införa nya och förbättrade inloggning loggar användarupplevelsen från i Azure AD-portalen. Med den nya inloggningar-användarupplevelsen får kunder nu du följande:

- Bättre svarstid från två timmar att inom 5 minuter. Kolla in våra [svarstid](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-latencies-azure-portal) docs för mer information.

- Möjligheten att lägga till filter som dynamiskt med knappen ”kolumner”. Genom att lägga till kolumner i rapporten i UX, kan du automatiskt se dem som filter som du kan använda.

- Möjlighet att sortera efter datum, användarnamn och program.

- Inkludering av äldre autentiseringar och möjlighet att filtrera för äldre autentiseringar med hjälp av kolumnen ”Klientappen”.

- Inkludering av ett nedladdningsbart PowerShell-skript som är anpassad baserat på de filtervillkor som du väljer i UX. Med det här PowerShell-skriptet kan du så många rader med data som du vill (baserat på dina filterkriterier) som ger resultatet i ett CSV-format.

Mer information om den här funktionen finns [inloggning aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins).
 

---
 


### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Versionen av AADConnect innehåller en offentlig förhandsgranskning av integrering med PingFederate viktiga säkerhetsuppdateringar, kan programfel många korrigeringar och nya bra nya felsökningsverktyg. 

**Typ:** ändrade funktionen  
**Tjänstekategori:** AD Connect  
**Produkten kapaciteten:** Identitetshantering livscykel
 

Versionen av AADConnect innehåller en offentlig förhandsgranskning av integrering med PingFederate viktiga säkerhetsuppdateringar, kan programfel många korrigeringar och nya bra nya felsökningsverktyg. Du kan hitta viktig information [här](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

 

---
 


### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-åtkomst granskar: tillämpa automatiskt

**Typ:** ändrade funktionen  
**Tjänstekategori:** åtkomst granskningar  
**Produkten kapaciteten:** styrning
 

Åtkomst går igenom grupper och appar finns nu allmänt tillgänglig som en del av Azure AD Premium P2. En administratör kan konfigurera för att automatiskt använda den granskare ändringar till gruppen eller app som åtkomst granska är klar. Administratören kan också ange vad som händer med användarens fortsatt åtkomst om granskare inte svara, ta bort åtkomst, blockera åtkomst eller ta system rekommendationer. 

 

---
 


### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>ID-token kan inte längre returneras med frågan response_mode för nya appar. 

**Typ:** ändrade funktionen  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 

Appar som har skapats på eller efter 2018/25/4 kommer inte längre att kunna begära ett **id_token** med hjälp av den **frågan** response_mode.  Detta öppnar Azure AD infogade med OIDC-specifikationer och bidrar till att minska risken för angrepp appar.  Appar som har skapats innan 2018/25/4 inte är blockerade från att använda den **frågan** response_mode med en response_type av **id_token**.  Felet som returnerades när du begär en id_token från AAD, är **AADSTS70007: 'frågan' är inte ett giltigt värde för 'response_mode' när du begär en token**.

Den **fragment** och **form_post** response_modes fortsätter att fungera - när du skapar nya programobjekt (t.ex. för App Proxy användning), se till att använda något av dessa response_modes innan de skapar en ny programmet.  
 

---
 



## <a name="april-2018"></a>April 2018
 


### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C-åtkomsttoken är GA

**Typ:** ny funktion  
**Tjänstekategori:** B2C - identitetshanteringen  
**Produkten kapaciteten:** B2B/B2C
 

Du kan nu komma åt Web API skyddas av Azure AD B2C med åtkomsttoken. Funktionen flyttas från förhandsversion till GA. Användargränssnitt för att konfigurera Azure AD B2C-program och webb-API: er har förbättrats och andra mindre förbättringar har gjorts.
 
Mer information finns i [Azure AD B2C: begärande åtkomsttoken](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).


---
 

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa enkel inloggningkonfigurationen för SAML-baserade program

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** enkel inloggning
 

När du konfigurerar SAML baserat SSO-program kan du testa integreringen på konfigurationssidan. Om det uppstår ett fel under inloggning kan du ange fel i tester upplevelsen och ger Azure AD Lösningssteg att lösa det specifika felet.

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)


---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD-användningsvillkor har nu rapportering per användare

**Typ:** ny funktion  
**Tjänstekategori:** användningsvillkoren  
**Produkten kapaciteten:** kompatibilitet
 

Administratörer kan markera ett visst villkor för användning och visa alla användare som har godkänt för villkoren och vad datum/tid den ägde rum.


Mer information finns i [användningsvillkoren för Azure AD-funktion](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Utlåsningsskydd för riskfyllda IP-adresser för AD FS-extranätet 

**Typ:** ny funktion  
**Tjänstekategori:** andra  
**Produkten kapaciteten:** övervakning och rapportering
 

Connect Health nu stöder möjligheten att identifiera IP-adresser som överskrider ett tröskelvärde för misslyckad U/P inloggningar på timvis eller daglig basis. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antalet misslyckade inloggningar som genereras på varje timme/daglig basis med anpassningsbara tröskelvärdet.
- E-postbaserad aviseringar som visas när en specifik IP-adress har överskridit tröskelvärdet för misslyckade U/P inloggningar på varje timme/daglig basis.
- Ett alternativ för att göra en detaljerad analys av data


Mer information finns i [riskfyllda IP-rapport](https://aka.ms/aadchriskyip).

 

---
 

### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel appkonfiguration med metadatafil eller URL

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** enkel inloggning
 

Administratörer kan överföra en SAML-metadatafil om du vill konfigurera baserat SAML-inloggning för AAD-galleriet och icke-galleriet programmet på sidan Enterprise-program.

Du kan dessutom använda Azure AD application federation metadata-URL för att konfigurera enkel inloggning med det aktuella programmet.

Mer information finns i [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).
 

---
 

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD-användningsvillkor är nu allmänt tillgängliga

**Typ:** ny funktion  
**Tjänstekategori:** användningsvillkoren  
**Produkten kapaciteten:** kompatibilitet
 

Azure AD användningsvillkoren har flyttats från förhandsversion till allmänt tillgänglig.

Mer information finns i [användningsvillkoren för Azure AD-funktion](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

 

---
 

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

**Typ:** ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapaciteten:** B2B/B2C
 

Nu kan du ange vilka partnerorganisationer som du vill dela och samarbeta med i Azure AD B2B-samarbete. Om du vill göra det, kan du skapa en lista över specifika tillåta eller neka domäner. När en domän blockeras med hjälp av dessa funktioner, kan anställda inte längre skicka inbjudningar till personer i domänen.

Det hjälper dig att styra åtkomsten till resurser, samtidigt som en smidig användarupplevelse godkända.

Funktionen B2B-samarbete är tillgänglig för alla kunder i Azure Active Directory och kan användas tillsammans med Azure AD Premium-funktioner som villkorlig åtkomst och identitet skydd för mer detaljerad kontroll över när och hur externa användare loggar i och få åtkomst.

Mer information finns i [Tillåt eller blockera inbjudningar till B2B användare från specifika organisationer](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

 

---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar finns tillgängliga i Azure AD-appgalleriet

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** 3 part-integrering
 

I April 2018 har vi lagt till stöd för följande 13 nya appar i vår App galleriet med Federation:



Kriterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [hemlighet Server (lokalt)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamiska Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [Organisationsschema Nu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Prestandaövervakaren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco molnet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), hylla, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)



 Du hittar listan över tillgängliga självstudier här: [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

Mer information finns i [visa ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).


 

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Bevilja B2B-användare i Azure AD åtkomst till dina lokala program (allmänt tillgänglig förhandsversion)

**Typ:** ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapaciteten:** B2B/B2C
 

Som en organisation som använder Azure Active Directory (AD Azure) B2B-samarbetesfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD, kan du nu ange dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns i [Grant B2B-användare i Azure AD åtkomst till lokala program](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)
 

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Hämta SSO-integrationsjälvstudierna från Azure Marketplace

**Typ:** ändrade funktionen  
**Tjänstekategori:** andra  
**Produkten kapaciteten:** 3 part-integrering
 

Om ett program som anges i den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) har stöd för SAML-baserade enkel inloggning, klicka på **blir det nu** ger dig integration kursen som är associerade med programmet. 


---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för Azure AD-automatisk användaretablering till SaaS-program

**Typ:** ändrade funktionen  
**Tjänstekategori:** App-etablering  
**Produkten kapaciteten:** 3 part-integrering
 

Tidigare kunder som använder Azure Active Directory-kopplingar för SaaS-program (till exempel Salesforce ServiceNow och rutan) för användaretablering uppleva mycket långsamt om sina Azure AD-klienter som finns över 100 000 kombinerade användare och grupper och de med användar- och tilldelningar för att avgöra vilka användare som ska etableras.

På den 2 April har mycket betydande prestandaförbättringar distribuerats till Azure AD etablering tjänsten att avsevärt minska den tid som behövs för att utföra en inledande synkronisering mellan Azure Active Directory och mål SaaS-program.


Därför många kunder som hade inledande synkronisering till appar som tog många dagar eller aldrig slutförts nu slutförs inom några minuter eller timmar.

Mer information finns i [vad som händer under etablering?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---
 

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Återställning av lösenord med självbetjäning från Windows 10-låsskärmen för Azure AD-hybridanslutna datorer

**Typ:** ändrade funktionen  
**Tjänstekategori:** Self för återställning av lösenord  
**Produkten kapaciteten:** användarautentisering
 

Vi har uppdaterat funktionen SSPR för Windows 10 om du vill inkludera stöd för datorer som är hybrid anslutna Azure AD. Den här funktionen är tillgänglig i Windows 10 RS4 tillåter användare att återställa sina lösenord från låsskärmen för en Windows 10-dator. Användare som är aktiverad och registrerats för lösenordsåterställning via självbetjäning kan använda den här funktionen.

Mer information finns i [Azure AD-lösenordsåterställning från inloggningssidan](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).
 

---



## <a name="march-2018"></a>Mars 2018
 

### <a name="certificate-expire-notification"></a>Meddelande om att certifikatet upphör att gälla

**Typ:** fast  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** enkel inloggning
 
Azure AD skickar ett meddelande när ett certifikat för ett galleri eller icke-galleriet program slutar snart att gälla. 

Vissa användare fick inte meddelanden för företagsprogram som har konfigurerats för SAML-baserade enkel inloggning. Det här problemet har lösts. Azure AD skickar meddelanden för certifikat som upphör att gälla i 7, 30 och 60 dagar. Du kan se den här händelsen i granskningsloggarna. 

Mer information finns i:

- [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

 
---
 

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- och GitHub-identitetsproviders i Azure AD B2C

**Typ:** ny funktion  
**Tjänstekategori:** B2C - identitetshanteringen  
**Produkten kapaciteten:** B2B/B2C
 
Du kan nu lägga till Twitter eller GitHub som en identitetsleverantör i Azure AD B2C. Twitter flyttas från förhandsversion till GA. GitHub frigörs som förhandsversion.


Mer information finns i [vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---


### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Begränsa webbläsaråtkomst med Intune Managed Browser med Azure AD-programbaserad villkorsstyrd åtkomst för iOS och Android

**Typ:** ny funktion  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd
 

**Nu i public preview!**

**Intune Managed Browser SSO:** dina anställda kan använda enkel inloggning över interna klienter (till exempel Microsoft Outlook) och Intune Managed Browser för alla Azure AD-anslutna appar.

**Intunes hanterade webbläsare villkorlig åtkomst Support:** du nu kan kräva anställda att använda Intune Managed browser med principer för program-baserad villkorlig åtkomst.

Läs mer om detta i vår [blogginlägget](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Mer information finns i:

- [Konfigurera programmet-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

- [Konfigurera principer för hanterade webbläsare](https://aka.ms/managedbrowser)  



---
 

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Approxy-cmdletar i Powershell GA-modulen

**Typ:** ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapaciteten:** åtkomstkontroll
 
Stöd för Application Proxy-cmdlets är nu i Powershell-modulen GA! Observera att detta kräver att du vill hålla reda på Powershell-moduler - om du blir mer än ett år bakom vissa cmdletar kan sluta fungera. 


Mer information finns i [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365-inbyggda klienter stöds av sömlös enkel inloggning med ett icke-interaktivt protokoll

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 
Användare som använder Office 365 interna klienter (version 16.0.8730.xxxx och senare) får en tyst inloggning med sömlös SSO. Den här support tillhandahålls av tillägg ett icke-interaktiv protokoll (WS-Trust) till Azure AD.

Mer information finns i [hur logga in på en intern klient med sömlös SSO arbete?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

 
---
 

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenanted-endpoints"></a>Användare får en tyst inloggningsupplevelse med sömlös enkel inloggning om ett program skickar inloggningsbegäranden till Azure AD:s klientslutpunkter

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 
Användarna får en tyst inloggning, med sömlös SSO om ett program (till exempel `https://contoso.sharepoint.com`) skickar inloggningsförfrågningar till Azure AD-klient-slutpunkter – det vill säga `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för vanliga Azure AD-slutpunkt (`https://login.microsoftonline.com/common/<...>`).

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Behöver bara lägga till en Azure AD-URL, istället för två URL:er som tidigare, till användares zoninställningar för Intranätet för att rulla ut sömlös enkel inloggning

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 
Om du vill distribuera sömlös SSO till användarna, måste du lägga till endast en Azure AD-URL till användarnas intranät Zoninställningar med hjälp av Grupprincip i Active Directory: `https://autologon.microsoftazuread-sso.com`. Kunder har tidigare krävs för att lägga till två webbadresser.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** 3 part-integrering
 
I mars 2018 har vi lagt till stöd för följande 15 nya appar i vår App galleriet med Federation:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), assistenten som FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu moln](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Du hittar dokumentationen för alla program som här: [https://aka.ms/appstutorial](https://aka.ms/appstutorial)


 
---
 

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgänglig

**Typ:** ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Om du använder Azure AD Privileged Identity Management för directory roller, kan du nu använda PIMS Tidsbundna åtkomst och tilldelning funktioner för Azure-resurshanteraren roller, till exempel prenumerationer, resursgrupper, virtuella datorer och andra resurser som stöds i Azure Resource Manager. Använda Multifaktorautentisering när du aktiverar roller Just-In-Time och schemalägga aktiveringar tillsammans med godkända ändra windows. Den här versionen lägger dessutom till förbättringar som inte är tillgängliga under förhandsversion, inklusive en uppdaterad UI, godkännandearbetsflöden och möjligheten att utöka roller upphör snart att gälla och förnya utgångna roller.

Mer information finns i [PIM för Azure-resurser (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägger till valfria anspråk till din apps tokens (allmänt tillgänglig förhandsversion)

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 
Azure AD-appar kan nu anpassade eller valfri förfrågninsanspråk i JWTs eller SAML token.  Dessa är anspråk om användaren eller klient som inte ingår som standard i token, på grund av storlek eller tillämplighet villkor.  Detta är för närvarande i förhandsversion för Azure AD-appar på v1.0 och v2.0-slutpunkter.  Finns i dokumentationen för information om vilka anspråk kan läggas till och redigerar programmanifestet och be dem.  

Mer information finns i [valfritt anspråk i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 
Azure AD-dokument har uppdaterats för att Observera stöd för PKCE som möjliggör säkrare kommunikation under flödet för OAuth 2.0-auktoriseringskod bevilja.  Både S256 och klartext code_challenges stöds på version 1.0 och v2.0-slutpunkter. 

Mer information finns i [begär en Auktoriseringskoden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 

 
---
 

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Stöd för etablering av alla användarattributvärden i Workday Get_Workers-API:et

**Typ:** ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapaciteten:** 3 part-integrering
 
Förhandsversion av inkommande etablering från Workday till Active Directory och Azure AD nu stöder möjligheten att extrahera och etablering av alla attributvärden som är tillgängliga i Workday Get_Workers API. Detta lägger till stöd för hundratals ytterligare standard och anpassade attribut utöver de som levereras med den första versionen av arbetsdagen inkommande etablerar anslutningen.

Mer information finns: [Anpassa lista över användarattribut för Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---



### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändrar gruppmedlemskap från dynamisk till statisk och vice versa

**Typ:** ny funktion  
**Tjänstekategori:** grupphantering  
**Produkten kapaciteten:** samarbete
 
Det är möjligt att ändra hur hanteras medlemskap i en grupp. Detta är användbart när du vill behålla samma namn och ID i systemet, så att alla befintliga referenser i gruppen är fortfarande giltiga. Skapa en ny grupp krävs uppdaterar du dessa referenser.
Vi har uppdaterat Administrationscenter Azure AD för att lägga till stöd för den här funktionen. Kunder kan nu konvertera befintliga grupper från dynamiskt medlemskap till tilldelade medlemskap och vice versa. De befintliga PowerShell-cmdletarna finns också kvar.

Mer information finns i [ändra dynamiskt medlemskap till statisk och vice versa](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa)

 

 
---
 

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat utloggningsbeteende med sömlös enkel inloggning

**Typ:** ändrade funktionen  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering
 
Tidigare, även om användarna uttryckligen loggat ut från ett program som skyddas av Azure AD, de skulle loggas automatiskt igen med hjälp av sömlös SSO om de försöker komma åt ett Azure AD-program igen inom deras corpnet från sina domänanslutna enheter. Logga ut stöds med den här ändringen.  Detta kan användarna välja samma eller andra Azure AD-konto för att logga tillbaka i med i stället för att automatiskt loggas in med sömlös SSO.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)

 
---
 

### <a name="application-proxy-connector-version-154020-released"></a>Programproxy för anslutningsapp version 1.5.402.0 har släppts

**Typ:** ändrade funktionen  
**Tjänstekategori:** App Proxy  
**Produkten kapaciteten:** identitet säkerhet och skydd
 
Den här connector-versionen som gradvis sprids via November. Den här nya connector-versionen innehåller följande ändringar:

- Kopplingen nu anger domän nivån cookies i stället underordnade domännivå. Detta garanterar en smidigare inloggning och förhindrar redundant autentisering anvisningarna.
- Stöd för chunked-kodning begäranden
- Förbättrad connector hälsoövervakning 
- Flera felkorrigeringar och stabilitet

Mer information finns i [förstå Azure AD Application Proxy kopplingar](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

 
---
 

 



## <a name="february-2018"></a>Februari 2018
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** ändringsplan  
**Tjänstekategori:** Directory Management  
**Produkten kapaciteten:** Directory
 

Navigering upplevelsen för att hantera användare och grupper har effektiviserats. Nu kan du navigera från översikt över directory direkt till en lista över alla användare enklare åtkomst till listan över borttagna användare. Du kan också navigera från översikt över directory direkt till listan över alla grupper med lättare åtkomst till inställningar för hantering av grupper. Och även från översikt katalogsidan, du kan söka efter en användare, grupp, företagsprogram eller appregistrering.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgängligheten för inloggningar och granska rapporter i Microsoft Azure drivs av 21Vianet (Azure Kina 21Vianet)

**Typ:** ny funktion  
**Tjänstekategori:** statliga moln  
**Produkten kapaciteten:** övervakning och rapportering
 

Azure AD loggen aktivitetsrapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet) instanser. Det ingår följande loggar:

- **Inloggningar aktivitetsloggar** -innehåller alla inloggningar loggar som är associerade med din klient.

- **Granskningsloggarna för lösenord via självbetjäning** -innehåller alla SSPR granskningsloggarna.

- **Directory Management granska loggar** -innehåller alla directory management relaterade granskningsloggar som användaren management, hantering och andra.

Med dessa loggar få du insikter om hur du gör din miljö. Med dessa data kan du:

- Avgör hur dina appar och tjänster används av användarna.

- Felsöka problem som hindrar användare från att få sitt arbete.

Mer information om hur du använder de här rapporterna finns [Azure Active Directory reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använd ”rapporten Reader” roll (role icke-administratörer) för att visa aktivitetsrapporter för Azure AD

**Typ:** ny funktion  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** övervakning och rapportering
 

Vi har aktiverat möjligheten för användare i rollen ”rapportläsare” åtkomst inloggningar och granska aktivitet i Azure Portal samt med vår Graph-API: er som en del av kunder feedback för att aktivera roller för icke-administratörer har åtkomst till Azure AD-aktivitet loggar. 

Mer information hur du använder de här rapporterna finns [Azure Active Directory reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID anspråk tillgängliga som användarattribut och användar-ID

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** enkel inloggning
 

Du kan konfigurera **EmployeeID** som användar-ID och användarattribut för användare och B2B gäster i SAML-baserade inloggning program från Enterprise programgränssnitt.

Mer information finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad hantering av program med hjälp av jokertecken i Azure AD Application Proxy

**Typ:** ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapaciteten:** användarautentisering
 

För att underlätta programdistribution och minskar de administrativa omkostnaderna, stöder vi nu möjligheten att publicera program med jokertecken. Om du vill publicera ett program med jokertecken kan du följa standardprogrammet publishing flödet, men använder jokertecken i de interna och externa URL: er.

Mer information finns i [jokertecken program i Azure Active Directory application proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nya cmdletar för att stöd för konfiguration av Application Proxy

**Typ:** ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapaciteten:** plattform
 

Den senaste versionen av modulen AzureAD PowerShell Preview innehåller nya cmdlet: ar som ger kunder möjlighet att konfigurera Application Proxy-program med hjälp av PowerShell.

De nya cmdletarna är: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nya cmdletar för att stöd för konfiguration av grupper

**Typ:** ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapaciteten:** plattform
 

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdletar för att hantera grupper i Azure AD. Dessa cmdletar tidigare var tillgängliga i modulen AzureADPreview och läggs nu till modulen AzureAD

Grupp-cmdletar som är nu version för allmän tillgänglighet är: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>En ny version av Azure AD Connect är tillgänglig

**Typ:** ny funktion  
**Tjänstekategori:** AD Sync  
**Produkten kapaciteten:** plattform
 

Azure AD Connect är det rekommenderade verktyget för att synkronisera data mellan Azure AD och lokala datakällor, inklusive Windows Server Active Directory och LDAP.

**Viktigt**
 
Den här versionen innehåller schemat och sync regel ändringar. Azure AD Connect synkroniseringstjänsten utlöser en fullständig Import och fullständig synkronisering steg efter en uppgradering. Information om hur du ändrar det här problemet finns i [så att skjuta upp fullständig synkronisering efter uppgraderingen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Den här versionen har följande uppdateringar och ändringar:

**Fast problem**

- Åtgärda tidsinställning fönster på bakgrundsaktiviteter för filtrering av Partition sida när du växlar till nästa sida.
- Fast ett fel som orsakade åtkomstfel när den anpassade åtgärden ConfigDB.
- Fast ett programfel att återställa från timeout för sql-anslutningen.
- Fast ett programfel om certifikat med jokertecken för SAN-nätverk inte uppfyllde kontroll.
- Fast ett programfel som orsakar miiserver.exe krasch under exporten för AAD-koppling.
- Fast ett programfel som felaktiga lösenord inloggad DC när du kör AAD ansluta för att ändra konfigurationen

**Nya funktioner och förbättringar**


 
- Programtelemetri - administratörer kan växla den här klassen av data på/av.

- Azure AD Health-data – administratörer måste besöka health-portalen för att styra inställningar för hälsotillstånd. När tjänsten principen har ändrats, läser agenterna och använda den.

- Åtgärder för tillbakaskrivning av enhet och en förloppsindikator för att initiera dialogrutan lagts till.

- Förbättrad allmän diagnostik med HTML-rapport och fullständig datainsamling i en ZIP-Text / HTML-rapport.

- Förbättrad tillförlitlighet Auto uppgradera och lägga till ytterligare telemetri för att kontrollera hälsotillståndet för servern som kan fastställas.

- Begränsa behörigheter som är tillgängliga för privilegierade konton på kontot för AD-koppling. För nya installationer guiden begränsar de behörigheter som Privilegierade konton har på MSOL konto när du har skapat kontot MSOL. Ändringarna påverkar express installationer och anpassade installationer med Autoskapa konto.

- Installationsprogrammet för att inte kräva SA privilegiet ren installation av AADConnect har ändrats.

- Nya verktyg för att felsöka synkroniseringsproblem med för ett specifikt objekt. För närvarande kontrollerar verktyget följande:

    - UserPrincipalName matchningsfel mellan synkroniserade användarobjektet och användarkontot i Azure AD-klient.
  
    - Om objektet är filtrerad från synkronisering på grund av domän filtrering
  
    - Om objektet är filtrerad från synkronisering på grund av organisationsenhet (OU) filtrering

- Nya verktyg för att synkronisera den aktuella lösenords-hash som lagras i Active Directory lokalt för ett specifikt användarkonto. Verktyget kräver inte en lösenordsändring. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Stödjande Intune App Protection principer som lagts till för program med Azure AD application-baserad villkorlig åtkomst

**Typ:** ändrade funktionen  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd
 

Vi har lagt till fler program som har stöd för program-baserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av dessa godkända klientprogram.

Följande program kommer att läggas till i slutet av februari 

- Microsoft PowerBI

- Microsoft starta

- Microsoft fakturering

Mer information finns i:

- [Godkända klienten app-krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>Villkor för användning av uppdatera till mobila upplevelsen 

**Typ:** ändrade funktionen  
**Tjänstekategori:** användningsvillkoren  
**Produkten kapaciteten:** kompatibilitet
 

När användningsvillkoren visas kan du nu på **har problem med att visa? Klicka här**. Om du klickar på den här länken öppnas användningsvillkoren internt på enheten. Oavsett teckenstorleken i dokumentet eller skärmstorlek av enhet du zooma och läsa dokumentet efter behov. 
 

---
 
## <a name="january-2018"></a>Januari 2018
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet 

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** 3 part-integrering
 

Följande nya appar med stöd för identitetsfederation lades till i appgalleriet i januari 2018:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust sekretess hanteringsprogramvara](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federerad Directory](https://go.microsoft.com/fwlink/?linkid=864699) och [återgivning NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

En fullständig översikt över alla tillgängliga kurser finns [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Inloggning med identifierad ytterligare risk

**Typ:** ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapaciteten:** identitet säkerhet och skydd
 

Den information som du får en identifierad risk händelse är bundet till din Azure AD-prenumeration. Med Azure AD Premium P2-edition får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte omfattas av din licens som händelsen risk logga in med ytterligare risk upptäcktes.

Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomst paneler

**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning
 

Du kan nu bättre hantera hur Office 365-program visas på användarnas åtkomst paneler via en ny Användarinställning. Det här alternativet är användbart för att minska mängden appar i en användares åtkomst panelerna om du vill visa bara Office-appar i Office-portalen. Inställningen finns i den **användarinställningar** och etiketteras **användare kan bara visa Office 365-appar i Office 365-portalen**.
 

Mer information finns i [dölja ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös logga in på appar som har aktiverats för lösenord SSO direkt från appens URL 

**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning
 

Webbläsartillägget för Mina appar finns nu tillgänglig via ett enkelt verktyg som ger dig den Mina appar enkel inloggning på kapaciteten som en genväg i webbläsaren. När du installerar användarens ikonen waffle i webbläsaren ger som dem snabb åtkomst till appar. Användare kan nu dra nytta av:

- Möjligheten att direkt logga in på lösenord SSO-baserade appar från appens inloggningssidan
- Starta alla appar som använder funktionen Snabbsökning
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängliga för gräns, Chrome och Firefox.
 
Mer information finns i [Mina appar säker inloggning tillägget](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administration upplevelse i klassiska Azure-portalen har tagits bort

**Typ:** föråldrad   
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** Directory
 

Från och med 8 januari 2018, Azure AD-administration upplevelse i den klassiska Azure-portalen har tagits bort. Detta ägde rum tillsammans med tillbakadragningen av den klassiska Azure-portalen sig själv. Framöver, bör du använda den [administrationscentret för Azure AD](https://aad.portal.azure.com) för din portal-baserad administration av Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webbportalen PhoneFactor har tagits bort

**Typ:** föråldrad  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** Directory
 

Från och med den 8 januari 2018 har webbportalen PhoneFactor tagits bort. Den här portalen användes för administration av MFA-server, men de här funktionerna har flyttats till Azure portal på portal.azure.com. 

MFA-konfigurationen finns på: **Azure Active Directory \> MFA-Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Föråldrad Azure AD-rapporter


**Typ:** föråldrad  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** Identitetshantering livscykel  


Med den allmänna tillgängligheten för den nya Azure Active Directory-administrationskonsolen och nya API: erna finns nu tillgängligt för både aktivitet och säkerhet kan rapporten API: er under ”/ rapporter” slutpunkt har tagits bort från slutet av den 31 December 2017.


**Vad är tillgängliga?**

Som en del av övergången till en ny Administratörskonsol, har vi gjort 2 nya API: er tillgängliga för hämtning av Azure AD-aktivitetsloggar. Ny uppsättning API: er ger bättre filtrera och sortera funktioner förutom att tillhandahålla bättre gransknings- och logga in aktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporter kan nu nås via identitetsskydd riskhändelser API i Microsoft Graph.

Mer information finns i:

- [Kom igång med Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>December 2017
 

### <a name="terms-of-use-in-the-access-panel"></a>Villkor för användning i åtkomstpanelen

**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** kompatibilitet
 
Du kan nu gå till åtkomstpanelen och visa användningsvillkoren som du tidigare har accepterat.

Följ de här stegen:

1. Gå till den [MyApps portal](https://myapps.microsoft.com), och logga in.

2. Välj ditt namn i det övre högra hörnet och välj sedan **profil** från listan. 

3. På din **profil**väljer **granska användningsvillkoren**. 

4. Nu kan du granska användningsvillkoren accepteras. 

Mer information finns i [Azure AD-användningsvillkor Använd funktion (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nya Azure AD-inloggningsupplevelsen

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** användarautentisering
 
Azure AD och identitetssystem användargränssnitt för Microsoft-konto har designats om så att de har ett konsekvent utseende. Dessutom kan sidan för Azure AD samlar in användarnamnet först, följt av autentiseringsuppgifter på en annan skärm.

Mer information finns i [den nya Azure AD inloggningen är nu tillgänglig som förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggning prompter: en ny ”jag vill förbli inloggad” upplevelse för Azure AD-inloggning

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** användarautentisering
 
Den **Håll mig inloggad** kryssrutan på sidan för Azure AD har ersatts med en ny fråga som visas när du kunna autentisera. 

Om du svarar **Ja** till den här uppmaningen tjänsten ger dig en beständig uppdateringstoken. Det här beteendet är samma som om du har valt den **Håll mig inloggad** kryssrutan i gamla upplevelsen. För federerade klienter visar den här uppmaningen när du autentisera med den federerade tjänsten.

Mer information finns i [färre inloggning prompter: ”Jag vill förbli inloggad” avanmäla för Azure AD är en förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration för att kräva användningsvillkor som ska expanderas innan accepterar

**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** kompatibilitet
 
Ett alternativ för administratörer kräver användarna att expandera användningsvillkoren innan du godkänner villkoren.

Välj antingen **på** eller **av** användare måste expandera användningsvillkoren. Den **på** inställningen kräver att användarna ska visa användningsvillkoren innan du godkänner dem.

Mer information finns i [Azure AD-användningsvillkor Använd funktion (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Begränsade aktivering för berättigade rolltilldelningar

**Typ:** ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Du kan använda begränsade aktivering för att aktivera berättigade Azure-resurs rolltilldelningar med mindre självständigt än de ursprungliga tilldelningen standardvärdena. Ett exempel är om du tilldelade till ägare av en prenumeration i din klient. Du kan aktivera ägarrollen för upp till fem resurser som ingår i prenumerationen (till exempel resursgrupper och virtuella datorer) med begränsade aktivering. Ange omfång aktiveringen kan minska risken för körning oönskade ändringar av kritiska Azure-resurser.

Mer information finns i [vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i appgalleriet för Azure AD

**Typ:** ny funktion  
**Tjänstekategori:** företagsappar  
**Produkten kapaciteten:** 3 part-integrering
 
Följande nya appar med stöd för identitetsfederation lades till i appgalleriet i December 2017:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

En fullständig översikt över alla tillgängliga kurser finns [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännandearbetsflöden för Azure AD directory roller

**Typ:** ändrade funktionen  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Arbetsflöde för godkännande för Azure AD directory roller är allmänt tillgänglig.

Med Godkännandearbetsflöde kan privilegierade rollen administratörer kräva kvalificerade rollmedlemmar till begäran rollaktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan vara delegerade godkännande ansvarsområden. Berättigad rollmedlemmar få meddelanden när godkännande har slutförts och deras roll är aktiv.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Direkt-autentisering: Skype för företag-support

**Typ:** ändrade funktionen  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering


Direkt autentiseringen nu stöder användarinloggningar till Skype för företag klientprogram som stöder modern autentisering, vilket innefattar online och hybridtopologier. 

Mer information finns i [Skype för företag-topologier som stöds med modern autentisering](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar till Azure AD Privileged Identity Management för Azure RBAC (förhandsgranskning)

**Typ:** ändrade funktionen  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Med förhandsversion uppdatering av Azure AD Privileged Identity Management (PIM) för rollbaserad åtkomstkontroll (RBAC) kan du nu:

* Använd Just Enough Administration.
* Kräv godkännande för att aktivera resursroller för.
* Schemalägga en framtida aktivering av en roll som kräver godkännande för både Azure AD och Azure RBAC-roller.

 
Mer information finns i [Privileged Identity Management för Azure-resurser (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control service pensionering



**Typ:** ändringsplan  
**Tjänstekategori:** Access Control service  
**Produkten kapaciteten:** Access Control service 


 Azure Active Directory-åtkomstkontroll (även kallat Access Control service) ska tas bort i sen 2018. Mer information som innehåller en detaljerad schema och övergripande migrering vägledning ges i nästa några veckor. Du kan lämna kommentarer på den här sidan om du har frågor om tjänsten åtkomstkontroll och en gruppmedlem besvara dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsaråtkomst till Intune Managed Browser 


**Typ:** ändringsplan  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd




Du kan begränsa webbläsaråtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av Intune Managed Browser som en godkänd app. 

Nu kan du konfigurera följande villkor för program-baserad villkorlig åtkomst:

**-Klientprogram:** webbläsare

**Vad är effekten av ändringen?**

Idag, blockeras åtkomst när du använder det här villkoret. När förhandsgranskningen är tillgänglig, kommer all åtkomst kräver användning av den hanterade webbläsaren. 

Leta efter den här funktionen och mer information finns i kommande bloggar och viktig information. 

Mer information finns i [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD app-baserad villkorlig åtkomst

 
**Typ:** ändringsplan  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd




Följande appar är tänkta att läggas till i listan över [godkända klientprogram](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Mer information finns i:

- [Godkända klienten app-krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användningsvillkor för stöd för flera språk



**Typ:** ny funktion    
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** kompatibilitet





Administratörer kan nu skapa nya villkor för användning som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med motsvarande språk. PDF-filen visas för användarna med motsvarande språk utifrån deras preferenser. Om det finns ingen matchning, visas standardspråk.


---
 

### <a name="real-time-password-writeback-client-status"></a>Realtid lösenord tillbakaskrivning klientstatus



**Typ:** ny funktion  
**Tjänstekategori:** Självbetjäning för lösenordsåterställning  
**Produkten kapaciteten:** användarautentisering


 

Du kan nu granska statusen för din lokala lösenord tillbakaskrivning. Det här alternativet är tillgängligt i den **lokalt integration** avsnitt i den [lösenordsåterställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) sidan. 

Om det finns problem med anslutningen till din lokala tillbakaskrivning klient, visas ett felmeddelande som ger dig med:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivning av klienten.
- En länk till dokumentationen som hjälper dig att lösa problemet. 


Mer information finns i [lokalt integration](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD app-baserad villkorlig åtkomst 



 
**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** identitet säkerhet och skydd





Du kan nu begränsa åtkomst till Office 365 och andra Azure AD-anslutna molnappar till [godkända klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) som stöder Intune app protection-principer med hjälp av [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Intune app protection-principer används för att konfigurera och skydda företagets data på dessa program.

Genom att kombinera [app-baserade](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) med [enhetsbaserad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) principer för villkorlig åtkomst, har du möjlighet att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med app-baserad villkorlig åtkomst:

**Villkor för plattform som stöds**

- iOS
- Android

**Klienten appar villkor**

- Mobila appar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkänd klientapp


Mer information finns i [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure-portalen



**Typ:** ny funktion  
**Tjänstekategori:** enhetsregistrering och hantering  
**Produkten kapaciteten:** identitet säkerhet och skydd

 



Nu hittar du alla dina enheter som är anslutna till Azure AD och enhetsrelaterade aktiviteter i ett och samma ställe. Det finns en ny administration-miljö för att hantera identiteter för enheten och inställningar i Azure-portalen. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, bland annat din hybrid Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för dina Azure AD-anslutna enheter, hantera din enhet med Intune och mycket mer.
- Hantera Azure AD-enhetsrelaterade inställningar.

Mer information finns i [hantera enheter med hjälp av Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som en enhetsplattform för villkorlig åtkomst i Azure AD 



**Typ:** ny funktion    
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd 
 

Du kan nu inkludera (eller exkludera) macOS som en enhet plattform villkor i din Azure AD-principen för villkorlig åtkomst. Med tillägg av macOS till enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS enheter med hjälp av Intune.** Precis som andra plattformar som iOS och Android, ett företagets portalprogram är tillgängliga för macOS att enhetlig registreringar. Du kan använda nya företagsportal-appen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Kontrollera macOS enheter följa efterlevnadsprinciper för din organisation som definierats i Intune.** I Intune på Azure-portalen kan kan du nu ställa in policyer för efterlevnad för macOS enheter. 
- **Begränsa åtkomsten till program i Azure AD för att endast kompatibla macOS enheter.** Skapa principer med villkorlig åtkomst har macOS som ett separat enhet plattform. Du kan nu skapa macOS-specifika villkorliga åtkomstprinciper för det aktuella programmet i Azure.

Mer information finns i:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Network Policy Servertillägg för Azure Multi-Factor Authentication 


**Typ:** ny funktion    
**Tjänstekategori:** multifaktorautentisering  
**Produkten kapaciteten:** användarautentisering




NPS-tillägget för Azure Multi-Factor Authentication lägger till molnbaserade multifaktorautentisering funktioner till din infrastruktur för autentisering med hjälp av din befintliga servrar. Med filnamnstillägget Network Policy Server du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering till ditt befintliga autentiseringsflödet. Du behöver inte installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda virtuella privata nätverksanslutningar utan att distribuera Azure Multi-Factor Authentication-servern. Nätverksprincipservern tillägg fungerar som ett kort mellan RADIUS och molnbaserad Azure Multi-Factor Authentication för att tillhandahålla en andra faktor-autentisering för federerad eller synkroniserade användare.


Mer information finns i [integrera din befintliga infrastruktur för Network Policy Server med Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare


**Typ:** ny funktion    
**Tjänstekategori:** användarhantering  
**Produkten kapaciteten:** Directory 



I administrationscentret för Azure AD kan du nu:

- Återställa en borttagen användare. 
- Ta bort en användare permanent.


**Prova det:**

1. Välj i administrationscentret för Azure AD [alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i den **hantera** avsnitt. 

2. Från den **visa** väljer **bort användare nyligen**. 

3. Välj en eller flera nyligen borttagna användare och sedan återställa dem eller ta bort dem permanent.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD app-baserad villkorlig åtkomst

 
**Typ:** ändrade funktionen  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd


Följande appar har lagts till i listan över [godkända klientprogram](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Mer information finns i:

- [Godkända klienten app-krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använd ”eller” mellan kontroller i en princip för villkorlig åtkomst 


**Typ:** ändrade funktionen    
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd

 
Nu kan du använda ”eller” (kräver en av de markerade kontrollerna) för villkorlig åtkomstkontroller. Du kan använda den här funktionen när du skapar principer med ”eller” mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att användaren måste logga in med hjälp av Multi-Factor authentication ”eller” på en kompatibel enhet.

Mer information finns i [kontrollerna i Azure AD villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Sammanställning av realtidsskyddet riskhändelser


**Typ:** ändrade funktionen    
**Tjänstekategori:** identitetsskydd  
**Produkten kapaciteten:** identitet säkerhet och skydd


I Azure AD Identity Protection samman alla realtid riskhändelser som kommer från samma IP-adress på en viss dag nu för varje typ av händelse risk. Den här ändringen begränsar mängden riskhändelser framgår användarsäkerhet utan ändringar.

Den underliggande realtid identifieringen fungerar varje gång användaren loggar in. Om du har en säkerhetsprincip för inloggning risk konfigurera Multi-Factor authentication eller blockera åtkomst, utlöses den fortfarande under varje riskfyllda inloggning.

 
---
 




## <a name="october-2017"></a>Oktober 2017


### <a name="deprecate-azure-ad-reports"></a>Föråldrad Azure AD-rapporter


**Typ:** ändringsplan  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** Identitetshantering livscykel  



Azure-portalen ger dig:

- En ny Azure AD-administrationskonsolen.
- Nya API: er för aktiviteten och säkerhet.
 
På grund av de nya funktionerna som rapporten API: er under/Reports slutpunkten har avslutats på 10 December 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Fältet för automatisk inloggning identifiering


**Typ:** fast   
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  



Azure AD stöder automatisk inloggning fältet identifiering för program som återger en HTML-Användarfält namn och lösenord. De här stegen finns dokumenterade i [så att automatiskt samla in inloggning fält för ett program](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Du hittar den här funktionen genom att lägga till en *icke-galleriet* på den **företagsprogram** sidan i den [Azure-portalen](http://aad.portal.azure.com). Du kan också konfigurera den **enkel inloggning** läge för den här nya programmet till **lösenordsbaserade enkel inloggning**, ange en URL och spara sedan sidan.
 
Den här funktionen har tillfälligt inaktiverats på grund av ett problem med tjänsten. Problemet har lösts och fältet för automatisk inloggning identifieringen är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya funktioner för multifaktorautentisering


**Typ:** ny funktion  
**Tjänstekategori:** multifaktorautentisering  
**Produkten kapaciteten:** identitet säkerhet och skydd  



Multifaktorautentisering (MFA) är en viktig del av att skydda din organisation. Om du vill göra mer anpassningsbar autentiseringsuppgifter och erfarenhet smidigare följande funktioner har lagts till: 

- Multi-Factor utmaning resultat integreras direkt i Azure AD-inloggning rapporten som innehåller Programmeringsåtkomst till MFA resultat.
- MFA-konfigurationen är mer djupt integrerad med Azure AD-konfiguration upplevelse i Azure-portalen.

Med den här offentliga förhandsversionen är en integrerad del av kärnor Azure AD-konfiguration av MFA hantering och rapportering. Du kan nu hantera MFA management portal-funktionerna i Azure AD-upplevelse.

Mer information finns i [referens för rapportering av MFA i Azure portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Användningsvillkor



**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** kompatibilitet  



Du kan använda Azure AD användningsvillkoren presentera information, till exempel relevanta FRISKRIVNINGAR för juridiska eller efterlevnadskrav till användare.

Du kan använda Azure AD-villkor för användning i följande scenarier:

- Allmänna villkor för användning av alla användare i din organisation
- Särskilda villkor för användning baserat på en användares attribut (till exempel läkare kontra sjuksköterskor) eller inrikes kontra internationella anställda, utförs av dynamiska grupper
- Särskilda villkor för användning av åtkomst till Hög inverkan branschspecifika appar, t.ex. Salesforce

Mer information finns i [användningsvillkoren för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av Privileged Identity Management


**Typ:** ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management  


Du kan hantera, styr och övervaka åtkomst till Azure-resurser (förhandsgranskning) inom organisationen med Azure AD Privileged Identity Management:

- Prenumerationer
- Resursgrupper
- Virtuella datorer 

Alla resurser i Azure-portalen som använder funktionen Azure RBAC kan dra nytta av säkerhets- och hanteringsfunktioner för livscykel som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privileged Identity Management för Azure-resurser](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Åtkomstgranskningar


**Typ:** ny funktion  
**Tjänstekategori:** åtkomst till granskningar  
**Produkten kapaciteten:** kompatibilitet  



Organisationer kan använda åtkomst granskningar (förhandsversion) för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare bestämma effektivt om så att gästerna fortsatt åtkomst baserat på de insikter som tillhandahålls av access granskningar.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst till Azure AD granskar](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj tredjepartsprogram från Mina appar och Office 365 app programstart



**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  



Nu hantera appar som visas på användarnas portaler via en ny **dölja app** egenskapen. Du kan dölja appar för att i fall där appen paneler visas för backend-tjänster eller dubbla paneler och oreda användarnas app startprogram. Att växlingsknappen är i den **egenskaper** avsnitt av appen från tredje part och etiketteras **synligt för användaren?** Du kan även dölja en app programmässigt med hjälp av PowerShell. 

Mer information finns i [dölja tredjepartsprogram från användarens upplevelse i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Vad är tillgängliga?**

 Som en del av övergången till en ny Administratörskonsol två nya API: er för att hämta Azure AD-aktivitet loggar är tillgängliga. Ny uppsättning API: er ger bättre filtrera och sortera funktioner förutom att tillhandahålla bättre gransknings- och logga in aktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporter nu kan nås via Identity Protection Risk händelser API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabbkorrigering för Identity Manager


**Typ:** ändrade funktionen  
**Tjänstekategori:** Identity Manager  
**Produkten kapaciteten:** Identitetshantering livscykel  



En sammanslagning snabbkorrigeringen (build 4.4.1642.0) är tillgängliga från och med 25 September 2017 för Identity Manager 2016 Service Pack 1. Det här paketet:

- Löser problem och lägger till förbättringar.
- Är en kumulativ uppdatering som ersätter alla Identity Manager 2016 Service Pack 1 uppdateringar upp till build 4.4.1459.0 för Identity Manager 2016. 
- Du måste ha Identity Manager 2016 skapa 4.4.1302.0. 

Mer information finns i [samlade paketet (build 4.4.1642.0) är tillgänglig för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
