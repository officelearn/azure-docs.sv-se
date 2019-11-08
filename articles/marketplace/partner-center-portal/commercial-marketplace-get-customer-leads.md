---
title: Konfigurera kund leads | Azure Marketplace
description: Konfigurera kund leads på kommersiella marknads platser.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812659"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Kund leads från Marketplace-erbjudandet

Leads är kunder som är intresserade av eller distribuerar dina erbjudanden från [Azure Marketplace](https://azuremarketplace.microsoft.com) eller från [AppSource](https://appsource.microsoft.com). Du får kund ledare när ditt erbjudande har publicerats på Marketplace. Den här artikeln förklarar följande:

* Hur ditt Marketplace-erbjudande genererar kunder, vilket garanterar att du inte missa affärs möjligheter. 
* Anslut din CRM till ditt erbjudande så att du kan hantera dina leads på en central plats.
* Förstå lead-data vi skickar dig, så att du kan följa upp kunder som har nått dig.

## <a name="generate-customer-leads"></a>Generera kund leads

Här är platser där ett lead genereras:

1. När en kund samtycker att dela sin information efter att ha valt "kontakta mig" från Marketplace. Det här leadet är ett **inledande intresse** som leder till att vi delar information om kunden som har visat intresse för att få din produkt. Leadet ligger överst i förvärvs tratten.

      ![Dynamics 365 kontakta mig](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. När en kund väljer "Hämta nu" eller "skapa" (i [Azure Portal](https://portal.azure.com/)) för att hämta ditt erbjudande, är det här leadet ett **aktivt lead**där vi delar information om en kund som har börjat distribuera produkten.

    ![Hämta nu i SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Skapa Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. En kund tar en "test enhet" eller startar en "kostnads fri utvärdering" av ditt erbjudande. Test enheter eller kostnads fria utvärderings versioner kan påskyndas för att du ska kunna dela din verksamhet direkt med potentiella kunder utan några hinder i registreringen.

    ![Dynamics 365-testenhet](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365-testenhet](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Anslut till ditt CRM-system

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Förstå lead-data

Varje lead som du får under kund förvärvs processen har data i specifika fält. Det första fältet som ska inblickas för är `LeadSource` fältet, som följer det här formatet: **käll åtgärds** | **erbjudande**.

**Källor**: värdet för det här fältet fylls i baserat på marknads platsen som skapade leadet. Möjliga värden är `"AzureMarketplace"`, `"AzurePortal"`och `"AppSource (SPZA)"`.

**Åtgärder**: värdet för det här fältet fylls i baserat på den åtgärd som kunden vidtog i Marketplace, vilket genererade ett lead. 

Möjliga värden:

- "INS"--installation. Den här åtgärden finns på Azure Marketplace eller AppSource när en kund köper din produkt.
- "PLT"--står för partner LED-utvärdering. Den här åtgärden är på AppSource när en kund använder alternativet kontakta mig.
- "DNC"--kontakta inte. Den här åtgärden finns på AppSource när en partner som var korsad på din app-sida uppmanas att kontaktas. Vi delar upp de huvuden som den här kunden hade korsat i din app, men de behöver inte kontaktas.
- "Skapa" – den här åtgärden finns bara i Azure Portal och genereras när en kund köper ditt erbjudande till sitt konto.
- "StartTestDrive" – den här åtgärden gäller endast för test enheter och genereras när en kund startar sin test-enhet.

**Erbjudanden**: du kan ha flera erbjudanden i Marketplace. Värdet för det här fältet fylls i baserat på det erbjudande som genererade leadet. Utgivar-ID och erbjudande-ID skickas både i det här fältet och är värden som du angav när du publicerade erbjudandet till Marketplace.

I följande exempel visas exempel värden i förväntat format `publisherid.offerid`: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Kund information

Kundens information skickas via flera fält. I följande exempel visas kund information som finns i ett lead.

- FirstName: John
- LastName: Smith
- E-post: jsmith\@microsoft.com
- Telefon: 1234567890
- Land: US
- Företag: Microsoft
- Rubrik: CTO

>[!Note]
>Alla data i föregående exempel är alltid tillgängliga för varje lead. Eftersom du får leads från flera steg som nämns i avsnittet kund uppgifter, är det bästa sättet att hantera leads att ta bort posterna och anpassa uppföljningen. På så sätt får varje kund ett lämpligt meddelande och du skapar en unik relation.

## <a name="best-practices-for-lead-management"></a>Metod tips för ledar hantering

1. *Process* – definiera en klar försäljnings process, med mil stolpar, KPI: er och ta bort teamets ägarskap.
2. *Kvalificering* – definiera förutsättningar, som anger om ett lead har blivit fullständigt kvalificerat. Se till att Sälj-eller marknadsförings representanter kvalificerar leads noggrant innan du tar dem genom hela försäljnings processen.
3. *Följ* upp – Glöm inte att följa upp, vänta en typisk transaktion att kräva 5 till 12 uppföljnings anrop
4. *Nurture* – Nurture dina leads, så att du kan komma på väg till en högre vinst marginal.

## <a name="leads-frequently-asked-questions"></a>Vanliga frågor och svar om leads

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Var kan jag få hjälp med att ställa in mitt lead-mål?

Du hittar dokumentation [här](#connect-to-your-crm-system) eller skickar ett support ärende via aka.MS/marketplacepublishersupport och väljer sedan **"erbjudande skapa"** → **din typ av erbjudande** → **"konfiguration av lead-hantering".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Är jag tvungen att konfigurera ett lead-mål för att publicera ett erbjudande på Marketplace?

Svaret beror på vilken typ av erbjudande du publicerar. SaaS och Dynamics 365 för kund engagemang ger en lista som "kontakta mig", alla Dynamics 365 for Operations erbjuder, alla Dynamics 365 Business Central-erbjudanden och alla konsult tjänst erbjudanden kräver en anslutning till ett lead-mål. Om erbjudande typen inte finns med i listan, krävs det inte. Vi rekommenderar dock att du konfigurerar ett lead-mål så att du inte missa affärs möjligheter.

### <a name="how-can-i-find-the-test-lead"></a>Hur kan jag hitta testets lead?

Sök efter `"MSFT_TEST"` i ditt lead-mål, här är ett exempel på ett test från Microsoft:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Jag har ett Live-erbjudande, men jag ser inte några leads?

Kontrol lera att din anslutning till målet för målet är giltig. Vi kommer att skicka ett test lead när du har publicerat det på ditt erbjudande i Partner Center. Om du ser testets lead är anslutningen giltig. Du kan också testa din lead-anslutning genom att försöka hämta för hands versionen av erbjudandet under för hands versions steget genom att klicka på "Hämta nu", "kontakta mig" eller "kostnads fri utvärdering" på listan på Marketplace.

Kontrol lera också att du letar efter rätt data. Innehållet i avsnittet [förstå lead-data](#understand-lead-data) i det här dokumentet beskriver de lead-data vi skickar till ditt lead-mål.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Jag har konfigurerat Azure-BLOBBEN som mitt lead-mål, varför ser jag inte mitt lead?

Det finns inte längre stöd för Azure Blob-leadadressen så att du saknar kund ledare som genereras av ditt erbjudande. Växla till något av de andra [mål alternativen för lead](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Jag har fått ett e-postmeddelande från Marketplace, varför kan jag inte hitta något lead i min CRM?

Det är möjligt att slutanvändarens e-postdomän är från. edu. Av säkerhets skäl skickar vi inte PII-data från. edu-domän. Skicka in ett support ärende via aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Jag har konfigurerat Azure-tabellen som mitt lead-mål, hur kan jag se leads?

Du kan komma åt lead-data som lagras i Azure-tabellen från Azure-portalen, eller så kan du ladda ned och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) kostnads fritt för att visa dina Azure Storage-kontos tabell data.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Jag har konfigurerat Azure-tabellen som mitt lead-mål, kan jag få ett meddelande när ett nytt lead skickas av Marketplace?

Ja, följ instruktionerna för att konfigurera ett Microsoft Flow som skickar ett e-postmeddelande om ett lead läggs till i Azure-tabellen i dokumentationen [här](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Jag har konfigurerat Salesforce som mitt lead-mål, varför kan jag inte hitta några leads?

Kontrol lera om formuläret "webb till lead" är ett obligatoriskt fält baserat på en listruta. Om du väljer Ja växlar du över fältet till ett fält som inte är obligatoriskt.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Det uppstod ett problem med mitt lead-mål och jag missade några leads. Kan jag skicka dem till mig i ett e-postmeddelande?

På grund av PII-principer (Private identifierbar information) kan vi inte dela ledar information via oskyddad e-post.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Jag har konfigurerat Azure-tabellen som mitt lead-mål, hur mycket kostar det?

Leadets gen data är låg (< 1 GB för nästan alla utgivare). Kostnaden beror på antalet mottagna leads, om 1 000 leads tas emot under en månad, kostar det cirka 50 cent. Mer information om lagrings priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

Om din fråga fortfarande inte besvaras kan du kontakta supporten via aka.ms/marketplacepublishersupport och sedan välja **"erbjudande skapa"** → **din typ av erbjudande** → **konfiguration av lead-hantering.** 

## <a name="next-steps"></a>Nästa steg

När den tekniska konfigureringen är på plats bör du inkludera dessa leads i din aktuella försäljning & marknadsförings strategi och operativa processer. Vi är intresserade av att bättre förstå den övergripande Sälj processen och vill arbeta nära dig med att tillhandahålla högkvalitativa leads och tillräckligt med data för att lyckas. Vi välkomnar din feedback om hur vi kan optimera och förbättra de leads som vi skickar till dig med ytterligare data för att hjälpa kunderna att lyckas. Berätta för oss om du är intresse rad av att [ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag för att göra det möjligt för ditt säljteam att bli mer framgångs rik med marknads ledande kunder.
