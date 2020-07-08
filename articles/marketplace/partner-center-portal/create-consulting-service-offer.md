---
title: Skapa ett konsult tjänst erbjudande – Microsoft Commercial Marketplace
description: Lär dig hur du publicerar ett konsult tjänst erbjudande till antingen Microsoft AppSource eller Azure Marketplace med hjälp av Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b9bac7b5b23c4e7d4042b53b93347628488be953
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984278"
---
# <a name="create-a-consulting-service-offer"></a>Skapa ett konsulttjänsterbjudande

Den här artikeln beskriver hur du publicerar ett konsult tjänst erbjudande till antingen [Microsoft AppSource](https://appsource.microsoft.com/) eller [Azure Marketplace](https://azuremarketplace.microsoft.com/). Lista konsult tjänst erbjudanden baserade på Microsoft [Dynamics 365](https://dynamics.microsoft.com/) och Power Platform på AppSource. Visa en lista över konsult tjänst erbjudanden baserade på Microsoft Azure på Azure Marketplace. Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

Innan du skapar ditt erbjudande bör du gå igenom kraven i [konsult service krav](consulting-service-prerequisites.md).

## <a name="publishing-benefits"></a>Publicera förmåner

Fördelar med att publicera till den kommersiella marknads platsen:

- Marknadsför företaget genom att använda Microsoft varumärke.
- Det kan finnas fler än 100 000 000 Office 365-och Dynamics 365-användare på AppSource och fler än 200 000 organisationer via Azure Marketplace.
- Ta emot leads med hög kvalitet från dessa marknads platser.
- Få dina tjänster upphöjda av Microsoft-fälten och inköps teamet för köp

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ ny erbjudande**  >  **konsult tjänst**.

    ![Visar menyn till vänster-navigering.](./media/new-offer-consulting-service.png)

>[!NOTE]
>När ett erbjudande har publicerats visas bara ändringar som gjorts i Partner Center i butiker efter publiceringen av erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1**, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Det går inte att ändra namnet på erbjudandet när du har valt **skapa**.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="connect-lead-management"></a>Anslut lead-hantering

När du publicerar ditt erbjudande till Marketplace med partner Center _måste_ du ansluta det till ett system för kund Relations hantering (CRM) eller marknadsförings automatisering. På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten.

1. Välj **Anslut** för att ange var du vill att vi ska skicka kund leads. Partner Center har stöd för följande system:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Om ditt CRM-system inte finns med i listan ovan använder du [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) eller [https-slutpunkten](commercial-marketplace-lead-management-instructions-https.md) för att lagra kundens lead-data och sedan exportera data till CRM-systemet.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center kommer vi att validera anslutningen och skicka ett test lead. Medan du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.
4. Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

Här följer några ytterligare resurser för ledar hantering:

- [Översikt över ledar hantering](commercial-marketplace-get-customer-leads.md)
- [Vanliga frågor och svar om lead management](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Vanliga fel vid lead-konfiguration](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [Översikt över ledar hantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (kontrol lera att blockeringen av popup-fönster är inaktive rad)

Välj **Spara utkast** innan du fortsätter.

### <a name="properties"></a>Egenskaper

På den här sidan kan du ange den primära produkt som din konsult tjänst erbjuder bäst skydd, ange en konsult tjänst typ och välja tillämpliga produkter.

1. Välj en **primär produkt** i list rutan.
2. Välj en **konsult tjänst typ** i list rutan:

    - **Utvärdering**: en utvärdering av en kunds miljö för att fastställa tillämpligheten för en lösning och ge en uppskattning av kostnader och tids inställningar.
    - **Kort**: en introduktion till en lösning eller en konsult tjänst för att dra nytta av kundernas intresse genom att använda ramverk, demonstrationer och kund exempel.
    - **Implementering**: en fullständig installation som resulterar i en helt fungerande lösning. Begränsa till lösningar som kan implementeras på två veckor eller mindre.
    - **Koncept bevis**: en begränsad omfattning för att avgöra om en lösning uppfyller kundens krav.
    - **Workshop**: ett interaktivt engagemang i en kunds lokala. Det kan omfatta utbildning, kortfattade, utvärderingar eller demonstrationer som bygger på kundens data eller miljö.

3. Om du har valt **Azure** som primär produkt väljer du upp till tre **lösnings områden**. Detta gör det enklare för kunderna på Azure Marketplace att hitta ditt erbjudande. Hoppa över det här steget om du inte har valt Azure.

    | Lösnings områden | | | |
    | --- | --- | --- | --- |
    | Analys | Backup | DevOps | Migrering |
    | App-modernisering | Stordata | Haveriberedskap | Nätverk |
    | Arkiv | Data plattform | Identitet | Säkerhet |
    | AI + Machine Learning | Data Center hantering | Sakernas Internet | Storage |

1. Om du har valt **Azure** som primär produkt har du möjlighet att välja upp till sex **branscher**. Detta gör det enklare för kunderna på Azure Marketplace att hitta ditt erbjudande. Se den fullständiga listan med branscher i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md). Hoppa över det här steget om du inte har valt Azure.
1. Om du har valt en *annan* primär produkt än Azure väljer du upp till tre **tillämpliga produkter**. Detta gör det enklare för kunder i AppSource att hitta ditt erbjudande. Mer information finns i avsnittet [Microsoft AppSource konsult service](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) distail Guidelines (PDF).
1. Om du har valt en *annan* primär produkt än Azure har du möjlighet att välja upp till två **branscher** och två **lodräta** för varje bransch. Detta gör det enklare för kunder i AppSource att hitta ditt erbjudande. Se den fullständiga listan över branscher och lodräta i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).
1. Lägg till upp till tre **kompetenser** som ditt företag har fått att visa i din konsult tjänst erbjudande lista. Minst en kompetens krävs förutom Azure expert MSP och Azure Networking MSP.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

Här definierar du de erbjudande uppgifter som visas i Marketplace. Detta inkluderar erbjudandets namn, beskrivning, bilder och så vidare. Se till att följa de principer som beskrivs på [sidan för certifierings principer för kommersiella marknads platser](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) när du konfigurerar det här erbjudandet.

> [!NOTE]
> Erbjudande information krävs inte på engelska om erbjudande beskrivningen börjar med frasen, &quot; är det här programmet endast tillgängligt på [språk som inte är engelska]. &quot; Det går också att tillhandahålla en användbar länk för att erbjuda innehåll på ett annat språk än det som används i listan med erbjudande information.

Här är ett exempel på hur information om erbjudandet visas på Azure Marketplace (alla noterade priser är till exempel endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Visar hur det här erbjudandet visas på Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Pris
3. Lösnings områden
4. Branscher
5. Erbjudandets namn
6. Sammanfattning
7. Description
8. Skärm bilder/videor

<br>Här är ett exempel på hur information om erbjudandet visas i Microsoft AppSource (de priser som visas i listan är endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Pris
3. Produkter
4. Branscher
5. Erbjudandets namn
6. Sammanfattning
7. Description
8. Skärm bilder/videor
9. Dokument

### <a name="name"></a>Name

Namnet som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i automatiskt med den text som du angav i rutan för **erbjuden alias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler).
- Får inte vara mer än 50 tecken långt.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av erbjudandet. Detta kan vara upp till 100 tecken långt och används i Sök resultaten för Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="keywords"></a>Nyckelord

Ange upp till tre Sök nyckelord som är relevanta för din primära produkt och konsult tjänst. Detta gör det lättare att hitta ditt erbjudande.

### <a name="duration"></a>Varaktighet

Ange den förväntade varaktigheten för det här engagemanget hos kunden.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **primär** och **sekundär kontakt**. Den här informationen visas inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas av CSP-partner (Cloud Solution Provider).

### <a name="supporting-documents"></a>Stöd dokument

Lägg till upp till tre (men minst en) stöd för PDF-dokument för ditt erbjudande.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange PNG-filer för ditt erbjudandes logo typ i var och en av följande pixel storlekar:

- **Liten (48 x 48)**
- **Stor (216 x 216)**

Alla logo typer krävs och används på olika platser i Marketplace-listan.

#### <a name="screenshots-optional"></a>Skärm dum par (valfritt)

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fyra videor som demonstrerar ditt erbjudande. Ange videons namn, webb adress (URL) och en miniatyr bild av videon med 1280 x 720 pixlar i storlek.

Välj **Spara utkast** innan du fortsätter.

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Här definierar du element som prissättning, marknad och en privat nyckel.

1. **Marknad**: Ställ in marknaden som ditt erbjudande kommer att vara tillgängligt i. Du kan bara välja en marknad per erbjudande.
    1. För USA-eller Kanada-marknader väljer du **Redigera tillstånd** (eller **provinser**) för att ange var ditt erbjudande ska vara tillgängligt.
2. **Förhands gransknings mål**: Konfigurera knappen **Dölj** som används för att ange den privata mål gruppen för ditt erbjudande.
3. **Prissättning**: Ange om erbjudandet är ett **kostnads fritt** eller **betalt** erbjudande.

    > [!NOTE]
    > Konsult tjänster erbjuder endast för registrering. Eventuella transaktioner sker direkt, utanför den kommersiella marknads platsen.

4. För ett betalt erbjudande anger du **pris och valuta** och om priset är **fast** eller **uppskattat**. Om det är uppskattat måste du ange i beskrivningen vilka faktorer som ska påverka priset.
5. Välj **Spara utkast** innan du fortsätter.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka ditt erbjudande till granskning och publicering.

1. När du är redo att publicera ditt konsult tjänst erbjudande klickar du på **Granska och publicera**.
2. Granska informationen på sidan slutlig överföring.
3. Om det behövs skriver du en anteckning till certifierings teamet om du anser att någon information om Erbjudandet kräver förklaring.
4. När du är klar väljer du **Skicka**.
5. Sidan **erbjudande översikt** visar vilken publicerings fas ditt erbjudande befinner sig i.

Mer information om hur länge du kan vänta dig att ditt erbjudande ska vara i varje publicerings steg finns i [kontrol lera publicerings status för ditt kommersiella Marketplace-erbjudande](publishing-status.md).

## <a name="update-your-existing-consulting-service-offers"></a>Uppdatera dina befintliga erbjudanden om konsult tjänster

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](update-existing-offer.md)
