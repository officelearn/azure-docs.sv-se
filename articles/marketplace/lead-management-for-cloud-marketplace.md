---
title: Lead-hantering för kommersiell marknads plats | Azure Marketplace och AppSource
description: En översikt över olika avsnitt om publicering av erbjudanden och tekniska artefakter till Azure Marketplace och AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: c41831f528ae425b35a04503180a956f67762b70
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789852"
---
# <a name="lead-management-for-commercial-marketplace"></a>Lead-hantering för kommersiell marknads plats

Kunderna är i mitten av en eventuell verksamhet. I transformeringen av dagens produkt förvärv måste marknads förare fokusera på att ansluta till kunder direkt och skapa en relation. Detta är anledningen till att generera högkvalitativa leads är ett viktigt verktyg för din sälj cykel. När du har angett ditt erbjudande i [partner Center](https://partner.microsoft.com/), finns det verktyg som har Aktiver ATS för att du ska få kontakt information via programmering direkt efter att en kund uttrycker intresse eller distribuerar produkten på Marketplace. 

## <a name="what-are-leads-in-the-marketplace"></a>Vad är leads på Marketplace?

Leads är från kunder som är intresserade eller distribuerar dina produkter från Marketplace. Oavsett om produkten är listad på Azure Marketplace eller AppSource kommer du att kunna ta emot leads från kunder när den har kon figurer ATS korrekt från din CRM till din lista (er) i Partner Center. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Så här ansluter du ditt CRM-system med partner Center

För att komma igång med leads, har lead management Connector i Partner Center utformats så att den enkelt kan anslutas till din CRM-information till en lista över tillgängliga CRM-system. Nu kan du enkelt utnyttja de leads som genereras av Marketplace utan en betydande teknik ansträngning för att integrera med ett externt system.

Här följer stegvisa instruktioner om hur du ansluter vart och ett av de möjliga mål målen:

**Dynamics CRM Online** – se [Konfigurera ledar hantering för Dynamics 365 för kund engagemang](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) för instruktioner om hur du konfigurerar Dynamics CRM Online för att få leads.

**Marketo** -se [Konfigurera ledar hantering i Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) för instruktioner om hur du konfigurerar Marketo-ledar konfiguration för att hämta leads.

**Salesforce** – se [Konfigurera hantering av leads för Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) för instruktioner om hur du konfigurerar din Salesforce-instans för att hämta leads.

**Azure-tabell** – se [Konfigurera hantering av leads med en Azure-tabell](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) för instruktioner om hur du konfigurerar ditt Azure Storage-konto för att hämta leads i en Azure-tabell.

**Https-slutpunkt** – se [Konfigurera hantering av leads med en HTTPS-slutpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) för instruktioner om hur du konfigurerar din HTTPS-slutpunkt för att hämta leads.

När du har konfigurerat ditt lead-mål och har publicerat publicera på ditt erbjudande, kommer vi att validera anslutningen och skicka ett test lead. När du visar erbjudandet innan du går live kan du också testa din lead-anslutning genom att försöka skaffa erbjudandet själv i för hands versionen. Det är viktigt att se till att dina lead-inställningar hålls uppdaterade så att du inte förlorar några leads, så se till att du uppdaterar anslutningarna när något har ändrats i slutet.

### <a name="what-are-the-next-steps"></a>Vad är nästa steg?

När den tekniska konfigurationen är på plats bör du inkludera dessa leads i din aktuella försäljning & marknadsförings strategi och operativa processer. Vi är intresserade av att bättre förstå den övergripande Sälj processen och vill arbeta nära dig med att tillhandahålla högkvalitativa leads och tillräckligt med data för att lyckas. Vi välkomnar din feedback om hur vi kan optimera och förbättra de leads som vi skickar till dig med ytterligare data för att hjälpa kunderna att lyckas. Berätta för oss om du är intresse rad av att ge feedback och förslag för att göra det möjligt för ditt säljteam att bli mer framgångs rik med marknads ledande kunder.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a>Vanliga fel vid lead-konfiguration under publicering i Partner Center

**Det gick inte att spara leadet i Dynamics CRM. Kontrol lera konto inställningarna för Dynamics CRM. LastCRMError: det går inte att logga in på Dynamics CRM, LastCRMException:** 

> Om du valde O365-autentisering kontrollerar du om användar kontot och lösen ordet är giltigt. Om AAD valdes kontrollerar du om klient-ID, program-ID och program hemlig nyckel matchar vad som har kon figurer ATS i AAD. Följ instruktionerna [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Om kontots användar namn/lösen ord är giltigt kontrollerar du att det har åtkomst till Dynamics 365 och har en tilldelad licens (steg 11-15 om du använder Azure Active Directory eller säkerhets inställningar om du använder en Office-användare). 

**Det gick inte att spara leadet i Dynamics CRM. Användaren har inte behörighet att skapa leadsourcecode-attribut i entiteten lead** 

> Programmet/användaren saknar säkerhets roll (er) för att Microsoft Marketplace ledar skrivare. Följ steg 11-15 om du använder Azure Active Directory eller säkerhets inställningar om du använder en Office-användare [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: klienten hittades inte. Den här instansen kan inträffa om det inte finns några aktiva prenumerationer för klienten.**  

> Katalog-ID: t i avsnittet om lead management är inte en giltig katalog. Hämta katalog-ID: t baserat på anvisningarna i steg 2 (under Azure Active Directory, härifrån [.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Det gick inte att spara leadet i Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser misslyckades-inga roller har tilldelats till användaren.**  

> Lösning: tilldela en säkerhets roll till Microsoft Marketplace ledar skrivare. Följ anvisningarna [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) under säkerhets inställningar.

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: det gick inte att hitta programmet med identifieraren i katalogen** 

> Det program-ID som angavs i lead management-avsnittet är inte en giltig katalog. Hämta katalog-ID: t baserat på anvisningarna i steg 8 (under Azure Active Directory [härifrån](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: begärd klient-ID är inte giltigt och ogiltigt format för extern domän** 

> Katalog-ID: t i avsnittet om lead management är inte en giltig katalog. Hämta katalog-ID: t baserat på instruktionerna i steg 2 (under Azure Active Directory, härifrån [).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: fel vid verifiering av autentiseringsuppgifter.: ogiltig klient hemlighet har angetts.** 

> Lösning: Logga in på Azure Portal, kontrol lera om program nyckeln matchar det som finns i Partner Center. Skapa lösen ord baserat på instruktionen i steg 10 (under [Azure Active Directory), härifrån.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Det gick inte att spara leadet i Dynamics CRM. LastCRMError: begär ande kanalen nådde tids gränsen i väntan på ett svar efter 00:02:00. Öka timeout-värdet som skickas till anropet till begäran eller öka SendTimeout-värdet för bindningen. Den tid som tilldelats den här åtgärden kan ha varit en del av en längre tids gräns.**  

> Lösning: Logga in på Partner Center, kontrol lera butik information >>  >> URL för mål, kontrol lera om det är en giltig dynamisk CRM-instans.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vad är leads och varför är de viktiga för mig som utgivare på Marketplace?** 

Leads är kunder som distribuerar dina produkter från Marketplace. Oavsett om produkten är listad på [Azure Marketplace](https://azuremarketplace.microsoft.com) eller [AppSource](https://appsource.microsoft.com/)kommer du att kunna ta emot kunder som är intresserade av produkten om du har konfigurerat lead-målet på ditt erbjudande.  

**Var kan jag få hjälp med att ställa in mitt lead-mål?** 

Du hittar dokumentation på [Hämta kund leads](./partner-center-portal/commercial-marketplace-get-customer-leads.md) eller skicka in ett support ärende till [Hjälp och support](https://partner.microsoft.com/support/v2/?stage=1). Välj erbjudande typ och lead-hantering. 

**Är jag tvungen att konfigurera ett lead-mål för att publicera ett erbjudande på Marketplace?**

Ja, om du publicerar en kontakta mig SaaS-app eller konsult tjänster.  
 
**Hur kan jag bekräfta att lead-konfigurationen är korrekt?**

Publicera ditt erbjudande när du har konfigurerat ditt erbjudande och lead-mål. I steg-för-steg-validering skickar Marketplace ett test lead till det lead-mål som har kon figurer ATS i erbjudandet. 

**Hur kan jag hitta testets lead?**

Sök efter "MSFT_TEST" i ditt lead-mål, här är ett exempel på lead-test data: 

företag = MSFT_TEST_636573304831318844 

land = US 

Beskrivning = MSFT_TEST_636573304831318844 

e-post =MSFT_TEST_636573304831318844@test.com

Encoding = UTF-8 

Encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 | \<Namn på erbjudande> 

OID = 00Do0000000ZHog 

telefon = 1234567890 

title = MSFT_TEST_636573304831318844 

**Jag har ett Live-erbjudande, men jag ser inte några leads?**

Varje lead har data som skickas i fält i ditt valda mål för lead, leads visas i följande format: **käll åtgärd | Erbjudande** 

  *Ursprung*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Action*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Budgivning*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Här är exempel data från kund information*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Ta reda på mer under [lead-information](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Jag har konfigurerat Azure-BLOBBEN som mitt lead-mål, varför ser jag inte mitt lead?** 

Leadet skrivs bara när du väljer Azure BLOB Storage som ditt lead-mål. Växla till Azure-tabellen för att ta emot ledtid i real tid.

**Jag har fått ett e-postmeddelande från Marketplace, varför kan jag inte hitta något lead i min CRM?**  

Det är möjligt att slutanvändarens e-postdomän är från. edu. Av säkerhets skäl skickar vi inte personliga identifierbara data från. edu-domän. Skicka ett support ärende till [Hjälp och support](https://partner.microsoft.com/support/v2/?stage=1).

**Jag har konfigurerat Azure Table/Azure-BLOBBEN som mitt lead-mål, hur kan jag se leads?** 

Du kan komma åt blobben eller tabellen från Azure Portal, eller så kan du ladda ned och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) kostnads fritt om du vill visa ditt Azure Storage-kontos tabeller/blobbar. 

**Jag har konfigurerat Azure-tabellen som mitt lead-mål, kan jag få ett meddelande när ett nytt lead skickas av Marketplace?** 

Ja, följ instruktionerna för att konfigurera Azure Table + Function i dokumentationen [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Jag har konfigurerat Salesforce som mitt lead-mål, varför kan jag inte hitta några leads?** 

Kontrol lera om webb-till-lead-formuläret är ett obligatoriskt fält baserat på en listruta. Om du väljer Ja växlar du över fältet till ett fält som inte är obligatoriskt.  
 
**Det uppstod ett problem med mitt lead-mål och jag missade några leads. Kan jag skicka dem till mig i ett e-postmeddelande?** 

På grund av sekretess policys kan vi inte dela ledar information via oskyddad e-post. 

**Jag har konfigurerat Azure Storage (BLOB/tabell) som mitt lead-mål, hur mycket kostar det?** 

Leadets gen data är låg (<1 GB för nästan alla utgivare). Kostnaden beror på antalet mottagna leads, om 1 000 leads tas emot under en månad, kostar det cirka 50 cent. 
