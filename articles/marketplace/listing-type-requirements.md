---
title: Krav enligt listtyp | Azure
description: I den här artikeln beskrivs villkoren för berättigande och publicerings krav som försöker förstå hur du publicerar appar på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684347"
---
# <a name="requirements-by-listing-type"></a>Krav efter listtyp  
De tekniska och marknadsförings innehålls kraven varierar beroende på butik, erbjudande typ och list typ. Granska följande specifikationer för att kontrol lera efterlevnaden.  
1. Butik krav:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Krav för listtyp och typ av erbjudande:  
    *   Mer information om olika typer av typer och erbjudanden finns på sidan för att avgöra vilken typ av lösning som finns på [docs.Microsoft.com/Azure/Marketplace/Determine-Your-Listing-Type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Butik krav: AppSource  
I följande tabell beskrivs de nödvändiga kraven för publicering på AppSource.  

| Krav | Information | Krävs eller rekommenderas |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Din app måste tillåta Azure Active Directory federerad enkel inloggning (Azure AD federerad SSO) med medgivande aktiverat.<ul> <li>Mer information om hur du aktiverar federerad enkel inloggning i Azure AD finns i Konfigurera enkel inloggning till program som inte finns på sidan för Azure Active Directory program galleriet på [docs.Microsoft.com/Azure/Active-Directory/Active-Directory-SaaS-Custom-Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Krävs |   
| ***Integrering med Microsoft Cloud tjänster*** | Din app bör integreras med andra Microsoft Cloud tjänster som Microsoft Power BI, Cortana Intelligence eller Microsoft Azure tjänster.<ul> <li>Ett exempel på en Microsoft Cloud tjänst är Sakernas Internet.</li> </ul> | Rekommenderas |  
| ***Målgrupp*** | Din app måste vara för branschspecifika användare och företags ägare. | Krävs | 
| ***SaaS-app (Software as a Service) för företag*** | Din app måste uppfylla följande krav.<ul> <li>En SaaS-app (Line-of-Business)</li> <li>Prioriterad affärs process</li> <li>Riktad till företags kunder</li> <li>Gör det möjligt för användare att använda sina autentiseringsuppgifter för att logga in, till exempel användar namn och lösen ord</li> </ul> | Krävs |  
| ***Kostnads fri utvärderings period och utvärderings version*** | Din app måste innehålla något av följande alternativ för att en kund ska kunna använda din app kostnads fritt under en begränsad tid.<ul> <li>Ange en `try` metod så att kunder kan starta en utvärderings version av din app i AppSource</li> <li>Ange ett `request trial` alternativ i AppSource så att kunder kan begära en utvärderings version av din app</li> </ul>Den kostnads fria utvärderings versionen som du tillhandahåller måste ge kunden en förinställd tid för att testa din app utan extra kostnad. | Krävs |  
| ***Enkel konfigurerings bar lösning som är redo att använda*** | Din app måste vara enkel och snabb att konfigurera och konfigurera utan anpassning krävs. | Krävs |  
| ***Leadhantering*** | Gör det möjligt för din CRM att acceptera lead-data innan du får leads från butik.<ul> <li>Exempel på CRMs är Marketo, Microsoft Dynamics eller Salesforce</li> </ul> | Krävs |  
| ***Sekretess policy och användnings villkor*** | Din app måste innehålla en länk till din sekretess policy sida med hjälp av en offentlig URL. Dina användnings villkor måste anges under publicering som text. | Krävs |  
| ***Support*** | Din app måste innehålla en länk till din kund support sida med hjälp av en offentlig URL. Om din app är en utvärderings version måste du ha support utan extra kostnad under utvärderings perioden. | Krävs |  

## <a name="storefront-requirements-azure-marketplace"></a>Butik-krav: Azure Marketplace  
Följande är krav krav för List typer i Azure Marketplace.  

| Krav | Information | Listtyp |  
|:--- |:--- |:--- |  
| ***Deltagande principer*** | Din app måste följa principerna för deltagande i Azure Marketplace.<ul> <li>Mer information om principerna för deltagande finns på sidan för deltagande principer för Azure Marketplace på [Azure.Microsoft.com/support/Legal/Marketplace/Participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />trial |  
| ***Integrering med Microsoft*** | Ditt erbjudande bör använda eller utöka Microsoft Azure tjänst typer som beräkning, nätverk eller lagring. Ditt erbjudande bör överensstämma med en befintlig Azure Marketplace-kategori, till exempel databaser, säkerhet eller nätverk.<ul> <li>Mer information om Marketplace-erbjudanden finns på sidan Marketplace-appar som finns på [azuremarketplace.Microsoft.com/Marketplace/Apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />trial |  
| ***Målgrupp*** | Ditt erbjudande måste vara för IT-proffs, moln utvecklare eller andra tekniska kund roller. | lista<br />Transact<br />trial |  
| ***Leadhantering*** | Aktivera din CRM (Marketo, Microsoft Dynamics eller Salesforce) för att acceptera lead-data innan du får leads från butik. | lista<br />Transact<br />trial |  
| ***Sekretess policy och användnings villkor*** | Din app måste innehålla en länk till din sekretess policy sida med hjälp av en offentlig URL. Dina användnings villkor måste anges under publicering som text. | lista<br />Transact<br />trial |  
| ***Support*** | Ditt erbjudande måste innehålla en länk till din kund support sida med hjälp av en offentlig URL. Om ditt erbjudande är en utvärderings version måste du support utan extra kostnad under utvärderings perioden. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Icke-Transact-listor  
I det här avsnittet beskrivs alla erbjudande typer som inte använder typen av Transact-listor. 

### <a name="list"></a>Visa lista  
Listan över List typer innehåller följande typer av erbjudanden på butiker i Marketplace.  

| Erbjudandetyp | Butik | Information |  
|:---        |:---        |:---     |  
| Rådgivningstjänster | AppSource | Krav: AppSource: lista: konsult tjänster |  
| Rådgivningstjänster | Azure Marketplace | Krav: Azure Marketplace: lista: konsult tjänster |  
| Kontakta mig | AppSource | [](#) |  
| Kontakta mig | Azure Marketplace | Krav: AppSource: lista: kontakta mig |  

#### <a name="requirements-appsource-list-consulting-service"></a>Krav: AppSource: lista: konsult tjänsten  

| Krav | Information |  
|:--- |:--- |  
| Egenskaper för tjänst erbjudande | Konsult tjänsten måste uppfylla följande kriterier.<ul> <li>Leverera ett åtagande med fast omfattning, fast varaktighet, fast pris (eller kostnads fri).</li> <li>Placera främst i förväg för försäljning.</li> <li>Begränsa till en enskild kund.</li> <li>Genomför på plats.</li> </ul> |  
| Partner krav för konsult tjänster | Du uppfyller villkoren i det relevanta avsnittet för din tjänst.<table><tr><th>Lösnings områden</th><th>Kriterie</th></tr><tr><td>Dynamics 365 för Customer Engagement</td><td>Ha kompetens för att få en silver-eller guld-moln kund Relations hantering.</td></tr><tr><td>Dynamics 365 för finanser och drift, Enterprise Edition</td><td>Har silver eller guld företags resurs planerings kompetens och intäkter från din moln verksamhet under de efterföljande 12 månaderna med $25 000 eller mer.</td></tr><tr><td>Dynamics 365 för finanser och drift, Business Edition</td><td>Tjäna som Cloud Services leverantör (CSP) eller digital partner of Record (DPOR) för en eller flera kunder.</td></tr><tr><td>Power BI</td><td>Uppfyller villkoren för lösnings partner.</td></tr><tr><td>PowerApps</td><td>Ha en partner demonstrations lösning.</td></tr></table><ul> <li>Mer information om kund Relations hantering finns på sidan Cloud Customer Relations hantering på [partner.Microsoft.com/membership/Cloud-Customer-Relationship-Management-Competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Mer information om resurs planering finns på företags resurs planerings sidan som finns på [partner.Microsoft.com/membership/Enterprise-Resource-Planning-Competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Mer information om CSP finns på sidan Cloud Services Provider som finns på [partner.Microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Mer information om DPOR finns på sidan digital partner of Record and partner Association på [partner.Microsoft.com/membership/digital-partner-of-Record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Mer information om lösnings partner kriterier finns i Översikt över lösnings partner och incitament som finns [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives)på.</li> <li>Mer information om partner Showcase finns på partner Showcase-sidan som finns på [PowerApps.Microsoft.com/partner-Showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Krav: Azure Marketplace: lista: konsult tjänsten  

| Krav | Information |  
|:--- |:--- |  
| Egenskaper för tjänst erbjudande | Konsult tjänsten måste uppfylla följande kriterier.<ul> <li>Leverera ett åtagande med fast omfattning, fast varaktighet, fast pris (eller kostnads fri).</li> <li>Placera främst i förväg för försäljning.</li> <li>Begränsa till en enskild kund.</li> <li>Genomför på plats.</li> </ul> |  
| Partner krav för konsult tjänster | Du måste ha silver eller guld i någon av följande kompetenser i det relevanta avsnittet för din tjänst. <table><tr><th>Lösnings områden</th><th>Skaffa</th></tr><td>Moln plattform och infrastruktur</td><td>Moln plattform<br />Datacenter</td><tr><td>Program utveckling och ISV</td><td>Apputveckling<br />Programintegrering<br />DevOps</td></tr><tr><td>Datahantering och analys</td><td>Dataanalys<br />Data plattform</td></tr></table><ul> <li>Om du vill ha mer information om kompetenser kan du besöka kompetensen via Microsoft Partner Network sidan på [partner.Microsoft.com/membership/Competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Mer information om hur du registrerar finns på sidan med rådgivnings tjänster för Azure Marketplace på [docs.Microsoft.com/Azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Utvärdering  

| Erbjudandetyp | Butik | Information |  
|:---        |:---        |:---     |  
| Kostnads fri/SaaS-utvärdering | AppSource | Krav för List typ: utvärdering |  
| Kostnads fri/SaaS-utvärdering | Azure Marketplace | Krav: Azure Marketplace: utvärdering: utvärdering av kostnads fri utvärderings version/SaaS |  
| Interaktiv demo | AppSource | Krav för List typ: utvärdering |  
| Interaktiv demo | Azure Marketplace | [Krav: Azure Marketplace: utvärdering: interaktiv demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Test enhet | AppSource | Krav för List typ: utvärdering |  
| Test enhet | Azure Marketplace | [Krav: Azure Marketplace: utvärdering: test enhet](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Krav: Azure Marketplace: utvärdering  

| Krav | Information |  
|:--- |:--- |  
| Kostnads fri utvärderings period och utvärderings version | Din kund kan använda din app kostnads fritt under en begränsad tid.<br /><br />Kunden behöver inte betala några licens-eller prenumerations avgifter för erbjudandet eller appen. Kunden behöver inte betala för den underliggande Microsoft-produkten eller tjänsten från första part. Alla utvärderings alternativ distribueras till din Azure-prenumeration. Du har ensam kontroll över kostnads optimering och hantering.<br /><br />Du kan välja en kostnads fri utvärderings version, en interaktiv demonstration eller en testen het. Oavsett vad du väljer, måste din kostnads fria utvärderings period erbjuda kunden en i förväg angiven tid för att testa appen utan extra kostnad.<ul> <li>Om du vill påbörja processen med att skapa en testen het skickar du [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)ett e-postmeddelande till.</li> </ul>Obs! Azure Marketplace-SaaS utvärderings versioner måste tillåta kunder att använda arbets uppgifter för att logga in.<ul> <li>Mer information finns i avsnittet AppSource utvärderings upplevelser på [docs.Microsoft.com/Azure/Active-Directory/develop/Active-Directory-devhowto-appsource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Enkel konfigurerings bar lösning som är redo att använda | Din app måste vara enkel och snabb att konfigurera och konfigurera. |  
| Tillgänglighet/drift tid | Din SaaS-app eller-plattform måste ha en drift tid på minst 99,9%. |  
| Azure Active Directory | Ditt erbjudande måste tillåta Azure Active Directory (Azure AD) federerad enkel inloggning (SSO) (Azure AD federerad SSO) med medgivande aktiverat. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Krav: Azure Marketplace: utvärdering: utvärdering av kostnads fri utvärderings version/SaaS  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för en kund att prova produkten innan du köper med en automatiserad metod för att konvertera till betald användning. Möjliggör även korrektur av koncept för kunden och ett gemensamt engagemang med Microsoft Sales Teams. | Din lösning är en mall för virtuella datorer eller lösningar.<br /><br />Din lösning är ett SaaS erbjudande och du erbjuder en SaaS-produkt med flera innehavare.<br /><br />Du har en första körnings upplevelse som gör det enkelt att komma igång med kunden.<br /><br />Du har en enda klient men lägger till kunder som gäst användare. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Krav: Azure Marketplace: utvärdering: interaktiv demo  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för dina kunder att se din lösning i praktiken utan att det är komplicerat att konfigurera. | Din lösning kräver komplexa inställningar som skulle vara svåra att uppnå inom utvärderings perioden. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Krav: Azure Marketplace: utvärdering: test enhet  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för en kund att testa produkten innan de köper.<br /><br />Innehåller en guidad upplevelse av din lösning med förkonfigurerade inställningar.<br /><br />Följande är ytterligare förmåner när du använder en test-enhet.<ul> <li>27% av användar sökningar på Marketplace är raffinerade av användarna att bara Visa erbjudanden med test enheter.</li> <li>Erbjudanden med test enheter genererar 38% fler leads än erbjudanden utan.</li> <li>36% av de nya kund förvärven på Marketplace kommer från kunder som tog en test-enhet.</li> <li>Test enheter gör det möjligt för Microsofts fält säljare att bättre förstå din produkt för samförsäljnings ansträngningar.</li> </ul> | Din lösning är en virtuell dator, en lösnings mall eller en SaaS-app med en enda klient eller är komplicerad att etablera. <br /><br />Du har inte någon metod för att konvertera din utvärderings version till ett betalt erbjudande. |  

---

## <a name="transact-specific-listings"></a>Transact-/regionsspecifika listor

### <a name="transact"></a>Transact  

| Erbjudandetyp | Butik | Information |   
|:---        |:---        | :--- |  
| Azure Apps: hanterad app | Azure Marketplace | Krav: Azure Marketplace: Transact: Azure Apps: hanterad app |  
| Azure Apps: lösnings mal len | Azure Marketplace | Krav: Azure Marketplace: Transact: Azure Apps: lösnings mal len |  
| Containrar | Azure Marketplace | [Krav: Azure Marketplace: Transact: container](#requirements-azure-marketplace-transact-container) |  
| SaaS-app  | Azure Marketplace | [Krav: Azure Marketplace: Transact: SaaS app](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuell dator | Azure Marketplace | [Krav: Azure Marketplace: Transact: virtuell dator](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Krav: Azure Marketplace: Transact: container  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöder antingen den kostnads fria eller BYOL fakturerings modellen. |  
| Docker-baserad avbildning | Behållar avbildningen måste vara baserad på Docker-bildningens avbildnings format och måste hämtas från Azure Container Registration. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Krav: Azure Marketplace: Transact: SaaS app  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Ditt erbjudande priss ätts till en månatlig fast taxa. Användnings priser och användnings *sanna* alternativ stöds inte för tillfället. |  
| Annullering | Ditt erbjudande kan avbrytas av kunden när som helst. |  
| Transaktions landnings sida | Var värd för en Azure Co-anpassad transaktions landnings sida. Landnings sidan gör det möjligt för dina kunder att skapa och hantera ditt SaaS-tjänstkonto. |  
| API för SaaS-prenumeration | Tillhandahålla en tjänst som samverkar med SaaS-prenumerationen för att skapa, uppdatera och ta bort ett användar konto och en tjänste plan. Alla kritiska API-ändringar måste stödjas inom 24 timmar. Eventuella icke-kritiska API-ändringar uppdateras regelbundet. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Krav: Azure Marketplace: Transact: virtuell dator  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Din virtuella dator måste ha stöd för antingen BYOL eller betala per användning. |  
| Azure-kompatibel virtuell hård disk (VHD) | Virtuella datorer måste byggas på Windows eller Linux.<ul> <li>Mer information om hur du skapar en Linux-VHD finns i [Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)som har godkänts på Azure.</li> <li>Mer information om hur du skapar en virtuell Windows-hårddisk finns i [skapa en Azure-kompatibel virtuell hård disk](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg
*   Besök [Azure Marketplace-och AppSource Publisher-guide](./marketplace-publishers-guide.md) sidan.  

