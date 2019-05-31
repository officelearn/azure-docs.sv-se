---
title: Skapa ett nytt SaaS-erbjudande i Marketplace för kommersiella
description: Hur du skapar en ny programvara som en tjänst (SaaS)-erbjudande för lista eller sälja på Azure Marketplace, AppSource, eller via programmet Cloud Solution Provider (CSP) med hjälp av kommersiella Marketplace-portalen på Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9750aa924ba4b6e4e5f3a51e5b34531d3fab0a6e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243314"
---
# <a name="create-a-new-saas-offer"></a>Skapa ett nytt SaaS-erbjudande

För att börja skapa programvara som en tjänst (SaaS) erbjuder, se till att som du första [skapa ett Partnercenter-konto](./create-account.md) och öppna den [kommersiella Marketplace instrumentpanelen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), med den **erbjuder** fliken som valts. 

![Kommersiella Marketplace-instrumentpanel på Partner Center](./media/commercial-marketplace-offers.png)

Välj den + **skapar du en ny...** knappen och välj den **programvara som en tjänst** menyalternativ. 

Om du väljer en av de andra typerna av erbjudanden, omdirigeras du till den äldre [Cloud Partner Portal](https://cloudpartner.azure.com/).  Endast SaaS-erbjudanden är tillgängliga i den kommersiella Marketplace-portalen på Partner Center just nu. 

![Skapa erbjudande fönster på Partner Center](./media/new-offer.png)


Den **nytt erbjudande** dialogrutan visas. ![Dialogrutan Nytt erbjudande](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>Erbjudande-ID och namn

- **Erbjudande-ID**: Skapa en unik identifierare för varje erbjudande i ditt konto. Detta ID ska vara synliga för kunder i URL-adressen för marketplace-erbjudande och Azure Resource Manager-mallar (om tillämpligt). Erbjudande-ID måste vara gemener, alfanumeriskt (inklusive bindestreck och understreck, men inga blanksteg). Detta är begränsad till 50 tecken och kan inte uppdateras när du har valt skapa.  
Exempel: test-erbjudande-1
<br>Vilket resulterar i URL: en: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Namn på erbjudande**: Officiella namnet på ditt SaaS-program erbjudande, konsekventa i publikationer, annonser och webbplatser.  Det här namnet kan vara produktnamn.  Erbjuder namnet får inte innehålla blanksteg, emojis (om de inte är symbolen varumärke eller copyright) och måste vara högst 50 tecken.
<br>Exempel: Testa erbjudande 1&#8482;

Välj **Skapa**.  En **erbjuder översikt** sida skapas för det här erbjudandet.  

![Översikt för erbjudandet på Partner Center](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Erbjudande-översikt

Den **erbjuder översikt** finns: 

- Den **Publiceringsstatus** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet och hur lång tid varje steg tar för att slutföra. Ofullständig publishing steg ikoner nedtonade. 

- Den **erbjuder översikt** menyn innehåller en lista med länkar för att utföra åtgärder på detta erbjudande. Den här listan över åtgärder ska ändras baserat på det val du gör för ditt erbjudande.  
    - Om erbjudandet är ett utkast – ta bort utkast 
    - Om erbjudandet är live – stoppa sälja erbjudande 
    - Om erbjudandet som är i förhandsversion – Go live 
    - Om du inte har slutfört publisher logga ut – Avbryt publicera

## <a name="offer-setup"></a>Installationsprogrammet för erbjudandet

Den **erbjuder installationsprogrammet** fliken ombeds att ange följande information. Välj **spara** när du har slutfört de här fälten.

- **Vill du sälja via Microsoft?** (Ja/Nej)
    - **Ja**, du vill sälja ditt erbjudande via Microsoft med Microsoft som är värd för marketplace-transaktioner för din räkning eller 
    - **Inte**, du föredrar att bara visa ditt erbjudande via marknadsplatser, bearbetning av alla monetära transaktionerna oberoende av Microsoft.    

### <a name="sell-through-microsoft"></a>Sälj via Microsoft

Sälja via Microsoft ger bättre kunden identifiering och förvärv, kan Microsoft värd marketplace transaktioner för din räkning och drar nytta av Microsofts globalt tillgänglig commerce-funktioner.

#### <a name="saas-offer-requirements"></a>Krav för SaaS-erbjudande

För att visa en lista över programvara som en tjänst (SaaS) erbjuder med kommersiella Marketplace på Partner Center, måste följande kriterier uppfyllas:

- Erbjudandet måste vara kompatibla med Azure-klienter. (Ofta SaaS-appar finns på Azure för bästa prestanda och kompatibilitet, men detta är inte ett krav.) 
- Erbjudandet måste använda [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) för Identitetshantering och autentisering.
- Erbjudandet måste använda [SaaS Techtrends API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) att integrera med Azure Marketplace.

#### <a name="billing-infrastructure-costs"></a>Fakturering infrastrukturkostnader
För SaaS-erbjudanden kan du, som utgivare, måste ta hänsyn till avgifter för användning av Azure-infrastrukturen och licensavgifter som ett enda kostnaden-objekt. Den här kostnaden representeras som en fast månadsavgift för kunden. Användning av Azure-infrastrukturen hanteras och debiteras för partner kan du direkt. Faktisk användning infrastrukturavgifter ses inte av kunden. Utgivare välja vanligtvis för att bifoga avgifter för användning av Azure-infrastrukturen i deras programvara licens priser. 

Programvara för licensavgifter visas som en fast avgift för varje månad, återkommande platsbaserad prenumeration och mäts inte eller konsumtionsbaserat.

|**Din licens kostnad**|**100 USD per månad**|
|:---|:---|
|Användningen av Azure-kostnad (D1/1-kärna)|Faktureras direkt till utgivaren inte kunden|
|Faktureras kunden av Microsoft|$100,00 per månad (utgivare måste ta hänsyn till några kostnader eller direkt infrastrukturkostnader i licensavgiften)|

- I det här scenariot Microsoft fakturerar $100,00 för din programvarulicensen och betalar ut $80.00 till utgivaren.
- Partner som har kvalificerat för den **minskas serviceavgift för Marketplace** visas en minskad transaktionsavgift på SaaS erbjuder från maj 2019 fram till juni 2020. I det här scenariot Microsoft fakturerar $100,00 för din programvarulicensen och betalar ut $90.00 till utgivaren.

> [!NOTE]
> **Minskad serviceavgift för Marketplace**: För vissa kan SaaS du att du har publicerat på vår kommersiella Marketplace, Microsoft minskar dess serviceavgift för Marketplace från 20% (enligt beskrivningen i Publiceringsavtalet för Microsoft) till 10%. För ditt erbjudande att kvalificera måste minst en av dina erbjudanden har angetts av Microsoft som antingen IP-medförsäljning är klara eller IP-medförsäljning prioriteras.  Kvalificering måste uppfyllas minst fem (5) arbetsdagar innan slutet av varje månad för att ta emot den här minskade serviceavgift för Marketplace för månaden.  Det minskar serviceavgift för Marketplace gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella Marketplace.  Det minskar serviceavgift för Marketplace kommer bara att tillgängligt för kvalificerade erbjudanden för licensavgifter som samlas in av Microsoft mellan den 1 maj 2019 och den 30 juni 2020.  Efter den tiden kan returnerar serviceavgift för Marketplace till dess normala värde. 

|**Microsoft fakturor**|**100 USD per månad**|
|:---|:---|
|Microsoft betalar du 80% av dina kostnader för licens <br>**För kvalificerade SaaS-appar betalar Microsoft 90% av dina kostnader för licens*|$80.00 per månad <br>*$* 90.00 per månad *|


#### <a name="csp-program-opt-in"></a>Anmälan Program för CSP
Den [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programmet gör det möjligt för programvara erbjudanden att nå miljontals kvalificerade Microsoft-kunder med minimal investering för marknadsföring och försäljning.

- **Kanaler: Tillgängliggöra min erbjudandet i CSP-programmet** (kryssruta)

Välja ska göra ditt erbjudande i CSP-programmet gör det möjligt för Cloud Solution Providers att sälja din produkt som en del av en paketerad lösning till sina kunder. 

### <a name="list-through-microsoft"></a>Lista via Microsoft

Marknadsföra ditt företag med Microsoft genom att skapa en marketplace-lista. Om du väljer att visa endast ditt erbjudande och inte transact via Microsoft innebär att Microsoft inte deltar direkt i software license transaktioner. Det finns inga associerade transaktionsavgift och utgivaren håller 100% av alla avgifter som samlas in från kunden för programvarulicenser. Dock utgivaren ansvarar för att stödja alla aspekter av programvara licens transaktioner, inklusive men inte begränsat till: order betjäna, Avläsning av programvara, fakturering, fakturering, betalning och samling. 

- **Hur vill du potentiella kunder att interagera med erbjudandet lista?**

##### <a name="get-it-now-free"></a>Hämta den nu (kostnadsfritt)
Lista ditt erbjudande till kunder utan kostnad genom att ange en giltig URL (som börjar med http eller https) där de kan komma åt din app.  Exempel: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Kostnadsfri utvärderingsversion
Lista ditt erbjudande till kunder på en kostnadsfri utvärderingsversion genom att ange en giltig URL (som börjar med http eller https) där de kan komma åt din app.  Exempel: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Kontakta mig
Samla in kontaktuppgifter till kunder genom att ansluta datorn kunden relation Management (CRM). Kunden ombeds för behörighet att dela sin information. Dessa kunduppgifter, tillsammans med erbjudandenamn, ID och marketplace där de går att hitta källan ditt erbjudande skickas till CRM-systemet som du har konfigurerat. Läs mer om hur du konfigurerar din CRM [Connect lead-hantering](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Exempel marketplace erbjuder lista

![Exempel marketplace erbjudanden med anteckningar](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Aktivera en provkörning

Testa är ett bra sätt att visa upp ditt erbjudande till potentiella kunder genom att ge dem kan ”testa innan du köper”, vilket resulterar i ökad konvertering och högt kvalificerade leads generation. [Läs mer om provkörningarna.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Aktivera en testenhet** (kryssruta) 

Genom att aktivera provkörning, blir du ombedd att konfigurera en demonstrationsmiljö för kunderna att testa ditt erbjudande för en fastställd tidsperiod. 

### <a name="type-of-test-drive"></a>Typ av provkörning

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : En Distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder endast Azure-resurser.
- **[Dynamics 365 för företag centrala](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft är värd för och underhåller tjänsten test drive (inklusive etablering och distribution) för en Business Central Företagsresursplanering system (ekonomi, åtgärder, leveranskedja, CRM, osv.).  
- **[Dynamics 365 för kundengagemang](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft är värd för och underhåller tjänsten test drive (inklusive etablering och distribution) för ett Customer Engagement-system (försäljning, service, projekt-tjänsten, fälttjänst osv.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft är värd för och underhåller tjänsten test drive (inklusive etablering och distribution) för en Finance and Operations Företagsresursplanering system (ekonomi, åtgärder, tillverkning, leveranskedja, osv.). 
- **[Logikapp](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : En Distributionsmall som omfattar alla komplexa lösningsarkitekturer. Alla anpassade produkter bör använda den här typen av Test Drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : En inbäddad länk till en anpassade instrumentpanel. Produkter som du vill visa en interaktiva Power BI-objekt bör använda den här typen av Test Drive. Allt du behöver ladda upp här är din inbäddade Power BI-URL.

#### <a name="additional-test-drive-resources"></a>Ytterligare test drive-resurser
- [Test Drive teknisk bästa praxis](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Testkör marknadsföring Metodtips](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Testa enheten översikt över en personsökare](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Ansluta lead-hantering

Kontakt med kunder direkt genom att visa en lista över ditt erbjudande i av marknadsplatser och kopplar datorn kunden relation Management (CRM) så att du kan få kundkontaktinformation omedelbart efter det att en kund uttrycker intresse eller distribuerar din produkt.

- **Välj ett mål för lead** (nedrullningsbar meny): Ange anslutningsinformationen till CRM-systemet där du vill vi skicka kundleads. 

Partner Center stöder följande CRM-system för lead-hantering. Klicka på länken för konfigurationsanvisningar.

- Azure Blob – Ange kontaktens e-post, namn och anslutningssträng för lagringskonto. 
- [Azure-tabell](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – Ange kontaktens e-post och anslutningssträng för lagringskonto. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – Ange kontaktpersonens e-postadress och URL-autentiseringsläge (Office 365 eller Azure Active Directory).
- [Https-slutpunkt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – Ange kontaktens e-post och HTTPS slutpunkts-URL. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – Ange kontaktens e-post, formulär-ID, Munchkin konto-ID och server-ID.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -Ange kontaktens e-post och organisations-ID. 

#### <a name="additional-lead-management-resources"></a>Ytterligare lead management-resurser
- [Lead-hantering vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga lead-konfigurationsfel](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Leda Management översikt över en personsökare](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

## <a name="properties"></a>Egenskaper
Den **egenskaper** fliken ber dig att definiera kategorier och branscher som används för att gruppera ditt erbjudande i marknadsplatser, juridiska avtal som stöd för ditt erbjudande och din appversion. 

Välj **spara** när du har slutfört de här fälten. 

### <a name="category"></a>Category
Välj ett minimum av en (1) och högst tre (3) kategorier används för att gruppera ditt erbjudande i lämplig marketplace search områden. Anropa reda på hur ditt erbjudande stöder dessa kategorier i beskrivningen för erbjudandet. 

### <a name="industry"></a>Bransch
Välj upp till två (2) branscher används för att gruppera ditt erbjudande i lämplig marketplace search områden. Om ditt erbjudande inte är specifika för en bransch, Markera inte något. Anropa reda på hur ditt erbjudande har stöd för de valda branscher i beskrivningen för erbjudandet. 

### <a name="app-version"></a>Appversion
Det här är ett valfritt fält som används i AppSource-marknadsplatsen för att identifiera det lägre versionsnumret för ditt erbjudande. 

### <a name="standard-contract"></a>Standardavtal

- **Använda standardavtal?**

För att förenkla inköpsprocessen för kunder och minska juridiska komplexiteten för programvaruleverantörer, erbjuder Microsoft en standardavtal mall för att hjälpa att underlätta en transaktion i marketplace. 

I stället för att utforma anpassade villkor, kan Azure Marketplace-utgivare välja att erbjuda sin programvara under standardavtal som kunder behöver bara se och acceptera en gång. 

Standard kontraktet finns här: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Användningsvillkor

Om din licensvillkoren skiljer sig från Standard kontraktet, kan du välja att ange dina egna juridiska villkor här. Du kan också ange upp till 10 000 tecknen i texten i det här fältet. Om dina användningsvillkor kräver en längre beskrivning, kan du ange en enskild URL-länk i fältet där din ytterligare licensvillkoren finns. Den visas för kunder som en aktiv länk.

Kunder måste acceptera villkoren innan de kan testa din app. 

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

## <a name="offer-listing"></a>Erbjudandet lista

Erbjudandet visar fliken visar språk (och marknader) där erbjudandet är tillgängligt, är för närvarande engelska (USA) bara tillgängliga. Dessutom kan visar den här sidan status för språkspecifika lista och datum/tid som det har lagts till. Du måste definiera marketplace-informationen (erbjuder namn, beskrivning, söktermer, etc.) för varje språk / marknaden.

### <a name="offer-listings"></a>Erbjudanderegistreringar

Ange information som ska visas i marketplace, inklusive beskrivningar av ditt erbjudande och marknadsföring tillgångar.

- **Namn på** (krävs): Namnet som anges här kommer att visas som rubrik på erbjudandet på marketplace(s) som du har valt. Namnet innehåller redan baserat på dina tidigare **nytt erbjudande** posten.  Detta kan vara produktnamn.  Detta får inte innehålla blanksteg, emojis (om de inte är varumärken och copyright symboler) och måste vara högst 50 tecken.
- **Sammanfattning** (krävs): Ange en kort beskrivning av ditt erbjudande som ska användas i sökresultat för marketplace-lista (listor). Upp till 100 tecken kan skrivas in i det här fältet.
- **Beskrivning av** (krävs): Ange en beskrivning av ditt erbjudande som ska visas i marketplace lista (listor) översikt. Överväg att ta ett värdeförslag, viktiga fördelar, kategori eller branschspecifika associationerna, köp i appen affärsmöjligheter, alla obligatoriska utlämnande och en länk till mer information.
Upp till 3 000 tecknen i texten kan anges i det här fältet. Ytterligare tips finns i artikeln [skriva en bra appbeskrivning](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Sök efter nyckelord**: Ange upp till tre sökord som kunder kan använda för att hitta ditt erbjudande i marketplace(s).
- **Komma igång instruktioner** (krävs): Beskriver hur du konfigurerar och börja använda din app för potentiella kunder.  Den här snabbstarten kan innehålla länkar till mer detaljerad onlinedokumentationen. Upp till 3 000 tecknen i texten kan anges i det här fältet. 

#### <a name="links"></a>Länkar

- **Sekretesspolicy** (krävs): Länka till din organisations sekretesspolicy. Du ansvarar för att säkerställa att din app uppfyller sekretesslagar och förordningar och för att tillhandahålla en giltig sekretesspolicy
- **CSP-programmet marknadsföringsmaterial** (valfritt): Du måste ange en länk till marknadsföringsmaterial om du väljer att utöka ditt erbjudande till den [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program. CSP utökar ditt erbjudande till ett bredare spektrum av kvalificerade kunder genom att aktivera CSP-partner att paketera, marknaden och sälja ditt erbjudande. Dessa återförsäljare behöver åtkomst till material för marknadsföring av ditt erbjudande. Mer information finns i [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Användbara länkar** (valfritt): Valfria ytterligare online dokument om din app eller relaterade tjänster som visas genom att tillhandahålla en **rubrik** och **URL**. Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en URL**.

#### <a name="contact-information"></a>Kontaktinformation

- **Kontakter**: För varje kundkontakt, anger du en anställd **namn** , **telefonnummer**, och **e-post** adress.  (Dessa *inte* visas offentligt). En **Support URL** krävs också för de **Supportkontakt** grupp.  (Den här informationen *kommer* visas offentligt).

**Supportkontakt** (krävs): För allmänna frågor.

**Tekniska kontakt** (krävs): För tekniska frågor.

**Kanal-Manager Kontakta** (krävs): Återförsäljare frågor relaterade till CSP-programmet.

#### <a name="files-and-images"></a>Filer och bilder

- **Dokument** (krävs): Lägga till relaterade marknadsföring dokument för ditt erbjudande i PDF-format, vilket ger ett minimum av en (1) och högst tre (3) dokument per erbjudandet.
- **Bilder** (valfritt): Det finns flera platser där ditt erbjudande logotypbilder kan förekomma i marketplace(s), kräver följande storlekar – liten: 48 x 48 bildpunkter _(obligatoriskt),_ Medium: 90 x 90 bildpunkter, stor: 216 x 216 bildpunkter _(obligatoriskt),_ Wide: 255 x 115 bildpunkter och Hero: 815 x 290 bildpunkter. Alla avbildningar måste finnas i. PNG-format.
- **Skärmbilder** (krävs): Lägg till skärmbilder som visar ditt erbjudande. Högst fem (5) skärmdumpar kan läggas till och bör ändras på minst 1 280 x 720 bildpunkter. Alla avbildningar måste finnas i. PNG-format.
- **Videor** (valfritt): Lägga till länkar till videor som visar ditt erbjudande. Du kan använda länkar till YouTube eller Vimeo videor som visas tillsammans med ditt erbjudande till kunder. Du måste också ange en miniatyrbild för video-storlek till 1 280 x 720 bildpunkter i PNG-format. Du kan visa upp till fyra videor per erbjudandet.

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare marketplace lista resurser

- [Metodtips för marketplace erbjuder listor](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Förhandsversion

Den **förhandsversion** fliken kan du definiera en begränsad **förhandsversion målgrupp** för att lansera ditt erbjudande innan du publicerar ditt erbjudande live till den bredare publiken i marketplace.

> [!IMPORTANT]
> Du måste välja **Driftsätt** innan ditt erbjudande ska publiceras live på den offentliga marketplace-målgruppen när du har kontrollerat att ditt erbjudande i en förhandsversion.

- **Definiera en förhandsversion målgrupp: Lägg till en enda AAD/MSA e-postadress per rad, tillsammans med en valfri beskrivning.**

Lägga till upp till tio (10) e-postadresser manuellt eller 20 (20) om och överför en CSV-fil för befintliga Microsoft konto (MSA) eller Azure Active Directory (AAD)-konton för att verifiera ditt erbjudande innan du publicerar live. Genom att lägga till dessa konton, definierar du en målgrupp som ska tillåtas förhandsversion åtkomst till ditt erbjudande innan det publiceras till marketplace(s). Om ditt erbjudande redan är aktiv, kan du fortfarande definiera en förhandsversion publik för att testa alla ändringar och uppdateringar på ditt erbjudande.

> [!NOTE]
> Förhandsversion av målgruppen skiljer sig från en privat publik. En förhandsversion publik får åtkomst till ditt erbjudande _tidigare_ att publiceras live i av marknadsplatser. Du kan också välja att skapa en plan och gör den tillgänglig endast för en privat publik. I den **plan lista** fliken kan du definiera en privat publik med den **det här är en privat plan** kryssrutan. Därefter anger du ett privat målgruppen för upp till 20 000 kunder som använder Azure-klient-ID: N.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den **teknisk konfiguration** fliken definierar teknisk information (URL-sökvägen, webhook, klient-ID och app-ID) som används för att ansluta till ditt erbjudande. Den här anslutningen gör det möjligt för oss att tillhandahålla ditt erbjudande som en resurs i kundens Azure-prenumeration om de väljer att hämta den.

- **Webbadress för informationssida** (krävs): Definiera platsen URL som kunder kommer att dirigeras till hamnar på när du hämtar ditt erbjudande från marketplace. Den här URL: en kommer också att den slutpunkt som ska ta emot anslutningen API: er för att underlätta handel med Microsoft.

- **Anslutningen webhook** (krävs): För alla asynkrona händelser som behöver skickar till dig för kundens räkning (exempel: Azure-prenumeration har gått ogiltig), vi kräver att du tillhandahåller en anslutning webhook. Om du inte redan har ett webhook-system på plats, den enklaste konfigurationen är att ha en Logikapp för HTTP-slutpunkt som ska lyssna efter alla händelser publiceras till den och hantera dem på rätt sätt (t.ex. https:\//prod-1westus.logic.azure.com:443/work). Mer information finns i [anropa, utlösare, eller kapsla arbetsflöden med HTTP-slutpunkter i logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure AD-klient-ID** (krävs): I Azure-portalen kräver vi att du [skapa en app i Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) så att vi kan verifiera anslutningen mellan våra två tjänster som finns bakom en autentiserad kommunikation. Att hitta den [klient-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), gå till Azure Active Directory och välj **egenskaper**, leta efter den **katalog-ID** nummer anges (t.ex.) 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD app-ID** (krävs): Du måste också din [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) och en autentiseringsnyckel. För att få dessa värden, gå till Azure Active Directory och välj **appregistreringar**, leta efter den **program-ID** nummer anges (t.ex. 50c464d3-4930-494c-963c-1e951d15360e). För att hitta autentiseringsnyckeln, gå till **inställningar** och välj **nycklar**. Du måste ange en beskrivning och varaktighet och tas sedan erbjuds ett numeriskt värde.

 Observera att Azure-program-ID är kopplad till Publicerings-ID, så se till att samma program-ID används i alla dina erbjudanden.

## <a name="plan-overview"></a>– Översikt

Den **– översikt** fliken låter dig erbjuda en mängd olika alternativ inom samma erbjudandet. De här planerna (kallas ibland SKU: er) kan skiljer sig åt vad gäller nivåer av tjänsten, intäkter och version. Du måste konfigurera minst en plan för att kunna sälja ditt erbjudande i marketplace.

När du skapat visas din plan namn, ID, prismodeller, tillgänglighet (offentliga eller privata), aktuella publicera status och alla tillgängliga åtgärder.

**Åtgärder** tillgängliga i den **– översikt** varierar beroende på den aktuella statusen för din plan och kan innehålla:

- Om Planstatus är **Draft** – ta bort utkast
- Om Planstatus är **Live** – stoppa sälja plan eller synkronisera privata målgrupp

**Skapa en ny plan** (minimum för en plan för kunder som väljer för att sälja via Microsoft)

- **Plan-ID:** Skapa ett unikt plan-ID för varje plan i det här erbjudandet. Detta ID ska vara synliga för kunder i de produkten URL och Azure Resource Manager-mallarna (om tillämpligt). Använd endast gemena alfanumeriska tecken, bindestreck och understreck. Högst 50 tecken tillåts för den här planen-ID. Observera att det ID: T inte kan ändras efter att välja Skapa.
- **Namn på prenumerationsavtal:** Kunderna ser det här namnet när du bestämmer vilken plan att välja i ditt erbjudande. Skapa en unik erbjudandenamn för varje plan i det här erbjudandet. Plannamnet används för att skilja programvaruplaner som kan vara en del av samma erbjudandet (t.ex. Erbjudandets namn: Windows Server. planer: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Plan lista

Den **plan lista** fliken visar språk (och marknader) där planen är tillgänglig, engelska (USA) är för närvarande den enda platsen som är tillgänglig. Dessutom kan visar den här sidan status för språkspecifika lista och datum/tid som det har lagts till. Du måste definiera marketplace-informationen (erbjuder namn, beskrivning, söktermer, etc.) för varje språk / marknaden.

#### <a name="plan-listing-details"></a>Lista information om plan

När du väljer ett av de plan språk visas den **plan lista** information, inklusive **namn** och **beskrivning.**

- **Namn på**: Förifylld baserat på förhandsversionen av **ny plan** posten och visas som rubrik på din erbjudandets ”programvara plan” visas i marketplace.
- **Beskrivning:** Den här beskrivningen är en möjlighet att förklara vad gör den här programvaruplanen unika och eventuella skillnader från andra programvaruplaner i ditt erbjudande. Kan innehålla upp till 500 tecken.

Välj **spara** när du har slutfört de här fälten.

#### <a name="plan-pricing-and-availability"></a>Planera priser och tillgänglighet

Den **priser och tillgänglighet** fliken kan du konfigurera marknader som den här planen kommer att vara tillgänglig i önskad intäkter modellen, priser och fakturering termen. Dessutom kan ange du om du vill att planen ska visas för alla eller bara till specifika kunder (en privat publik).

#### <a name="markets"></a>Marknader

- **Redigera marknader** (valfritt)

Var plan måste finnas i minst en marknad. Markera kryssrutan för valfri marknaden plats där du vill tillhandahålla den här planen. En sökrutan och knappen för att välja ”skatt efterges” länder där Microsoft återförvisar försäljning och Använd skatt för din räkning är att. 


Om du redan har ställt in priser för din plan i USA dollar (USD) och Lägg till en annan plats på marknaden, beräknas priset för den nya marknaden enligt de aktuella växelkurserna. Du bör alltid läsa igenom priset för varje marknad innan du publicerar. Priser kan granskas med hjälp av länken ”exportera priser (xlsx)” när du har sparat ändringarna.

#### <a name="pricing"></a>Prissättning

- **Prismodellen**: Fasta eller klient baserat

**Fast pris:** Aktivera åtkomst till ditt erbjudande med ett fast pris per månad eller årlig pris-pris. Detta är ibland kallas platsbaserad priser.

**Plats baserat:** Aktivera åtkomst till ditt erbjudande med priset baserat på antalet användare kommer åt erbjudandet eller några *platser*. Den här klientbaserade modellen kan du ange lägsta och högsta antal tillåtna klienter baserat på priset. På så sätt kan kan olika prispunkter konfigureras baserat på hur många användare genom att konfigurera flera planer.  De här fälten är valfria. Om fältet lämnas tomt tolkas antalet platser som inte har en gräns (min 1) och max för så många som har stöd för systemet. De här fälten kan redigeras som en del av en uppdatering av din plan.

När publicerats kan inte fakturering prissättning modellen valet ändras. Dessutom måste alla planer för erbjudandet i samma dela samma prismodellen.

- **Fakturering termen**: Månatligt eller årligt

Välj den frekvens som kunderna måste betala priset som anges. Du måste tillhandahålla minst en månad eller årlig pris, eller båda alternativen kan göras tillgängliga för kunder.

- **Priset**: USD per månad eller USD per år

Ange priser i lokal valuta (USD = US-Dollar) omvandlas till lokal valuta för alla valda marknader med hjälp av de aktuella växelkurserna under installationen. Verifiera dessa priser innan du publicerar genom att exportera priserna kalkylblad och granska priset på alla marknader. Om du vill ange anpassade priser i en enskild marknad, ändra och importera prissättning kalkylbladet. Du ansvarar för att verifiera den här prissättningen och äger de här inställningarna.
**Du måste först spara dina ändringar i prissättning för att möjliggöra export av priser för data.*

Granska dina priser noggrant innan du publicerar, eftersom det inte finns några begränsningar för vad som kan ändra när en plan har publicerats:

- När en plan har publicerats kan inte ändras prismodellen.
- När en fakturering term har publicerats för en plan, den kan inte tas bort senare.
- När ett pris för en marknad i din plan har publicerats kan den inte ändras senare.

### <a name="plan-audience"></a>Planera målgrupp

Du har möjlighet att konfigurera varje plan ska vara synlig för alla eller för en specifik målgrupp du väljer. Du kan tilldela medlemskap i den här begränsad publik med hjälp av Azure AD-klient ID: N.

#### <a name="privacy"></a>Sekretess

- **Det här är en privat plan** (valfritt kryssruta)

Den här kryssrutan om du vill göra din plan privata och visas endast begränsad publik valfritt. När publicerats som en privat plan kan du uppdatera målgruppen eller välja att göra planen som är tillgänglig för alla. När en plan har publicerats som synligt för alla, måste det vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

- **Begränsad publik (klient-ID)**

Tilldela målgruppen som har åtkomst till den här privata planen. Åtkomst tilldelas med hjälp av klient-ID: N med alternativet för att inkludera en beskrivning av varje klient-ID som har tilldelats. Högst 10 klient-ID: N kan läggas till eller 20 000 kunder klient-ID: N om du importerar en .csv-fil i kalkylbladet.

En klient är en representation av en organisation med ett ID som representeras som en GUID (Globally Unique Identifier, ett 128-bitars heltal som används för att identifiera resurser). Det är en dedikerad instans av Azure AD som en organisation eller apputvecklare får när organisationen eller apputvecklaren skapar en relation med Microsoft – som att registrera sig för Azure, Microsoft Intune eller Microsoft 365. Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter. Du kan kontrollera klienten genom att logga in på Azure-portalen med det konto som du vill använda för att hantera ditt program. Om du har en klient kommer du automatiskt att loggas in och kan se klientorganisationens namn direkt under namnet på ditt konto. Hovra över ditt kontonamn längst upp till höger i Azure-portalen så visas namn, e-post, katalog/klient-ID (ett GUID) och domän. Om ditt konto är kopplat till flera klienter måste du välja namnet på ditt konto för att öppna en meny där du kan växla mellan klienter. Varje klient har sitt eget klient-ID. Du kan också leta upp med hjälp av en domän-URL för namnet på din organisations klient-ID: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

SaaS-erbjudanden använder klient-ID: N för att definiera en privat publik, kan andra typer av erbjudanden använda Azure-prenumeration ID: N (som representeras också som GUID).

> [!NOTE]
> Privata målgrupp (eller begränsad publik) skiljer sig från en förhandsversion publik. I den **[förhandsversion](#preview)** fliken kan du definiera en förhandsversion publik. En förhandsversion publik får åtkomst till ditt erbjudande *tidigare* till erbjudandet live publiceras i marketplace. Medan privata målgrupp beteckning gäller endast för en specifik plan, förhandsversion målgruppen kan visa alla prenumerationer (privat eller inte), men endast i förhandsversionen begränsad medan planen testats och godkänts.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exempel lista med planer inom ett marketplace-erbjudande

![Exempel marketplace plan lista med anteckningar](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Testkör

Den **Testkör** fliken kan du ställa in en demonstration (eller ”Testkör”) vilket innebär att kunder kan prova erbjudandet innan du bestämmer dig för att köpa den. Mer information finns i artikeln [vad är en provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Om du inte längre vill att tillhandahålla en testenhet för ditt erbjudande, återgår du till den **[erbjuder installationsprogrammet](#offer-setup)** sidan och avmarkera **aktivera provkörning**.

### <a name="technical-configuration"></a>Teknisk konfiguration
Följande typer av test Drive är tillgängliga, var och en med sin egen teknisk konfigurationskrav.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikapp](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (teknisk konfiguration krävs inte)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Testkör teknisk konfiguration för Azure Resource Manager

En Distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder endast Azure-resurser. Läs mer om hur du konfigurerar en [Azure Resource Manager-provkörning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regioner** (krävs): Det finns för närvarande 26 stöds av Azure-regioner där din provkörning kan göras tillgängliga. Normalt kommer du att tillgängliggöra din provkörning i regioner där du tror att det största antalet kunder, så att de kan välja den närmaste regionen för bästa prestanda. Du måste se till att din prenumeration kan du distribuera alla resurser som behövs i var och en av de regioner som du väljer.

- **Instanser**: Välj typ (frekvent eller kallt) och nummer över tillgängliga instanser som ska multipliceras med antalet regioner med ditt erbjudande.

**Frekvent**: Den här instansen är distribuerade och väntar på åtkomst per valda regionen. Kunder kan direkt komma åt *frekvent* instanser av en testenhet i stället för att behöva vänta på en distribution. Nackdelen är att dessa instanser körs alltid på din Azure-prenumeration, så leder de en större upptid kostnad. Vi rekommenderar starkt att du har minst en *frekvent* instans, eftersom de flesta kunder inte vill vänta för fullständig distributioner, vilket resulterar i en Samlingsbibliotek i KUNDANVÄNDNING om ingen *frekvent* instans är tillgänglig.

**Kalla**: Den här typen av instans representerar det totala antalet instanser som eventuellt kan distribueras per region. Kalla instanser kräver hela testa enheten Resource Manager-mallen ska distribueras när en kund begär provkörning, så *kalla* instanser är mycket längre tid att läsa in än *frekvent* instanser. Nackdelen är att du behöver bara betalar bara för test drive, är det *inte* alltid körs på Azure-prenumerationen som med en *frekvent* instans.

- **Test drive Azure Resource Manager-mall**: Ladda upp ZIP som innehåller Azure Resource Manager-mallen.  Läs mer om hur du skapar en Azure Resource Manager-mall i snabbstartsartikeln [skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Varaktighet för provkörningen** (krävs): Ange hur lång tid som Test Drive förblir aktiv i antal timmar. Test Drive avslutas automatiskt efter denna tid har löpt ut. Varaktigheten kan bara ställas in med ett heltal timmar (t.ex.) ”2” timmar är ”1.5” inte giltig).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Testkör teknisk konfiguration för Dynamics 365

Microsoft kan ta bort komplexiteten för att ställa in en testkörning av som är värd för och underhålla Tjänstetablering och distribution med hjälp av den här typen av provkörning. Konfigurationen för den här typen av värdbaserade provkörning är detsamma oavsett om test drive är inställd på en Business Central, Kundengagemang eller åtgärder publik.

- **Maximalt antal samtidiga provkörningarna** (krävs): Ange det maximala antalet kunder som kan använda din provkörning i taget. Varje samtidig användare förbrukar en Dynamics 365-licens, medan test drive är aktiv, så måste du kontrollera att du har tillräckligt med licenser för att stödja den maximala storleksgränsen. Rekommenderat värde från 3 till 5.

- **Varaktighet för provkörningen** (krävs): Ange hur lång tid som Test Drive förblir aktiva genom att definiera hur många timmar. Efter detta antal timmar sessionen avslutas och inte längre använda en av dina licenser. Vi rekommenderar ett värde på 2 – 24 timmar beroende på komplexiteten i ditt erbjudande. Varaktigheten kan bara ställas in med ett heltal timmar (t.ex.) ”2” timmar är ”1.5” inte giltig).  Användaren kan begära en ny session om de körs utanför tid och vill använda test drive igen.

- **Instans-URL: en** (krävs): URL: en där kunden kommer att börja sina provkörning. Vanligtvis URL: en för din Dynamics 365-instans som kör din app med exempeldata som är installerat (t.ex. https://testdrive.crm.dynamics.com).

- **Instans-URL: en för webb-API: et** (krävs): Hämta URL: en för webb-API för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och gå till **inställningar** \&gt; **Anpassning** \&gt; **Utvecklarresurser** \&gt; **Instans webb-API (tjänstens rot-URL)** , kopiera den URL som finns här (t.ex. https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Rollnamnet** (krävs): Ange säkerhetsrollsnamn som du har definierat i din anpassade provkörning i Dynamics 365. Detta kommer att tilldelas till användaren under deras provkörning (t.ex. test-animera roller).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Teknisk konfiguration för Logic app-provkörning

Alla anpassade produkter bör använda den här typen av test drive Distributionsmall som omfattar en mängd olika komplexa lösningsarkitekturer. Mer information om hur du konfigurerar Logikapp testkörningar, besök [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [Kundengagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (krävs, enskild markering listrutan): Det finns för närvarande 26 stöds av Azure-regioner där din provkörning kan göras tillgängliga. Resurser för din logikapp ska distribueras i den region som du väljer. Om din Logikapp har alla anpassade resurser som lagras i en viss region kan du kontrollera att den regionen väljs här. Det bästa sättet att göra detta är att fullständigt distribuera Logikappen lokalt på din Azure-prenumeration i portalen och kontrollera att den fungerar korrekt innan du gör det här alternativet.

- **Maximalt antal samtidiga provkörningarna** (krävs): Ange det maximala antalet kunder som kan använda din provkörning i taget. Dessa test enheter redan har distribuerats, vilket innebär att direkt komma åt dem utan att behöva vänta en distribution.

- **Varaktighet för provkörningen** (krävs): Ange hur lång tid som Test Drive förblir aktiv i antal timmar. Test drive avslutas automatiskt efter denna tid har löpt ut.

- **Azure resursgruppens namn** (krävs): Ange den [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) namn där din Logikapp provkörning har sparats.

- **Azure logic app name** (krävs): Ange namnet på den logikapp som tilldelar test drive för användaren. Den här logikappen måste sparas i gruppen Azure-resurser.

- **Avetablering namn för logikappen** (krävs): Ange namnet på den logikapp som deprovisions test drive när kunden har slutförts. Den här logikappen måste sparas i gruppen Azure-resurser.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Teknisk konfiguration krävs inte för Power BI-provkörningar

Produkter som du vill visa en interaktiva Power BI-objekt som kan använda en inbäddad länk för att dela en instrumentpanel som skapats specifikt som deras provkörning, ingen ytterligare teknisk konfiguration krävs. Läs mer om hur du konfigurerar[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) mall appar.

### <a name="deployment-subscription-details"></a>Distributionsinformation för prenumeration

Skapa och tillhandahålla en separat, unika Azure-prenumeration för att distribuera provkörning för din räkning. (Krävs inte för Power BI test Drive).

- **Azure-prenumerations-ID** (krävs för Azure Resource Manager och Logic apps): Ange prenumerations-ID om du vill bevilja åtkomst till din Azure-Kontotjänster för Resursanvändning reporting och fakturering. Vi rekommenderar att du överväger [skapar en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) ska användas för test Drive om du inte redan har en. Du kan hitta din Azure-prenumerations-ID genom att logga in på [Azure-portalen](https://portal.azure.com/) och navigera till den **prenumerationer** fliken i menyn till vänster. När du väljer fliken visas ditt prenumerations-ID (t.ex. ”a83645ac-1234-5ab6-6789-1h234g764ghty”).

- **Azure AD-klient-ID** (krävs): Ange din Azure Active Directory (AD) [klient-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). För att hitta detta ID, logga in på den [Azure-portalen](https://portal.azure.com/), Välj fliken Active Directory i den vänstra menyn, Välj **egenskaper** , leta efter den **katalog-ID** nummer anges (t.ex.) 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp din organisations klient-ID med hjälp av din domän namn URL på: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Azure AD-klientnamn** (krävs för Dynamics 365): Ange namnet på din Azure Active Directory (AD). För att hitta det här namnet, logga in på den [Azure-portalen](https://portal.azure.com/), i det övre högra hörnet ditt klientnamn visas under namnet på ditt konto.

- **Azure AD app-ID** (krävs): Ange din Azure Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). För att hitta detta ID, logga in på den [Azure-portalen](https://portal.azure.com/), Välj fliken Active Directory i den vänstra menyn, Välj **appregistreringar**, leta efter den **program-ID** tal i listan (t.ex. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appnyckeln** (krävs): Ange din Azure Active Directory (AD) [programnyckel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). För att hitta detta ID, logga in på den [Azure-portalen](https://portal.azure.com/), Välj fliken Active Directory i den vänstra menyn, Välj **appregistreringar** och välj sedan **inställningar**  >  **Nycklar**.

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

### <a name="test-drive-listings-optional"></a>Test drive listor (valfritt)

Den **Test Drive publiceringar** alternativet finns under den **Testkör** fliken visar språk (och marknader) där din provkörning är tillgänglig, engelska (USA) är för närvarande den enda platsen som är tillgänglig . Dessutom kan visar den här sidan status för språkspecifika lista och datum/tid som det har lagts till. Du måste definiera test drive information (beskrivning, Användarhandbok, videor, osv.) för varje språk /-marknaden.

- **Beskrivning av** (krävs): Beskriver din provkörning vad ska kunna visas, mål för användaren att experimentera med och vilka funktioner du vill utforska relevant information som hjälper användaren avgöra om att hämta ditt erbjudande. Upp till 3 000 tecknen i texten kan anges i det här fältet. 

- **Komma åt information** (krävs för Azure Resource Manager och logik testenheter): Förklara vad en kund behöver veta för att komma åt och använda den här testkörningen. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör veta att komma åt funktionerna i hela test drive. Upp till 10 000 tecknen i texten kan anges i det här fältet.

- **Användarhandbok** (krävs): En detaljerad genomgång av din test drive-upplevelse. Användarhandboken bör omfatta exakt vad du vill att kunden ska göras med hjälp av upplever test drive och fungerar som en referens för frågor som de kan ha. Filen måste vara i PDF-format och ge dem namn (max till 255 tecken) när du har överfört.

- **Videor: Lägg till videor** (valfritt): Videor kan laddas upp till YouTube eller Vimeo och refereras till här med en länk och miniatyr-avbildning (533 x 324 bildpunkter), så att en kund kan se en genomgång av information som hjälper dem att bättre förstå test drive, inklusive hur du använder funktionerna i din erbjuder och förstå scenarier som visar deras fördelar.
  - **Namn på** (krävs)
  - **URL (YouTube eller Vimeo endast)** (krävs)
  - **Miniatyr (533 x 324px)** : Image-filen måste vara i PNG-format.

Välj **spara** när du har slutfört de här fälten.

## <a name="publish"></a>Publicera

#### <a name="submit-offer-to-preview"></a>Skicka erbjuder att förhandsgranska

När du har slutfört alla avsnitt som krävs för erbjudandet, Välj **publicera** i det övre högra hörnet i portalen. Du kan till den **granskning och publicera** sidan. 

Om det här är första gången du publicerar det här erbjudandet kan du:

- Se Slutförandestatus för varje avsnitt i erbjudandet.
    - *Inte startad* – innebär att avsnittet inte har använt och måste slutföras.
    - *Ofullständig* – innebär avsnittet har fel som behöver åtgärdas eller kräver mer information anges. Gå tillbaka till avsnitt och uppdatera den.
    - *Fullständig* – innebär avsnittet är klar, alla nödvändiga data har angetts och det finns inga fel. Alla delar av erbjudandet måste vara i tillståndet klar innan du kan skicka erbjudandet.
- Ge testet instruktioner till certification-teamet så att din app har testats korrekt, utöver eventuella aviserar om ytterligare kommentarer som är användbart för att förstå din app.
- Skicka erbjudande för publicering genom att välja **skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en förhandsversion av erbjudandet är tillgängligt för dig att granska och godkänna. Du måste gå tillbaka till Partner Center och välj **Go live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om en privat erbjuder till privata publik).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Marketplace för kommersiella](./update-existing-offer.md)
