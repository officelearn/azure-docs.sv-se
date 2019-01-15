---
title: Krav genom att ange typ | Azure
description: Den här artikeln beskrivs kriterierna för kvalificering och publicera krav partner försöker förstå hur du publicerar appar på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: c02bc75a978325f8cb0dc8733fb8d296e8710975
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264690"
---
# <a name="requirements-by-listing-type"></a>Krav genom att ange typ  
Teknik- och innehåll kraven varierar i butiken, typ av erbjudande och listtyp. Granska följande specifikationer för att kontrollera efterlevnaden.  
1. Storefront krav:  
    *   [AppSource](#storefront-requirements-appSource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Lista typ och krav för typ av erbjudande:  
    *   Mer information om typer av lista och typer av erbjudanden finns avgör vilken lista för din lösning sidan finns på [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Storefront krav: AppSource  
I följande tabell beskrivs de nödvändiga kraven för att publicera på AppSource.  

| Krav | Information | Krävs eller rekommenderas |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Din app måste tillåta Azure Active Directory federerad enkel inloggning (Azure AD-federerad enkel inloggning) med medgivande aktiverat.<ul> <li>För mer information om hur du aktiverar Azure AD-federerad enkel inloggning, finns i Konfigurera enkel inloggning till program som inte ingår i Azure Active Directory appen galleriet på sidan finns på [docs.microsoft.com/azure/active-directory/ Active directory-saas-anpassad-appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Krävs |   
| ***Integrering med Microsoft-molntjänster*** | Din app ska integrera med andra Microsoft Cloud-tjänster som Microsoft Power BI, Cortana Intelligence eller Microsoft Azure-tjänster.<ul> <li>Ett exempel på en Microsoft Cloud-tjänst är Sakernas Internet.</li> </ul> | Rekommenderas |  
| ***Målgrupp*** | Din app måste vara för line-of-business-användare och företagsägare. | Krävs | 
| ***Programvara som en tjänst (SaaS)-app för företag*** | Din app måste uppfylla följande krav.<ul> <li>En line-of-business SaaS-app</li> <li>Affärsprocess fokuserar</li> <li>Riktade till företagskunder</li> <li>Användarna kan använda sina autentiseringsuppgifter för inloggning, till exempel användarnamn och lösenord</li> </ul> | Krävs |  
| ***Kostnadsfria utvärderingsperioden och utvärderingsmiljö*** | Din app måste inkludera en följande alternativ för att en kund att använda appen utan kostnad under en begränsad tid.<ul> <li>Ange en `try` metod, så kunder kan starta en utvärdering av din app i AppSource</li> <li>Ange en `request trial` alternativet i AppSource, så kunder kan begära en utvärderingsversion av din app</li> </ul>Den kostnadsfria utvärderingsversionen som du anger måste erbjuda kunden en förinställda lång tid att testa din app utan extra kostnad. | Krävs |  
| ***Enkelt att konfigurera och färdiga att använda lösning*** | Din app måste vara enkelt och snabbt att konfigurera och konfigurera med Ingen anpassning krävs. | Krävs |  
| ***Leadhantering*** | Aktivera din CRM att acceptera lead-data innan du får leads från storefront.<ul> <li>Exempel på CRM är Marketo, Microsoft Dynamics eller Salesforce</li> </ul> | Krävs |  
| ***Sekretesspolicy och användningsvillkor*** | Din app måste ange en länk till webbsidan sekretess princip med hjälp av en offentlig URL. Dina användningsvillkor måste anges vid publicering som text. | Krävs |  
| ***Support*** | Din app måste ange en länk till din kund supportsida med hjälp av en offentlig URL. Om din app är en utvärderingsversion, sedan du måste ha stöd för utan ytterligare kostnad under utvärderingsperioden. | Krävs |  

## <a name="storefront-requirements-azure-marketplace"></a>Storefront krav: Azure Marketplace  
Här följer nödvändiga kraven för att visa en lista över typer i Azure Marketplace.  

| Krav | Information | Listtyp |  
|:--- |:--- |:--- |  
| ***Deltagandepolicyer för*** | Din app måste följa deltagandepolicyer för Azure Marketplace.<ul> <li>Mer information om deltagande principer finns i Deltagandepolicyer för Azure Marketplace sidan finns på [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />trial |  
| ***Integrering med Microsoft*** | Ditt erbjudande ska använda eller utöka typer av Microsoft Azure-tjänster, till exempel beräkning, nätverk och lagring. Ditt erbjudande ska justeras till en befintlig Azure Marketplace-kategori, till exempel databaser, säkerhet och nätverk.<ul> <li>Mer information om Marketplace-erbjudanden besök Marketplace-appar sida finns på [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />trial |  
| ***Målgrupp*** | Erbjudandet måste vara för IT-proffs, molnutvecklare eller andra tekniska kundroller. | lista<br />Transact<br />trial |  
| ***Leadhantering*** | Aktivera din CRM (Marketo, Microsoft Dynamics eller Salesforce) att acceptera lead-data innan du får leads från storefront. | lista<br />Transact<br />trial |  
| ***Sekretesspolicy och användningsvillkor*** | Din app måste ange en länk till webbsidan sekretess princip med hjälp av en offentlig URL. Dina användningsvillkor måste anges vid publicering som text. | lista<br />Transact<br />trial |  
| ***Support*** | Erbjudandet måste ange en länk till din kund supportsida med hjälp av en offentlig URL. Om ditt erbjudande är en utvärderingsversion, sedan du måste ha stöd för utan ytterligare kostnad under utvärderingsperioden. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Listor med ett icke transact  
Det här avsnittet beskriver alla typer av erbjudanden som inte använder Transact listtyp. 

### <a name="list"></a>Visa lista  
Listan listtyp innehåller följande typer av erbjudanden på butiker i marketplace.  

| Erbjudandetyp | Butik | Information |  
|:---        |:---        |:---     |  
| Konsulttjänster | AppSource | [Krav: AppSource: Lista: Konsulttjänster](#requirements-appsource-list-consulting-services) |  
| Konsulttjänster | Azure Marketplace | [Krav: Azure Marketplace: Lista: Konsulttjänster](#requirements-azure-marketplace-list-consulting-services) |  
| Kontakta mig | AppSource | [](#) |  
| Kontakta mig | Azure Marketplace | [Krav: AppSource: Lista: Kontakta mig](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Krav: AppSource: Lista: Konsulttjänst  

| Krav | Information |  
|:--- |:--- |  
| Egenskaper för tjänst-erbjudande | Din konsulttjänst måste uppfylla följande kriterier.<ul> <li>Leverera en fast omfång, fast varaktighet, fast pris (eller kostnadsfri) engagement.</li> <li>Orientera främst för aktiviteter före försäljning.</li> <li>Begränsa till en enda kund.</li> <li>Genomför på platsen.</li> </ul> |  
| Partnerkrav för konsulttjänster | Du uppfyller villkoret i området relevanta för din tjänst.<table><tr><th>Lösningsområde</th><th>Kriterie</th></tr><tr><td>Dynamics 365 för Customer Engagement</td><td>Ha Silver eller Gold Cloud Customer Relationship Management kompetens.</td></tr><tr><td>Dynamics 365 för Finance and Operations, Enterprise edition</td><td>Har Silver eller Gold Enterprise Resource Planning kompetens och intäkter från din molnanvändning i de avslutande 12 månaderna 25 000 eller fler.</td></tr><tr><td>Dynamics 365 för finans och åtgärder, Business edition</td><td>Fungera som Cloud Services Provider (CSP) eller Digital Partner of Record (DPOR) för en eller flera kunder.</td></tr><tr><td>Power BI</td><td>Uppfyll villkoren för Solution Partner.</td></tr><tr><td>PowerApps</td><td>Har en Partnerdemonstration-lösning.</td></tr></table><ul> <li>Mer information om hantering av kundrelationer besöka sidan finns i molnet Customer Relationship Management [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Mer information om resursplanering för finns i resursplanering sidan finns på [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Mer information om CSP finns Molntjänstleverantören sidan finns på [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Mer information om DPOR finns Digital Partner of Record och Partner Association sidan finns på [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Mer information om lösningen partner villkor finns Lösningsöversikt för Partner och incitament dokumentet finns på [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Mer information om partnerdemonstration finns Partnerdemonstration sidan finns på [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Krav: Azure Marketplace: Lista: Konsulttjänst  

| Krav | Information |  
|:--- |:--- |  
| Egenskaper för tjänst-erbjudande | Din konsulttjänst måste uppfylla följande kriterier.<ul> <li>Leverera en fast omfång, fast varaktighet, fast pris (eller kostnadsfri) engagement.</li> <li>Orientera främst för aktiviteter före försäljning.</li> <li>Begränsa till en enda kund.</li> <li>Genomför på platsen.</li> </ul> |  
| Partnerkrav för konsulttjänster | Du måste ha silver eller gold i någon av de följande uppnådda i området relevanta för din tjänst. <table><tr><th>Lösningsområde</th><th>Kompetens</th></tr><td>Molnplattform och infrastruktur</td><td>Molnplattform<br />Datacenter</td><tr><td>Programutveckling och ISV</td><td>Apputveckling<br />Integrering av program<br />DevOps</td></tr><tr><td>Datahantering av och analyser</td><td>Datanalys<br />Data Platform</td></tr></table><ul> <li>Mer information om kompetenser finns uppnådda via Microsoft Partner Network sidan finns på [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Mer information om lista finns i Azure Marketplace-konsulttjänster sidan finns på [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Utvärdering  

| Erbjudandetyp | Butik | Information |  
|:---        |:---        |:---     |  
| Kostnadsfria / SaaS-utvärdering | AppSource | [Lista typ krav: Utvärderingsversion](#listing-type-requirements-trial) |  
| Kostnadsfria / SaaS-utvärdering | Azure Marketplace | [Krav: Azure Marketplace: Utvärderingsversion: Kostnadsfri utvärderingsversion / SaaS-utvärdering](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktiv demo | AppSource | [Lista typ krav: Utvärderingsversion](#listing-type-requirements-trial) |  
| Interaktiv demo | Azure Marketplace | [Krav: Azure Marketplace: Utvärderingsversion: Interaktiv demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Testkör | AppSource | [Lista typ krav: Utvärderingsversion](#listing-type-requirements-trial) |  
| Testkör | Azure Marketplace | [Krav: Azure Marketplace: Utvärderingsversion: Testkör](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Krav: Azure Marketplace: Utvärdering  

| Krav | Information |  
|:--- |:--- |  
| Kostnadsfria utvärderingsperioden och utvärderingsmiljö | Kunden kan använda din app utan kostnad under en begränsad tid.<br /><br />Kunden behöver inte betala alla avgifter licens eller prenumeration för din erbjudandet eller app. Kunden är inte skyldig att betala för de underliggande Microsoft från första part produkt eller tjänst. Alla utvärderingsversionsalternativ som distribueras till din Azure-prenumeration. Har du ensam kontroll utvärderingsversion av kostnadsoptimeringar och hantering.<br /><br />Du kan välja en kostnadsfri utvärderingsversion, Interaktiv demo eller Testkör. Oavsett vad du väljer, erbjuda din kostnadsfria utvärderingsversion kunden en förinställda lång tid att testa appen utan extra kostnad.<ul> <li>Starta processen med att skapa en testenhet genom att skicka ett e- [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Obs! Azure Marketplace SaaS utvärderingsversion upplevelser måste kan kunderna använda autentiseringsuppgifter för arbetet för att logga in.<ul> <li>Mer information finns i AppSource utvärderingsversion upplevelser avsnittet finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Enkelt att konfigurera och färdiga att använda lösning | Din app måste vara enkelt och snabbt att göra inställningar. |  
| Tillgänglighet / drifttid | Din SaaS-app eller en plattform måste ha en drifttid på minst 99,9%. |  
| Azure Active Directory | Erbjudandet måste tillåta Azure Active Directory (Azure AD) federerad enkel inloggning (SSO) (Azure AD-federerad enkel inloggning) med medgivande aktiverat. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Krav: Azure Marketplace: Utvärderingsversion: Kostnadsfri utvärderingsversion / SaaS-utvärdering  

| Fördelar | Krav |  
|:--- |:--- |  
| Kan en kund att testa din produkt innan köpa med en automatiserad metod för att konvertera till betald användning. Möjliggör också koncepttester för kunden och gemensamma engagemang med Microsofts säljteam. | Din lösning är en virtuell dator eller mall.<br /><br />Din lösning är en SaaS erbjudande och du erbjuda en multitenant SaaS-produkt.<br /><br />Du har en första körningen av att en kund komma igång och snabbt.<br /><br />Du har en enda klient men lägger till kunder som gästanvändare. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Krav: Azure Marketplace: Utvärderingsversion: Interaktiv demo  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för kunderna att se din lösning i praktiken utan att komplexiteten för att ställa in. | Din lösning kräver avancerade inställningar som skulle vara svåra att uppnå inom utvärderingsperioden är slut. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Krav: Azure Marketplace: Utvärderingsversion: Testkör  

| Fördelar | Krav |  
|:--- |:--- |  
| Kan en kund att testa din produkt innan de köper.<br /><br />Ger en interaktiv upplevelse av din lösning med hjälp av förkonfigurerade inställningar.<br /><br />Följande är ytterligare förmåner när du använder en testenhet.<ul> <li>27% av användarnas sökningar på marketplace mer genom att endast visa erbjudanden med test Drive användarna.</li> <li>Erbjudanden med test Drive generera leads för 38% mer än erbjudanden utan.</li> <li>36% av den nya kunden förvärv på marketplace kommer från kunder som tog en testenhet.</li> <li>Test Drive aktivera Microsofts fältsäljare att bättre förstå din produkt för medförsäljning arbete.</li> </ul> | Din lösning är en virtuell dator, mall eller SaaS-app med en enda klient eller komplicerade att etablera. <br /><br />Du har inte en metod för att konvertera din utvärderingsversion till en betalprenumeration. |  

---

## <a name="transact-specific-listings"></a>Transact-specifika listor

### <a name="transact"></a>Transaktion  

| Erbjudandetyp | Butik | Information |   
|:---        |:---        | :--- |  
| Azure-appar: Hanterad app | Azure Marketplace | [Krav: Azure Marketplace: Transact: Azure-appar: Hanterad app](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Azure-appar: Lösningsmall | Azure Marketplace | [Krav: Azure Marketplace: Transact: Azure-appar: Lösningsmall](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Containrar | Azure Marketplace | [Krav: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS-app  | Azure Marketplace | [Krav: Azure Marketplace: Transact: SaaS-app](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuell dator | Azure Marketplace | [Krav: Azure Marketplace: Transact: Virtuell dator](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Krav: Azure Marketplace: Transact: Container  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöd för antingen den kostnadsfria eller BYOL faktureringsmodell. |  
| Docker-baserad avbildning | Behållaravbildningen måste baseras på Docker-avbildningsformatet och måste hämtas från Azure Container Registry. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Krav: Azure Marketplace: Transact: SaaS-app  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Ditt erbjudande debiteras med en månatlig fast pris. Användningsbaserad prissättning och användningsbaserad *SANT upp* kan inte användas just nu. |  
| Annulleringen | Erbjudandet är avbrytbar av kunden när som helst. |  
| Landningssida för transaktion | Vara värd för en landningssida för Azure anpassade transaktion. Denna sida kan kunderna skapa och hantera ditt SaaS-tjänstkonto. |  
| SaaS-prenumeration API | Ange en tjänst som interagerar med SaaS-prenumeration för att skapa, uppdatera och ta bort ett användaravtal för kontot och tjänsten. Alla kritiska API-ändringar måste stödjas inom 24 timmar. Alla icke-kritiska API-ändringar uppdateras regelbundet. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Krav: Azure Marketplace: Transact: Virtuell dator  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Den virtuella datorn måste ha stöd för BYOL eller användningsbaserad fakturering. |  
| Azure-kompatibel virtuell hårddisk (VHD) | Virtuella datorer måste vara baserade på Windows eller Linux.<ul> <li>Mer information om hur du skapar en Linux-VHD finns [Linux-distributioner på Azure-godkända](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Läs mer om hur du skapar en virtuell Windows-Hårddisk [skapa en Azure-kompatibel VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg
*   Gå till den [Azure Marketplace och AppSource Publiceringsguiden](./marketplace-publishers-guide.md) sidan.  

