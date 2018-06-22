---
title: Krav per listtyp | Azure
description: Den här artikeln beskriver kriterierna och publishing krav partners försök att förstå hur du publicerar appar på Azure Marketplace.
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
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 07a62dfa2d7e1c71daf547c5aa7c8c7d15830bfd
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309324"
---
# <a name="requirements-by-listing-type"></a>Krav per listtyp  
Teknisk och marknadsföring innehåll kraven varierar beroende på storefront och erbjudandetypen listtyp. Granska följande specifikationer för att kontrollera din kompatibiliteten.  
1. Storefront krav:  
    *   [AppSource](#storefront-requirements-appSource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Listtyp och erbjudande krav:  
    *   Mer information om lista över typer och erbjudandetyper finns avgör vilken lista för din lösning sidan på [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Krav för storefront: AppSource  
I följande tabell beskrivs de nödvändiga kraven för publicering på AppSource.  
| Krav | Information | Krävs eller rekommenderas |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (AD Azure)*** | Appen måste tillåta Azure Active Directory federerad enkel inloggning (Azure AD federerad enkel inloggning) med samtycke aktiverad.<ul> <li>För mer information om hur du aktiverar Azure AD federerad enkel inloggning, finns i Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory application gallery sidan finns på [docs.microsoft.com/azure/active-directory/ Active directory-saas-anpassad-appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Krävs |   
| ***Integrering med Microsoft-molntjänster*** | Din app ska integreras med andra Microsoft Cloud-tjänster som Microsoft Power BI Cortana Intelligence eller Microsoft Azure-tjänster.<ul> <li>Ett exempel på en Microsoft Cloud-tjänst är Sakernas Internet.</li> </ul> | Rekommenderas |  
| ***målgrupp*** | Appen måste vara för line-of-business-användare och projektägare. | Krävs | 
| ***Programvara som en tjänst (SaaS)-app för företag*** | Appen måste uppfylla följande krav.<ul> <li>En line-of-business SaaS-app</li> <li>Affärsprocess fokuserar</li> <li>Avsedda för kunder</li> <li>Användarna kan använda sina autentiseringsuppgifter för inloggning, till exempel användarnamn och lösenord</li> </ul> | Krävs |  
| ***Utvärderingsperioden och utvärderingsversionen*** | Appen måste innehålla en följande alternativ för en kund att använda appen kostnadsfritt under en begränsad tid.<ul> <li>Ange en `try` metod, så kunder kan starta en utvärdering av din app i AppSource</li> <li>Ange en `request trial` alternativet i AppSource, så kunder kan begära en utvärderingsversion av din app</li> </ul>Den kostnadsfria utvärderingsversionen av som du anger måste erbjuda kunden en förinställd tidsperiod att testa appen med utan extra kostnad. | Krävs |  
| ***Enkelt konfigureras, färdiga att använda lösning*** | Appen måste vara snabbt och enkelt att konfigurera och ställa in med finns inte någon anpassning krävs. | Krävs |  
| ***Administrera leads*** | Du måste aktivera din CRM att acceptera lead data innan du får leads från storefront.<ul> <li>Exempel på CRM är Marketo, Microsoft Dynamics eller Salesforce</li> </ul> | Krävs |  
| ***Sekretesspolicy och användningsvillkor*** | Din app måste ange en länk till webbsidan sekretess principen med hjälp av en offentlig URL. Du måste ange dina villkor för användning vid publicering som text. | Krävs |  
| ***Support*** | Din app måste ange en länk till din kund supportsida med hjälp av en offentlig URL. Om din app är en utvärderingsversion, måste sedan du stödja utan extra kostnad under utvärderingsperioden. | Krävs |  

## <a name="storefront-requirements-azure-marketplace"></a>Krav för storefront: Azure Marketplace  
Följande är nödvändiga krav för att visa en lista över typer i Azure Marketplace.  
| Krav | Information | Listtyp |  
|:--- |:--- |:--- |  
| ***Deltagande principer*** | Appen måste följa Azure Marketplace deltagande principer.<ul> <li>Mer information om deltagande principer finns i sidan finns på Azure Marketplace deltagande principer [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />utvärderingsversion |  
| ***Integrering med Microsoft*** | Erbjudandet ska använda eller utöka typer av Microsoft Azure-tjänster, till exempel beräkning, nätverk och lagring. Erbjudandet ska justeras mot en befintlig Azure Marketplace-kategori som databaser, säkerhet eller nätverk.<ul> <li>Mer information om Marketplace-erbjudanden finns Marketplace-appar som sidan finns på [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />utvärderingsversion |  
| ***målgrupp*** | Erbjudandet måste vara för IT-proffs, molnet utvecklare eller andra tekniska kundroller. | lista<br />Transact<br />utvärderingsversion |  
| ***Administrera leads*** | Du måste aktivera din CRM (Marketo, Microsoft Dynamics eller Salesforce) att acceptera lead data innan du får leads från storefront. | lista<br />Transact<br />utvärderingsversion |  
| ***Sekretesspolicy och användningsvillkor*** | Din app måste ange en länk till webbsidan sekretess principen med hjälp av en offentlig URL. Du måste ange dina villkor för användning vid publicering som text. | lista<br />Transact<br />utvärderingsversion |  
| ***Support*** | Erbjudandet måste ange en länk till din kund supportsida med hjälp av en offentlig URL. Om erbjudandet är en utvärderingsversion, måste sedan du stödja utan extra kostnad under utvärderingsperioden. | Transact<br />utvärderingsversion |    

## <a name="non-transact-listings"></a>Icke-transact listor  
Det här avsnittet beskriver alla erbjudandetyper av som inte använder Transact listtyp. 

### <a name="list"></a>Visa lista  
Listan listtyp innehåller följande typer av erbjudandet på skyltfönster på marketplace.  

| Erbjudandetyp | Butik | Information |  
|:---        |:---        |:---     |  
| Rådgivning | AppSource | [Krav: AppSource: lista: rådgivning](#requirements-appsource-list-consulting-services) |  
| Rådgivning | Azure Marketplace | [Krav för: Azure Marketplace: lista: rådgivning](#requirements-azure-marketplace-list-consulting-services) |  
| Kontakta mig | AppSource | [](#) |  
| Kontakta mig | Azure Marketplace | [Krav: AppSource: lista: kontakta mig](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Krav: AppSource: lista: samråd med tjänsten  

| Krav | Information |  
|:--- |:--- |  
| Tjänsten erbjudande egenskaper | Rådgivning tjänsten måste uppfylla följande kriterier.<ul> <li>Leverera en fast omfång, fast varaktighet, fast pris (eller gratis) engagement.</li> <li>Orientera främst för förförsäljnings.</li> <li>Begränsa till en kund.</li> <li>Gör på platsen.</li> </ul> |  
| Partnerkrav för samråd tjänster | Du uppfyller villkoren i området relevanta för din tjänst.<table><tr><th>Lösning område</th><th>Kriterie</th></tr><tr><td>Dynamics 365 för kunden Engagement</td><td>Ha kompetenspartners (Silver eller guld molnet Customer Relationship Management).</td></tr><tr><td>Dynamics 365 för ekonomi och åtgärder, Enterprise edition</td><td>Har kompetenspartners (Silver eller guld resursplanering för företag) och intäkter dina moln-åtgärder i efterföljande 12 månader i minst 25 000.</td></tr><tr><td>Dynamics 365 för ekonomi och åtgärder, Business edition</td><td>Fungera som moln-providers (CSP) eller Digital Partner av posten (DPOR) för en eller flera kunder.</td></tr><tr><td>Power BI</td><td>Uppfyller samarbetspartner villkor.</td></tr><tr><td>PowerApps</td><td>Har en Partner samlade lösning.</td></tr></table><ul> <li>Mer information om hantering av kunden besöka sidan finns i molnet Customer Relationship Management [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Mer information om resursplanering för finns i den Enterprise resursplanering sidan finns på [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Mer information om Kryptografiprovider finns i molnet tjänsteleverantör sidan finns på [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Mer information om DPOR finns digitala partnern och Partner Association sidan finns på [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Mer information om lösningen partner villkor finns Partneröversikt lösning och uppmuntra dokumentet finns på [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Mer information om partner samlade finns Partner-samlade sidan finns på [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Krav för: Azure Marketplace: lista: samråd med tjänsten  

| Krav | Information |  
|:--- |:--- |  
| Tjänsten erbjudande egenskaper | Rådgivning tjänsten måste uppfylla följande kriterier.<ul> <li>Leverera en fast omfång, fast varaktighet, fast pris (eller gratis) engagement.</li> <li>Orientera främst för förförsäljnings.</li> <li>Begränsa till en kund.</li> <li>Gör på platsen.</li> </ul> |  
| Partnerkrav för samråd tjänster | Du måste ha silver eller guld i något av följande behörighet i området relevanta för din tjänst. <table><tr><th>Lösning område</th><th>Kompetensen</th></tr><td>Molnplattform och infrastruktur</td><td>Molnplattform<br />Datacenter</td><tr><td>Programutveckling och ISV</td><td>Apputveckling<br />Integrering av program<br />DevOps</td></tr><tr><td>Datahantering och analys</td><td>Datanalys<br />Data Platform</td></tr></table><ul> <li>Mer information om behörighet finns befogenheter via Microsoft Partner Network sidan finns på [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Mer information om lista finns i Azure Marketplace samråd tjänster sidan finns på [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Utvärdering  

| Erbjudandetyp | Butik | Information |  
|:---        |:---        |:---     |  
| Ledigt / SaaS-utvärderingsversion | AppSource | [Visar en lista över krav: utvärderingsversion](#listing-type-requirements-trial) |  
| Ledigt / SaaS-utvärderingsversion | Azure Marketplace | [Krav för: Azure Marketplace: utvärderingsversion: kostnadsfri utvärderingsversion / SaaS-utvärderingsversion](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktiv demo | AppSource | [Visar en lista över krav: utvärderingsversion](#listing-type-requirements-trial) |  
| Interaktiv demo | Azure Marketplace | [Krav för: Azure Marketplace: utvärderingsversion: interaktiva demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Testkör | AppSource | [Visar en lista över krav: utvärderingsversion](#listing-type-requirements-trial) |  
| Testkör | Azure Marketplace | [Krav för: Azure Marketplace: utvärderingsversion: Testkör](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Krav för: Azure Marketplace: utvärderingsversion  

| Krav | Information |  
|:--- |:--- |  
| Utvärderingsperioden och utvärderingsversionen | Kunden kan använda din app kostnadsfritt under en begränsad tid.<br /><br />Kunden behöver inte betala några avgifter licens eller prenumeration för ditt erbjudande eller en app. Kunden behöver inte betala för den underliggande Microsoft från första part produkt eller tjänst. Alla alternativ för testversionen har distribuerats till din Azure-prenumeration. Du har ensam kontroll utvärderingsversion av kostnadsoptimering av och hantering.<br /><br />Du kan välja en kostnadsfri utvärderingsversion, interaktiva demo eller Testkör. Oavsett vad du väljer erbjuda den kostnadsfria utvärderingsversionen kunden en förinställd tidsperiod försöka app utan extra kostnad.<ul> <li>Om du vill påbörja processen att skapa en test-enhet, skicka ett e-postmeddelande till [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Obs: Azure Marketplace SaaS utvärderingsversion upplevelser måste tillåta kunderna att använda autentiseringsuppgifter för inloggning.<ul> <li>Mer information finns i AppSource utvärderingsversion upplevelser avsnittet finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Enkelt konfigureras, färdiga att använda lösning | Appen måste vara snabbt och enkelt att konfigurera och ställa in. |  
| Tillgänglighet / drifttid | Din SaaS app eller en plattform måste ha en drifttid minst 99,9%. |  
| Azure Active Directory | Erbjudandet måste tillåta Azure Active Directory (AD Azure) federerad enkel inloggning (SSO) (Azure AD federerad enkel inloggning) med samtycke aktiverad. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Krav för: Azure Marketplace: utvärderingsversion: kostnadsfri utvärderingsversion / SaaS-utvärderingsversion  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för kunder att prova produkten innan du köper med en automatiserad metod för att konvertera till betald användning. Du kan också bevis på koncept för kund- och gemensamma interaktion med Microsoft försäljning team. | Lösningen är en virtuell dator eller lösningsmall.<br /><br />Din lösning är en SaaS erbjuder du erbjuda en multitenant SaaS-produkt.<br /><br />Du har en första gången på en gång en kund och snabbt.<br /><br />Du har en enda klient men lägger till kunder som gästanvändare. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Krav för: Azure Marketplace: utvärderingsversion: interaktiva demo  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för kunderna att se din lösning i praktiken utan komplexitet för att konfigurera. | Lösningen kräver avancerade inställningar som skulle vara svårt att uppnå inom utvärderingsperioden. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Krav för: Azure Marketplace: utvärderingsversion: Testkör  

| Fördelar | Krav |  
|:--- |:--- |  
| Gör det möjligt för kunder att prova produkten innan de köpa.<br /><br />Ger en interaktiv upplevelse i lösningen med förkonfigurerade inställningar.<br /><br />Följande är ytterligare fördelar när du använder en test-enhet.<ul> <li>27% av användaren söker på marketplace förfinad av användare att visa erbjudanden med test-enheter.</li> <li>Erbjudanden med test enheter generera leads 38% mer än erbjudanden utan.</li> <li>36% av den nya kunden förvärv på marketplace komma från kunder som tog en test-enhet.</li> <li>Testa enheter aktivera Microsoft fältet Säljare att bättre förstå produkten för samtidigt säljer arbete.</li> </ul> | Lösningen är en virtuell dator, lösningsmall för eller SaaS-app med en enskild klient eller komplicerade att etablera. <br /><br />Du har inte en metod för att konvertera en utvärderingsversion till en betalprenumeration. |  

---

## <a name="transact-specific-listings"></a>Transact-specifika listor

### <a name="transact"></a>Transaktion  

| Erbjudandetyp | Butik | Information |   
|:---        |:---        | :--- |  
| Azure apps: hanterad app | Azure Marketplace | [Krav för: Azure Marketplace: Transact: Azure apps: hanterad app](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Appar i Azure: lösningsmall | Azure Marketplace | [Krav för: Azure Marketplace: Transact: Azure apps: lösningsmall](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Behållare | Azure Marketplace | [Krav för: Azure Marketplace: Transact: behållare](#requirements-azure-marketplace-transact-container) |  
| SaaS-app  | Azure Marketplace | [Krav för: Azure Marketplace: Transact: SaaS-app](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuell dator | Azure Marketplace | [Krav för: Azure Marketplace: Transact: virtuell dator](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Krav för: Azure Marketplace: Transact: behållare  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöd för antingen den kostnadsfria eller BYOL faktureringsmodell som tillämpas. |  
| Docker-baserad avbildning | Behållaren avbildningen måste baseras på bildformat Docker och måste hämtas från Azure-behållare register. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Krav för: Azure Marketplace: Transact: SaaS-app  

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Erbjudandet är kostar debiteras per månad. Användningsbaserad prisnivå och användningsbaserad *true up* alternativ stöds inte just nu. |  
| Annullering | Erbjudandet är cancelable av kunden när som helst. |  
| Landningssida för transaktion | Värd för en landningssida för Azure anpassade transaktion. Denna sida kan kunderna skapa och hantera din SaaS-tjänstkontot. |  
| SaaS-prenumeration API | Tillhandahålla en tjänst som interagerar med SaaS-prenumeration för att skapa, uppdatera och ta bort en användare konto och service-plan. Alla ändringar för kritiska API måste stödjas inom 24 timmar. Ändringar för icke-kritiska API uppdateras regelbundet. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Krav för: Azure Marketplace: Transact: virtuell dator  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Den virtuella datorn måste ha stöd för BYOL eller betala per användning månatliga fakturering. |  
| Azure-kompatibel virtuell hårddisk (VHD) | Virtuella datorer måste baseras på Windows- eller Linux.<ul> <li>Mer information om hur du skapar en Linux VHD finns i Skapa en Azure-kompatibel virtuell Hårddisk (Linux-baserade) avsnitt finns på [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Mer information om hur du skapar en Windows-VHD finns i Skapa en Azure-kompatibel virtuell Hårddisk (Windows-baserade) avsnitt finns på [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg
*   Besök den [Azure Marketplace och AppSource Publisher Guide](./marketplace-publishers-guide.md) sidan.  
 
---  
