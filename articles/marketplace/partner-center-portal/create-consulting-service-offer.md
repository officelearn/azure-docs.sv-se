---
title: Skapa ett erbjudande om konsulttjänster i Partner Center - Azure Marketplace
description: Lär dig hur du publicerar ett erbjudande om konsulttjänster till Antingen Azure Marketplace eller AppSource med Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ce8df36d3417417a5f70a5385aa94d9c8c7ff0cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674285"
---
# <a name="consulting-service-creation-overview"></a>Översikt över att skapa konsulttjänster

> [!IMPORTANT]
> Vi flyttar hanteringen av dina konsulttjänster från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Azure och Dynamics 365 konsulttjänsterbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du publicerar ett erbjudande om konsulttjänster på antingen [Azure Marketplace](https://azuremarketplace.microsoft.com/) eller [AppSource](https://appsource.microsoft.com/). Listkonsulttjänster erbjuder baserat på Microsoft [Dynamics 365](https://dynamics.microsoft.com/) och Power Platform på AppSource. Erbjudanden om listkonsulttjänster baserat på Microsoft Azure på Azure Marketplace.

## <a name="publishing-benefits"></a>Publiceringsförmåner

Fördelar med att publicera på den kommersiella marknaden:

- Marknadsför ditt företag med hjälp av Microsofts varumärke.
- Potentiellt nå mer än 100 miljoner Office 365- och Dynamics 365-användare på AppSource och mer än 200 000 organisationer via Azure Marketplace.
- Få högkvalitativa leads från dessa marknadsplatser.
- Få dina tjänster marknadsförda av Microsoft-fält- och telefonförsäljningsteamen

## <a name="requirements"></a>Krav

### <a name="business-requirements"></a>Affärskrav

För erbjudanden där Azure väljs som primärprodukt måste ditt erbjudande lista minst en av följande fullständiga intjänade kompetenser:

- Apputveckling
- Integrering av program
- Hantering av programmets livscykel
- Molnplattform
- Dataanalys
- Datacenter
- Dataplattform
- DevOps

För erbjudanden med något av följande alternativ som valts som primärprodukt måste du uppfylla respektive behörighetskrav som anges eller ha ett samförsäljningserbjudande för den primära produkt som tjänsteerbjudandet är relaterat till.

**Program för kundengagemang**

- **Gäller för:** Dynamics 365 Sales, Dynamics 365 Marketing, Dynamics 365 Customer Service, Dynamics 365 Field Service, Dynamics 365 Human Resources

- **Kriterier:** Måste vara guld- eller silvercertifierade i [molnet affärsprogram kompetens](https://partner.microsoft.com/membership/cloud-business-applications-competency) för kundengagemang alternativ.

**Finansierings- och verksamhetsapplikationer**

- **Gäller för:** Dynamics 365 Finance, Dynamics 365 Operations, Dynamics 365 Commerce, Dynamics 365 Human Resources, Dynamics 365 Project Service Automation

- **Villkor:** Måste vara guld- eller silvercertifierade i [cloud business applications-kompetensen](https://partner.microsoft.com/membership/cloud-business-applications-competency) för unified operations-alternativet.

**Dynamics 365 Customer Insights**

- **Kriterier**: Måste ha minst en lyckad implementering av [Dynamics 365 Customer Insights](https://dynamics.microsoft.com/ai/customer-insights/) med minst fem mått och fem segment.

**Dynamics 365 Business Central**

- **Villkor:** Måste vara guld- eller silvercertifierat i [kompetensen Företagsresursplanering](https://partner.microsoft.com/membership/enterprise-resource-planning-competency) och betjäna minst tre kunder eller ha publicerat ett Business Central-program i Microsoft AppSource.

**Power BI**

- **Villkor:** Måste anges på [Power BI-partner showcase](https://powerbi.microsoft.com/partner-showcase/).

**Power Apps**

- **Kriterier:** Måste vara berättigade till avancerade förmåner i [Power Apps Partnership-programmet.](https://aka.ms/PowerAppsPartner)

Mer information om hur du uppfyller dessa förutsättningar finns i [förutsättningarna för konsulttjänster](consulting-service-prerequisites.md).

### <a name="logistical-requirements"></a>Logistiska krav

Om du vill skapa ett erbjudande om konsulttjänster på Antingen Azure Marketplace eller AppSource måste du först [ha ett utgivarkonto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)och ditt konto måste vara registrerat i det kommersiella marketplace-programmet.

## <a name="create-a-new-consulting-service-offer-in-partner-center"></a>Skapa ett nytt konsultserviceerbjudande i Partner Center

När du har uppfyllat de krav som beskrivs ovan följer du dessa steg för att skapa ett konsultserviceerbjudande.

1. Logga in i [Partnercenter](https://partner.microsoft.com)och välj sedan **Instrumentpanelen** på den övre menyn.
2. Välj **Kommersiell marknadsplats**i fältet till vänster och välj sedan **Översikt**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Illustrerar menyn för kommersiell marknadsplats":::

3. Välj **+ Nytt erbjudande**och välj sedan **Konsulttjänst**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Illustrerar knappen för att skapa ett nytt erbjudande.":::

4. Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

    - Detta ID är synligt för kunder i webbadressen för marketplace-erbjudandet.
    - Använd bara gemener, siffror, streck och understreck, men inga blanksteg. Längden är begränsad till 50 tecken. Om du till exempel anger **test-erbjudande-1**blir `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`erbjudande-URL:en .
    - Erbjudande-ID:t kan&#39;inte ändras när du har valt **Skapa**.

5. Ange ett **offeralias**. Det här är namnet som används för att referera till erbjudandet i Partner Center.

    - Det här namnet används&#39;inte på marknadsplatsen. Det&#39;skiljer sig från erbjudandets namn och andra värden som visas för kunderna. Du kan använda det här fältet för att tilldela ett namn till erbjudandet som är mer användbart för dig för att identifiera erbjudandet internt. det visas inte för kunderna.
    - Erbjudandet alias kan&#39;inte ändras när du väljer **Skapa**.

När du har angett dessa två värden väljer du **Skapa** för att fortsätta till **inställningssidan Erbjudande.**

## <a name="offer-setup"></a>Inställningar för erbjudande

När du har angett ett offer-ID- och erbjudandealias skapar Partner Center ett utkasterbjudande och visar **inställningssidan för Erbjudande.** Följ dessa steg för att ställa in ditt erbjudande.

### <a name="connect-lead-management"></a>Anslut leadhantering

När du publicerar ditt erbjudande på marknadsplatsen med Partner Center _måste_ du ansluta det till ett CRM-system (Customer Relationship Management) eller marketing automation. Detta gör att du kan få kundkontaktinformation så snart någon uttrycker intresse för eller använder din produkt.

1. Välj **Anslut** för att ange var du vill att vi ska skicka kundleads. Partner Center stöder följande system:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kundengagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system&#39;inte visas ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra leaddata för kunder och exporterar sedan data till CRM-systemet.

2. Anslut ditt erbjudande till leaddestinationen när du publicerar i Partner Center.
3. Kontrollera att anslutningen till lead-målet är korrekt konfigurerad. När du har publicerat den i Partner Center&#39;vi validera anslutningen och skicka en testledare till dig. Medan du förhandsgranskar erbjudandet innan det visas kan du också testa din leadanslutning genom att försöka köpa erbjudandet själv i förhandsversionen.
4. Se till att anslutningen till leaddestinationen förblir uppdaterad så att du inte&#39;förlora några leads.

Här är några ytterligare lead hanteringsresurser:

- [Översikt över leadhantering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över leadhantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Kontrollera att popup-blockeraren är avstängd)

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Egenskaper.

### <a name="properties"></a>Egenskaper

På den här sidan kan du ställa in den primära produkt som din konsulttjänst erbjuder bäst täcker, ange en konsulttjänsttyp och välja tillämpliga produkter.

1. Välj en **primär produkt** i listrutan.
2. Välj en **konsulttjänsttyp** i listrutan:

    - **Bedömning** : En utvärdering av en kund&#39;miljö för att avgöra tillämpligheten av en lösning och ge en uppskattning av kostnad och timing.
    - **Briefing** : En introduktion till en lösning eller en konsulttjänst för att dra kundernas intresse genom att använda ramverk, demonstrationer och kundexempel.
    - **Implementering** : En komplett installation som resulterar i en fullt fungerande lösning. Begränsa till lösningar som kan implementeras på två veckor eller mindre.
    - **Proof of concept** : En implementering med begränsad omfattning för att avgöra om en lösning uppfyller kundernas krav.
    - **Workshop** : Ett interaktivt engagemang som utförs på en kund&#39;lokaler. Det kan handla om utbildning, genomgångar, utvärderingar eller demonstrationer som bygger på kundens&#39;data eller miljö.

1. Om du har valt en primär produkt av **Azure**väljer du upp till tre **lösningsområden**. Dessa gör det enklare för kunder på Azure Marketplace att hitta ditt erbjudande. Om du inte&#39;du inte valde Azure hoppar du över det här steget.
2. Om du har valt en _annan_ primärprodukt än Azure väljer du upp till tre **tillämpliga produkter**. Dessa gör det enklare för kunder i AppSource att hitta ditt erbjudande. Mer information finns i [Microsoft AppSource Consulting Service Listing Guidelines](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Välj upp till sex **branscher** som ditt erbjudande gäller för. Detta kommer att göra det lättare för kunderna att hitta ditt erbjudande.
4. Lägg till upp till tre **kompetenser** som ditt företag har tjänat för att visa på din konsulttjänster erbjudande notering. Minst en kompetens krävs förutom Azure Expert MSP&#39;s och Azure Networking MSP&#39;s.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Offer list.

## <a name="offer-listing"></a>Lista över erbjudande

Här&#39;du definiera erbjudandeinformation som visas på marknadsplatsen. Detta inkluderar erbjudandet namn, beskrivning, bilder och så vidare. Var noga med att följa de principer som beskrivs på [Microsoft&#39;:s policysida](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) när du konfigurerar det här erbjudandet.

> [!NOTE]
> Erbjudandeuppgifter är&#39;inte behöver vara på engelska om erbjudandebeskrivningen &quot;börjar med frasen, Denna ansökan är endast tillgänglig på [icke-engelska språket]. &quot; Det&#39;också okej att ge en användbar länk för att erbjuda innehåll på ett språk som&#39;skiljer sig från den som används i Erbjudandet notering detaljer.

### <a name="name"></a>Namn

Namnet du anger här visas som titeln på ditt erbjudande. Det här fältet är förfyllt med den text som du angav i rutan **Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara varumärkesskyddade (och du kan inkludera varumärkes- eller upphovsrättssymboler).
- Kan&#39;inte vara mer än 50 tecken lång.
- Kan&#39;inte innehålla emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

Ge en kort beskrivning av ditt erbjudande. Detta kan vara upp till 100 tecken långt och används i marketplace-sökresultat.

### <a name="description"></a>Beskrivning

Ge en längre beskrivning av ditt erbjudande, upp till 3000 tecken. Detta visas för kunder i översikten för marknadsplatsannonser.

Inkludera ett eller flera av följande i beskrivningen:

- Värdet och de viktigaste fördelarna med ditt erbjudande ger
- Kategori- eller branschorganisationer, eller båda
- Köpmöjligheter i appen
- Alla nödvändiga upplysningar

Här är några tips för att skriva din beskrivning:

- Beskriv tydligt värdet av ditt erbjudande i de första meningarna i din beskrivning. Inkludera följande:
  - Beskrivning av erbjudandet.
  - Den typ av användare som drar nytta av erbjudandet.
  - Kundens behov eller utfärdar erbjudandeadresserna.
- Kom ihåg att de första meningarna kan visas i sökresultaten.
- Lita inte&#39;på funktioner för att sälja din produkt. Fokusera istället på det värde som ditt erbjudande ger.
- Försök att använda branschspecifika ordförråd eller förmånsbaserad ordalydelse.

Om du vill göra beskrivningen mer engagerande använder du RTF-redigeraren för att formatera beskrivningen. Med RTF-redigeraren kan du lägga till tal, punkter, fetstil, kursiv stil och indrag för att göra beskrivningen mer läsbar.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Illustrerar RTF-redigeraren för att skriva erbjudandebeskrivningen." border="false":::

### <a name="keywords"></a>Nyckelord

Ange upp till tre sökord som är relevanta för din primära produkt- och konsulttjänst. Dessa kommer att göra det lättare att hitta ditt erbjudande.

### <a name="duration"></a>Varaktighet

Ange den förväntade varaktigheten för det här engagemanget med kunden.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-post och telefonnummer för en **primär** och **sekundär kontakt**. Den här informationen visas&#39;inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner (Cloud Solution Provider).

### <a name="supporting-documents"></a>Verifikationer

Lägg till upp till tre (men minst ett) stöddokument för ditt erbjudande.

### <a name="marketplace-images"></a>Marketplace-bilder

Ange logotyper och bilder som du kan använda med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

#### <a name="store-logos"></a>Lagra logotyper

Ange .png-filer för ditt erbjudande&#39;s logotyp i var och en av följande pixelstorlekar:

- **Liten (48 x 48)**
- **Stor (216 x 216)**

Alla logotyper krävs och används på olika platser på marknadsplatsen notering.

#### <a name="screenshots-optional"></a>Skärmdumpar (valfritt)

Lägg till upp till fem skärmbilder som visar hur erbjudandet fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i .png-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fyra videor som visar ditt erbjudande. Ange videons namn&#39;, dess webbadress (URL) och en miniatyrbild av videon med en storlek på 1 280 x 720 pixlar.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Prissättning och tillgänglighet.

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Här definierar du element som prissättning, marknad och en privat nyckel.

1. **Marknad:** Ställ in marknaden ditt erbjudande kommer att finnas tillgänglig i. Du får endast välja en marknad per erbjudande.
    1. För marknaderna i USA eller Kanada väljer du **Redigera delstater** (eller **provinser)** för att ange var erbjudandet ska vara tillgängligt.
2. **Förhandsgranska målgrupp:** Konfigurera **dölj nyckel** som används för att ställa in den privata målgruppen för ditt erbjudande.
3. **Prissättning**: Ange om ditt erbjudande är ett **gratis** eller **betalt** erbjudande.

    > [!NOTE]
    > Konsulttjänster erbjudanden är endast för notering. Alla transaktioner kommer att ske direkt, utanför den kommersiella marknaden.

4. För ett betalt erbjudande anger du **pris och valuta** och om priset är **fast** eller **uppskattat**. Om du uppskattar måste du i beskrivningen ange vilka faktorer som ska påverka priset.
5. Välj **Spara utkast**.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in ditt erbjudande om att granska och publicera.

1. När du är redo att publicera ditt konsulttjänsterbjudande klickar du på **Granska och publicera**.
2. Gå igenom detaljerna på den slutliga inlämningssidan.
3. Om det behövs, skriv en anteckning till certifieringsteamet om du tror att någon av detaljerna i ditt erbjudande kräver förklaring.
4. När du är klar väljer du **Skicka**.
5. På sidan **Erbjudandeöversikt** visas vilken publiceringsfas ditt erbjudande finns i.

Mer information om hur länge du kan förvänta dig att erbjudandet ska vara i varje publiceringsfas finns i [Kontrollera publiceringsstatusen för ditt Commercial Marketplace-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Uppdatera dina befintliga konsulttjänster erbjudanden

- [Uppdatera ett befintligt erbjudande på den kommersiella marknaden](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
