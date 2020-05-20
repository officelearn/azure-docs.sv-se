---
title: Skapa ett erbjudande för Power BI app i Microsoft Commercial Marketplace
description: Lär dig hur du skapar och publicerar ett Power BI app-erbjudande i Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 9c31c6ca4ccb5ff328faa5db1803134aa6b0c873
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701131"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Skapa en Power BI-app för Microsoft AppSource

Den här artikeln beskriver hur du skapar och publicerar ett Power BI app-erbjudande till Microsoft [AppSource](https://appsource.microsoft.com/).

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Power BI tjänst program**.

   ![Visar menyn till vänster-navigering.](./media/new-offer-pbi-app.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar som gjorts i Partner Center i butiker efter publiceringen av erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

> [!IMPORTANT]
> Om **Power BI tjänst program** inte visas eller aktive ras har ditt konto inte behörighet att skapa den här erbjudande typen. Kontrol lera att du har uppfyllt alla [krav](create-power-bi-app-overview.md) för den här erbjudande typen, inklusive registrering av ett Developer-konto.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudandet-1** här, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Det går inte att ändra namnet på erbjudandet när du har valt **skapa**.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-overview"></a>Erbjudande översikt

På den här sidan visas en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och kommande) och hur lång tid varje steg ska ta att slutföra.

Den innehåller länkar för att utföra åtgärder på det här erbjudandet baserat på det val du gör. Till exempel:

- Om erbjudandet är ett utkast – [ta bort utkast erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Om erbjudandet är Live- [sluta att sälja erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Om erbjudandet är i för hands version- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Om du inte har slutfört Publisher-utloggning – [Avbryt publicering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="connect-lead-management"></a>Anslut lead-hantering

När du publicerar ditt erbjudande till Marketplace med partner Center måste du ansluta det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kund engagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system inte finns med i listan ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [https-slutpunkt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra kundens lead-data. Exportera sedan data till CRM-systemet.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center kommer vi att validera anslutningen och skicka ett test lead. Medan du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.
4. Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

Här följer några ytterligare resurser för ledar hantering:

- [Översikt över ledar hantering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över ledar hantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (kontrol lera att blockeringen av popup-fönster är inaktive rad)

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj minst en och högst tre kategorier. Dessa kategorier används för att placera ditt erbjudande i lämpliga sökområden för Marketplace och visas på sidan med erbjudande information. I erbjudande beskrivningen förklarar du hur ditt erbjudande stöder dessa kategorier.

### <a name="industry"></a>Industri

Du kan också välja upp till två branscher och två lodräta under varje bransch. Även om kategorier används för att visa ditt erbjudande, används branschen och de lodräta i Sök filter och används i butik. Om Erbjudandet riktar sig till en viss bransch och/eller lodrät använder du en beskrivning av erbjudandet för att förklara hur erbjudandet stöder de valda branscher eller lodräta. Lämna avsnittet tomt om ditt erbjudande inte är branschspecifika.

> [!NOTE]
> När vi arbetar för att introducera nya branscher och lodräta funktioner för att förbättra identifieringen av erbjudandet, kan vissa branscher och lodräta inte visas i butik ännu. Branscher och lodräta som är markerade med en (*) är tillgängliga vid ett framtida datum. Alla publicerade erbjudanden kan upptäckas via nyckelords sökning.
<p>&nbsp;

| **Industri** | **Under bransch** |
| --- | --- |
| * Bil | * Bil |
| Jordbruk | * Annat – segment |
| Distribution | * Grossist<br>Paket-och paket leverans |
| Education | *Högre utbildning <br> * Primär och sekundär utbildning/K-12<br>* Bibliotek och Museums |
| Ekonomiska tjänster | *Bank-och kapital <br> marknader* Försäkringsbokf |
| Myndigheter | *Försvar och information (som används för att kallas för nationell och offentlig <br> säkerhet)* Allmän säkerhet och rättvisa<br>* Civila myndigheter |
| Sjukvård (används för att kallas hälsa) | *Hälso betalare <br> * Hälso leverantör<br>* Farmaceutiska |
| Tillverkning och resurser (används för att kallas produktion) | *Kemisk och Agrochemical <br> * Diskret tillverkning<br>* Energi |
| Butiks-och konsument varor (som används för att kallas detalj handel) | *Konsument varor <br> * Detaljist |
| * Media och kommunikation (som används för att anropa media och underhållning) | *Media och underhållning <br> * Marknaden |
| Professionella tjänster | *Legal <br> * Partner Professional-tjänster |
| * Arkitektur och konstruktion (används för att kallas arkitektur teknik) | * Annat – segment |
| * Service och resa | *Hotell och fritid <br> * Resor och transport<br>* Restauranger och livsmedels tjänster |
| * Andra branscher i offentlig sektor | *Skogsbruk och fiske <br> * Ideella föreningar |
| * Fastigheter | * Annat – segment |

### <a name="legal"></a>Juridisk information

#### <a name="terms-and-conditions"></a>Villkor

Om du vill ange egna anpassade villkor anger du upp till 10 000 tecken i rutan **allmänna** villkor. Om dina villkor kräver en längre Beskrivning anger du en enskild webblänk dit de kan hittas. Den kommer att visas för kunder som en aktiv länk.

Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, erbjud listan.

## <a name="offer-listing"></a>Erbjudande lista

Här definierar du de erbjudande uppgifter som visas i Marketplace. Detta inkluderar erbjudandets namn, beskrivning, bilder och så vidare.

### <a name="language"></a>Språk

Välj det språk som ditt erbjudande ska visas i. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Definiera Marketplace-information (t. ex. namn, beskrivning och avbildningar för erbjudandet) för varje språk/marknad. Välj språk/marknads namn för att ange den här informationen.

> [!NOTE]
> Erbjudande information behöver inte vara på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det går också att tillhandahålla en användbar länk för att erbjuda innehåll på ett annat språk än det som används i erbjudande listan.

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

### <a name="search-keywords"></a>Sök efter nyckelord

Ange upp till tre valfria Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du också använda dessa nyckelord i beskrivningen.

### <a name="helpprivacy-web-addresses"></a>Hjälp/sekretess-webbadresser

Tillhandahåller länkar som hjälper kunder att bättre förstå ditt erbjudande.

#### <a name="help-link"></a>Hjälp länk

Ange webb adressen där kunderna kan lära sig mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange webb adressen till din organisations sekretess policy. Du är ansvarig för att se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du ansvarar också för att publicera en giltig sekretess policy på din webbplats.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas av CSP-partner (Cloud Solution Provider).

- Support kontakt (krävs): för allmänna supportfrågor.
- Teknisk kontakt (krävs): för tekniska frågor och certifierings problem.
- Kontakta CSP-programmet (valfritt): för åter försäljar frågor som är relaterade till CSP-programmet.

I avsnittet **support kontakt** anger du webb adressen till **Support webbplatsen** där partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett och upp till tre relaterade marknadsförings dokument i PDF-format. Till exempel fakta blad, broschyrer, check listor eller presentationer.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange. png-filer för ditt erbjudandes logo typ i två bild punkter:
- **Liten** (48 x 48)
- **Stor** (216 x 216)

Båda logo typerna krävs och används på olika platser i Marketplace-listan.

#### <a name="screenshots"></a>Skärmbilder

Lägg till minst en och upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 bild punkter i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som demonstrerar ditt erbjudande. Ange videons namn, webb adress och miniatyr bild av videon med 1280 x 720 pixlar i storlek.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

Om du vill veta mer om att skapa erbjudande listor, se [erbjudande lista med bästa praxis](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Teknisk konfiguration

Flytta upp din app i Power BI tjänst till produktion och ange den Power BI App Installer-länken som gör det möjligt för kunderna att installera din app. Mer information finns i [publicera appar med instrument paneler och rapporter i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Kompletterande innehåll

Ange ytterligare information om ditt erbjudande för att hjälpa oss att validera det. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="validation-assets"></a>Validerings till gångar

Du kan också lägga till instruktioner (upp till 3 000 tecken) för att hjälpa Microsoft validerings teamet att konfigurera, ansluta och testa din app. Inkludera vanliga konfigurations inställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut data. Den här informationen visas bara för validerings teamet och används endast i validerings syfte.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka ditt erbjudande till granskning och publicering.

I det övre högra hörnet av portalen väljer du **Granska och publicera**.

På sidan Granska kan du:

- Se slut för ande status för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har marker ATS som slutförda.
  - **Inte startad** -avsnittet har inte startats och måste slutföras.
  - **Ofullständig** – avsnittet innehåller fel som måste åtgärdas eller kräver att du anger mer information. Se avsnittet tidigare i det här dokumentet för vägledning.
  - **Slutfört** -avsnittet innehåller alla data som krävs och det finns inga fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka in erbjudandet.
- Ange test instruktioner till certifierings teamet för att se till att appen testas korrekt. Ange också eventuella kompletterande kommentarer som hjälper dig att förstå ditt erbjudande.

Om du vill skicka publicerings erbjudandet väljer du **publicera**.

Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Om du vill publicera erbjudandet till allmänheten (eller om ett privat erbjudande till en privat mål grupp) går du till Partner Center och väljer **Go-Live**.
