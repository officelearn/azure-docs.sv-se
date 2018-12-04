---
title: Lead-hantering för marknadsplats i molnet | Azure Marketplace och AppSource
description: En översikt över olika ämnen som rör publicering av erbjudanden och tekniska artefakter till Azure Marketplace och AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: Marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 8bb964308e11d238def4eed3f194ffc9ef8ab086
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840171"
---
# <a name="lead-management-for-cloud-marketplace"></a>Lead-hantering för marknadsplats i molnet


Kunder kan i mitten av alla bra verksamheter. I omvandlingen av dagens produkten förvärv måste marknadsförare att fokusera på direkt kontakt med kunder och skapa en relation. Det är därför generera leads för hög kvalitet är ett viktigt verktyg för din. Efter att visa en lista över ditt erbjudande i den [Cloud Partner Portal](https://cloudpartner.azure.com/), det finns verktyg är aktiverade att programmässigt ta emot kundkontaktinformation omedelbart efter det att en kund uttrycker intresse eller distribuerar din produkt i den Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Vad är leads i marketplace?

Den potentiella kunden kommer från kunder som vill eller distribuera dina produkter från Marketplace. Om din produkt finns på Azure Marketplace eller AppSource kan ska du kunna ta emot leads från kunder när den har ställts in korrekt från dina CRM till din lista (listor) i partnerportalen i molnet 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Hur du ansluter din CRM-system med partnerportalen i molnet

Om du vill börja få leads, är Lead Management-anslutningstjänsten på partnerportalen i molnet utformad så att den kan enkelt kopplas in med dina CRM-uppgifter till en lista i CRM-system som är tillgängliga. Du kan nu enkelt utnyttja leads som skapades av marketplace utan en betydande engineering ansträngning att integrera med ett externt system.

Här följer stegvisa instruktioner om hur du ansluter var och en av de möjliga lead-mål:

**Dynamics CRM Online** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) att hämta anvisningarna om hur du konfigurerar Dynamics CRM Online för att få leads.

**Marketo** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) här för instruktioner för att konfigurera Marketo leda konfiguration för att få leads.

**Salesforce** - [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) här för instruktioner för att konfigurera din Salesforce-instans för att få leads.

**Azure-tabell** – [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) här för instruktioner för hur du konfigurerar ditt Azure storage-konto för att få leads i en Azure-tabell.

**Https-slutpunkt** – [Klicka här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) här för instruktioner för att konfigurera Https-slutpunkt för att få leads.

När du har konfigurerat ditt mål för lead korrekt och har nått publicera på ditt erbjudande kan vi verifiera anslutningen och skickar dig ett test-lead. Du kan också testa din lead-anslutning genom att hämta erbjudandet själv i förhandsversionsmiljön när du visar erbjudandet innan du går vidare live. Det är viktigt att se till att dina lead inställningar Håll dig uppdaterad så att du inte förlorar alla leads, så se till att du uppdatera dessa anslutningar när något har ändrats från din sida.


### <a name="what-are-the-next-steps"></a>Vad är nästa steg?

När de tekniska konfigurera är på plats, ska du införliva dessa leads i din aktuella Försäljning & marknadsföringsstrategi och driftrutiner. Vi vill att bättre förstå din övergripande försäljningsprocess och vill samarbeta med dig på att tillhandahålla hög kvalitet leads och tillräckligt med data för att du ska lyckas. Vi uppskattar din feedback på hur vi kan optimera och förbättra leads som vi skickar dig med ytterligare data för att göra dessa kunder lyckas. Berätta för oss om du är intresserad av att tillhandahålla feedback och förslag för att aktivera försäljningsteamet ska lyckas med Marketplace-Leads.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Vanliga lead-konfigurationsfel vid publicering på partnerportalen i molnet 

**Det gick inte att spara ditt lead till Dynamics CRM. Kontrollera inställningarna för Dynamics CRM-konto. LastCRMError: Det gick inte att logga in på Dynamics CRM, LastCRMException:** 

> Om O365-autentisering har valts, kontrollerar du om användarkontot och lösenordet är giltigt. Om AAD har valts, kontrollerar du om klient-ID, program-ID och programmet hemlig nyckel matchar vad konfigurerades på AAD. Följ instruktionerna [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Om kontot användarnamn/lösenord är giltigt, kontrollera att den har åtkomst till Dynamics 365 och tilldelas en licens (steg 11-15 om du använder Azure Active Directory eller säkerhetsinställningar om du använder en Office-användare). 

 
**Det gick inte att spara ditt lead till Dynamics CRM. Användaren har inte skapa behörigheter för attributet leadsourcecode i entiteten lead** 

> Program/användaren saknar security roll(er) till Microsoft Marketplace lead writer. Följ steg 11-15 om du använder Azure Active Directory eller säkerhetsinställningar om du använder en Office-användare [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Gick inte att spara ditt lead i Dynamics CRM med AAD. Undantag:: Innehavaren hittades inte. Den här instansen kan inträffa om det finns inga aktiva prenumerationer för klienten.**  

> Katalog-Id som anges i avsnittet om lead-hantering är inte en giltig katalog. Skaffa katalog-Id som baseras på anvisningarna i steg 2 (under Azure Active Directory, från [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Det gick inte att spara ditt lead till Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser misslyckades – inga roller har tilldelats till användaren.**  

> Lösning: Tilldela säkerhetsrollen till lead-skrivaren för Microsoft Marketplace. Följ instruktionerna [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) under säkerhetsinställningar 

**Gick inte att spara ditt lead i Dynamics CRM med AAD. Undantag:: Programmet med ID: t hittades inte i katalogen** 

> Program-Id som anges i avsnittet om lead-hantering är inte en giltig katalog. Skaffa katalog-Id som baseras på anvisningarna i steg 8 (under Azure Active Directory, från [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Gick inte att spara ditt lead i Dynamics CRM med AAD. Undantag:: Begärt klient-ID är inte giltigt och inte giltig extern domän format** 

> Katalog-Id som anges i avsnittet om lead-hantering är inte en giltig katalog. Skaffa katalog-Id som baseras på anvisningarna i steg 2 (under Azure Active Directory, från [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Gick inte att spara ditt lead i Dynamics CRM med AAD. Undantag: Fel vid validering av autentiseringsuppgifter.: Ogiltig klienthemlighet har angetts.** 

> Lösning: Logga in på Azure-portalen, kontrollerar du om programnyckeln matchar vad som finns i partnerportalen i molnet. Generera lösenord baserat på instruktionen på steg 10 (under Azure Active Directory), från [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Det gick inte att spara ditt lead till Dynamics CRM. LastCRMError: Begärandekanalen nådde sin tidsgräns medan du väntar på svar efter 00:02:00. Öka timeout-värdet skickas till anropet på begäran eller öka värdet SendTimeout på bindningen. Den tid som tilldelats till den här åtgärden kan ha varit en del av en längre tidsgräns.**  

> Lösning: Inloggningen till Cloud Partner Portal klientkontrollen Storefront >> Lead mål >> URL, kontrollera om det är en giltig dynamisk CRM-instans

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vilka leads och varför de viktiga för mig som utgivare på Marketplace?** 

Leads är kunder som distribuerar dina produkter från Marketplace. Om din produkt visas på [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) eller [AppSource](https://appsource.microsoft.com/), du kommer att kunna ta emot leads kunder som är intresserade av din produkt om du har konfigurerat lead-målet på ditt erbjudande.  


**Var kan jag få hjälp att konfigurera min lead-mål?** 

Du hittar dokumentationen här: [hämta kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) eller skicka in ett supportärende via aka.ms/marketplacepublishersupport Välj erbjudandetypen och lead-hantering. 



**Måste jag konfigurera ett lead-mål för att publicera ett erbjudande på Marketplace?**

Ja, om du publicerar en kontakta mig SaaS-app eller -konsulttjänster.  
 


**Hur kan jag bekräftar att lead-konfigurationen är korrekt?**

När du skapat ett erbjudande och lead mål kan du publicera ditt erbjudande. Lead-verifiering steget skickar Marketplace ett test-lead till lead-mål som konfigurerats i ditt erbjudande. 


**Hur hittar jag test lead?**


Sök efter ”MSFT_TEST” i ditt lead-mål, här är en lead testdata exemplet: 

företagets = MSFT_TEST_636573304831318844 

land = USA 

Beskrivning = MSFT_TEST_636573304831318844 

e-post = MSFT_TEST_636573304831318844@test.com

kodning = UTF-8 

kodning = UTF-8 

Förnamn = MSFT_TEST_636573304831318844 

Efternamn = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844 MSFT_TEST_636573304831318844 |<Offer Name> 

OID = 00Do0000000ZHog 

Phone = 1234567890 

Rubrik = MSFT_TEST_636573304831318844 

 

**Jag har ett levande erbjudande, men jag ser inte alla leads?**

Varje lead har data som skickas i fälten i ditt valda lead-mål, den potentiella kunden kommer i följande format: **åtgärd för källa | Erbjudande** 

  *Källor:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Åtgärder:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Erbjuder:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Här är exempeldata för kundens information*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Läs mer under [leda Info](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Jag har konfigurerat Azure BLOB som min lead-mål, varför ser jag ditt lead?** 

Ditt lead skrivs bara när du väljer Azure BLOB storage som mål lead. Växla till Azure-tabell att ta emot realtid lead 


**Jag har fått ett e-postmeddelande från Marketplace, varför inte kan jag hitta ditt lead i min CRM?**  

Det är möjligt att slutanvändarens e-postdomän är från .edu. Av sekretesskäl inte vi överföra PII-data från .edu domän. Skicka in ett supportärende via aka.ms/marketplacepublishersupport 


 **Jag har konfigurerat Azure tabell-/ Azure BLOB som min lead-mål, hur kan jag se de potentiella kunderna?** 

Du kan komma åt blob eller table från Azure Portal, eller du kan ladda ned och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) kostnadsfritt för att visa ditt Azure storage-konto tabeller/blobbar. 


**Jag har konfigurerat Azure Table som min lead-mål, kan jag få ett meddelande när ett nytt lead skickas av Marketplace?** 

Ja, följ instruktionerna för att lägga upp Azure Table + funktion om dokumentationen [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Jag har konfigurerat Salesforce som min lead-mål, varför inte kan jag hitta den potentiella kunden?** 

Kontrollera om webben till leda formulär är ett obligatoriskt fält baserat på en listruta. Om Ja, kan du växla över fältet till ett icke-obligatorisk textfält.  
 

**Det uppstod ett problem med min lead-mål och jag har missat del leads. Kan jag ha dem i ett e-postmeddelande har skickats till mig?** 

Vi kan inte dela lead-information via oskyddat e-post på grund av personligt identifierbar information (privat identifierbar Information) principer. 



**Jag har konfigurerat Azure Storage (BLOB/Table) som min lead-mål, hur mycket kostar det?** 

Lead gen data är låg (< 1 GB för nästan alla utgivare). Kostnaderna beror på antalet leads som tagits emot, om 1 000 leads tas emot i en månad, det kostar cirka 50 cent. 
