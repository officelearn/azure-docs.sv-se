---
title: Leadhantering för molnmarknadsplats | Azure Marketplace och AppSource
description: En översikt över olika ämnen relaterade till publiceringserbjudanden och tekniska artefakter till Azure Marketplace och AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: dsindona
ms.openlocfilehash: 94510d02a28e0364f1c715dbcf9ff641fe2b14fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286140"
---
# <a name="lead-management-for-cloud-marketplace"></a>Leadhantering för marknadsplatsen i molnet


Kunderna är centrum för alla bra affärer. I omvandlingen av dagens produktförvärv måste marknadsförare fokusera på att få kontakt med kunder direkt och bygga upp en relation. Det är därför generera högkvalitativa leads är ett viktigt verktyg för din säljcykel. När du har listat ditt erbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/)finns det verktyg som är aktiverade för att programmässigt ta emot kundkontaktinformation omedelbart efter att en kund uttrycker intresse eller distribuerar din produkt på marknaden. 



## <a name="what-are-leads-in-the-marketplace"></a>Vad är leads på marknaden?

Leadsen kommer från kunder som är intresserade eller distribuerar dina produkter från Marketplace. Oavsett om din produkt finns med på Azure Marketplace eller AppSource kan du ta emot leads från kunder när den har konfigurerats korrekt från crm till dina uppgifter i Cloud Partner Portal 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Så här ansluter du ditt CRM-system till molnpartnerportalen

För att börja få leads är Lead Management-kontakten på Cloud Partner Portal utformad så att den enkelt kan anslutas med crm-informationen till en lista över tillgängliga CRM-system. Nu kan du enkelt utnyttja de leads som genereras av marknaden utan en betydande teknisk ansträngning att integrera med ett externt system.

Här är steg-för-steg-instruktioner om hur du ansluter var och en av de möjliga leaddestinationerna:

**Dynamics CRM Online** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) för att få instruktioner om hur du konfigurerar Dynamics CRM Online för att få leads.

**Marketo** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) för att få instruktioner för att ställa in Marketo Lead Configuration för att få leads.

**Salesforce** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) för att få instruktioner för hur du konfigurerar din Salesforce-instans för att få leads.

**Azure Table** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) för att få instruktioner för hur du konfigurerar ditt Azure-lagringskonto för att få leads i en Azure-tabell.

**Https Slutpunkt** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) för att få instruktioner för hur du konfigurerar din Https-slutpunkt för att få leads.

När du har konfigurerat din leaddestination ordentligt och har träffat Publicera på ditt erbjudande validerar vi anslutningen och skickar en testled. När du visar erbjudandet innan du sänder live kan du också testa din leadanslutning genom att försöka skaffa erbjudandet själv i förhandsversionen. Det är viktigt att se till att dina leadinställningar förblir uppdaterade så att du inte förlorar några leads, så se till att uppdatera dessa anslutningar när något har ändrats på din sida.


### <a name="what-are-the-next-steps"></a>Vilka är nästa steg?

När den tekniska uppsättningen är på plats, bör du införliva dessa leder i din nuvarande försäljning & marknadsföringsstrategi och operativa processer. Vi är intresserade av att bättre förstå din övergripande försäljningsprocess och vill arbeta nära dig på att ge högkvalitativa leads och tillräckligt med data för att göra dig framgångsrik. Vi välkomnar din feedback på hur vi kan optimera och förbättra de leads vi skickar dig med ytterligare data för att hjälpa till att göra dessa kunder framgångsrika. Meddela oss om du är intresserad av att ge feedback och förslag så att ditt säljteam kan bli mer framgångsrikt med Marketplace Leads.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Vanliga leadkonfigurationsfel under publicering på molnpartnerportalen 

**Det gick inte att spara leadet i Dynamics CRM. Kontrollera inställningarna för Dynamics CRM-konto. LastCRMError: Det går inte att logga in på Dynamics CRM, LastCRMException:** 

> Om O365-autentisering har valts kontrollerar du om användarkontot och lösenordet är giltiga. Om AAD har valts kontrollerar du om klient-ID, program-ID och programhemlighetsnyckel matchar vad som har ställts in på AAD. Följ instruktionerna [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Om kontots användarnamn/lösenord är giltigt kontrollerar du att det har åtkomst till Dynamics 365 och har en licens tilldelad (steg 11-15 om du använder Azure Active Directory eller Säkerhetsinställningar om du använder en Office-användare). 

 
**Det gick inte att spara leadet i Dynamics CRM. Användaren har inte skapa behörigheter för leadsourcecode-attributet i leadentiteten** 

> Programmet/användaren saknar säkerhetsroller till Microsoft Marketplace lead writer. Följ steg 11-15 om du använder Azure Active Directory eller Säkerhetsinställningar om du använder en Office-användare [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Det gick inte att spara leadet till Dynamics CRM med AAD. Undantag:: Klienten hittades inte. Den här instansen kan inträffa om det inte finns några aktiva prenumerationer för klienten.**  

> Katalog-ID:et som anges i leadhanteringsavsnittet är inte en giltig katalog. Vänligen hämta katalog-ID baserat på instruktionerna i steg 2 (under Azure Active Directory, [härifrån](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Det gick inte att spara leadet i Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser misslyckades - inga roller tilldelas användaren.**  

> Lösning: Tilldela säkerhetsroll till Microsoft Marketplace-leadförfattare. Följ instruktionerna [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) under Säkerhetsinställningar 

**Det gick inte att spara leadet till Dynamics CRM med AAD. Undantag:: Det gick inte att hitta ett program med identifierare i katalogen** 

> Program-ID:et som anges i leadhanteringsavsnittet är inte en giltig katalog. Vänligen hämta katalog-ID baserat på instruktionerna i steg 8 (under Azure Active Directory, [härifrån).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Det gick inte att spara leadet till Dynamics CRM med AAD. Undantag:: Begärd klientidentifierare är ogiltig och ogiltigt externt domänformat** 

> Katalog-ID:et som anges i leadhanteringsavsnittet är inte en giltig katalog. Vänligen hämta katalog-ID baserat på instruktionerna i steg 2 (under Azure Active Directory, [härifrån](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Det gick inte att spara leadet till Dynamics CRM med AAD. Undantag:: Fel uppstod när autentiseringsuppgifterna skulle valideras.: Ogiltig klienthemlighet tillhandahålls.** 

> Lösning: Logga in på Azure Portal, kontrollera om programnyckeln matchar vad som finns i Cloud Partner Portal. Skapa lösenord baserat på instruktionen i steg 10 (under Azure Active Directory), [härifrån](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Det gick inte att spara leadet i Dynamics CRM. LastCRMError: Kanalen för begäran tidsinställdes i väntan på ett svar efter 00:02:00. Öka timeout-värdet som skickas till anropet till Begär eller öka SendTimeout-värdet på bindningen. Den tid som avsatts för den här åtgärden kan ha varit en del av en längre timeout.**  

> Lösning: Logga in på Cloud Partner Portal, kontrollera Information om storefront >> lead-mål >> URL, kontrollera om det är en giltig Dynamisk CRM-instans

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vad är leads och varför är de viktiga för mig som utgivare på Marketplace?** 

Leads är kunder som distribuerar dina produkter från Marketplace. Oavsett om din produkt finns med på [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) eller [AppSource](https://appsource.microsoft.com/)kan du ta emot leads för kunder som är intresserade av din produkt om du har konfigurerat leadmålet på ditt erbjudande.  


**Var kan jag få hjälp med att konfigurera mitt leadmål?** 

Du hittar dokumentation här: [Få kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) eller skicka in en supportbiljett via aka.ms/marketplacepublishersupport välj erbjudandetyp och leadhantering. 



**Måste jag konfigurera en leaddestination för att kunna publicera ett erbjudande på Marketplace?**

Ja, om du publicerar en Kontakta Mig SaaS-app eller Konsulttjänster.  
 


**Hur kan jag bekräfta att leadkonfigurationen är korrekt?**

När du har konfigurerat erbjudandet och leaddestinationen publicerar du erbjudandet. I steget leadvalidering skickar Marketplace ett testleads till leadmålet som konfigurerats i erbjudandet. 


**Hur hittar jag testledningen?**


Sök efter "MSFT_TEST" i din leaddestination, här är ett exempel test bly data: 

företag = MSFT_TEST_636573304831318844 

land = USA 

beskrivning = MSFT_TEST_636573304831318844 

e-post =MSFT_TEST_636573304831318844@test.com

kodning = UTF-8 

kodning = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844| \<Namn på erbjudande> 

oid = 00Do000000ZHog 

telefon = 1234567890 

titel = MSFT_TEST_636573304831318844 

 

**Jag har ett liveerbjudande, men jag ser inga ledtrådar?**

Varje lead kommer att ha data som skickas i fält i ditt valda leadmål, leads kommer i detta format: **Source-Action| Erbjudande** 

  *Källor:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Åtgärder:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Erbjuder:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Här är exempeldata för kundinformationen*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Läs mer under [Lead Info](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Jag har konfigurerat Azure BLOB som min leaddestination, varför ser jag inte leadet?** 

Leadet skrivs bara när du väljer Azure BLOB-lagring som huvuddestination. Växla till Azure-tabellen för att få leadet i realtid 


**Jag fick ett e-postmeddelande från Marketplace, varför kan jag inte hitta leadet i min CRM?**  

Det är möjligt att slutanvändarens e-postdomän kommer från .edu. Av sekretesskäl skickar vi inte PII-data från .edu-domänen. Skicka in en supportbiljett via aka.ms/marketplacepublishersupport 


 **Jag har konfigurerat Azure Table/Azure BLOB som min huvudmål, hur kan jag visa leads?** 

Du kan komma åt blob eller tabellen från Azure Portal, eller så kan du ladda ned och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratis för att visa dina Azure-lagringskontos tabeller/blobbar. 


**Jag har konfigurerat Azure Table som min leaddestination, kan jag få ett meddelande när ett nytt lead skickas av Marketplace?** 

Ja, följ instruktionerna för att konfigurera Azure Table + Function på dokumentationen [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Jag har konfigurerat Salesforce som min huvuddestination, varför kan jag inte hitta leads?** 

Kontrollera om webbformen för att leda formuläret är ett obligatoriskt fält baserat på en plocklista. Om ja växlar du över fältet till ett icke-obligatoriskt textfält.  
 

**Det var ett problem med min ledande destination, och jag missade några leder. Kan jag få dem skickade till mig i ett e-postmeddelande?** 

På grund av PII-policyer (Private Identifiee Information) kan vi inte dela leadinformation via oskyddad e-post. 



**Jag har konfigurerat Azure Storage (BLOB/Table) som min leaddestination, hur mycket kostar det?** 

Lead gen-data är låg (<1 GB för nästan alla utgivare). Kostnaden beror på antalet mottagna leads, om 1000 leads tas emot i en månad, kostar det runt 50 cent. 
