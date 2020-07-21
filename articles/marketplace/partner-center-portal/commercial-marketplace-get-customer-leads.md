---
title: Hantering av leads från Microsofts kommersiella marknads platser
description: Lär dig mer om att skapa och ta emot kund leads från din Microsoft AppSource och Azure Marketplace-erbjudanden
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 9ee433f226b37c8ffd6ad466cca7cbd844d53524
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535985"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Kunden får ett erbjudande från ditt kommersiella Marketplace

Leads är kunder som är intresserade av eller distribuerar dina erbjudanden från [Microsoft AppSource](https://appsource.microsoft.com) och [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan ta emot kund leads när ditt erbjudande har publicerats till den kommersiella marknads platsen. I den här artikeln förklaras följande koncept för lead-hantering:

* Hur ditt kommersiella Marketplace-erbjudande genererar kund leads för att se till att du inte kan missa affärs möjligheter. 
* Så här ansluter du ditt CRM-system (Customer Relations hip Management) till ditt erbjudande så att du kan hantera dina leads på en central plats.
* Lead-data vi skickar dig så att du kan följa upp kunder som har nått dig.

## <a name="generate-customer-leads"></a>Generera kund leads

Här är platser där ett lead genereras:

- En kund samtycker till att dela sin information när de har valt **kontakta mig** från den kommersiella Marketplace. Det här leadet är ett *initialt intresse* lead. Vi delar information med dig om kunden som har visat intresse för att få din produkt. Leadet ligger överst i förvärvs tratten.

    ![Dynamics 365 kontakta mig](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- En kund väljer **Hämta nu** (eller väljer **skapa** i [Azure Portal](https://portal.azure.com/)) för att få ditt erbjudande. Det här leadet är ett *aktivt* lead. Vi delar information med dig om kunden som har börjat distribuera produkten.

    ![Knappen Hämta nu i SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Knappen Skapa i Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- En kund väljer **test enhet** eller **kostnads fri utvärderings version** för att testa ditt erbjudande. Test enheter eller kostnads fria utvärderings versioner kan påskyndas för att du ska kunna dela din verksamhet direkt med potentiella kunder utan några hinder i registreringen.

    ![Knappen Dynamics 365-testenhet](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Kostnads fri utvärderings knapp för Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Anslut till ditt CRM-system

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Förstå lead-data

Varje lead som du får under kund förvärvs processen har data i specifika fält. Det första fältet som ska inblickas för är `LeadSource` fältet, som följer detta format: **käll åtgärds**  |  **erbjudande**.

**Källor**: värdet för det här fältet fylls i baserat på marknads platsen som skapade leadet. Möjliga värden är `"AzureMarketplace"` , `"AzurePortal"` och `"AppSource (SPZA)"` .

**Åtgärder**: värdet för det här fältet fylls i baserat på den åtgärd som kunden vidtog i marknads platsen som skapade leadet.

Möjliga värden:

- **"Ins"**: står för *installation*. Den här åtgärden är i Azure Marketplace eller AppSource när en kund köper din produkt.
- **"PLT"**: står för en *utvärderings version av partner lampan*. Den här åtgärden är i AppSource när en kund väljer alternativet **kontakta mig** .
- **"DNC"**: står *inte för kontakt*. Den här åtgärden är i AppSource när en partner som fanns tvärs över på din app-sida uppmanas att kontaktas. Vi delar ett meddelande om att kunden fanns tvärt i listan i din app, men de behöver inte kontaktas.
- **"Skapa"**: den här åtgärden finns bara i Azure Portal och genereras när en kund köper ditt erbjudande till sitt konto.
- **"StartTestDrive"**: den här åtgärden gäller endast för alternativet **test enhet** och genereras när en kund startar sin test-enhet.

**Erbjudanden**: du kan ha flera erbjudanden i den kommersiella marknads platsen. Värdet för det här fältet fylls i baserat på det erbjudande som genererade leadet. Utgivar-ID och erbjudande-ID skickas både i det här fältet och är värden som du angav när du publicerade erbjudandet till Marketplace.

I följande exempel visas värden i förväntat format `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Kund information

Kundens information skickas via flera fält. I följande exempel visas kund information som finns i ett lead:

- FirstName: John
- LastName: Smith
- E-post: jsmith \@ Microsoft.com
- Telefon: 1234567890
- Land: US
- Företag: Microsoft
- Rubrik: CTO

>[!NOTE]
>Alla data i föregående exempel är alltid tillgängliga för varje lead. Eftersom du får leads från flera steg som nämns i avsnittet "Skapa kund leads" är det bästa sättet att hantera leads att ta bort posterna och anpassa uppföljningen. På så sätt får varje kund ett lämpligt meddelande och du skapar en unik relation.

## <a name="best-practices-for-lead-management"></a>Metod tips för ledar hantering

Här följer några rekommendationer för att driva leads genom din försäljnings cykel:

- **Process**: definiera en klar försäljnings process, med mil stolpar, analys och ta bort teamets ägarskap.
- **Kvalificering**: definiera förutsättningar, som anger om ett lead har varit fullständigt. Se till att Sälj-eller marknadsförings representanter kvalificerar leads noggrant innan du tar dem genom hela försäljnings processen.
- **Uppföljning**: Glöm inte att följa upp inom 24 timmar. Du får leadet i din CRM-valmöjlighet direkt efter att kunden har distribuerat en testenhet. skicka dem med e-post medan de fortfarande är i varma. Begär att schemalägga ett telefonsamtal för att bättre förstå om produkten är en bra lösning för deras problem. Vänta en typisk transaktion för att kräva ett antal uppföljnings anrop.
- **Nurture**: Nurture dina leads för att få dig på väg till en högre vinst marginal. Checka in, men Bombard inte. Vi rekommenderar att du skickar e-post minst några gånger innan du stänger ut dem. lämna inte upp efter det första försöket. Kom ihåg att dessa kunder direkt arbetar med din produkt och förbrukade tid i en kostnads fri utvärderings version. de är fantastiska utsikter.

## <a name="common-questions-about-lead-management"></a>Vanliga frågor om ledar hantering

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Var kan jag få hjälp med att ställa in mitt lead-mål?

Följ stegen i avsnittet [Anslut till ditt CRM-system](#connect-to-your-crm-system)eller skicka in ett support ärende via [Hjälp och support för partner Center](https://aka.ms/marketplacepublishersupport). Välj sedan **erbjud skapa**  >  **din typ av**  >  **konfiguration för hantering av lead-hantering**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Är jag tvungen att konfigurera ett lead-mål för att publicera ett erbjudande i den kommersiella marknads platsen?

Svaret beror på vilken typ av erbjudande du publicerar. Program vara som en tjänst (SaaS) och Dynamics 365 kund engagemang använder **kontakta mig** för att lista alla Dynamics 365 för finans-och drift erbjudanden, alla Dynamics 365 Business Central-erbjudanden och alla konsult tjänst erbjudanden. Det innebär att de kräver en anslutning till ett lead-mål. Om erbjudande typen inte finns med, krävs ingen anslutning till ett lead-mål. Vi rekommenderar att du konfigurerar ett lead-mål så att du inte saknar affärs möjligheter.

### <a name="how-can-i-find-the-test-lead"></a>Hur kan jag hitta testets lead?

Sök efter `"MSFT_TEST"` i ditt lead-mål. Nedan visas ett exempel på ett lead-test från Microsoft. Observera att test-leadets format varierar beroende på lead-målet.

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Jag har ett Live-erbjudande, men varför ser jag inte några leads?

Kontrol lera att din anslutning till målet för målet är giltig. Vi skickar ett test lead när du har valt **publicera** på ditt erbjudande i Partner Center. Om du ser testets lead är anslutningen giltig. Du kan också testa din lead-anslutning genom att försöka skaffa för hands versionen av erbjudandet under för hands versions steget. Välj **Hämta nu**, **kontakta mig**eller **kostnads fri utvärderings version** på listan på den kommersiella Marketplace.

Kontrol lera också att du letar efter rätt data. Innehållet i avsnittet [förstå lead-data](#understand-lead-data) i den här artikeln beskriver lead-data som vi skickar till ditt lead-mål.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Jag har konfigurerat Azure Blob Storage som mitt lead-mål, men varför ser jag inte mitt lead?

Azure Blob Storage stöds inte längre som ett lead-mål, så du saknar kund ledare som genereras av ditt erbjudande. Växla till något av de andra [mål alternativen för lead](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Jag har fått ett e-postmeddelande från den kommersiella marknads platsen, men varför kan jag inte hitta något lead i min CRM?

Det är möjligt att slutanvändarens e-postdomän är från. edu. Av säkerhets skäl skickar vi inte personlig information från. edu-domänen. Skicka in ett support ärende via [Hjälp och support för partner Center](https://aka.ms/marketplacepublishersupport).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Jag har konfigurerat en Azure-tabell som mitt lead-mål. Hur kan jag Visa leads?

Du kan komma åt lead-data som lagras i Azure-tabellen från Azure Portal. Du kan också hämta och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) kostnads fritt om du vill visa tabell data för ditt Azure Storage-konto.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Jag har konfigurerat en Azure-tabell som mitt lead-mål. Kan jag få ett meddelande när ett nytt lead från en kommersiell marknads plats skickas?

Ja. Följ anvisningarna i [Konfigurera hantering av leads med hjälp av en Azure-tabell](./commercial-marketplace-lead-management-instructions-azure-table.md) för att konfigurera ett Microsoft Flow som skickar ett e-postmeddelande om ett lead läggs till i Azure-tabellen.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Jag har konfigurerat Salesforce som mitt lead-mål, men varför kan jag inte hitta några leads?

Kontrol lera om webb-till-lead-formuläret är ett obligatoriskt fält baserat på en plock lista. Om så är fallet byter du fältet till ett fält som inte är obligatoriskt.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Det uppstod ett problem med mitt lead-mål och jag missade några leads. Kan jag skicka dem till mig i ett e-postmeddelande?

På grund av personliga informations principer kan vi inte dela information om leads via oskyddad e-post.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Jag har konfigurerat en Azure-tabell som mitt lead-mål. Hur mycket kostar det?

Data för lead-generering är låg. Det är mindre än 1 GB för nästan alla utgivare. Kostnaden beror på antalet mottagna leads. Om till exempel 1 000 leads tas emot under en månad är kostnaden cirka 50 cent. Mer information om lagrings priser finns i [Azure Storage översikts priser](https://azure.microsoft.com/pricing/details/storage/).

Om din fråga inte besvaras kontaktar du Microsoft Support via [partner Center Hjälp och support](https://aka.ms/marketplacepublishersupport). Välj sedan **erbjud skapa**  >  **din typ av**  >  **konfiguration för hantering av lead-hantering**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Jag får e-postmeddelanden när nya kund ledare tas emot. Hur kan jag konfigurera någon annan att ta emot e-postmeddelanden?

Kom åt ditt erbjudande i Partner Center och gå till sidan för **erbjudande konfiguration** > **hantering av lead-hantering**  >  **Edit**. Uppdatera e-postadresserna under fältet **kontakt epost** .

## <a name="next-steps"></a>Nästa steg

När den tekniska konfigurationen är på plats kan du lägga till dessa leads i din aktuella strategi och drift process för försäljning och marknadsföring. Vi är intresserade av att bättre förstå den övergripande Sälj processen och vill arbeta nära dig med att tillhandahålla högkvalitativa leads och tillräckligt med data för att lyckas. Vi välkomnar din feedback om hur vi kan optimera och förbättra de leads som vi skickar till dig med ytterligare data för att hjälpa kunderna att lyckas. Berätta för oss om du är intresse rad av att [ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag för att göra det möjligt för ditt säljteam att bli mer framgångs rik med leads på kommersiella platser.
