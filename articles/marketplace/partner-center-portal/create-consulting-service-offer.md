---
title: Skapa ett konsult tjänst erbjudande i Partner Center – Azure Marketplace
description: Lär dig hur du publicerar ett konsult tjänst erbjudande till antingen Azure Marketplace eller AppSource med partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869783"
---
# <a name="create-a-consulting-service-offer"></a>Skapa ett konsult tjänst erbjudande

> [!IMPORTANT]
> Vi flyttar hantering av konsult tjänst erbjudanden från Cloud Partner Portal till Partner Center. Följ anvisningarna i [Azure-och Dynamics 365 Consulting Service-erbjudandet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) för Cloud Partner Portal att hantera dina erbjudanden innan du migrerar dina erbjudanden.

Den här artikeln beskriver hur du publicerar ett konsult tjänst erbjudande på antingen [Azure Marketplace](https://azuremarketplace.microsoft.com/) eller [AppSource](https://appsource.microsoft.com/). Lista konsult tjänst erbjudanden baserade på Microsoft [Dynamics 365](https://dynamics.microsoft.com/) och Power Platform på AppSource. Visa en lista över konsult tjänst erbjudanden baserade på Microsoft Azure på Azure Marketplace.

Om du vill skapa ett konsult tjänst erbjudande i Azure Marketplace eller AppSource Consulting Services måste du först [ha ett utgivar konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)och ditt konto måste vara registrerat i det kommersiella Marketplace-programmet. Innan du skapar ditt erbjudande bör du gå igenom kraven i [konsult service krav](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Publicera förmåner

Fördelar med att publicera till den kommersiella marknads platsen:

- Marknadsför företaget genom att använda Microsoft varumärke.
- Det kan finnas fler än 100 000 000 Office 365-och Dynamics 365-användare på AppSource och fler än 200 000 organisationer via Azure Marketplace.
- Ta emot leads med hög kvalitet från dessa marknads platser.
- Få dina tjänster upphöjda av Microsoft-fälten och inköps teamet för köp

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

När du uppfyller de krav som beskrivs ovan följer du de här stegen för att skapa ett konsult tjänst erbjudande.

1. Logga in på [partner Center](https://partner.microsoft.com)och välj sedan **instrument panel** på den översta menyn.
2. Välj **kommersiell Marketplace**i det vänstra navigerings fältet och välj sedan **Översikt**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Visar menyn för kommersiell Marketplace":::

3. Välj **+ nytt erbjudande**och välj **konsult tjänst**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Visar knappen för att skapa ett nytt erbjudande.":::

4. Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

    - Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet.
    - Använd bara gemena bokstäver, siffror, bindestreck och under streck, men inga blank steg. Längden är begränsad till 50 tecken. Om du till exempel anger **test-erbjudande-1**är erbjudande-URL: en `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - Erbjudande-ID: t kan&#39;t ändras när du har valt **skapa**.

5. Ange ett **erbjudande alias**. Detta är det namn som används för att referera till erbjudandet i Partner Center.

    - Namnet inte är&#39;t som används i Marketplace. Den&#39;s skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna. Du kan använda det här fältet för att tilldela ett namn till erbjudandet som är mer användbart för att identifiera erbjudandet internt. den visas inte för kunderna.
    - Du kan&#39;t ändra erbjudandets alias när du har valt **skapa**.

När du har angett dessa två värden väljer du **skapa** för att fortsätta till sidan **erbjudande konfiguration** .

## <a name="offer-setup"></a>Erbjudande konfiguration

När du har angett ett erbjudande-ID och ett alias, skapar Partner Center ett utkast erbjudande och visar sidan **erbjudande konfiguration** . Följ dessa steg för att konfigurera erbjudandet.

### <a name="connect-lead-management"></a>Anslut lead-hantering

När du publicerar ditt erbjudande till Marketplace med partner Center _måste_ du ansluta det till ett system för kund Relations hantering (CRM) eller marknadsförings automatisering. På så sätt kan du få kund kontakt information så snart någon uttrycker intresse för eller använder produkten.

1. Välj **Anslut** för att ange var du vill att vi ska skicka kund leads. Partner Center har stöd för följande system:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kund engagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system inte är&#39;t i listan ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [https-slutpunkt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra kundens lead-data och sedan exportera data till ditt CRM-system.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center&#39;du se hur anslutningen är klar och skickar ett test lead. Medan du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.
4. Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads&#39;t.

Här följer några ytterligare resurser för ledar hantering:

- [Översikt över ledar hantering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över ledar hantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (kontrol lera att blockeringen av popup-fönster är inaktive rad)

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, egenskaper.

### <a name="properties"></a>Egenskaper

På den här sidan kan du ange den primära produkt som din konsult tjänst erbjuder bäst skydd, ange en konsult tjänst typ och välja tillämpliga produkter.

1. Välj en **primär produkt** i list rutan.
2. Välj en **konsult tjänst typ** i list rutan:

    - **Utvärdering** : en utvärdering av en kund&#39;s-miljö för att fastställa tillämpligheten för en lösning och ge en uppskattning av kostnader och tids inställningar.
    - **Kort** : en introduktion till en lösning eller en konsult tjänst för att dra nytta av kundernas intresse genom att använda ramverk, demonstrationer och kund exempel.
    - **Implementering** : en fullständig installation som resulterar i en helt fungerande lösning. Begränsa till lösningar som kan implementeras på två veckor eller mindre.
    - **Koncept bevis** : en begränsad omfattning för att avgöra om en lösning uppfyller kundens krav.
    - **Workshop** : ett interaktivt engagemang som utförs på en kund&#39;lokal. Det kan omfatta utbildning, kortfattade, utvärderingar eller demonstrationer som bygger på kundens&#39;data eller miljö.

1. Om du har valt en primär produkt i **Azure**väljer du upp till tre **lösnings områden**. Detta gör det enklare för kunderna på Azure Marketplace att hitta ditt erbjudande. Om du överensstämde&#39;t väljer du Azure, hoppar över det här steget.
2. Om du har valt en _annan_ primär produkt än Azure väljer du upp till tre **tillämpliga produkter**. Detta gör det enklare för kunder i AppSource att hitta ditt erbjudande. Mer information finns i avsnittet [Microsoft AppSource konsult service](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) distail Guidelines (PDF).
3. Välj upp till sex **branscher** som ditt erbjudande gäller för. På så sätt blir det enklare för kunderna att hitta ditt erbjudande.
4. Lägg till upp till tre **kompetenser** som ditt företag har fått att visa i din konsult tjänst erbjudande lista. Minst en kompetens krävs förutom Azure expert MSP&#39;s och Azure Network MSP&#39;s.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, erbjud listan.

## <a name="offer-listing"></a>Erbjudande lista

Här kan du&#39;l definiera erbjudande information som visas i Marketplace. Detta inkluderar erbjudandets namn, beskrivning, bilder och så vidare. Se till att följa principerna som beskrivs på [sidan med Microsoft&#39;s-principer](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) när du konfigurerar det här erbjudandet.

> [!NOTE]
> Erbjudande information är&#39;t måste vara på engelska om erbjudande beskrivningen börjar med frasen, &quot;är det här programmet endast tillgängligt på [språk som inte är engelska]. &quot; Det&#39;också att tillhandahålla en användbar länk för att erbjuda innehåll på ett språk som&#39;s skiljer sig från det som används i informationen om erbjudande listan.

### <a name="name"></a>Name

Namnet som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i automatiskt med den text som du angav i rutan för **erbjuden alias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler).
- Får&#39;t vara mer än 50 tecken långt.
- Kan&#39;t inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av erbjudandet. Detta kan vara upp till 100 tecken långt och används i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

Ange en längre Beskrivning av erbjudandet, upp till 3 000 tecken. Detta visas för kunder i Översikt över Marketplace-registrering.

Inkludera ett eller flera av följande i din beskrivning:

- Värdet och viktiga fördelar som ditt erbjudande ger
- Kategori-eller bransch kopplingar, eller båda
- Köp möjligheter i appen
- Eventuella upplysningar som krävs

Här följer några tips om hur du skriver beskrivningen:

- Beskriv tydligt värdet för ditt erbjudande i de första meningarna i beskrivningen. Ta med följande objekt:
  - Beskrivning av erbjudandet.
  - Typen av användare som fördelar från erbjudandet.
  - Kunden behöver eller utfärdar erbjudandena adresser.
- Kom ihåg att de första meningarna kan visas i Sök resultaten.
- Dan&#39;t förlitar sig på funktioner och funktioner för att sälja produkten. I stället fokuserar du på det värde som erbjudandet erbjuder.
- Försök att använda branschspecifika vokabulär eller kunskapsbaserade orda lydelse.

Om du vill göra din beskrivning mer intressant använder du RTF-redigeraren för att formatera beskrivningen. Med RTF-redigeraren kan du lägga till siffror, punkter, fet, kursiv stil och indrag för att göra beskrivningen mer lättläst.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Visar text redigeraren för att skriva en beskrivning av erbjudandet." border="false":::

### <a name="keywords"></a>Nyckelord

Ange upp till tre Sök nyckelord som är relevanta för din primära produkt och konsult tjänst. Detta gör det lättare att hitta ditt erbjudande.

### <a name="duration"></a>Varaktighet

Ange den förväntade varaktigheten för det här engagemanget hos kunden.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **primär** och **sekundär kontakt**. Den här informationen inte är&#39;t. ex. för kunder. Den är tillgänglig för Microsoft och kan tillhandahållas av CSP-partner (Cloud Solution Provider).

### <a name="supporting-documents"></a>Stöd dokument

Lägg till upp till tre (men minst en) stöd för PDF-dokument för ditt erbjudande.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange. png-filer för erbjudande&#39;s-logotypen i var och en av följande pixel storlekar:

- **Liten (48 x 48)**
- **Stor (216 x 216)**

Alla logo typer krävs och används på olika platser i Marketplace-listan.

#### <a name="screenshots-optional"></a>Skärm dum par (valfritt)

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 bild punkter i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fyra videor som demonstrerar ditt erbjudande. Ange video&#39;s namn, webb adress (URL) och en miniatyr bild av videon med 1280 x 720 pixlar i storlek.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, prissättning och tillgänglighet.

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Här definierar du element som prissättning, marknad och en privat nyckel.

1. **Marknad**: Ställ in marknaden som ditt erbjudande kommer att vara tillgängligt i. Du kan bara välja en marknad per erbjudande.
    1. För USA-eller Kanada-marknader väljer du **Redigera tillstånd** (eller **provinser**) för att ange var ditt erbjudande ska vara tillgängligt.
2. **Förhands gransknings mål**: Konfigurera knappen **Dölj** som används för att ange den privata mål gruppen för ditt erbjudande.
3. **Prissättning**: Ange om erbjudandet är ett **kostnads fritt** eller **betalt** erbjudande.

    > [!NOTE]
    > Konsult tjänster erbjuder endast för registrering. Eventuella transaktioner sker direkt, utanför den kommersiella marknads platsen.

4. För ett betalt erbjudande anger du **pris och valuta** och om priset är **fast** eller **uppskattat**. Om det är uppskattat måste du ange i beskrivningen vilka faktorer som ska påverka priset.
5. Välj **Spara utkast**.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka ditt erbjudande till granskning och publicering.

1. När du är redo att publicera ditt konsult tjänst erbjudande klickar du på **Granska och publicera**.
2. Granska informationen på sidan slutlig överföring.
3. Om det behövs skriver du en anteckning till certifierings teamet om du anser att någon information om Erbjudandet kräver förklaring.
4. När du är klar väljer du **Skicka**.
5. Sidan **erbjudande översikt** visar vilken publicerings fas ditt erbjudande befinner sig i.

Mer information om hur länge du kan vänta dig att ditt erbjudande ska vara i varje publicerings steg finns i [kontrol lera publicerings status för ditt kommersiella Marketplace-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Uppdatera dina befintliga erbjudanden om konsult tjänster

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
