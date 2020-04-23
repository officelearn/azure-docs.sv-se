---
title: Skapa ett nytt SaaS-erbjudande för Microsofts kommersiella marknadsplats
description: Så här skapar du ett nytt SaaS-erbjudande (Software as a Service) för att lista eller sälja i Microsoft AppSource, Azure Marketplace eller via CSP-programmet (Cloud Solution Provider) med hjälp av Microsofts kommersiella marknadsplatsprogram i Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: a39e1e19f65722b4b5ae809ca943da719a3c6e22
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869778"
---
# <a name="create-a-new-saas-offer"></a>Skapa ett nytt SaaS-erbjudande

Om du vill börja skapa SaaS-erbjudanden (Software as a Service) kontrollerar du att du först [skapar ett Partner Center-konto](./create-account.md) och öppnar [instrumentpanelen för kommersiella marknadsplatser](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)med fliken **Översikt** markerad.

![Instrumentpanelen för kommersiella marknadsplatser i Partner Center](./media/new-offer-overview.png)

>[!Note]
> När ett erbjudande har publicerats uppdateras redigeringarna av erbjudandet i Partner Center endast i systemet och lagrar fronter efter återpublicering. Se till att du skickar in erbjudandet för publicering när du har gjort ändringar.

Välj **+ Nytt erbjudande...** väljer du sedan menyalternativet **Programvara som tjänst.**

Om du väljer en annan erbjudandetyp kan du omdirigeras till den äldre [Cloud Partner Portal](https://cloudpartner.azure.com/). Endast Erbjudanden från SaaS och Dynamics 365 är tillgängliga i den kommersiella Marketplace-portalen på Partner Center just nu.

![Skapa erbjudandefönster i Partnercenter](./media/new-offer-click.png)

Dialogrutan **Nytt erbjudande** visas.

![Dialogrutan Nytt erbjudande](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

- **Erbjudande-ID:** Unik identifierare för varje erbjudande i ditt konto. Det här ID:t visas för kunder i URL-adressen för marketplace-erbjudandet och Azure Resource Manager-mallar (om tillämpligt). Erbjudande-ID måste vara gemen, alfanumeriskt (inklusive bindestreck och understreck, men inget blanktecken). **Erbjudande-ID:t** är begränsat till 50 tecken och kan inte ändras när du har valt *Skapa*.  
Exempel: test-erbjudande-1
<br>Vilket resulterar i webbadressen:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Offeralias**: Namnet som används för att referera till erbjudandet i Partner Center-portalen. Det här namnet används inte på marknadsplatsen och skiljer sig från *erbjudandenamnet* och andra värden som visas för kunderna. Det går inte att ändra det här värdet när du har valt *Skapa*.

<br>Exempel: Testerbjudande 1&#8482;

Välj **Skapa**.  En **översiktssida för erbjudande** skapas för det här erbjudandet.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Översikt över erbjudandet

Sidan **Erbjudandeöversikt** innehåller:

- **Publiceringsstatusen** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet och hur lång tid varje steg tar att slutföra. Ofullständiga publiceringsstegikoner kommer att vara nedtonade.

- **Menyn Erbjudandeöversikt** innehåller en lista över länkar för att utföra åtgärder på det här erbjudandet. Den här listan över åtgärder ändras baserat på det val du gör för ditt erbjudande.  
    - Om erbjudandet är ett utkast - Ta bort utkast
    - Om erbjudandet är live - Sluta sälja erbjudande
    - Om erbjudandet är i förhandsgranskning - Go-live
    - Om du inte har slutfört utgivarut logga ut - Avbryt publicering

## <a name="offer-setup"></a>Inställningar för erbjudande

På fliken **Erbjudande** frågar du efter följande information. Välj **Spara** när du har slutfört dessa fält.

- **Vill du sälja via Microsoft?** (Ja/Nej)
    - **Ja,** du vill sälja ditt erbjudande via Microsoft, med Microsoft som är värd för marknadsplatstransaktioner för din räkning. Eller 
    - **Nej,** du föredrar att bara lista ditt erbjudande via marknadsplatserna, bearbetning av monetära transaktioner oberoende av Microsoft.

### <a name="sell-through-microsoft"></a>Sälja via Microsoft

Genom att sälja via Microsoft kan du använda bättre identifiering och förvärv av kunder, microsoft kan vara värd för marknadsplatstransaktioner åt dig och dra nytta av Microsofts globalt tillgängliga handelsfunktioner.

#### <a name="saas-offer-requirements"></a>SaaS erbjuder krav

För att kunna lista erbjudanden om programvara som en tjänst (SaaS) med Commercial Marketplace på Partner Center måste följande kriterier vara uppfyllda:

- Ditt erbjudande måste använda [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) för identitetshantering och autentisering.
- Ditt erbjudande måste använda [SaaS Uppfyllelse API:er](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) för att integreras med Azure Marketplace.
- Mer omfattande krav finns i [SaaS Offer Publishing Guide](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>SaaS prissättning och faktureringsalternativ

Med SaaS-lösningar som körs i utgivarens Azure-prenumeration inkluderar licensavgifter som betalas av kunder kostnaden för den infrastruktur som programvaran distribueras på. Azure-infrastrukturanvändning hanteras och faktureras direkt till dig, partnern. Faktiska infrastrukturanvändningsavgifter ses inte av kunden. Utgivare bör paketera azure-infrastrukturanvändningsavgifter i sina programvarulicenspriser. 

SaaS erbjuder support månadsvis eller årlig fakturering baserat på en fast avgift, per användare eller förbrukningsavgifter med hjälp av den datoriserade faktureringstjänsten. Microsofts kommersiella marknadsplats fungerar på en byråmodell, där utgivare sätter priser, Microsoft fakturerar kunder och Microsoft betalar intäkter till utgivaren samtidigt som de undanhåller en byråavgift.

Följande tabell visar en exempeluppdelning av kostnader och utbetalningar för att demonstrera byråmodellen.

|**Din licenskostnad**|**$100 per månad**|
|:---|:---|
|Kostnaden för Azure-användning (D1/1-Core)|Faktureras direkt till utgivaren, inte kunden|
|Kunden faktureras av Microsoft|$100.00 per månad (Publisher måste redovisa eventuella uppkomna eller genomströmningsinfrastrukturkostnader i licensavgiften)|

|**Microsoft-räkningar**|**$100 per månad**|
|:---|:---|
|Microsoft betalar dig 80 % av licenskostnaden <br>**För kvalificerade SaaS-appar betalar Microsoft 90 % av licenskostnaden*|$80.00 per månad <br>*$* 90,00 per månad*|

- I det här exemplet fakturerar Microsoft 100,00 USD till kunden för din programvarulicens och betalar ut 80,00 USD till utgivaren.
- Partner som har kvalificerat sig för serviceavgiften för **reducerad marknadsplats** ser en reducerad transaktionsavgift på SaaS-erbjudandena från maj 2019 till juni 2020. I det här fallet fakturerar Microsoft $ 100,00 för din programvarulicens och betalar ut $ 90,00 till utgivaren.

> [!NOTE]
> **Reducerad marketplace-serviceavgift:** För vissa SaaS-erbjudanden som du har publicerat på vår kommersiella marknadsplats kommer Microsoft att minska sin Marketplace-serviceavgift från 20 % (enligt beskrivningen i Microsofts utgivaravtal) till 10 %. För att ditt erbjudande ska kvalificeras måste minst ett av dina erbjudanden ha utsetts av Microsoft som antingen IP-samförsäljning redo eller IP-samförsäljning prioriteras.  Berättigande måste uppfyllas minst fem (5) arbetsdagar före slutet av varje kalendermånad för att få denna reducerade Marketplace-serviceavgift för månaden.  Serviceavgiften för reducerad marknadsplats gäller inte virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknadsplats.  Serviceavgiften för reducerad marknadsplats är endast tillgänglig för kvalificerade erbjudanden för licensavgifter som samlas in av Microsoft mellan den 1 maj 2019 och den 30 juni 2020.  Efter denna tid återgår Marketplace Service Fee till sitt normala belopp.

### <a name="list-through-microsoft"></a>Lista via Microsoft

Marknadsför ditt företag med Microsoft genom att skapa en marknadsplatsbeskrivning. Att välja att bara lista ditt erbjudande och inte handla via Microsoft innebär att Microsoft inte deltar direkt i programvarulicenstransaktioner. Det finns ingen tillhörande transaktionsavgift och utgivaren behåller 100% av alla licensavgifter för programvara som samlas in från kunden. Utgivaren ansvarar dock för att stödja alla aspekter av programvarulicenstransaktionen, inklusive men inte begränsat till: orderuppfyllelse, mätning, fakturering, fakturering, betalning och insamling.

- **Hur vill du att potentiella kunder ska interagera med det här listerbjudandet?**

#### <a name="get-it-now-free"></a>Få det nu (gratis)

Lista ditt erbjudande till kunder gratis genom att ange en giltig URL (som börjar med *http* eller *https)* där de kan få en utvärderingsversion genom [autentisering med ett klick med hjälp av Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Exempel: `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Gratis provperiod (lista)

Lista ditt erbjudande till kunder med en länk till en kostnadsfri utvärderingsversion genom att tillhandahålla en giltig URL (som börjar med *http* eller *https*), där de kan få en utvärderingsversion genom [autentisering med ett klick med hjälp av Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)  Till exempel: `https://contoso.com/trial/saas-app`. Kostnadsfria utvärderingsversioner skapas, hanteras och konfigureras av tjänsten och har inte prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som ditt program får via din utvärderingslänk kan endast användas för att få användarinformation via Azure AD för att automatisera kontoskapande i din app. Microsoft-konton (MSA) stöds inte för autentisering med den här token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kundkontaktinformation genom att ansluta ditt CRM-system (Customer Relationship Management). Kunden kommer att bli ombedd att dela sin information. Dessa kunduppgifter, tillsammans med erbjudandenamnet, ID och marknadsplatskällan där de hittade ditt erbjudande, skickas till CRM-systemet som du har konfigurerat. Mer information om hur du konfigurerar CRM finns i [Ansluta leadhantering](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Exempel på Marketplace-erbjudande

![Exempel på marknadsplatserbjudanden med anteckningar](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Aktivera en provkörning

En provkörning är ett bra sätt att visa upp ditt erbjudande till potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och generering av högt kvalificerade leads. Mer information finns i [Tillåt dina kunder att provköra ditt erbjudande](./test-drive.md).

- **Aktivera en provkörning** (kryssruta)

Genom att aktivera provkörning kommer du att bli ombedd att konfigurera en demonstrationsmiljö för kunder att prova ditt erbjudande under en viss tidsperiod. 

### <a name="test-drive-resources"></a>Provkörningsresurser

- [Bästa praxis för](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- testkörning för[testkörning för](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) testkörning
- [Översikt över Test Drive (PDF-nedladdning)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Anslut leadhantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Ytterligare leadhanteringsresurser
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över leadhantering en personsökare](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Kom ihåg att **spara** innan du går vidare till nästa avsnitt.

## <a name="properties"></a>Egenskaper

På fliken **Egenskaper** uppmanas du att definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatserna, de juridiska kontrakt som stöder ditt erbjudande och din appversion.

Välj **Spara** när du har slutfört dessa fält.

### <a name="category"></a>Kategori

Välj minst en (1) och högst tre (3) kategorier som används för att gruppera ditt erbjudande i lämpliga marknadsplatssökområden. Ropa ut hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen.

### <a name="industry"></a>Bransch

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Appversion

Det här fältet är valfritt och används på AppSource-marknadsplatsen för att identifiera versionsnumret för ditt erbjudande.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardkontrakt för Microsofts kommersiella marknadsplats

Microsoft tillhandahåller en standardmall för kontrakt.

- **Vill du använda standardkontraktet för Microsofts kommersiella marknadsplats?**

För att förenkla upphandlingsprocessen för kunder och minska den juridiska komplexiteten för programvaruleverantörer erbjuder Microsoft ett standardavtal för microsofts kommersiella marknadsplats för att underlätta transaktioner på marknaden. Hellre än crafting anpassade villkor, kommersiella marknadsplats förlag kan välja att erbjuda sin programvara enligt standardavtalet, som kunderna bara behöver veterinär och acceptera en gång. Standardavtalet hittar du här: https://go.microsoft.com/fwlink/?linkid=2041178.

Du kan välja att använda standardkontraktet i stället för att tillhandahålla dina egna anpassade villkor genom att välja kryssrutan "Använd standardkontraktet för den kommersiella marknadsplatsen".

![Använda kryssrutan Standardkontrakt](./media/use-standard-contract.png)

> [!NOTE]
> När du har publicerat ett erbjudande med standardkontraktet för Microsofts kommersiella marknadsplats kan du inte använda dina egna anpassade villkor. Det är ett "eller" scenario. Du erbjuder antingen din lösning enligt standardavtalet **eller** dina egna villkor. Om du vill ändra villkoren i standardavtalet kan du göra det genom standardavtalsändringar.

#### <a name="standard-contract-amendments"></a>Standardändring av kontrakt

Med standardavtalsändringar kan utgivare välja standardavtalsvillkor för enkelhet och anpassa villkoren för sin produkt eller verksamhet. Kunderna behöver bara granska ändringarna i avtalet om de redan har granskat och accepterat Microsofts standardavtal.

Det finns två typer av ändringar tillgängliga för kommersiella marknadsplatsutgivare:

- Universella ändringar: Dessa ändringar tillämpas allmänt på standardavtalet för alla kunder. Universella ändringar visas för varje kund av erbjudandet i inköpsflödet. Kunderna måste acceptera villkoren i standardavtalet och ändringen innan de kan använda ditt erbjudande.
- Anpassade ändringar: Dessa ändringar är särskilda ändringar av standardkontraktet som endast är riktade till specifika kunder via Azure-klient-ID:er. Utgivare kan välja vilken klient de vill rikta in sig på. Endast kunder från klienten kommer att presenteras med de anpassade ändringsvillkoren i erbjudandets inköpsflöde.  Kunderna måste acceptera villkoren i standardavtalet och de ändringar som gäller innan de kan använda ditt erbjudande.

>[!NOTE]
> Dessa två typer av ändringsförslag stapla ovanpå varandra. Kunder som är inriktade på anpassade ändringar kommer också att få den universella ändringen av standardavtalet vid köp.

**Universella ändringsvillkor i standardavtalet för Microsofts kommersiella marknadsplats**: Ange de universella ändringsvillkoren i den här rutan. Du kan tillhandahålla en enda universell ändring per erbjudande. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure-portalen under identifierings- och inköpsflödet.

**Anpassade ändringsvillkor i standardkontraktet för Microsofts kommersiella marknadsplats:** Börja med att välja **Lägg till anpassade ändringsvillkor**. Du kan ange upp till 10 anpassade ändringsvillkor per erbjudande.

- **Anpassade ändringsvillkor**: Ange dina anpassade ändringsvillkor i rutan för anpassade ändringsvillkor. Du kan ange ett obegränsat antal tecken i den här rutan. Endast kunder från klient-ID:na som du anger för dessa anpassade villkor visas med de anpassade ändringsvillkoren i erbjudandets inköpsflöde i Azure-portalen.  
- **Klient-ID** (krävs): Varje anpassad ändring kan riktas till upp till 20 klient-ID:er. Om du lägger till en anpassad ändring måste du ange minst ett klient-ID. Klient-ID identifierar din kund i Azure. Du kan be kunden om det här ID:t och de kan hitta det genom att navigera till portal.azure.com > Azure Active Directory > Properties. Katalog-ID-värdet är klient-ID (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också slå upp organisationens klient-ID för din kund med hjälp av deras domännamns-URL på [Vad är mitt Microsoft Azure- och Office 365-klient-ID?](https://www.whatismytenantid.com).
- **Beskrivning** (valfritt): Ange en vänlig beskrivning av klient-ID som hjälper dig att identifiera kunden du riktar in dig på med ändringen.

#### <a name="terms-and-conditions"></a>Villkor

Om du vill ange dina egna anpassade villkor kan du välja att ange dem i fältet villkor. Du kan ange upp till 10 000 tecken text i det här fältet. Om dina villkor kräver en längre beskrivning anger du en enda URL-länk i det här fältet där dina villkor finns. Den visas för kunderna som en aktiv länk.

Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

Kom ihåg att **spara** innan du går vidare till nästa avsnitt.

## <a name="offer-listing"></a>Lista över erbjudande

Fliken Erbjudandelista visar de språk (och marknader) där ditt erbjudande är tillgängligt, för närvarande engelska (USA) är den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera marknadsplats detaljer (erbjudande namn, beskrivning, sökord, etc.) för varje språk / marknad.

> [!NOTE]
> Erbjudandelista innehåll (såsom erbjudande beskrivning, dokument, skärmdumpar, användarvillkor och sekretesspolicy) är inte skyldig att vara på engelska så länge erbjudandet beskrivningen börjar med frasen "Denna ansökan är endast tillgänglig på [icke-engelska språket]." Det är också acceptabelt att ange en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehåll för erbjudandelistor.

### <a name="offer-listings"></a>Listor över erbjudanden

Ange information som ska visas på marknaden, inklusive beskrivningar av ditt erbjudande och marknadsföringstillgångar.

- **Namn** (obligatoriskt): Namnet som definieras här visas som titeln på din erbjudandelista på den eller de marknadsplatser som du har valt. Namnet är förifolkat baserat på din tidigare **nya erbjudandepost.** Namnet kan vara varumärkesskyddat. Den kan inte innehålla emojis (såvida de inte är varumärkes- och upphovsrättssymboler) och måste begränsas till 50 tecken.
- **Sammanfattning** (obligatoriskt): Ge en kort beskrivning av ditt erbjudande som ska användas i marknadsplatslistor. Upp till 100 tecken text kan anges i det här fältet.
- **Beskrivning** (obligatoriskt): Ange en beskrivning av ditt erbjudande som ska visas i översikten över marknadsplatser. Överväg att inkludera ett värdeerbjudande, viktiga fördelar, alla kategori- eller branschorganisationer, köpmöjligheter i appen, eventuella nödvändiga upplysningar och en länk för att lära dig mer.
Upp till 3 000 tecken text kan anges i det här fältet, inklusive markering. Ytterligare tips finns i artikeln [Skriv en bra appbeskrivning](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Sökord:** Ange upp till tre sökord som kunderna kan använda för att hitta ditt erbjudande på marknadsplatserna.
- **Komma igång instruktioner** (krävs): Förklara hur du konfigurerar och börjar använda din app för potentiella kunder.  Den här snabbstarten kan innehålla länkar till mer detaljerad onlinedokumentation. Upp till 3 000 tecken text kan anges i det här fältet.

#### <a name="description"></a>**Beskrivning**

Det här fältet krävs. Objekt som ska inkluderas i **beskrivning:**

* Beskriv tydligt erbjudandets värdeerbjudande i de första meningarna i din beskrivning.  
* Tänk på att de första meningarna kan visas i sökmotorresultat.  
* Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde du levererar.  
* Använd branschspecifik vokabulär eller förmånsbaserad formulering så mycket som möjligt.

Centrala komponenter i ditt värdeerbjudande bör innehålla följande information:

* Beskrivning av produkten.
* Typ av användare som drar nytta av produkten.
* Kunden behöver eller smärta som produkten adresserar.

Om du vill göra erbjudandet **Beskrivning** mer engagerande använder du RTF-redigeraren för att formatera beskrivningen.

![Använda RTF-redigeraren](./media/text-editor2.png)

Använd följande instruktioner för att använda RTF-redigeraren:

- Om du vill ändra innehållets format markerar du den text som du vill formatera och markerar ett textformat, som visas nedan:

     ![Använda RTF-redigeraren för att ändra textformat](./media/text-editor3.png)

- Om du vill lägga till en punktlista eller numrerad lista i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att lägga till listor](./media/text-editor4.png)

- Om du vill lägga till eller ta bort indrag i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att dra in](./media/text-editor5.png)

#### <a name="links"></a>Länkar

- **Sekretesspolicy** (obligatorisk): Länka till organisationens sekretesspolicy. Du är ansvarig för att din app följer sekretesslagar och sekretessregler och för att tillhandahålla en giltig sekretesspolicy
- **CSP Program Marketing Materials** (valfritt): Ange en länk till marknadsföringsmaterial om du väljer att utöka ditt erbjudande till [CSP-programmet (Cloud Solution Provider).](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) CSP utökar ditt erbjudande till ett bredare utbud av kvalificerade kunder genom att göra det möjligt för CSP-partner att bunta, marknadsföra och sälja ditt erbjudande. Dessa återförsäljare kommer att behöva tillgång till material för marknadsföring ditt erbjudande. Mer information finns i [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Användbara länkar** (valfritt): Valfria kompletterande onlinedokument om din app eller relaterade tjänster som anges genom att tillhandahålla en **titel** och **webbadress**. Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en URL**.

#### <a name="contact-information"></a>Kontaktinformation

- **Kontakter**: Ange ett medarbetarnamn, **telefonnummer**och **E-postadress** för varje kundkontakt. **Name**  (Dessa *visas inte* offentligt). En **support-URL** krävs också för **supportkontaktgruppen.**  (Denna information *kommer att* visas offentligt).

**Supportkontakt** (obligatoriskt): För allmänna supportfrågor.

**Teknisk kontakt** (krävs): För tekniska frågor.

**Kanalchef kontakt** (krävs): För återförsäljare frågor som rör CSP-programmet.

#### <a name="files-and-images"></a>Filer och bilder

- **Dokument** (krävs): Lägg till relaterade marknadsföringsdokument för ditt erbjudande, i PDF-format, vilket ger minst en (1) och högst tre (3) dokument per erbjudande.
- **Bilder** (valfritt): Det finns flera platser där erbjudandets logotypbilder kan visas på hela marknadsplatserna, som kräver följande storlekar – Liten: 48 x 48 pixlar _(krävs),_ Medium: 90 x 90 pixlar _(krävs),_ Stor: 216 x 216 pixlar _(krävs),_ Bred: 255 x 115 pixlar och Hero: 815 x 290 pixlar. Alla bilder måste finnas i . PNG-format.
- **Skärmdumpar** (krävs): Lägg till skärmdumpar som visar ditt erbjudande. Högst fem (5) skärmdumpar kan läggas till och bör vara dimensionerade på 1280 x 720 pixlar. Alla bilder måste finnas i . PNG-format.
- **Videor** (valfritt): Lägg till länkar till videor som visar ditt erbjudande. Du kan använda länkar till YouTube- och/eller Vimeo-videor, som visas tillsammans med ditt erbjudande till kunder. Du måste också ange en miniatyrbild av videon, storleksstor till 1280 x 720 pixlar i PNG-format. Du kan visa högst fyra videor per erbjudande.

Kom ihåg att **spara** innan du går vidare till nästa avsnitt.

>[!Note]
>Om du har problem med att ladda upp filer https://upload.xboxlive.com kontrollerar du att det lokala nätverket inte blockerar tjänsten som används av Partner Center.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

- [Metodtips för marknadsplatserbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Förhandsversion

På fliken **Förhandsgranska** kan du definiera en begränsad **målgrupp** för att släppa ditt erbjudande innan du publicerar ditt erbjudande live för den bredare marknadsplatsens målgrupp.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i förhandsversion väljer du **Gå live** så att ditt erbjudande kan publiceras live till den offentliga målgruppen på marknaden.

- **Definiera en målgrupp för förhandsgranskning: Lägg till ett e-postmeddelande med ett AAD/MSA-konto per rad, tillsammans med en valfri beskrivning.**

Lägg till upp till 10 e-postadresser manuellt, eller 20 om du laddar upp en CSV-fil, för befintliga Microsoft-konto (MSA) eller Azure Active Directory-konton för att hjälpa till med att validera ditt erbjudande innan du publicerar live. Genom att lägga till dessa konton definierar du en målgrupp som får förhandsåtkomst till ditt erbjudande innan det publiceras på marknadsplatserna. Om ditt erbjudande redan är live kan du fortfarande definiera en målgrupp för förhandsversionen för att testa eventuella ändringar eller uppdateringar av ditt erbjudande.

> [!NOTE]
> Förhandsgranskningspubliken skiljer sig från en privat målgrupp. En förhandsgranskningsmålgrupp får åtkomst till ditt erbjudande _innan_ den publiceras live på marknadsplatserna. Du kan också välja att skapa en plan och göra den tillgänglig endast för en privat målgrupp. På fliken **Lista för planer** kan du definiera en privat målgrupp med kryssrutan Det här är en privat **plan.** Du kan sedan definiera en privat målgrupp på upp till 20 000 kunder med Azure Tenant ID:er.

## <a name="technical-configuration"></a>Teknisk konfiguration

På fliken **Teknisk konfiguration** definieras den tekniska information (URL-sökväg, webhook, klient-ID och app-ID) som används för att ansluta till ditt erbjudande. Denna anslutning gör det möjligt för oss att etablera ditt erbjudande för slutkunden om de väljer att förvärva det. Diagram som beskriver användningen av de insamlade fälten finns i dokumentationen för [SaaS uppfyllelse API:er](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL för målsida** (obligatoriskt): Definiera webbadressen som kunderna kommer att landa på efter att ha hämtat ditt erbjudande från marknadsplatsen. Den här URL:en är den slutpunkt som tar emot en token när en kund dirigeras till sidan. Den token kan bytas ut för etableringsinformation med hjälp av lösa api:er för uppfyllelse. Dessa uppgifter och alla andra du samlar in kan användas som en del av en kund-interaktiv webbsida inbyggd i din upplevelse för att slutföra registreringen och aktivera deras köp.

- **Anslutningswebbkrok** (obligatoriskt): För alla asynkrona händelser som Microsoft behöver skicka till dig för kundens räkning (t.ex. saas-prenumerationen har blivit ogiltig) kräver vi att du tillhandahåller en anslutningswehook. Om du inte redan har ett webhook-system på plats är den enklaste konfigurationen att ha en HTTP Endpoint Logic App som lyssnar\/efter alla händelser som publiceras på den och sedan hanterar dem på rätt sätt (till exempel https: /prod-1westus.logic.azure.com:443/work). Mer information finns i [Anropa, utlösare eller kapsla arbetsflöden med HTTP-slutpunkter i logikappar](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure AD-klient-ID** (obligatoriskt): Inuti Azure-portalen kräver vi att du [skapar en AZURE Active Directory -app (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) så att vi kan validera anslutningen mellan våra två tjänster ligger bakom en autentrad kommunikation. Om du vill hitta [klient-ID:t](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)går du till din Azure Active Directory och väljer **Egenskaper**och letar sedan efter **katalog-ID-numret** som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-app-ID** (obligatoriskt): Du behöver också ditt [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) och en autentiseringsnyckel. Om du vill hämta dessa värden går du till din Azure Active Directory och väljer **Appregistreringar**och letar sedan efter **program-ID-numret** som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Om du vill hitta autentiseringsnyckeln går du till **Inställningar** och väljer **Nycklar**. Du måste ange en beskrivning och varaktighet och kommer då att få ett talvärde.

>[!Note]
>Azure-program-ID är kopplat till ditt utgivar-ID, så se till att samma program-ID används i alla dina erbjudanden.

## <a name="plan-overview"></a>Översikt över plan

På fliken **Planera översikt** kan du ange en mängd olika planalternativ i samma erbjudande. Dessa planer (kallas ibland SKU: er) kan skilja sig åt när det gäller version, intäktsgenerering eller servicenivåer. Du måste ställa in minst en plan för att kunna sälja ditt erbjudande på marknaden.

När du har skapat ditt abonnemangsnamn, ID:er, prismodeller, tillgänglighet (offentlig eller privat), aktuell publiceringsstatus och alla tillgängliga åtgärder.

**Vilka åtgärder** som är tillgängliga i **översikten Planera** varierar beroende på planens aktuella status och kan omfatta:

- Om planens status är **Utkast** - Ta bort utkast
- Om planstatusen är **Live** - Sluta sälja plan eller Synkronisera privat målgrupp

**Skapa ny plan** (minst en plan för dem som väljer att sälja via Microsoft)

- **Plan-ID:** Skapa ett unikt plan-ID för varje plan i det här erbjudandet. Det här ID:t visas för kunder i mallarna produkt-URL och Azure Resource Manager (om tillämpligt). Använd bara gemener, alfanumeriska tecken, streck eller understreck. Högst 50 tecken tillåts för det här plan-ID:t. ID:t kan inte ändras när du har valt skapa.
- **Planens namn:** Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas inom ditt erbjudande. Skapa ett unikt erbjudandenamn för varje plan i det här erbjudandet. Plannamnet används för att skilja programvaruplaner som kan vara en del av samma erbjudande (till exempel Erbjudandenamn: Windows Server; planer: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Planera listning

På fliken **Lista över abonnemang** visas de språk (och marknader) där ditt abonnemang är tillgängligt, för närvarande engelska (USA) är den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera marknadsplats detaljer (erbjudande namn, beskrivning, sökord, etc.) för varje språk / marknad.

#### <a name="plan-listing-details"></a>Planera listningsinformation

Om du väljer ett av planspråken visas **planlistningsinformationen,** inklusive **Namn** och **Beskrivning.**

- **Namn:** Förifylld baserat på din förhandsversion **Ny planpost** och visas som titeln på erbjudandets "Programvaruplan" som visas på marknaden.
- **Beskrivning:** Denna beskrivning är ett tillfälle att förklara vad som gör denna programvara plan unik och eventuella skillnader från andra programplaner inom ditt erbjudande. Kan innehålla upp till 500 tecken.

Välj **Spara** när du har slutfört dessa fält.

#### <a name="plan-pricing-and-availability"></a>Planera priser och tillgänglighet

På fliken **Prissättning och tillgänglighet** kan du konfigurera de marknader som den här planen ska vara tillgänglig på, den önskade intäktsgenereringsmodellen, priset och faktureringsperioden. Dessutom kan du ange om planen ska vara synlig för alla eller bara för specifika kunder (en privat målgrupp).

##### <a name="enabling-free-trials"></a>Aktivera kostnadsfria utvärderingsversioner

SaaS erbjudanden via den kommersiella marknaden gör att du kan tillhandahålla en månads kostnadsfri utvärderingsversion när du säljer via Microsoft. För alla faktureringsmodeller och villkor utom abonnemang med datapriser stöds kostnadsfria utvärderingsversioner. Med det här alternativet kan kunderna ha en låg barriär för inträde genom en månad med fri tillgång.  Om du väljer att aktivera en kostnadsfri utvärderingsversion för abonnemang inom ditt erbjudande kan kunden inte konvertera till en betald prenumeration före utgången av den första enmånadersperioden.  Under den här tiden kan kunder som köper ditt erbjudande prova någon av de planer som stöds som har den kostnadsfria utvärderingsversionen aktiverad och konvertera mellan dem.  Konverteringen till en betald prenumeration sker automatiskt i slutet av perioden.

>[!Note]
>Om kunden väljer att konvertera till en plan utan kostnadsfria provperioder sker konverteringen, men den kostnadsfria provperioden går förlorad omedelbart.  Dessutom, när en kund börjar betala för en plan, de kan inte längre få gratis provperiod på samma prenumeration igen, även om de konverterar till en SKU som stöder gratis prövningar.

Möjligheten att konfigurera en kostnadsfri utvärderingsversion är tillgänglig för varje plan i ditt erbjudande. Navigera till priser och tillgänglighet för varje erbjudande och markera kryssrutan om du vill tillåta en månads provperiod.

![En månads kostnadsfri provperiod](./media/free-trial-enable.png)

>[!Note]
>När ditt transactable erbjudande har publicerats med en gratis provperiod, kan det inte inaktiveras för den planen. Kontrollera att den här inställningen är korrekt för den första publiceringen för att undvika att behöva återskapa planen.

Om du vill få information om kundprenumerationer `isFreeTrial`som för närvarande deltar i en kostnadsfri utvärderingsversion använder du den nya API-egenskapen , som markeras som sann eller falsk. Mer information finns i [SaaS Get Subscription API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Kostnadsfria utvärderingsversioner stöds inte för planer som utnyttjar marknadsplatsmätningstjänsten.

#### <a name="markets"></a>Marknader

- **Redigera marknader** (valfritt)

Varje plan måste finnas tillgänglig på minst en marknad. Markera kryssrutan för alla marknadsplatser där du vill göra planen tillgänglig. En sökruta och knapp för att välja "Moms remitterade" länder, där Microsoft skickar försäljning och använda skatt för din räkning, ingår för att hjälpa dig.

Om du redan har angett priser för din plan i USA-dollar (USD) och lägger till en annan marknadsplats, kommer priset för den nya marknaden att beräknas enligt aktuella valutakurser. Granska priset för varje marknad innan publicering. Prissättningen kan granskas med hjälp av länken "Exportpriser (xlsx)" när du har sparat ändringarna.

#### <a name="pricing"></a>Prissättning

- **Prismodell**: Schablonbelopp eller seat-baserad

**Schablonbelopp:** Aktivera tillgång till ditt erbjudande med ett fast pris per månad eller år. Detta kallas ibland platsbaserad prissättning. Med den här prismodellen kan du eventuellt definiera abonnemang med datapriser som använder API:et för marketplace-avläsning för att debitera kunder enligt icke-standardiserade enheter.  Mer information om fakturering med datapriser finns i [fakturering med datapriser med hjälp av marknadsplatsmätningstjänsten](./saas-metered-billing.md).

**Per användare:** Aktivera åtkomst till ditt erbjudande med priset baserat på antalet användare som får tillgång till erbjudandet eller upptar platser. Med den här användarbaserade modellen kan du ange det minsta och högsta antalet användare som tillåts baserat på priset. På så sätt kan olika prispunkter konfigureras baserat på antalet användare genom att konfigurera flera abonnemang.  Dessa fält är valfria. Om den lämnas omarkerad tolkas antalet användare som att de inte har en gräns (min på 1 och max för så många som systemet kan stödja). Dessa fält kan redigeras som en del av en uppdatering av planen.

När valet av faktureringsprismodell har publicerats kan det inte ändras. Dessutom måste alla planer för samma erbjudande dela samma prismodell.

- **Faktureringsperiod**: Månadsvis eller årlig

Välj den frekvens som kunderna måste betala det angivna priset. Minst ett månads- eller årspris måste anges, eller så kan båda alternativen göras tillgängliga för kunderna.

- **Pris:** USD per månad eller USD per år

Priserna som anges i USD (USD = US-dollar) konverteras till lokal valuta för alla valda marknader med hjälp av aktuella valutakurser när de sparas. Validera dessa priser innan du publicerar genom att exportera priskalkylbladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar och importerar du priskalkylbladet. Du är ansvarig för att validera denna prissättning och äger dessa inställningar.
*\*Du måste först spara dina prisändringar för att aktivera export av prisdata.*

Granska dina priser noggrant innan du publicerar, eftersom det finns vissa begränsningar för vad som kan ändras när en plan har publicerats:

- När en plan har publicerats kan inte prismodellen ändras.
- När en faktureringsperiod har publicerats för en plan kan den inte tas bort senare.
- När ett pris för en marknad i din plan har publicerats kan det inte ändras senare.

### <a name="plan-audience"></a>Planera målgrupp

Du har möjlighet att konfigurera varje plan så att den är synlig för alla eller för endast en viss målgrupp som du väljer. Du kan tilldela medlemskap i den här begränsade målgruppen med hjälp av Azure AD-klient-ID:er.

#### <a name="privacy"></a>Sekretess

- **Det här är en privat plan** (valfri kryssruta)

Markera den här rutan om du bara vill göra din plan privat och synlig för den begränsade målgrupp du väljer. När du har publicerats som en privat plan kan du uppdatera målgruppen eller välja att göra planen tillgänglig för alla. När en plan har publicerats som synlig för alla måste den vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

- **Begränsad målgrupp (klient-ID)**

Tilldela målgruppen som ska ha åtkomst till den här privata planen. Åtkomst tilldelas med klient-ID:er med möjlighet att inkludera en beskrivning av varje tilldelat klient-ID. Högst 10 klient-ID:n kan läggas till, eller 20 000 kunders klient-ID:n om du importerar en CSV-kalkylbladsfil.

En klient är en representation av en organisation, med ett ID representerat som ett GUID (Globalt unik identifierare, ett 128-bitars heltal som används för att identifiera resurser). Det är en dedikerad instans av Azure AD som en organisation eller apputvecklare får när organisationen eller apputvecklaren skapar en relation med Microsoft, till exempel när du registrerar dig för Azure, Microsoft Intune eller Microsoft 365. Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter. Om du vill kontrollera klientorganisationen loggar du in på Azure-portalen med det konto du vill använda för att hantera din app. Om du har en klient kommer du automatiskt att loggas in och kan se klientorganisationens namn direkt under namnet på ditt konto. Hovra över ditt kontonamn längst upp till höger i Azure-portalen så visas namn, e-post, katalog/klient-ID (ett GUID) och domän. Om ditt konto är kopplat till flera klienter måste du välja namnet på ditt konto för att öppna en meny där du kan växla mellan klienter. Varje klient har sitt eget klient-ID. Du kan också slå upp organisationens klient-ID med [https://www.whatismytenantid.com](https://www.whatismytenantid.com)hjälp av en domännamnsadress på: .

SaaS-erbjudanden använder klient-ID:er för att definiera en privat målgrupp, men andra erbjudandetyper kan använda Azure Subscription IDs (som också representeras som GUIDs).

> [!NOTE]
> Den privata målgruppen (eller den begränsade målgruppen) skiljer sig från en förhandsgranskningspublik. På fliken **[Förhandsgranska](#preview)** kan du definiera en målgrupp för förhandsgranskning. En förhandsgranskningsmålgrupp får åtkomst till ditt erbjudande *innan* erbjudandet publiceras live på marknaden. Medan den privata målgruppsbeteckningen endast gäller för en viss plan kan förhandsgranskningspubliken visa alla planer (privata eller inte), men bara under den begränsade förhandsgranskningsperioden medan planen testas och valideras.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exempellista över planer inom ett marknadsplatserbjudande

![Exempel på marknadsplatsabonnemang med anteckningar](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>CSP-återförsäljare (Cloud Solution Provider)

Genom att välja att göra ditt erbjudande tillgängligt i CSP-programmet kan Cloud Solution Providers sälja din produkt som en del av en medföljande lösning till sina kunder. Mer information finns i [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publicera

När du har slutfört alla nödvändiga delar av erbjudandet väljer du **publicera** i det övre högra hörnet på portalen. Du omdirigeras till sidan **Granska och publicera.**

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till förhandsgranskning

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet.
    - *Inte startad* - innebär att avsnittet inte har berörts och måste slutföras.
    - *Ofullständig* - innebär att avsnittet har fel som måste åtgärdas eller kräver mer information som ska tillhandahållas. Du måste gå tillbaka till avsnittet och uppdatera det.
    - *Komplett* - betyder att avsnittet är komplett, alla nödvändiga data har lämnats och det finns inga fel. Alla delar av erbjudandet måste vara i ett fullständigt tillstånd innan du kan skicka erbjudandet.
- Ge testinstruktioner till certifieringsteamet för att säkerställa att din app testas korrekt, utöver eventuella kompletterande anteckningar som kan vara till hjälp för att förstå din app.
- Skicka erbjudandet för publicering genom att välja **Skicka**. Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet är tillgänglig för dig att granska och godkänna. Du måste gå tillbaka till Partner Center och välja **Go-live** för att erbjudandet ska kunna publicera ditt erbjudande för allmänheten (eller om ett privat erbjudande, till den privata målgruppen).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
