---
title: Skapa ett nytt SaaS-erbjudande på den kommersiella Marketplace
description: Så här skapar du ett nytt SaaS-erbjudande (Software as a Service) för att lista eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace-portalen på Microsoft Partner Center.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7dd57e41144ec33aec6eb716716d0794d92071f5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013093"
---
# <a name="create-a-new-saas-offer"></a>Skapa ett nytt SaaS-erbjudande

För att börja skapa SaaS-erbjudanden (program vara som en tjänst) måste du först [skapa ett partner Center-konto](./create-account.md) och öppna [instrument panelen för instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)med fliken **Översikt** vald.

![Instrument panel för extern Marketplace på Partner Center](./media/new-offer-overview.png)

Välj det + **nya erbjudandet...** och välj sedan meny alternativet **program vara som en tjänst** . 

Om du väljer en annan erbjudande typ kan du omdirigeras till den äldre [Cloud Partner Portal](https://cloudpartner.azure.com/). Endast SaaS-och Dynamics 365-erbjudanden är tillgängliga i den kommersiella Marketplace för partner Center för tillfället.

![Skapa erbjudande fönster i Partner Center](./media/new-offer-click.png)

Dialog rutan **nytt erbjudande** visas. 

![Dialog rutan Nytt erbjudande](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

- **Erbjudande-ID**: Unik identifierare för varje erbjudande i ditt konto. Detta ID visas för kunder i URL-adressen för Marketplace-erbjudandet och Azure Resource Manager mallar (om tillämpligt). Erbjudande-ID måste vara gemener, alfanumeriska tecken (inklusive bindestreck och under streck, men inget blank steg). Detta är begränsat till 50 tecken och kan inte ändras när du har valt *skapa*.  
Exempel: test-erbjudande-1
<br>Resulterar i URL: en:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Erbjud alias**: Namnet som används för att referera till erbjudandet i Partner Center-portalen. Det här namnet används inte på Marketplace och skiljer sig från namnet på *erbjudandet* och andra värden som ska visas för kunderna. Det går inte att ändra det här värdet när du har valt *skapa*.

<br>Exempel: Test erbjudande 1&#8482;

Välj **Skapa**.  Sidan **erbjudande översikt** skapas för det här erbjudandet.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Erbjudande översikt

Sidan **erbjudande översikt** innehåller: 

- **Publicerings status** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet och hur lång tid varje steg tar att slutföra. Ofullständiga publicerings stegs ikoner blir grå. 

- Menyn **erbjudande översikt** innehåller en lista med länkar för att utföra åtgärder på det här erbjudandet. Den här listan över åtgärder ändras baserat på det val du gör för ditt erbjudande.  
    - Om erbjudandet är ett utkast – ta bort utkast 
    - Om erbjudandet är Live – stoppa Sälj erbjudandet 
    - Om erbjudandet är i för hands version – Go-Live 
    - Om du inte har slutfört utgivar utloggning – Avbryt publicering

## <a name="offer-setup"></a>Erbjudande konfiguration

På fliken **erbjudande konfiguration** ställer du in följande information. Välj **Spara** när du är klar med fälten.

- **Vill du sälja via Microsoft?** (Ja/Nej)
    - **Ja**, du vill sälja ditt erbjudande via Microsoft, med Microsoft hosting Marketplace-transaktioner för din räkning. eller 
    - **Nej**, du föredrar att bara lista ditt erbjudande via Marketplace, bearbeta alla monetära transaktioner oberoende av Microsoft.    

### <a name="sell-through-microsoft"></a>Sälj via Microsoft

Genom att sälja via Microsoft får du bättre kund identifiering och förvärv, som gör det möjligt för Microsoft att vara värd för Marketplace-transaktioner för din räkning och dra nytta av Microsofts globalt tillgängliga funktioner i Commerce.

#### <a name="saas-offer-requirements"></a>Krav för SaaS-erbjudande

Följande kriterier måste vara uppfyllda för att kunna visa SaaS-erbjudanden (Software as a Service) med kommersiell marknads plats på Partner Center:

- Ditt erbjudande måste använda [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) för identitets hantering och autentisering.
- Ditt erbjudande måste använda [SaaS-API: er](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) för att integrera med Azure Marketplace.
- Mer omfattande krav finns i publicerings [guiden för SaaS-erbjudandet](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Pris-och fakturerings alternativ för SaaS
Med SaaS-lösningar som körs i utgivarens Azure-prenumeration inkluderar licens avgifter som betalas av kunderna kostnaden för den infrastruktur som program varan distribueras på. Användningen av Azure-infrastrukturen hanteras och faktureras till dig, partnern direkt. Den faktiska infrastruktur användnings avgiften ses inte av kunden. Utgivare ska paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser. 

SaaS erbjuder stöd för månatlig eller årlig fakturering baserat på en fast avgift, per användare eller förbruknings avgifter med hjälp av den avgiftsbelagda fakturerings tjänsten. Microsofts kommersiella marknads plats fungerar på en byrå modell, där utgivare anger priser, Microsoft fakturerar kunder och Microsoft betalar intäkterna till utgivaren samtidigt som en byrå avgift debiteras.

I följande tabell visas ett exempel på uppdelning av kostnader och utbetalningar för att demonstrera byråns modell.

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
> **Minskad service avgift för Marketplace**: För vissa SaaS-erbjudanden som du har publicerat på vår kommersiella marknads plats kommer Microsoft att minska sin service avgift för Marketplace från 20% (enligt beskrivningen i Microsoft Publisher Agreement) till 10%. För att ditt erbjudande ska bli kvalificerat, måste minst ett av dina erbjudanden ha utsetts av Microsoft som en prioriterad eller IP-samkund i samma skick.  Berättigande måste uppfyllas minst fem (5) arbets dagar före slutet av varje kalender månad för att få denna minskade service avgift för Marketplace för månaden.  Den minskade service avgiften för Marketplace gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknads plats.  Den minskade service avgiften för Marketplace kommer bara att vara tillgänglig för kvalificerade erbjudanden för licens avgifter som samlas in av Microsoft mellan den 1 maj 2019 och 30 juni 2020.  Efter den här tiden kommer Marketplace-tjänstens avgift att återgå till sin normala mängd. 




#### <a name="csp-program-opt-in"></a>Deltagande i CSP-programmet
[Cloud Solution Provider (CSP)-](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programmet gör det möjligt för program erbjudanden att uppnå miljon tals kvalificerade Microsoft-kunder med minimal marknadsföring och försäljnings investeringar.

- **Kanal Gör mitt erbjudande tillgängligt i CSP-programmet** (kryss ruta)

Om du väljer att göra ditt erbjudande tillgängligt i CSP-programmet kan moln lösnings leverantörer sälja din produkt som en del av en samlad lösning till sina kunder. 

### <a name="list-through-microsoft"></a>Lista via Microsoft

Marknadsför ditt företag med Microsoft genom att skapa en Marketplace-lista. Om du väljer att bara lista ditt erbjudande och inte Transact via Microsoft innebär det att Microsoft inte deltar direkt i program licens transaktioner. Det finns ingen kopplad transaktions avgift och utgivaren behåller 100% av alla licens avgifter som samlas in från kunden. Utgivaren ansvarar dock för att stödja alla aspekter av program licens transaktionen, inklusive men inte begränsat till: order uppfyllelse, mätning, fakturering, fakturering, betalning och insamling. 

- **Hur vill du att potentiella kunder ska kunna interagera med det här registrerings erbjudandet?**

##### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)
Lista ditt erbjudande till kunder kostnads fritt genom att tillhandahålla en giltig URL (från och med *http* eller *https*) där de kan komma åt din app.  Exempel: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Kostnads fri utvärderings version (lista)
Lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version genom att tillhandahålla en giltig URL (från och med *http* eller *https*), där de kan få en utvärdering genom att [använda Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Till exempel: `https://contoso.com/trial/saas-app`. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure AD för att automatisera skapandet av konton i din app. Microsoft-konton (MSA) stöds inte för autentisering med denna token.

##### <a name="contact-me"></a>Kontakta mig
Samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [Anslut lead management](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Exempel lista för Marketplace-erbjudanden

![Exempel lista för Marketplace-erbjudanden med anteckningar](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Aktivera en testen het

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. [Läs mer om test enheter.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Aktivera en testen het** Rute 

Genom att aktivera test enheten uppmanas du att konfigurera en demonstrations miljö där kunder kan testa ditt erbjudande under en bestämd tids period. 

### <a name="type-of-test-drive"></a>Typ av test enhet

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser.
- **[Dynamics 365 för företag, Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett företags organisations planerings system för företag (finans, drift, leverans kedja, CRM osv.).  
- **[Dynamics 365 för kund engagemang](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett kund engagemang system (Sales, service, Project service, Field service osv.).  
- **[Dynamics 365 för åtgärder](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett ekonomi-och drift företags resurs planerings system (finans, drift, tillverkning, leverans kedja osv.). 
- **[Logic app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : En distributions mall som omfattar alla komplexa lösnings arkitekturer. Alla anpassade produkter bör använda den här typen av test enhet.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : En inbäddad länk till en anpassad instrument panel. Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt bör använda den här typen av test enhet. Allt du behöver ladda upp här är din inbäddade Power BI-URL.

#### <a name="additional-test-drive-resources"></a>Ytterligare resurser för enhets test
- [Test Drive tekniska metod tips](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metod tips för marknadsföring av test enheter](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över test enhet en pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Anslut lead-hantering

Anslut till kunder direkt genom att ange ditt erbjudande i Marketplace och koppla upp ditt kund Relations hanterings system (CRM) så att du kan ta emot kund kontakt uppgifter direkt efter att en kund har ett intresse eller distribuerar din momsproduktbokföringsmallar.

- **Välj ett mål för lead** (nedrullningsbar meny): Ange anslutnings information till det CRM-system där du vill att vi ska skicka kund leads. 

Partner Center stöder följande CRM-system för hantering av leads. Välj länken för installations anvisningar.

- Azure Blob – ange e-postadressen för kontakt, container namn och lagrings konto anslutnings sträng. 
- [Azure-tabell](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – ange anslutnings sträng för kontakt-och lagrings konto. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – ange e-postadress till kontakt, URL och autentiseringsläge (Office 365 eller Azure Active Directory).
- [Https-slutpunkt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – ange URL för kontakt-e-postadress och HTTPS-slutpunkt. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – ange e-postadress, formulär-ID, Munchkin-konto-ID och Server-ID.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) – ange kontakt-e-post och organisations-ID. 

#### <a name="additional-lead-management-resources"></a>Ytterligare resurser för ledar hantering
- [Vanliga frågor och svar om lead management](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga konfigurations fel för lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över ledar hantering en personsökare](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Kom ihåg att **Spara** innan du går vidare till nästa avsnitt!

## <a name="properties"></a>properties
På fliken **Egenskaper** uppmanas du att definiera de kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, de juridiska avtalen som stöder ditt erbjudande och din app-version. 

Välj **Spara** när du är klar med fälten. 

### <a name="category"></a>Category
Välj minst en (1) och högst tre (3) kategorier som används för att gruppera ditt erbjudande i lämpliga sökområden för Marketplace. Ta reda på hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. 

### <a name="industry"></a>Bransch
Välj upp till två (2) branscher som används för att gruppera ditt erbjudande i lämpliga Sök områden för Marketplace. Om erbjudandet inte är speciellt för en bransch ska du inte välja ett. Ta reda på hur ditt erbjudande stöder de valda branscher i beskrivningen av erbjudandet. 

### <a name="app-version"></a>Appversion
Detta är ett valfritt fält som används i AppSource Marketplace för att identifiera versions numret för ditt erbjudande. 

### <a name="standard-contract"></a>Standardavtal

- **Använd standard kontrakt?**

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft en standard kontrakts mal len för att hjälpa till att under lätta en transaktion i Marketplace. 

I stället för att utforma anpassade villkor kan Azure Marketplace-utgivare välja att erbjuda sin program vara under standard kontraktet, som kunderna bara behöver Undersök och godkänna en gång. 

Du hittar standard kontraktet här: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Användningsvillkor

Om licens villkoren skiljer sig från standard kontraktet kan du välja att ange egna juridiska användnings villkor här. Du kan också ange upp till 10 000 tecken text i det här fältet. Om dina användnings villkor kräver en längre Beskrivning anger du en enda URL-länk till det här fältet där du kan hitta ytterligare licens villkor. Den kommer att visas för kunder som en aktiv länk.

Kunder måste acceptera dessa villkor innan de kan testa din app. 

Kom ihåg att **Spara** innan du går vidare till nästa avsnitt!

## <a name="offer-listing"></a>Erbjudande lista

På fliken erbjudande lista visas de språk (och marknader) där ditt erbjudande är tillgängligt, för närvarande är engelska (USA) den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera Marketplace-information (erbjudande namn, beskrivning, Sök villkor osv.) för varje språk/marknad.

> [!NOTE]
> Erbjudande för List innehåll (till exempel Beskrivning av erbjudande, dokument, skärm bilder, användnings villkor och sekretess policy) behöver inte vara på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [icke-engelskt språk]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

### <a name="offer-listings"></a>Erbjudande listor

Ange information som ska visas på Marketplace, inklusive beskrivningar av ditt erbjudande och marknadsförings till gångar.

- **Namn** (krävs): Namnet som definieras här visas som rubrik på din erbjudande lista på de Marketplace som du har valt. Namnet är förifyllt baserat på din föregående **nya erbjudande** post.  Detta kan vara ett varumärke.  Det får inte innehålla blank steg, emojis (om de inte är varumärken och Copyright-symboler) och måste vara begränsade till 50 tecken.
- **Sammanfattning** (krävs): Ange en kort beskrivning av ditt erbjudande som ska användas i Sök resultaten för Marketplace-listor. Upp till 100 tecken text kan anges i det här fältet.
- **Beskrivning** (krävs): Ange en beskrivning av ditt erbjudande som ska visas i Översikt över Marketplace-listor. Överväg att inkludera ett värde förslag, viktiga fördelar, eventuella kategori-eller bransch associationer, inköps möjligheter för appar, eventuella obligatoriska upplysningar och en länk till mer information.
Upp till 3 000 tecken text kan anges i det här fältet. Ytterligare tips finns i artikeln [skriva en bra app-Beskrivning](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Sök efter nyckelord**: Ange upp till tre sökord som kunder kan använda för att hitta ditt erbjudande i Marketplace.
- **Komma igång-instruktioner** (krävs): Förklara hur du konfigurerar och börjar använda din app för potentiella kunder.  Den här snabb starten kan innehålla länkar till mer detaljerad onlinedokumentation. Upp till 3 000 tecken text kan anges i det här fältet. 

#### <a name="description"></a>**Beskrivning**

Det här fältet är obligatoriskt. Objekt som ska tas med i beskrivningen: 

* Beskriv tydligt ditt erbjudande värde i de första meningarna i beskrivningen.  
* Tänk på att de första meningarna kan visas i Sök motorns resultat.  
* Förlita dig inte på funktioner och funktioner för att sälja produkten. Fokusera i stället på det värde som du levererar.  
* Använd branschspecifika vokabulär eller kunskapsbaserade formuleringar så mycket som möjligt. 

Kärn komponenterna i dina värde förslag bör innehålla följande information: 

* Beskrivning av produkten. 
* Typ av användare som fördelar från produkten. 
* Kunden behöver eller målar produkten om produkt adressen. 

Om du vill göra din erbjudande Beskrivning mer engagerande kan du använda HTML-taggar för att formatera beskrivningen. 

1. Om du vill skapa stycken lägger du till `<p>` i begging i texten och lägger till `</p>` i slutet.

    **Exempel**: 

    `<p>`Detta är mitt första stycke. `</p>` <br>
    `<p>`Det här är mitt andra stycke. `</p>` <br>

    Ovanstående skulle se ut så här:

    <p> Detta är mitt första stycke. </p>
    <p> Det här är mitt andra stycke. </p>

1. Om du vill lägga till en **punkt lista med objekt**placerar du texten i `<li>` taggarna nedan. Du kan kopiera och klistra in fler punkt objekt `<li>` (objekt mellan taggarna och `</li>` ) i `<ul>` taggarna `</ul>` och. Se till att du lägger `<ul></ul>`till. 

    **Exempel**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    Ovanstående skulle se ut så här:
    <ul> 
        <li>Lägg till text här</li> 
        <li> Lägg till text här </li> 
        <li> Lägg till text här </li> 
    </ul> 

1. Till **fetstil** , Lägg till `<b>` i början av den text som du vill använda fetstil och Lägg `</b>` till i slutet av den text som du vill använda fetstil. 

    **Exempel**: `<b>`KOSTNADS FRI UTVÄRDERING`</b>`
    
    Ovanstående skulle medföra att den kostnads fria utvärderings versionen är fet i beskrivningen av erbjudandet i butik. 

    **KOSTNADS FRI UTVÄRDERING**

1. Om du vill lägga till **rad brytningar** mellan `<br>` innehållet lägger du till före det innehåll som du vill starta på en ny rad. Om du vill lämna ett blank steg och se till att innehållet börjar på en ny rad lägger `<br><br>` du till före innehållet. 

    **Exempel**:

    Detta är en textrad. `<br>`Detta är en text rad som börjar på en ny rad. `<br><br>`Det här är en rad som kommer att starta två rader nedan. 

    Ovanstående skulle se ut så här:

    Detta är en textrad. <br> Detta är en text rad som börjar på en ny rad. <br><br> Det här är en rad som kommer att starta två rader nedan. 

1. Om du vill **öka storleken på texten**väljer du först hur stor du vill att texten ska vara. Använd exemplen nedan. När du har valt textens storlek lägger du till motsvarande `<H*></H*>` Taggar i början och slutet av texten. 

    **Exempel**:

    `<h1>`Detta är rubrik 1`</h1>` <br>
    `<h2>`Detta är rubrik 2`</h2>` <br>
    `<h3>`Detta är rubrik 3`</h3>` <br>
    `<h4>`Detta är rubrik 4`</h4>` <br>
    `<h5>`Detta är rubrik 5`</h5>` <br>
    `<h6>`Detta är rubrik 6`</h6>` 

    Ovanstående skulle se ut så här:

    ![Exempel rubriker](./media/heading.png)

#### <a name="links"></a>Länkar

- **Sekretess policy** (krävs): Länk till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy
- **Marknadsförings material för CSP-program** (valfritt): Du måste ange en länk till marknadsförings material om du väljer att utöka erbjudandet till programmet för [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . CSP utökar erbjudandet till ett bredare utbud av kvalificerade kunder genom att göra det möjligt för CSP-partner att paketera, marknadsföra och sälja ditt erbjudande. Dessa åter försäljare behöver åtkomst till material för marknadsföring av ditt erbjudande. Mer information finns i [Go-to-Marketing-tjänster](https://partner.microsoft.com/reach-customers/gtm).
- **Användbara länkar** (valfritt): Valfria kompletterande online-dokument om din app eller relaterade tjänster som listas genom att tillhandahålla en **rubrik** och **URL**. Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en URL**.

#### <a name="contact-information"></a>Kontaktinformation

- **Kontakter**: Ange ett **namn** på en anställd, **telefonnummer**och **e-** postadress för varje kund kontakt.  (De *kommer inte* att visas offentligt). Det krävs också en **Support-URL** för **support kontakt** gruppen.  (Den här informationen *kommer* att visas offentligt).

**Support kontakt** (krävs): För allmänna supportfrågor.

**Teknisk kontakt** (krävs): För tekniska frågor.

**Kontakt i kanal hanteraren** (krävs): För åter försäljar frågor som är relaterade till CSP-programmet.

#### <a name="files-and-images"></a>Filer och avbildningar

- **Dokument** (krävs): Lägg till relaterade marknadsförings dokument för ditt erbjudande i PDF-format, och ange minst ett (1) och högst tre (3) dokument per erbjudande.
- **Bilder** (valfritt): Det finns flera platser där ditt erbjudandes Logo bilder kan visas på alla platser, vilket kräver följande storlekar – liten: 48 x 48 pixlar _(krävs),_ medel: 90 x 90 pixlar, stor: 216 x 216 pixlar _(krävs),_ bred: 255 x 115 pixlar och hjälte: 815 x 290 bild punkter. Alla avbildningar måste vara i. PNG-format.
- **Skärm bilder** (krävs): Lägg till skärm bilder som demonstrerar ditt erbjudande. Högst fem (5) skärm bilder kan läggas till och ska vara i storlek med 1280 x 720 pixlar. Alla avbildningar måste vara i. PNG-format.
- **Videor** (valfritt): Lägg till länkar till videor som demonstrerar ditt erbjudande. Du kan använda länkar till YouTube och/eller Vimeo-videor som visas tillsammans med ditt erbjudande till kunder. Du måste också ange en miniatyr bild av videon, med en storlek på 1280 x 720 pixlar i PNG-format. Du kan visa högst fyra videor per erbjudande.

Kom ihåg att **Spara** innan du går vidare till nästa avsnitt!

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

- [Metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Förhandsversion

På fliken förhands **granskning** kan du definiera en begränsad förhands **gransknings grupp** för att släppa ditt erbjudande innan du publicerar erbjudandet Live till den bredare Marketplace-publiken.

> [!IMPORTANT]
> Du måste välja **Go Live** innan ditt erbjudande publiceras Live till offentlig Marketplace-publik när du har kontrollerat ditt erbjudande i för hands versionen.

- **Definiera en förhands gransknings mål grupp: Lägg till ett enda AAD/MSA-konto per rad, tillsammans med en valfri beskrivning.**

Lägg till upp till tio (10) e-postadresser manuellt, eller 20 (20) om du överför en CSV-fil, för befintligt Microsoft-konto (MSA) eller Azure Active Directory-konton som hjälper dig att verifiera ditt erbjudande innan du publicerar Live. Genom att lägga till dessa konton definierar du en mål grupp som ska få för hands versions åtkomst till ditt erbjudande innan den publiceras på Marketplace (s). Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

> [!NOTE]
> Förhands gransknings publiken skiljer sig från en privat publik. En förhands gransknings mål grupp har åtkomst till ditt erbjudande _innan_ de publiceras Live på Marketplace. Du kan också välja att skapa en plan och bara göra den tillgänglig för en privat publik. På fliken **plan lista** kan du definiera en privat mål grupp med kryss rutan för **privat plan** . Du kan sedan definiera en privat mål grupp med upp till 20 000 kunder med hjälp av Azure klient-ID.

## <a name="technical-configuration"></a>Teknisk konfiguration

På fliken **teknisk konfiguration** definieras den tekniska informationen (URL-sökväg, webhook, klient-ID och app-ID) som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det. Diagram som beskriver användningen av de insamlade fälten finns i dokumentationen för [API: er för SaaS-utförande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL för landnings sida** (krävs): Definiera webbplats-URL: en som kunderna hamnar på efter att ha skaffat ditt erbjudande från Marketplace. Den här URL: en är slut punkten som tar emot en token när en kund dirigeras till sidan. Denna token kan bytas ut mot etablerings information med hjälp av lösa i API: er som uppfyller kraven. Informationen och andra som du samlar in kan användas som en del av en kundinteraktiv webb sida som skapats i din upplevelse för att slutföra registreringen och aktivera köpet.

- **Anslutning** -webhook (krävs): För alla asynkrona händelser som Microsoft behöver skicka till dig för kundens räkning (exempel: SaaS-prenumerationen är ogiltig), vi kräver att du anger en anslutning-webhook. Om du inte redan har ett webhook-system på plats är den enklaste konfigurationen att ha en http-slutpunkt som lyssnar efter händelser som publiceras till den och sedan hantera dem på rätt sätt (t. ex. https:\//Prod-1westus.Logic.Azure.com:443/Work). Mer information finns i [anropa, utlösa eller kapsla arbets flöden med HTTP-slutpunkter i Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID för Azure AD-klient** (krävs): Inom Azure Portal kräver vi att du [skapar en Azure Active Directory (AD)-app](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) så att vi kan verifiera anslutningen mellan våra två tjänster bakom en autentiserad kommunikation. Du hittar [klient-ID: t](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)genom att gå till din Azure Active Directory och välja **Egenskaper**och leta efter **katalog-ID** -numret i listan (t. ex. 50c464d3-4930-494c-963c-1e951d15360e).

- **ID för Azure AD-App** (krävs): Du behöver också ditt [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) och en autentiseringsnyckel. Om du vill hämta dessa värden går du till Azure Active Directory och väljer **Appregistreringar**och letar sedan efter det **program-ID** som anges (t. ex. 50c464d3-4930-494c-963c-1e951d15360e). Om du vill hitta autentiseringsnyckel går du till **Inställningar** och väljer **nycklar**. Du måste ange en beskrivning och varaktighet och kommer sedan att tillhandahålla ett Number-värde.

 Observera att Azure-programmets ID är associerat med ditt utgivar-ID, så se till att samma program-ID används i alla dina erbjudanden.

## <a name="plan-overview"></a>Plan översikt

På fliken **plan översikt** kan du ange olika plan alternativ inom samma erbjudande. Dessa planer (kallas ibland SKU: er) kan variera beroende på version, monetarisering eller tjänst nivåer. Du måste konfigurera minst en plan för att kunna sälja ditt erbjudande i Marketplace.

När du har skapat dina prenumerations namn, ID: n, pris modeller, tillgänglighet (offentlig eller privat), aktuell publicerings status och alla tillgängliga åtgärder.

Vilka **åtgärder** som är tillgängliga i **plan översikten** varierar beroende på planens aktuella status och kan vara:

- Om plan status är **Draft** – ta bort utkast
- Om plan status är **Live** – stoppa Sälj plan eller synkronisera privat publik

**Skapa ny plan** (minst en plan för de som väljer att sälja via Microsoft)

- **Plan-ID:** Skapa ett unikt plan-ID för varje plan i det här erbjudandet. Detta ID visas för kunder i produkt-URL och Azure Resource Manager mallar (om tillämpligt). Använd endast gemener, alfanumeriska tecken, bindestreck eller under streck. Högst 50 tecken tillåts för det här plan-ID: t. Observera att ID: t inte kan ändras när du har valt skapa.
- **Plan namn:** Kunder ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt erbjudande namn för varje plan i det här erbjudandet. Plan namnet används för att särskilja program varu planer som kan vara en del av samma erbjudande (t. ex. Erbjudandets namn: Windows Server; utgå Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Plan lista

På fliken **plan lista** visas de språk (och marknader) där ditt abonnemang är tillgängligt, för närvarande är engelska (USA) den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera Marketplace-information (erbjudande namn, beskrivning, Sök villkor osv.) för varje språk/marknad.

#### <a name="plan-listing-details"></a>Information om plan lista

Om du väljer ett av plan språken visas **planens registrerings** information, inklusive **namn** och **Beskrivning.**

- **Namn på**: Fylls i automatiskt baserat på din förhands gransknings **nya plan** och visas som rubrik för ditt erbjudandes "program plan" på Marketplace.
- **Beskrivning:** Den här beskrivningen är en möjlighet att förklara vad som gör att den här program varu planen är unik och eventuella skillnader jämfört med andra program varu planer i erbjudandet. Får innehålla upp till 500 tecken.

Välj **Spara** när du är klar med fälten.

#### <a name="plan-pricing-and-availability"></a>Planera priser och tillgänglighet

På fliken **priser och tillgänglighet** kan du konfigurera de marknader som planen kommer att vara tillgänglig i, den önskade uppräknings modellen, priset och fakturerings perioden. Dessutom kan du ange om du vill att planen ska vara synlig för alla eller bara för vissa kunder (en privat mål grupp).

##### <a name="enabling-free-trials"></a>Aktivera kostnads fria utvärderingar

SaaS erbjudanden via den kommersiella marknads platsen gör att du kan tillhandahålla en månads kostnads fri utvärderings version när du säljer via Microsoft. För alla fakturerings modeller och villkor förutom mätnings planer stöds kostnads fria utvärderings versioner. Med det här alternativet kan kunder ha en låg barriär för att komma in genom en månads kostnads fri åtkomst.  Om du väljer att aktivera en kostnads fri utvärderings version för planer i ditt erbjudande kan kunden inte konvertera till en betald prenumeration innan den första en månads period slutar att gälla.  Under den här tiden kan kunder som köper erbjudandet prova över de planer som stöds och som har den kostnads fria utvärderings versionen aktive rad och konvertera mellan dem.  Konverteringen till en betald prenumeration görs automatiskt i slutet av perioden.

>[!Note]
>Om kunden väljer att konvertera till en plan utan kostnads fria utvärderings versioner sker konverteringen, men den kostnads fria utvärderings versionen går förlorad omedelbart.  När en kund börjar betala för en plan kan de inte längre få en kostnads fri utvärderings version på samma prenumeration igen, även om de konverteras till en SKU som stöder kostnads fria utvärderings versioner.

Möjligheten att konfigurera en kostnads fri utvärderings version är tillgänglig för varje plan i erbjudandet. Du behöver bara gå till pris och tillgänglighet för varje erbjudande och markera kryss rutan för en månads utvärdering.

![En månads kostnads fri utvärderings ruta](./media/free-trial-enable.png)

För att få information om kund prenumerationer som för närvarande ingår i en kostnads fri utvärderings `isFreeTrial`version använder du den nya API-egenskapen som kommer att markeras som sant eller falskt. Mer information finns i [SaaS Hämta prenumerations-API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) .

>[!Note]
>Kostnads fria utvärderings versioner stöds inte för planer som utnyttjar Marketplace-avläsning.

#### <a name="markets"></a>Marknaden

- **Redigera marknader** valfritt

Varje plan måste vara tillgänglig på minst en marknad. Markera kryss rutan för alla marknads platser där du vill att den här planen ska vara tillgänglig. En sökruta och knapp för att välja "skatte mottagare", där Microsoft betalar försäljnings-och användnings skatt för din räkning, ingår i hjälpen. 


Om du redan har angett priser för din plan i USA dollar (USD) och lagt till en annan marknads plats kommer priset för den nya marknaden att beräknas enligt de aktuella växelkurserna. Du bör alltid granska priset för varje marknad innan du publicerar. Priserna kan granskas med hjälp av länken "export priser (xlsx)" när du har sparat ändringarna.

#### <a name="pricing"></a>Prissättning

- **Pris modell**: Fast pris eller plats baserad

**Fast pris:** Ge åtkomst till ditt erbjudande med ett enkelt pris per månad eller årligt pris. Detta kallas ibland för platsbaserade priser. Med den här pris sättnings modellen kan du välja att definiera förmätnings planer som använder API: et för avläsning av program vara för att debitera kunder enligt andra enheter än standard enheter.  Mer information om avgiftsbelagd fakturering finns i [mäta fakturering med Marketplace-avläsning](./saas-metered-billing.md).

**Per användare:** Ge åtkomst till ditt erbjudande med priset baserat på antalet användare som har åtkomst till erbjudandet eller med platser. Med den här användarbaserade modellen kan du ange det lägsta och högsta antalet användare som tillåts baserat på priset. På så sätt kan olika pris punkter konfigureras baserat på antalet användare genom att konfigurera flera planer.  Dessa fält är valfria. Om alternativet inte är markerat tolkas antalet användare som att de inte har en gräns (minst 1 och högst lika många som systemet kan stödja). De här fälten kan redige ras som en del av en uppdatering av planen.

När du har publicerat det kan du inte ändra fakturerings pris modellen. Dessutom måste alla planer för samma erbjudande dela samma pris modell.

- **Fakturerings period**: Månatlig eller årlig

Välj den frekvens som kunderna måste betala i det angivna priset. Minst ett månatligt eller årligt pris måste tillhandahållas, eller så kan båda alternativen göras tillgängliga för kunderna.

- **Pris**: USD per månad eller USD per år

Priser som anges i lokal valuta (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna som är tillgängliga under installationen. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar du och importerar pris kalkyl bladet. Du ansvarar för att verifiera priset och äga dessa inställningar.
**Du måste först spara dina pris ändringar för att möjliggöra export av pris information.*

Granska dina priser noggrant innan du publicerar, eftersom det finns vissa begränsningar för vad som kan ändras när en plan publiceras:

- När en plan har publicerats går det inte att ändra pris sättnings modellen.
- När en fakturerings period har publicerats för en plan kan du inte ta bort den senare.
- När ett pris för en marknad i planen har publicerats kan det inte ändras senare.

### <a name="plan-audience"></a>Planera mål grupp

Du har möjlighet att konfigurera varje plan så att den blir synlig för alla eller bara för en speciell grupp som du väljer. Du kan tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azure AD-klient-ID: n.

#### <a name="privacy"></a>Sekretess

- **Det här är en privat plan** (Valfri kryss ruta)

Markera den här kryss rutan om du vill att planen ska vara privat och endast synlig för den begränsade gruppen som du väljer. När du har publicerat som privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan publiceras som synlig för alla måste den vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

- **Begränsad mål grupp (klient-ID)**

Tilldela den mål grupp som ska ha åtkomst till den här privata planen. Åtkomst tilldelas med klient-ID: n med alternativet att inkludera en beskrivning av varje tilldelad klient-ID. Högst 10 klient-ID: n kan läggas till, eller 20 000 kunders klient-ID om du importerar en. csv-kalkylbladsfilen.

En klient organisation är en representation av en organisation med ett ID som representeras som GUID (globalt unik identifierare, ett 128-bitars heltal som används för att identifiera resurser). Det är en dedikerad instans av Azure AD som en organisation eller apputvecklare får när organisationen eller apputvecklaren skapar en relation med Microsoft – som att registrera sig för Azure, Microsoft Intune eller Microsoft 365. Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter. Om du vill kontrol lera klient organisationen loggar du in på Azure Portal med det konto som du vill använda för att hantera ditt program. Om du har en klient kommer du automatiskt att loggas in och kan se klientorganisationens namn direkt under namnet på ditt konto. Hovra över ditt kontonamn längst upp till höger i Azure-portalen så visas namn, e-post, katalog/klient-ID (ett GUID) och domän. Om ditt konto är kopplat till flera klienter måste du välja namnet på ditt konto för att öppna en meny där du kan växla mellan klienter. Varje klient har sitt eget klient-ID. Du kan också leta upp organisationens klient-ID med en domän namns-URL [https://www.whatismytenantid.com](https://www.whatismytenantid.com)på:.

Även om SaaS-erbjudanden använder klient-ID: n för att definiera en privat mål grupp kan andra erbjudande typer använda Azures prenumerations-ID (som också visas som GUID).

> [!NOTE]
> Den privata mål gruppen (eller den begränsade mål gruppen) skiljer sig från en förhands gransknings publik. På fliken **[förhands granskning](#preview)** kan du definiera en förhands gransknings mål grupp. En förhands gransknings mål grupp har åtkomst till ditt erbjudande *innan* erbjudandet publiceras Live på Marketplace. Även om den privata mål behållaren endast gäller för en viss plan, kan förhands gransknings publiken Visa alla planer (privata eller inte), men endast under den begränsade för hands perioden medan planen testas och verifieras.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exempel lista över planer i ett Marketplace-erbjudande

![Exempel på Marketplace-plan lista med anteckningar](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Testkör

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="publish"></a>Publicera

#### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **publicera** i det övre högra hörnet i portalen. Du omdirigeras sedan till sidan **Granska och publicera** . 

Om det här är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - *Inte startad* – innebär att avsnittet inte har vidrör ATS och måste slutföras.
    - *Ofullständig* – innebär att avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - *Complete* – innebär att avsnittet har slutförts, att alla nödvändiga data har angetts och att det inte finns några fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- Tillhandahåll test instruktioner till certifierings teamet för att se till att appen testas korrekt, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Du måste gå tillbaka till Partner Center och välja **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](./update-existing-offer.md)
