---
title: Krav per listtyp | Azure
description: I den här artikeln beskrivs kvalificeringskriterierna och publiceringskravspartner som försöker förstå hur du publicerar appar på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: 1ff6e34936234d6636fabc6c5e17dd32416b4ac2
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387860"
---
# <a name="requirements-by-listing-type"></a>Krav efter listtyp  
Kraven på tekniskt innehåll och marknadsföringsinnehåll varierar beroende på skyltfönster, erbjudandetyp och listtyp. Läs följande specifikationer för att verifiera din efterlevnad.  
1. Krav på skyltfönster:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Krav på listtyp och erbjudandetyp:  
    *   Mer information om listningstyper och erbjudandetyper finns på sidan Ange listtyp för din lösning som finns på [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Krav på skyltfönster: AppSource  
I följande tabell beskrivs de nödvändiga kraven för publicering på AppSource.  

| Krav | Information | Krävs eller rekommenderas |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Din app måste tillåta Azure Active Directory federerade enkel inloggning (Azure AD federerade SSO) med medgivande aktiverat.<ul> <li>Mer information om hur du aktiverar Azure AD-federerade SSO finns på sidan Konfigurera enkel inloggning till program som inte finns på sidan Azure Active Directory-programgalleri som finns på [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Krävs |   
| ***Integrering med Microsoft Cloud Services*** | Din app bör integreras med andra Microsoft Cloud-tjänster som Microsoft Power BI, Cortana Intelligence eller Microsoft Azure-tjänster.<ul> <li>Ett exempel på en Microsoft Cloud-tjänst är Sakernas Internet.</li> </ul> | Rekommenderas |  
| ***Målgrupp*** | Din app måste vara för företagsanvändare och företagare. | Krävs | 
| ***SaaS-app (Software as a Service) för företag*** | Din app måste uppfylla följande krav.<ul> <li>En bransch-SaaS-app</li> <li>Affärsprocess fokuserad</li> <li>Riktad till företagskunder</li> <li>Gör det möjligt för användare att använda sina arbetsuppgifter för att logga in, till exempel användarnamn och lösenord</li> </ul> | Krävs |  
| ***Kostnadsfri provperiod och provperiod*** | Din app måste innehålla ett av följande alternativ för att en kund ska kunna använda din app gratis under en begränsad tid.<ul> <li>Ange `try` en metod så att kunderna kan starta en utvärderingsversion av din app i AppSource</li> <li>Ange `request trial` ett alternativ i AppSource, så att kunder kan begära en utvärderingsversion av din app</li> </ul>Den kostnadsfria provperioden som du anger måste ge kunden en förinställd tid för att prova din app utan extra kostnad. | Krävs |  
| ***Lätt konfigurerbar, färdig att använda lösning*** | Appen måste vara enkel och snabb att konfigurera och konfigurera utan att någon anpassning krävs. | Krävs |  
| ***Leadhantering*** | Aktivera crm för att acceptera leaddata innan du tar emot leads från skyltfönstret.<ul> <li>Exempel på CRMs är Marketo, Microsoft Dynamics eller Salesforce</li> </ul> | Krävs |  
| ***Sekretesspolicy och användarvillkor*** | Din app måste tillhandahålla en länk till din sekretesspolicy sida med hjälp av en offentlig webbadress. Dina användarvillkor måste anges under publiceringen som text. | Krävs |  
| ***Support*** | Appen måste tillhandahålla en länk till kundsupportsidan med en offentlig webbadress. Om din app är en utvärderingsversion måste du ha support utan extra kostnad under provperioden. | Krävs |  

## <a name="storefront-requirements-azure-marketplace"></a>Krav på skyltfönster: Azure Marketplace  
Följande är krav för att lista typer i Azure Marketplace.  

| Krav | Information | Listningstyp |  
|:--- |:--- |:--- |  
| ***Policy för deltagande*** | Din app måste följa Azure Marketplace-deltagandeprinciperna.<ul> <li>Mer information om deltagandeprinciperna finns på sidan Azure Marketplace Participation Policies som finns på [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />trial |  
| ***Integrering med Microsoft*** | Ditt erbjudande bör använda eller utöka Microsoft Azure-tjänsttyper som beräkning, nätverk eller lagring. Ditt erbjudande bör anpassas till en befintlig Azure Marketplace-kategori, till exempel databaser, säkerhet eller nätverk.<ul> <li>Mer information om Marketplace-erbjudanden finns på sidan Marketplace Apps som finns på [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />trial |  
| ***Målgrupp*** | Ditt erbjudande måste vara för IT-proffs, molnutvecklare eller andra tekniska kundroller. | lista<br />Transact<br />trial |  
| ***Leadhantering*** | Aktivera CRM (Marketo, Microsoft Dynamics eller Salesforce) för att acceptera leaddata innan du tar emot leads från skyltfönstret. | lista<br />Transact<br />trial |  
| ***Sekretesspolicy och användarvillkor*** | Din app måste tillhandahålla en länk till din sekretesspolicy sida med hjälp av en offentlig webbadress. Dina användarvillkor måste anges under publiceringen som text. | lista<br />Transact<br />trial |  
| ***Support*** | Ditt erbjudande måste tillhandahålla en länk till din kundsupportsida med en offentlig webbadress. Om ditt erbjudande är en provperiod måste du ha support utan extra kostnad under provperioden. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Icke-transact listor  
I det här avsnittet beskrivs alla erbjudandetyper som inte använder listningstypen Verkställ. 

### <a name="list"></a>Visa lista  
Listlistningstypen innehåller följande erbjudandetyper på skyltfönster på marknadsplatsen.  

| Erbjudandetyp | Skyltfönster | Information |  
|:---        |:---        |:---     |  
| Rådgivningstjänster | AppSource | Krav: AppSource: Lista: Konsulttjänster |  
| Rådgivningstjänster | Azure Marketplace | Krav: Azure Marketplace: Lista: Konsulttjänster |  
| Kontakta mig | AppSource | [](#) |  
| Kontakta mig | Azure Marketplace | Krav: AppSource: Lista: Kontakta mig |  

#### <a name="requirements-appsource-list-consulting-service"></a>Krav: AppSource: Lista: Konsulttjänster  

| Krav | Information |  
|:--- |:--- |  
| Egenskaper för tjänsteutbud | Din konsulttjänst måste uppfylla följande kriterier.<ul> <li>Leverera ett fast scope, fast varaktighet, fast pris (eller gratis) engagemang.</li> <li>Orient främst för förköp.</li> <li>Gräns för en enskild kund.</li> <li>Uppförande på plats.</li> </ul> |  
| Partnerkrav för konsulttjänster | Du uppfyller kriterierna i det relevanta området för din tjänst.<table><tr><th>Lösningsområde</th><th>Kriterie</th></tr><tr><td>Dynamics 365 för Customer Engagement</td><td>Har silver- eller gold cloud-kundrelationshanteringskompetens.</td></tr><tr><td>Dynamics 365 för ekonomi och drift, Enterprise edition</td><td>Ha kompetensen för resursplanering för silver eller guldföretag från molnverksamheten under de avslutande 12 månaderna på 25 000 USD eller mer.</td></tr><tr><td>Dynamics 365 för ekonomi och drift, Business edition</td><td>Fungera som Cloud Services Provider (CSP) eller Digital Partner of Record (DPOR) för en eller flera kunder.</td></tr><tr><td>Power BI</td><td>Uppfyller kriterierna för lösningspartner.</td></tr><tr><td>PowerApps</td><td>Ha en Partner Showcase-lösning.</td></tr></table><ul> <li>Mer information om hantering av kundrelationer finns på sidan Hantering av molnkundrelationer som finns på [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Mer information om resursplanering finns på sidan Planering av företagsresurs som finns på [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Mer information om CSP finns på sidan Molntjänstleverantör som finns på [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Mer information om DPOR finns på sidan Digital Partner of Record and Partner Association som finns på [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Mer information om lösningspartnerkriterier finns i dokumentet Solution Partner Overview and Incentives som finns på [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Mer information om partnerpresentation finns på sidan Partner Showcase som finns på [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Krav: Azure Marketplace: Lista: Konsulttjänst  

| Krav | Information |  
|:--- |:--- |  
| Egenskaper för tjänsteutbud | Din konsulttjänst måste uppfylla följande kriterier.<ul> <li>Leverera ett fast scope, fast varaktighet, fast pris (eller gratis) engagemang.</li> <li>Orient främst för förköp.</li> <li>Gräns för en enskild kund.</li> <li>Uppförande på plats.</li> </ul> |  
| Partnerkrav för konsulttjänster | Du måste ha silver eller guld i någon av följande kompetenser inom det relevanta området för din tjänst. <table><tr><th>Lösningsområde</th><th>Kompetens</th></tr><td>Molnplattform och infrastruktur</td><td>Molnplattform<br />Datacenter</td><tr><td>Applikationsutveckling och ISV</td><td>Apputveckling<br />Integrering av program<br />DevOps</td></tr><tr><td>Datahantering och dataanalys</td><td>Dataanalys<br />Dataplattform</td></tr></table><ul> <li>Mer information om kompetenser finns på sidan Kompetenser via Microsoft Partner Network som finns på [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Mer information om listning finns på sidan Azure Marketplace Consulting Services som finns på [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Utvärdering  

| Erbjudandetyp | Skyltfönster | Information |  
|:---        |:---        |:---     |  
| Gratis / SaaS rättegång | AppSource | Lista typ krav: Trial |  
| Gratis / SaaS rättegång | Azure Marketplace | Krav: Azure Marketplace: Testperiod: Kostnadsfri utvärderingsversion / SaaS-utvärderingsversion |  
| Interaktiv demo | AppSource | Lista typ krav: Trial |  
| Interaktiv demo | Azure Marketplace | [Krav: Azure Marketplace: Utvärderingsversion: Interaktiv demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Provkörning | AppSource | Lista typ krav: Trial |  
| Provkörning | Azure Marketplace | [Krav: Azure Marketplace: Utvärderingsversion: Testenhet](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Krav: Azure Marketplace: Utvärderingsversion  

| Krav | Information |  
|:--- |:--- |  
| Kostnadsfri provperiod och provperiod | Din kund kan använda din app gratis under en begränsad tid.<br /><br />Kunden behöver inte betala några licens- eller prenumerationsavgifter för ditt erbjudande eller din app. Kunden behöver inte betala för den underliggande microsoft-produkten eller tjänsten från första part. Alla utvärderingsalternativ distribueras till din Azure-prenumeration. Du har ensam kontroll rättegång av kostnadsoptimering och förvaltning.<br /><br />Du kan välja en kostnadsfri utvärderingsversion, interaktiv demo eller provkörning. Oavsett vad du väljer måste din kostnadsfria provperiod erbjuda kunden en förinställd tid för att prova appen utan extra kostnad.<ul> <li>Om du vill påbörja processen med att [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)skapa en provkörning skickar du ett e-postmeddelande till .</li> </ul>Utvärderingsversioner av Azure Marketplace SaaS måste göra det möjligt för kunder att använda arbetsautentiseringsuppgifter för att logga in.<ul> <li>Mer information finns i avsnittet Testupplevelser för AppSource som finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Lätt konfigurerbar, färdig att använda lösning | Appen måste vara enkel och snabb att konfigurera och konfigurera. |  
| Tillgänglighet /drifttid | Din SaaS-app eller -plattform måste ha en drifttid på minst 99,9 %. |  
| Azure Active Directory | Ditt erbjudande måste tillåta Azure Active Directory (Azure AD) federerade enkel inloggning (SSO) (Azure AD federerade SSO) med medgivande aktiverat. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Krav: Azure Marketplace: Testperiod: Kostnadsfri utvärderingsversion / SaaS-utvärderingsversion  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för en kund att prova din produkt innan du köper med en automatiserad metod för konvertering till betald användning. Möjliggör även konceptbevis för kunden och gemensamt engagemang med Microsofts säljteam. | Din lösning är en virtuell dator eller lösningsmall.<br /><br />Din lösning är ett SaaS-erbjudande och du erbjuder en multitenant SaaS-produkt.<br /><br />Du har en första-run erfarenhet för att få en kund igång snabbt.<br /><br />Du har en enda klient men lägger till kunder som gästanvändare. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Krav: Azure Marketplace: Utvärderingsversion: Interaktiv demo  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för dina kunder att se din lösning i aktion utan komplexiteten i att ställa in. | Din lösning kräver komplexa inställningar som skulle vara svåra att uppnå under försöksperioden. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Krav: Azure Marketplace: Utvärderingsversion: Testenhet  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för en kund att prova din produkt innan de köper.<br /><br />Ger en guidad upplevelse av din lösning med förkonfigurerade inställningar.<br /><br />Följande är ytterligare fördelar när du använder en provkörning.<ul> <li>27 % av användarnas sökningar på marknadsplatsen förfinas av användare för att bara visa erbjudanden med provkörningar.</li> <li>Erbjudanden med provkörningar genererar 38% fler leads än erbjudanden utan.</li> <li>36% av de nya kundförvärven på marknaden kommer från kunder som tog en provkörning.</li> <li>Testenheter gör det möjligt för Microsoft-fältsäljare att bättre förstå din produkt för samförsäljning.</li> </ul> | Din lösning är en virtuell dator, lösningsmall eller SaaS-app med en enda klient, eller är komplicerad att etablera. <br /><br />Du har ingen metod för att konvertera din provperiod till ett betalt erbjudande. |  

---

## <a name="transact-specific-listings"></a>Transact-specifika listor

### <a name="transact"></a>Transact  

| Erbjudandetyp | Skyltfönster | Information |   
|:---        |:---        | :--- |  
| Azure-appar: Hanterad app | Azure Marketplace | Krav: Azure Marketplace: Transact: Azure-appar: Hanterad app |  
| Azure-appar: Lösningsmall | Azure Marketplace | Krav: Azure Marketplace: Transact: Azure-appar: Lösningsmall |  
| Containrar | Azure Marketplace | [Krav: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS-app  | Azure Marketplace | [Krav: Azure Marketplace: Transact: SaaS-app](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuell dator | Azure Marketplace | [Krav: Azure Marketplace: Transact: Virtuell dator](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Krav: Azure Marketplace: Transact: Container  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöd antingen den kostnadsfria faktureringsmodellen eller BYOL-faktureringsmodellen. |  
| Docker-baserad bild | Behållaravbildningen måste baseras på Docker-avbildningsformatet och måste hämtas från Azure Container Register. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Krav: Azure Marketplace: Transact: SaaS-app  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Ditt erbjudande är prissatt till ett månatligt schablonbelopp. Användningsbaserade priser och användningsbaserade *true-up-alternativ* stöds inte just nu. |  
| Annullering | Ditt erbjudande kan när som helst annulleras av kunden. |  
| Sidan För transaktionsmål | Vara värd för en azure-sida för sammärkta transaktioner. På målsidan kan dina kunder skapa och hantera ditt SaaS-tjänstkonto. |  
| Api för SaaS-prenumeration | Tillhandahålla en tjänst som interagerar med SaaS-prenumerationen för att skapa, uppdatera och ta bort ett användarkonto och en serviceplan. Alla kritiska API-ändringar måste stödjas inom 24 timmar. Alla icke-kritiska API-ändringar uppdateras regelbundet. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Krav: Azure Marketplace: Transact: Virtuell dator  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Din virtuella dator måste ha stöd för antingen BYOL eller Pay-As-You-Go månadsvis fakturering. |  
| Azure-kompatibel virtuell hårddisk (VHD) | Virtuella datorer måste byggas på Windows eller Linux.<ul> <li>Mer information om hur du skapar en Virtuell Linux-hårddisk finns i [Linux-distributioner som är godkända på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Mer information om hur du skapar en Virtuell Windows-hårddisk finns i [Skapa en Azure-kompatibel virtuell hårddisk](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg
*   Besök sidan [Azure Marketplace och AppSource Publisher Guide.](./marketplace-publishers-guide.md)  

