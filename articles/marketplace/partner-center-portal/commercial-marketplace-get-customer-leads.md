---
title: Konfigurera kundleads | Azure Marketplace
description: Konfigurera kundleads på kommersiell marknadsplats.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 05b166b2ec46900d9e3972025efb581d9619ec6a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252674"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Kundleads från ditt Marketplace-erbjudande

Leads är kunder som är intresserade av eller distribuerar dina erbjudanden från [Azure Marketplace](https://azuremarketplace.microsoft.com) eller från [AppSource](https://appsource.microsoft.com). Du kommer att få kundleads när ditt erbjudande har publicerats på marknaden. Denna artikel kommer att förklara:

* Hur ditt marknadsplatserbjudande genererar kunder leads, se till att du inte missar affärsmöjligheter. 
* Anslut din CRM till ditt erbjudande, så att du kan hantera dina leads på en central plats.
* Förstå de leaddata vi skickar till dig, så att du kan följa upp kunder som har kontaktat dig.

## <a name="generate-customer-leads"></a>Generera kundleads

Här är platser där ett lead genereras:

1. När en kund samtycker till att dela sin information efter att ha valt "Kontakta mig" från marknadsplatsen. Detta lead är en **initial intresse** lead, där vi delar information om kunden som har uttryckt intresse för att få din produkt. Ledningen är toppen av förvärvet tratten.

      ![Dynamics 365 Kontakta mig](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. När en kund väljer "Hämta det nu" eller "Skapa" (i [Azure-portalen)](https://portal.azure.com/)för att få ditt erbjudande, är det här leadet ett **aktivt lead**, där vi delar information om en kund som har börjat distribuera din produkt.

    ![SQL Hämta det nu](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Skapa Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. En kund tar en "Test Drive" eller startar en "gratis testperiod" av ditt erbjudande. Provkörningar eller kostnadsfria utvärderingsversioner är snabbare möjligheter för dig att dela ditt företag direkt med potentiella kunder utan några inträdeshinder.

    ![Dynamics 365 provkörning](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 provkörning](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Anslut till CRM-systemet

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Förstå leaddata

Varje lead som du får under kundinhämtningsprocessen har data i specifika fält. Det första fältet som ska `LeadSource` hålla utkik efter är fältet, som följer det här formatet: **Source-Action** | **Offer**.

**Källor**: Värdet för det här fältet fylls i baserat på den marknadsplats som genererade leadet. Möjliga värden `"AzureMarketplace"` `"AzurePortal"`är `"AppSource (SPZA)"`, och .

**Åtgärder**: Värdet för det här fältet fylls i baserat på den åtgärd som kunden vidtog på marknadsplatsen, vilket genererade leadet. 

Möjliga värden:

- "INS" - Installation. Den här åtgärden finns på Azure Marketplace eller AppSource när en kund köper din produkt.
- "PLT" - Står för Partner Led Trial. Den här åtgärden finns på AppSource när en kund använder alternativet Kontakta mig.
- "DNC" - Kontakta inte. Den här åtgärden finns på AppSource när en partner som korslistades på din appsida uppmanas att kontaktas. Vi delar förvarningen att den här kunden har korslistats i din app, men de behöver inte kontaktas.
- "Skapa" – Den här åtgärden finns bara i Azure-portalen och genereras när en kund köper ditt erbjudande till sitt konto.
- "StartTestDrive" – Den här åtgärden är endast till för testenheter och genereras när en kund startar sin provkörning.

**Erbjudanden:** Du kan ha flera erbjudanden på marknaden. Värdet för det här fältet fylls i baserat på erbjudandet som genererade leadet. Publisher-ID och Erbjudande-ID skickas båda i det här fältet och är värden som du angav när du publicerade erbjudandet på marknadsplatsen.

I följande exempel visas exempelvärden `publisherid.offerid`i förväntat format: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Information om kunder

Kundens information skickas via flera fält. I följande exempel visas kundinformationen som finns i ett lead.

- Förnamn: John
- Efternamn: Svensson
- E-post:\@jsmith microsoft.com
- Telefon: 1234567890
- Land: USA
- Företag: Microsoft
- Titel: CTO

>[!Note]
>Alla data i föregående exempel är inte alltid tillgängliga för varje lead. Eftersom du får leads från flera steg som nämns i avsnittet Kundleads är det bästa sättet att hantera leadsen att avd duplicera posterna och anpassa uppföljningarna. På så sätt får varje kund ett lämpligt meddelande och du skapar en unik relation.

## <a name="best-practices-for-lead-management"></a>Metodtips för leadhantering

1. *Process* - Definiera en tydlig försäljningsprocess med milstolpar, KPI:er och tydlig teamägande.
2. *Kvalificering* - Definiera förutsättningar, som anger om ett lead har kvalificerats fullt ut. Se till att försäljnings- eller marknadsföringsrepresentanter kvalificerar leads noggrant innan du tar dem genom hela försäljningsprocessen.
3. *Uppföljning* - Glöm inte att följa upp, förvänta dig den typiska transaktionen att kräva 5 till 12 uppföljning samtal
4. *Vårda* - Vårda dina leder, för att få dig på väg till en högre vinstmarginal.

## <a name="leads-frequently-asked-questions"></a>Vanliga frågor och svar leder till vanliga frågor

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Var kan jag få hjälp med att konfigurera mitt leadmål?

Du kan hitta dokumentation [här](#connect-to-your-crm-system) eller skicka in en supportbiljett via aka.ms/marketplacepublishersupport sedan välja **"erbjudandeskapande"** → **din typ av erbjudande** → **"lead management configuration".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Måste jag konfigurera en leaddestination för att kunna publicera ett erbjudande på marknadsplatsen?

Svaret beror på vilken typ av erbjudande du publicerar. SaaS och Dynamics 365 for Customer Engagement erbjuder sig att lista som "Kontakta mig", alla Dynamics 365 for Operations-erbjudanden, alla Dynamics 365 Business Central-erbjudanden och alla erbjudanden om konsulttjänster kräver en anslutning till en leaddestination. Om din erbjudandetyp inte fanns med i listan krävs det inte. Vi rekommenderar dock att du konfigurerar en leaddestination så att du inte missar affärsmöjligheter.

### <a name="how-can-i-find-the-test-lead"></a>Hur hittar jag testledningen?

Sök `"MSFT_TEST"` efter i din leaddestination, här är ett exempel test lead från Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Jag har ett liveerbjudande, men jag ser inga ledtrådar?

Kontrollera att anslutningen till leaddestinationen är giltig. Vi skickar dig en testledare efter att du har publicerat publicera på ditt erbjudande i Partner Center. Om testkabeln visas är anslutningen giltig. Du kan också testa din leadanslutning genom att försöka skaffa förhandsgranskningen under förhandsgranskningssteget genom att klicka på "get it now", "contacts me" eller "free trial" på listan på marknaden.

Se också till att du letar efter rätt data. Innehållet i avsnittet [Förstå leaddata](#understand-lead-data) i det här dokumentet beskriver de leaddata vi skickar till ditt leadmål.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Jag har konfigurerat Azure BLOB som min leaddestination, varför ser jag inte leadet?

Azure Blob-lead-målet stöds inte längre så du saknar alla kundleads som genereras av ditt erbjudande. Växla till något av de andra [leadmålalternativen](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Jag fick ett e-postmeddelande från Marketplace, varför kan jag inte hitta leadet i min CRM?

Det är möjligt att slutanvändarens e-postdomän kommer från .edu. Av sekretessskäl skickar vi inte privat identifierbar information från .edu-domänen. Skicka in en supportbiljett via aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Jag har konfigurerat Azure Table som min leaddestination, hur kan jag visa leads?

Du kan komma åt leaddata som lagras i Azure-tabellen från Azure-portalen, eller så kan du hämta och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratis för att visa dina Azure-lagringskontos tabelldata.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Jag har konfigurerat Azure Table som min leaddestination, kan jag få ett meddelande när ett nytt lead skickas av Marketplace?

Ja, följ instruktionerna för att konfigurera ett Microsoft-flöde som skickar ett e-postmeddelande om ett lead läggs till i Azure-tabellen i dokumentationen [här](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Jag har konfigurerat Salesforce som min huvuddestination, varför kan jag inte hitta leads?

Kontrollera om formuläret "web to lead" är ett obligatoriskt fält baserat på en plocklista. Om ja växlar du över fältet till ett icke-obligatoriskt textfält.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Det var ett problem med min ledande destination, och jag missade några leder. Kan jag få dem skickade till mig i ett e-postmeddelande?

På grund av privata identifierbara informationspolicyer kan vi inte dela leadinformation via oskyddad e-post.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Jag har konfigurerat Azure Table som min leaddestination, hur mycket kostar det?

Lead gen-data är låg (<1 GB för nästan alla utgivare). Kostnaden beror på antalet mottagna leads, om 1000 leads tas emot i en månad, kostar det runt 50 cent. Mer information om lagringspriser finns i [lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

Om din fråga fortfarande inte besvaras kontaktar du support via aka.ms/marketplacepublishersupport och väljer sedan **"skapa erbjudanden"** → **din typ av erbjudande** → **"lead management configuration".** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Jag får e-postmeddelanden när nya kundleads tas emot. Hur kan jag konfigurera vem som ska ta emot dessa e-postmeddelanden?

Öppna ditt erbjudande i Partner center och navigera till **inställningssidan för Erbjudande** -> **Lead Management** -> **Edit**. Uppdatera e-postadresserna under fältet **Kontakta e-post.**

## <a name="next-steps"></a>Nästa steg

När den tekniska uppsättningen är på plats, bör du införliva dessa leder i din nuvarande försäljning & marknadsföringsstrategi och operativa processer. Vi är intresserade av att bättre förstå din övergripande försäljningsprocess och vill arbeta nära dig på att ge högkvalitativa leads och tillräckligt med data för att göra dig framgångsrik. Vi välkomnar din feedback på hur vi kan optimera och förbättra de leads vi skickar dig med ytterligare data för att hjälpa till att göra dessa kunder framgångsrika. Meddela oss om du är intresserad av [att ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag så att ditt säljteam kan bli mer framgångsrikt med Marketplace Leads.
