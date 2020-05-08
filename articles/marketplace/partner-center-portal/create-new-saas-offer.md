---
title: Skapa ett nytt SaaS-erbjudande för Microsoft Commercial Marketplace
description: Så här skapar du ett nytt SaaS-erbjudande (Software as a Service) för att lista eller sälja i Microsoft AppSource, Azure Marketplace eller via program varan för Cloud Solution Provider (CSP) med hjälp av Microsoft-programmet för kommersiella marknads platser i Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 9ffa10726c1ca838f00b713e6640f609c122975a
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735104"
---
# <a name="create-a-new-saas-offer-in-the-commercial-marketplace"></a>Skapa ett nytt SaaS-erbjudande på den kommersiella Marketplace

Om du vill börja skapa SaaS-erbjudanden (program vara som en tjänst) i den kommersiella marknads platsen, måste du först [skapa ett partner Center-konto](./create-account.md) och öppna [instrument panelen för extern marknads](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)plats på fliken **Översikt** .

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace** > -**Översikt**.
3. På sidan Översikt väljer du **+ ny erbjudande** > **program vara som en tjänst**.

   ![Visar menyn till vänster-navigering.](./media/new-offer-saas.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar som gjorts i Partner Center i butiker efter publiceringen av erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudandet-1** här, är `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`webb adressen för erbjudandet.
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Det går inte att ändra namnet på erbjudandet när du har valt **skapa**.

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-overview"></a>Erbjudande översikt

**Publicerings status** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet och hur lång tid varje steg tar att slutföra. Ofullständiga publicerings stegs ikoner blir grå.

Menyn **erbjudande översikt** innehåller en lista med länkar för att utföra åtgärder på det här erbjudandet. Den här listan över åtgärder ändras baserat på det val du gör för ditt erbjudande.  

- Om erbjudandet är ett utkast till att ta bort utkast
- Om erbjudandet är Live-Stop Sälj erbjudande
- Om erbjudandet är i för hands version-Go-Live
- Om du inte har slutfört Publisher-utloggning-Avbryt publicering

## <a name="offer-setup"></a>Erbjudande konfiguration

Den här sidan ber om följande information.

- **Vill du sälja via Microsoft?** (Ja/Nej)
    - **Ja**, jag vill sälja via Microsoft och ha Microsoft värd transaktioner för mig
    - **Nej**, jag föredrar att bara lista mitt erbjudande via Marketplace och bearbeta transaktioner oberoende av varandra.

### <a name="sell-through-microsoft"></a>Sälj via Microsoft

Genom att sälja via Microsoft får du bättre kund identifiering och förvärv, som gör det möjligt för Microsoft att vara värd för Marketplace-transaktioner för din räkning och dra nytta av Microsofts globalt tillgängliga funktioner i Commerce.

#### <a name="saas-offer-requirements"></a>Krav för SaaS-erbjudande

Följande kriterier måste vara uppfyllda för att kunna visa SaaS-erbjudanden (Software as a Service) med kommersiell marknads plats på Partner Center:

- Ditt erbjudande måste använda [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) för identitets hantering och autentisering.
- Ditt erbjudande måste använda [SaaS-API: er](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) för att integrera med Azure Marketplace.
- Mer omfattande krav finns i [publicerings guiden för SaaS-erbjudandet](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Pris-och fakturerings alternativ för SaaS

Med SaaS-lösningar som körs i utgivarens Azure-prenumeration inkluderar licens avgifter som betalas av kunderna kostnaden för den infrastruktur som program varan distribueras på. Användningen av Azure-infrastrukturen hanteras och faktureras till dig, partnern direkt. Den faktiska infrastruktur användnings avgiften ses inte av kunden. Utgivare ska paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser. 

SaaS erbjuder stöd för månatlig eller årlig fakturering baserat på en fast avgift, per användare eller förbruknings avgifter med hjälp av den avgiftsbelagda fakturerings tjänsten. Microsofts kommersiella marknads plats fungerar på en byrå modell, där utgivare anger priser, Microsoft fakturerar kunder och Microsoft betalar intäkterna till utgivaren samtidigt som en byrå avgift debiteras.

Detta är ett exempel på en uppdelning av kostnader och utbetalningar för att demonstrera byråns modell:

|**Din licens kostnad**|**$100 per månad**|
|:---|:---|
|Användnings kostnad i Azure (D1/1-kärna)|Debiteras direkt till utgivaren, inte hos kunden|
|Kunden debiteras av Microsoft|$100,00 per månad (utgivare måste redovisa alla kostnader som uppstått eller genom att överföra infrastruktur i licens avgiften)|

|**Microsoft-räkningar**|**$100 per månad**|
|:---|:---|
|Microsoft betalar 80% av din licens kostnad <br>**För kvalificerade SaaS-appar betalar Microsoft 90% av din licens kostnad*|$80,00 per månad <br>*$* 90,00 per månad *|

- I det här exemplet har Microsoft fakturerar $100,00 till kunden för din program varu licens och ger ut $80,00 till utgivaren.
- Partner som har kvalificerat sig för den **minskade service avgiften för Marketplace** ser en reducerad transaktions avgift på SaaS-erbjudandena från maj 2019 till och med juni 2020. I det här scenariot faktureras Microsoft $100,00 för din program varu licens och du betalar ut $90,00 till utgivaren.

> [!NOTE]
> **Minskad service avgift för Marketplace** – för vissa SaaS-erbjudanden som du har publicerat på vår kommersiella marknads plats kommer Microsoft att minska sin service avgift för Marketplace från 20% (enligt beskrivningen i Microsoft Publisher Agreement) till 10%. För att ditt erbjudande ska bli kvalificerat, måste minst ett av dina erbjudanden ha utsetts av Microsoft som en prioriterad eller IP-samkund i samma skick. Berättigande måste uppfyllas minst fem (5) arbets dagar före slutet av varje kalender månad för att få denna minskade service avgift för Marketplace för månaden.  Den minskade service avgiften för Marketplace gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknads plats. Den minskade service avgiften för Marketplace kommer bara att vara tillgänglig för kvalificerade erbjudanden för licens avgifter som samlas in av Microsoft mellan den 1 maj 2019 och 30 juni 2020. Efter den här tiden kommer Marketplace-tjänstens avgift att återgå till sin normala mängd.

### <a name="list-through-microsoft"></a>Lista via Microsoft

Marknadsför ditt företag med Microsoft genom att skapa en Marketplace-lista. Om du väljer att bara lista ditt erbjudande och inte Transact via Microsoft innebär det att Microsoft inte deltar direkt i program licens transaktioner. Det finns ingen kopplad transaktions avgift och utgivaren behåller 100% av alla licens avgifter som samlas in från kunden. Utgivaren ansvarar dock för att stödja alla aspekter av program licens transaktionen, inklusive men inte begränsat till: order uppfyllelse, mätning, fakturering, fakturering, betalning och insamling.

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)

Lista ditt erbjudande till kunder kostnads fritt genom att tillhandahålla en giltig adress (från och med *http* eller *https*) där en utvärderings version kan utföras genom [en autentisering med hjälp av Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials). Till exempel `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Kostnads fri utvärderings version (lista)

Lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version genom att tillhandahålla en giltig adress (från och med *http* eller *https*), där de kan få en utvärdering genom att [använda Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials). Till exempel `https://contoso.com/trial/saas-app`. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure AD för att automatisera skapandet av konton i din app. Microsoft-konton (MSA) stöds inte för autentisering med denna token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [Anslut lead management](#connect-lead-management).

#### <a name="example-marketplace-offer-listing"></a>Exempel på Marketplace-erbjudande

![Exempel lista för Marketplace-erbjudanden med anteckningar](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Aktivera en testen het

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. [Läs mer om test enheter](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Om du vill aktivera en testenhet under en fast tids period markerar du kryss rutan **Aktivera en testenhet** . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

Mer information finns i [Testa ditt erbjudande på den kommersiella marknads platsen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

### <a name="test-drive-resources"></a>Testa enhets resurser

- [Metodtips för marknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF, se till att blockering av popup-fönster är inaktiverat)

### <a name="connect-lead-management"></a>Anslut lead-hantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Ytterligare resurser för ledar hantering
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över ledar hantering en personsökare](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan uppmanas du att definiera de kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, juridiska avtal som stöder ditt erbjudande och din program version.

### <a name="category"></a>Kategori

Välj minst en och upp till tre kategorier för att gruppera ditt erbjudande i lämpliga sökområden för Marketplace. Beskriv hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet.

### <a name="industries"></a>Branscher

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Appversion

Det här fältet är valfritt och används i AppSource Marketplace för att identifiera versions numret för ditt erbjudande.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard kontrakt för Microsofts kommersiella marknads plats

Microsoft tillhandahåller en standard kontrakts mal len.

- **Använd standard kontraktet för Microsoft Commercial Marketplace?**

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt för Microsofts kommersiella marknads plats för att under lätta transaktioner i Marketplace. I stället för att utforma anpassade villkor, kan kommersiella Marketplace-utgivare välja att erbjuda sin program vara under standard kontraktet, som kunderna bara behöver Undersök och godkänna en gång. Du hittar standard kontraktet på https://go.microsoft.com/fwlink/?linkid=2041178.

Du kan välja att använda standard kontraktet i stället för att ange egna anpassade villkor genom att markera kryss rutan Använd standard kontraktet för den kommersiella Marketplace.

![Använda standard kontrakt kryss rutan](./media/use-standard-contract.png)

> [!NOTE]
> När du har publicerat ett erbjudande med standard kontraktet för Microsoft Marketplace kan du inte använda dina egna anpassade villkor. Det är ett "eller"-scenario. Du erbjuder antingen din lösning enligt standard avtalet **eller** dina egna villkor. Om du vill ändra villkoren i standard kontraktet kan du göra det via standard kontrakts ändringar.

#### <a name="standard-contract-amendments"></a>Standard kontrakts ändringar

Med standard kontrakts ändringar kan utgivare välja standard avtals villkoren för enkelhetens skull och anpassa villkoren för deras produkt eller verksamhet. Kunderna behöver bara granska ändringarna i kontraktet, om de redan har granskat och godkänt Microsofts standard kontrakt.

Det finns två typer av ändringar som är tillgängliga för kommersiella Marketplace-utgivare:

- Universella ändringar: dessa ändringar tillämpas universellt på standard kontraktet för alla kunder. Universella ändringar visas för varje kund av erbjudandet i inköps flödet. Kunderna måste godkänna villkoren i standard kontraktet och ändringen innan de kan använda ditt erbjudande.
- Anpassade ändringar: dessa ändringar är särskilda ändringar i standard kontraktet som endast är riktade till specifika kunder via klient-ID: n för Azure. Utgivare kan välja den klient som de vill rikta in sig på. Endast kunder från klient organisationen visas med de anpassade ändrings villkoren i erbjudandets inköps flöde.  Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande.

>[!NOTE]
> Dessa två typer av ändringar staplas ovanpå varandra. Kunder som är riktade mot anpassade ändringar får också den universella ändringen av standard kontraktet under köpet.

**Generella ändrings villkor i standard kontraktet för Microsofts kommersiella marknads plats** – ange villkoren för Universal-ändringar i den här rutan. Du kan ange en enda universell ändring per erbjudande. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings-och inköps flödet.

**Anpassade ändrings villkor till standard kontraktet för Microsofts kommersiella marknads plats** – börja med att välja **Lägg till anpassade ändrings villkor**. Du kan ange upp till 10 anpassade ändrings villkor per erbjudande.

- **Anpassade ändrings villkor** – ange dina anpassade ändrings villkor i rutan anpassade ändrings villkor. Du kan ange ett obegränsat antal tecken i den här rutan. Endast kunder från klient-ID: n som du anger för de här anpassade villkoren visas med villkoren för anpassad ändring i erbjudandets inköps flöde i Azure Portal.  
- **Klient-ID** (obligatoriskt) – varje anpassad ändring kan riktas mot upp till 20 klient-ID: n. Om du lägger till en anpassad ändring måste du ange minst ett klient-ID. Klient-ID: t identifierar din kund i Azure. Du kan be kunden om detta ID och de kan hitta det genom att gå till portal.azure.com > Azure Active Directory > egenskaper. Värdet för katalog-ID är klient-ID (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp organisationens klient-ID för din kund genom att använda deras domän namns-URL i [Vad är mitt Microsoft Azure-och Office 365-klient-ID?](https://www.whatismytenantid.com).
- **Beskrivning** (valfritt) – du kan också ange en egen beskrivning för klient-ID: t som hjälper dig att identifiera kunden som du är mål för med ändringen.

#### <a name="terms-and-conditions"></a>Villkor

Om du vill ange egna anpassade villkor kan du välja att ange dem i fältet allmänna villkor. Du kan ange upp till 10 000 tecken text i det här fältet. Om dina villkor kräver en längre Beskrivning anger du en enda URL-länk i det här fältet där du kan hitta dina villkor. Den kommer att visas för kunder som en aktiv länk.

Kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

På den här sidan visas de språk (och marknader) där ditt erbjudande är tillgängligt, för närvarande är engelska (USA) den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera Marketplace-information (erbjudande namn, beskrivning, Sök villkor osv.) för varje språk/marknad.

> [!NOTE]
> Erbjudande för List innehåll (till exempel Beskrivning av erbjudande, dokument, skärm bilder, användnings villkor och sekretess policy) behöver inte vara på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [icke-engelskt språk]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

### <a name="offer-listings"></a>Erbjudande listor

Ange information som ska visas på Marketplace, inklusive beskrivningar av ditt erbjudande och marknadsförings till gångar.

- **Namn** (obligatoriskt) – det namn som definieras här visas som rubrik på din erbjudande lista på de Marketplace som du har valt. Namnet är förifyllt baserat på din föregående **nya erbjudande** post. Namnet kan vara ett varumärke. Den får inte innehålla emojis (om de inte är varumärken och Copyright-symboler) och måste vara begränsade till 50 tecken.
- **Sammanfattning** (krävs) – ange en kort beskrivning av ditt erbjudande som ska användas i Sök Resultat för Marketplace-listor. Upp till 100 tecken text kan anges i det här fältet.
- **Beskrivning** (krävs) – ange en beskrivning av ditt erbjudande som ska visas i Översikt över Marketplace-listor. Överväg att inkludera ett värde förslag, viktiga fördelar, eventuella kategori-eller bransch associationer, inköps möjligheter för appar, eventuella obligatoriska upplysningar och en länk till mer information. Upp till 3 000 tecken text kan anges i det här fältet, inklusive pålägg. Ytterligare tips finns i [skriva en fantastisk app-Beskrivning](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Sök Nyckelord** – ange upp till tre Sök nyckelord som kunder kan använda för att hitta ditt erbjudande på Marketplace.
- **Komma igång-instruktioner** (krävs) – förklara hur du konfigurerar och börjar använda din app för potentiella kunder.  Den här snabb starten kan innehålla länkar till mer detaljerad onlinedokumentation. Upp till 3 000 tecken text kan anges i det här fältet.

#### <a name="description"></a>**Beskrivning**

Det här fältet är obligatoriskt. Objekt som ska tas med i **beskrivningen**:

* Beskriv tydligt ditt erbjudande värde i de första meningarna i beskrivningen.  
* Tänk på att de första meningarna kan visas i Sök motorns resultat.  
* Förlita dig inte på funktioner och funktioner för att sälja produkten. Fokusera i stället på det värde som du levererar.  
* Använd branschspecifika vokabulär eller kunskapsbaserade formuleringar så mycket som möjligt.

Kärn komponenterna i dina värde förslag bör innefatta:

* Beskrivning av produkten
* Typ av användare som fördelar från produkten
* Kunden behöver eller smärta om produkt adresser

Om du vill göra din erbjudande Beskrivning mer engagerande använder du RTF-redigeraren för att tillämpa formatering.

![Använda Rich Text Editor](./media/rich-text-editor.png)

| <center>Ändra text format | <center>Lägga till punkter eller numrering | <center>Lägg till eller ta bort text indrag |
| --- | --- | --- |
| <center>![Använda RTF-redigeraren för att ändra text format](./media/text-editor3.png) |  <center>![Använda RTF-redigeraren för att lägga till listor](./media/text-editor4.png) |  <center>![Använda RTF-redigeraren för att dra in text](./media/text-editor5.png) |

#### <a name="links"></a>Länkar

- **Sekretess policy** (krävs) – länka till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy
- **CSP-program marknadsförings material** (valfritt) – ange en länk till marknadsförings material om du väljer att utöka erbjudandet till program varan för [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . CSP utökar erbjudandet till ett bredare utbud av kvalificerade kunder genom att göra det möjligt för CSP-partner att paketera, marknadsföra och sälja ditt erbjudande. Dessa åter försäljare behöver åtkomst till material för marknadsföring av ditt erbjudande. Mer information finns i [Go-to-Marketing-tjänster](https://partner.microsoft.com/reach-customers/gtm).
- **Användbara länkar** (valfritt) – valfria kompletterande online-dokument om din app eller relaterade tjänster som listas genom att tillhandahålla en **rubrik** och **URL**. Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en URL**.

#### <a name="contact-information"></a>Kontaktinformation

- **Kontakter** – för varje kund kontakt anger du ett **namn**, **telefonnummer**och **e-** postadress för en anställd (dessa visas *inte* offentligt). Det krävs en **Support-URL** för **support kontakt** gruppen (detta *kommer* att visas offentligt).

    - **Support kontakt** (krävs) – för allmänna supportfrågor.
    - Teknisk **kontakt** (krävs) – för tekniska frågor.
    - **Kanal chefs kontakt** (krävs) – för åter försäljar frågor som är relaterade till CSP-programmet.

#### <a name="files-and-images"></a>Filer och avbildningar

- **Dokument** (krävs) – Lägg till relaterade marknadsförings dokument för ditt erbjudande i PDF-format, vilket ger minst ett (1) och högst tre (3) dokument per erbjudande.
- **Avbildningar** (valfritt) – det finns flera platser där ditt erbjudandes Logo bilder kan visas på alla Marketplace, vilket kräver följande pixel storlekar i PNG-format:

    - **Liten** (48 x 48, krävs)
    - **Medel** (90 x 90, krävs)
    - **Stor** (216 x 216, krävs)
    - **Bred** (255 x 115)
    - **Hjälte** (815 x 290)

- **Skärm bilder** (krävs) – Lägg till skärm bilder som demonstrerar ditt erbjudande. Högst fem (5) skärm bilder kan läggas till och ska vara i storlek med 1280 x 720 pixlar. Alla avbildningar måste vara i. PNG-format.
- **Videor** (valfritt) – Lägg till länkar till videor som demonstrerar ditt erbjudande. Du kan använda länkar till YouTube och/eller Vimeo-videor som visas tillsammans med ditt erbjudande till kunder. Du måste också ange en miniatyr bild av videon, med en storlek på 1280 x 720 pixlar i PNG-format. Du kan visa högst fyra videor per erbjudande.

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

- [Metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Välj **Spara utkast** innan du fortsätter.

## <a name="preview-audience"></a>Förhandsgranska mål grupp

På den här sidan kan du definiera en begränsad **förhands gransknings grupp** för att släppa ditt erbjudande innan du publicerar erbjudandet Live till den bredare Marketplace-publiken.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i för hands version väljer du **Go Live** så att ditt erbjudande kan publiceras Live till den offentliga Marketplace-publiken.

Lägg till ett enda AAD/MSA-konto per rad, tillsammans med en valfri beskrivning.

Lägg till upp till 10 e-postadresser manuellt, eller 20 om du överför en CSV-fil, för befintligt Microsoft-konto (MSA) eller Azure Active Directory konton som hjälper dig att verifiera ditt erbjudande innan du publicerar Live. Genom att lägga till dessa konton definierar du en mål grupp som ska få för hands versions åtkomst till ditt erbjudande innan den publiceras på Marketplace (s). Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

> [!NOTE]
> Förhands gransknings publiken skiljer sig från en privat publik. En förhands gransknings mål grupp har åtkomst till ditt erbjudande _innan_ de publiceras Live på Marketplace. Du kan också välja att skapa en plan och bara göra den tillgänglig för en privat publik. På fliken **plan lista** kan du definiera en privat mål grupp med kryss rutan för **privat plan** . Du kan sedan definiera en privat mål grupp med upp till 20 000 kunder med hjälp av Azure klient-ID.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar den tekniska information (URL-sökväg, webhook, klient-ID och app-ID) som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det. Diagram som beskriver användningen av de insamlade fälten finns i dokumentationen för [API: er för SaaS-utförande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **Landnings sidans URL** (krävs) – definiera den plats-URL som kunderna ska landa efter när ditt erbjudande har hämtats från Marketplace. Den här URL: en är slut punkten som tar emot en token när en kund dirigeras till sidan. Denna token kan bytas ut mot etablerings information med hjälp av lösa i API: er som uppfyller kraven. Informationen och andra som du samlar in kan användas som en del av en kundinteraktiv webb sida som skapats i din upplevelse för att slutföra registreringen och aktivera köpet.

- **Anslutning-webhook** (krävs) – för alla asynkrona händelser som Microsoft behöver skicka till dig för kundens räkning (till exempel SaaS-prenumerationen är ogiltig), så kräver vi att du anger en anslutning-webhook. Om du inte redan har ett webhook-system på plats är den enklaste konfigurationen att ha en HTTP-slutpunkt som lyssnar efter händelser som skickas till den och sedan hantera dem på rätt sätt (t. ex. https:\//Prod-1westus.Logic.Azure.com:443/Work). Mer information finns i [anropa, utlösa eller kapsla arbets flöden med HTTP-slutpunkter i Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure AD-klient-ID** (krävs) – inuti Azure Portal kräver vi att du [skapar en Azure Active Directory (AD)-app](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) så att vi kan verifiera anslutningen mellan våra två tjänster bakom en autentiserad kommunikation. Du hittar [klient-ID: t](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)genom att gå till din Azure Active Directory och välja **Egenskaper**och leta efter **katalog-ID** -numret i listan (till exempel 50c464d3-4930-494c-963c-1e951d15360e).

- **ID för Azure AD-App** (krävs) – du behöver också ditt [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) och en autentiseringsnyckel. Om du vill hämta dessa värden går du till Azure Active Directory och väljer **Appregistreringar**och letar sedan efter det **program-ID-** nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Om du vill hitta autentiseringsnyckel går du till **Inställningar** och väljer **nycklar**. Du måste ange en beskrivning och varaktighet och kommer sedan att tillhandahålla ett Number-värde.

>[!Note]
>Azure-programmets ID är associerat med ditt utgivar-ID, så se till att samma program-ID används i alla dina erbjudanden.

Välj **Spara utkast** innan du fortsätter.

## <a name="plan-overview"></a>Plan översikt

På den här sidan kan du ange olika plan alternativ inom samma erbjudande. Dessa planer (kallas ibland SKU: er) kan variera beroende på version, monetarisering eller tjänst nivåer. Du måste konfigurera minst en plan för att kunna sälja ditt erbjudande i Marketplace.

När du har skapat dina prenumerations namn, ID: n, pris modeller, tillgänglighet (offentlig eller privat), aktuell publicerings status och alla tillgängliga åtgärder.

Vilka **åtgärder** som är tillgängliga i **plan översikten** varierar beroende på planens aktuella status och kan vara:

- Om plan status är **Draft** -ta bort utkast
- Om plan status är **Live** -Stop Sälj plan eller synkronisera privat publik

**Skapa en ny plan** (minst en plan för de som väljer att sälja via Microsoft)

- **Plan-ID:** Skapa ett unikt plan-ID för varje plan i det här erbjudandet. Detta ID visas för kunder i produkt-URL och Azure Resource Manager mallar (om tillämpligt). Använd endast gemener, alfanumeriska tecken, bindestreck eller under streck. Högst 50 tecken tillåts för det här plan-ID: t. Det går inte att ändra ID när du har valt skapa.
- **Plan namn:** Kunder ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt erbjudande namn för varje plan i det här erbjudandet. Plan namnet används för att särskilja program varu planer som kan vara en del av samma erbjudande (till exempel erbjudande namn: Windows Server; abonnemang: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Plan lista

På den här sidan kan du definiera plan namn och beskrivning. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Namn** – fylls i automatiskt baserat på din förhands gransknings **nya plan** post och visas som rubrik för ditt erbjudandes "program varu plan" på Marketplace.
- **Beskrivning** – den här beskrivningen är en möjlighet att förklara vad som gör att den här program varu planen är unik och eventuella skillnader jämfört med andra program varu planer i erbjudandet. Får innehålla upp till 500 tecken.

Välj **Spara utkast** innan du fortsätter.

#### <a name="pricing-and-availability"></a>Priser och tillgänglighet

På den här sidan kan du konfigurera de marknader som planen kommer att vara tillgänglig i, den önskade monetarisering-modellen, pris-och fakturerings perioden. Dessutom kan du ange om du vill att planen ska vara synlig för alla eller bara för vissa kunder (en privat mål grupp).

#### <a name="markets-optional"></a>Marknader (valfritt)

Varje plan måste vara tillgänglig på minst en marknad. Välj **Redigera marknader** och markera kryss rutan för alla marknads platser där du vill göra den här planen tillgänglig. Den här sidan innehåller en sökruta och ett alternativ för att välja "skatte mottagare", där Microsoft betalar försäljnings-och användnings skatt för din räkning.

Om du redan har angett priser för din plan i USA dollar (USD) och lagt till en annan marknads plats kommer priset för den nya marknaden att beräknas enligt de aktuella växelkurserna. Granska priset för varje marknad innan du publicerar. Visa priser med hjälp av länken "export priser (xlsx)" när du har sparat ändringarna.

Välj **Spara** innan du fortsätter.

#### <a name="pricing"></a>Prissättning

##### <a name="pricing-model"></a>Prismodell

**Fast** pris – ge åtkomst till ditt erbjudande med ett enkelt pris per månad eller årligt pris. Detta kallas ibland för platsbaserade priser. Med den här pris sättnings modellen kan du välja att definiera förmätnings planer som använder API: et för avläsning av program vara för att debitera kunder enligt andra enheter än standard enheter.  Mer information om avgiftsbelagd fakturering finns i [mäta fakturering med Marketplace-avläsning](./saas-metered-billing.md).

**Per användare** – aktivera åtkomst till ditt erbjudande med priset baserat på antalet användare som har åtkomst till erbjudandet eller har platser. Med den här användarbaserade modellen kan du ange det lägsta och högsta antalet användare som tillåts baserat på priset. På så sätt kan olika pris punkter konfigureras baserat på antalet användare genom att konfigurera flera planer.  Dessa fält är valfria. Om alternativet inte är markerat tolkas antalet användare som att de inte har en gräns (minst 1 och högst lika många som systemet kan stödja). De här fälten kan redige ras som en del av en uppdatering av planen.

När du har publicerat det kan du inte ändra fakturerings pris modellen. Dessutom måste alla planer för samma erbjudande dela samma pris modell.

##### <a name="user-limits"></a>Användargränser

Om tillämpligt, Välj och ange lägsta och högsta användar gräns.

##### <a name="billing-term-and-price"></a>Fakturerings tid och pris

Välj den **period** och det **pris** som kunderna måste betala för det pris som anges. Minst ett månatligt eller årligt pris måste tillhandahållas, eller så kan båda alternativen göras tillgängliga för kunderna.

Priser som angetts i USD (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna när de sparas. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar du och importerar pris kalkyl bladet. Du ansvarar för att verifiera priset och äga dessa inställningar.
*\*Du måste först spara dina pris ändringar för att möjliggöra export av pris information.*

Granska dina priser noggrant innan du publicerar, eftersom det finns vissa begränsningar för vad som kan ändras när en plan publiceras:

- När en plan har publicerats går det inte att ändra pris sättnings modellen.
- När en fakturerings period har publicerats för en plan kan du inte ta bort den senare.
- När ett pris för en marknad i planen har publicerats kan det inte ändras senare.

#### <a name="free-trial"></a>Kostnadsfri utvärderingsversion

SaaS erbjudanden via den kommersiella marknads platsen gör att du kan tillhandahålla en månads kostnads fri utvärderings version när du säljer via Microsoft. För alla fakturerings modeller och villkor förutom mätnings planer stöds kostnads fria utvärderings versioner. Med det här alternativet kan kunder ha en låg barriär för att komma in genom en månads kostnads fri åtkomst.  Om du väljer att aktivera en kostnads fri utvärderings version för planer i ditt erbjudande kan kunden inte konvertera till en betald prenumeration innan den första en månads period slutar att gälla.  Under den här tiden kan kunder som köper erbjudandet prova över de planer som stöds och som har den kostnads fria utvärderings versionen aktive rad och konvertera mellan dem.  Konverteringen till en betald prenumeration görs automatiskt i slutet av perioden.

>[!NOTE]
>Om kunden väljer att konvertera till en plan utan kostnads fria utvärderings versioner sker konverteringen, men den kostnads fria utvärderings versionen går förlorad omedelbart. När en kund börjar betala för en plan kan de inte längre få en kostnads fri utvärderings version på samma prenumeration igen, även om de konverteras till en SKU som stöder kostnads fria utvärderings versioner.

Du kan konfigurera en kostnads fri utvärderings version för varje plan i erbjudandet här. Markera kryss rutan om du vill att en utvärderings version av en månad ska tillåtas.

![En månads kostnads fri utvärderings ruta](./media/free-trial-enable.png)

>[!NOTE]
>När ditt transactable-erbjudande har publicerats med en kostnads fri utvärderings version kan det inte inaktive ras för den planen. Kontrol lera att den här inställningen är korrekt för den första publiceringen för att undvika att behöva återskapa planen.

För att få information om kund prenumerationer som för närvarande ingår i en kostnads fri utvärderings `isFreeTrial`version använder du den nya API-egenskapen som kommer att markeras som sant eller falskt. Mer information finns i API för [SaaS get-prenumeration](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!NOTE]
>Kostnads fria utvärderings versioner stöds inte för planer som utnyttjar Marketplace-avläsning.

#### <a name="plan-visibility"></a>Plan synlighet

Du kan konfigurera varje plan så att den blir synlig för alla eller till en speciell grupp som du väljer. Du kan tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azure AD-klient-ID: n.

##### <a name="privacy"></a>Sekretess

Välj **det här är en privat plan** för att göra din plan privat och endast synlig för den begränsade gruppen som du väljer. När du har publicerat som privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan publiceras som synlig för alla måste den förbli synlig för alla (den kan inte konfigureras som en privat plan igen).

##### <a name="restricted-audience-tenant-ids"></a>**Begränsad mål grupp (klient-ID)**

Tilldela den mål grupp som ska ha åtkomst till den här privata planen. Åtkomst tilldelas med klient-ID: n med alternativet att inkludera en beskrivning av varje tilldelad klient-ID. Högst 10 klient-ID: n kan läggas till, eller 20 000 kunders klient-ID om du importerar en. csv-kalkylbladsfilen.

En klient organisation är en representation av en organisation med ett ID som representeras som GUID (globalt unik identifierare, ett 128-bitars heltal som används för att identifiera resurser). Det är en dedikerad instans av Azure AD som en organisation eller app-utvecklare får när organisationen eller appens utvecklare skapar en relation med Microsoft, till exempel när du registrerar dig för Azure, Microsoft Intune eller Microsoft 365. Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter. Om du vill kontrollera klientorganisationen loggar du in på Azure-portalen med det konto du vill använda för att hantera din app. Om du har en klient kommer du automatiskt att loggas in och kan se klientorganisationens namn direkt under namnet på ditt konto. Hovra över ditt kontonamn längst upp till höger i Azure-portalen så visas namn, e-post, katalog/klient-ID (ett GUID) och domän. Om ditt konto är kopplat till flera klienter måste du välja namnet på ditt konto för att öppna en meny där du kan växla mellan klienter. Varje klient har sitt eget klient-ID. Du kan också leta upp organisationens klient-ID med en domän namns- [https://www.whatismytenantid.com](https://www.whatismytenantid.com)URL på.

Även om SaaS-erbjudanden använder klient-ID: n för att definiera en privat mål grupp kan andra erbjudande typer använda Azures prenumerations-ID (som också visas som GUID).

> [!NOTE]
> Den privata mål gruppen (eller den begränsade mål gruppen) skiljer sig från en förhands gransknings publik. På sidan **[Förhandsgranska](#preview-audience)** kan du definiera en förhands gransknings grupp. En förhands gransknings mål grupp har åtkomst till ditt erbjudande *innan* erbjudandet publiceras Live på Marketplace. Även om den privata mål behållaren endast gäller för en viss plan, kan förhands gransknings publiken Visa alla planer (privata eller inte), men endast under den begränsade för hands perioden medan planen testas och verifieras.

Välj **Spara utkast** innan du fortsätter.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exempel lista över planer i ett Marketplace-erbjudande

![Exempel på Marketplace-plan lista med anteckningar](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Moln lösnings leverantör (CSP) åter försäljare

Om du väljer att göra ditt erbjudande tillgängligt i CSP-programmet kan moln lösnings leverantörer sälja din produkt som en del av en samlad lösning till sina kunder. Mer information finns i [Cloud solution providers](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **Granska och publicera** i det övre högra hörnet i portalen.

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

Om det här är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - **Inte startat** – avsnittet har inte vidrör ATS och måste slutföras.
    - **Ofullständig** – avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Du måste gå tillbaka till avsnittet och uppdatera det.
    - **Slutfört** – avsnittet är slutfört, alla data som krävs har angetts och det finns inga fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- Tillhandahåll test instruktioner till certifierings teamet för att se till att appen testas korrekt, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Du måste gå tillbaka till Partner Center och välja **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
