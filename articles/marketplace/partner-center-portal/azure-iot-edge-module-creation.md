---
title: Skapa ett Azure IoT Edge-modulerbjudande med Partner Center - Azure Marketplace
description: Lär dig hur du skapar ett IoT Edge-modulerbjudande på Azure Marketplace med Partner Center
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: cca54e4e456fe766b190f64657cd1aca1d9520e0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869152"
---
# <a name="create-an-iot-edge-module-offer"></a>Skapa ett IoT Edge-modulerbjudande

> [!IMPORTANT]
> Vi flyttar hanteringen av dina IoT Edge-modulerbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [IoT Edge-modulen som erbjuder publiceringsöversikt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du skapar och publicerar ett IoT-modulerbjudande (Internet of Things) för Azure Marketplace.

Innan du kan skapa ett IoT Edge-modulerbjudande måste du ha ett kommersiellt marketplace-konto i Partner Center. Om du inte har skapat ett ännu läser du [Skapa ett kommersiellt marketplace-konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på Partner Center.
2. Välj Översikt för kommersiell **marknadsplats** > i menyn till**vänster.**

    ![Illustrerar menyn till vänster.](./media/cs-menu-overview.png)

3. Välj **+ Ny erbjudande** > **IoT Edge modul**. Dialogrutan **Nytt erbjudande** visas.

> [!IMPORTANT]
> När ett erbjudande har publicerats visas ändringar som görs i Partner center endast i skyltfönster efter att erbjudandet har publicerats på nytt. Se till att du alltid publicerar om när du har gjort ändringar.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t är synligt för kunder i webbadressen för marketplace-erbjudande och Azure Resource Manager-mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Den kan innehålla bindestreck och understreck, men inga blanksteg och är begränsad till 50 tecken. Om du till exempel anger **test-erbjudande-1**blir `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`erbjudandewebbadressen .
- Erbjudande-ID:t kan inte ändras när du har valt Skapa.

Ange ett **offeralias**. Det här är namnet som används för att referera till erbjudandet i Partner Center.

- Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna.
- Detta kan inte ändras när du har valt **Skapa**.

När du har angett dessa två värden väljer du **Skapa** innan du fortsätter till nästa sida, Erbjudandeöversikt.

## <a name="offer-overview"></a>Översikt över erbjudandet

**Översiktssidan Erbjudande** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och kommande) och hur lång tid varje steg ska ta att slutföra.

Den här sidan innehåller länkar för att utföra åtgärder på det här erbjudandet baserat på det val du gör. Ett exempel:

- Om erbjudandet är ett utkast - [Ta bort utkast erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Om erbjudandet är live - [Sluta sälja erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Om erbjudandet är i förhandsgranskning - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Om du inte har slutfört utgivningsut logga ut - [Avbryt publicering.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Inställningar för erbjudande

Följ dessa steg för att ställa in ditt erbjudande.

### <a name="connect-lead-management"></a>Anslut leadhantering

När du publicerar ditt erbjudande på marknadsplatsen med Partner Center kan du eventuellt ansluta det till ditt CRM-system (Customer Relationship Management). På så sätt kan du få information om kundkontakter så snart någon uttrycker intresse för eller använder din produkt.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kundengagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system inte visas ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra leaddata för kunder och exporterar sedan data till CRM-systemet.

2. Anslut ditt erbjudande till leaddestinationen när du publicerar i Partner Center.
3. Kontrollera att anslutningen till lead-målet är korrekt konfigurerad. När du har publicerat den i Partner Center validerar vi anslutningen och skickar en testledare till dig. Medan du förhandsgranskar erbjudandet innan det visas kan du också testa din leadanslutning genom att försöka köpa erbjudandet själv i förhandsversionen.
4. Se till att anslutningen till leaddestinationen förblir uppdaterad så att du inte förlorar några leads.

Här är några ytterligare lead hanteringsresurser:

- [Översikt över leadhantering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över leadhantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Kontrollera att popup-blockeraren är avstängd).

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Egenskaper.

### <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier som används för att gruppera ditt erbjudande på marknadsplatsen och de juridiska kontrakt som stöder ditt erbjudande.

#### <a name="category"></a>Kategori

Välj minst en och högst fem kategorier. Dessa kategorier används för att placera ditt erbjudande i lämpliga marknadsplats sökområden och visas på din erbjudandeinformation sida. Förklara i erbjudandebeskrivningen hur ditt erbjudande stöder dessa kategorier. På bläddringssidorna visas alla IoT Edge-moduler under **kategorin Sakernas Internet > IoT Edge-modulen.** 

#### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet. Du kan välja mellan två alternativ:

- Använd standardkontraktet för Microsoft Commercial Marketplace.
- Ange dina egna villkor.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardkontrakt för Microsoft Commercial Marketplace

Vi erbjuder en standardavtalsmall för att underlätta transaktioner på den kommersiella marknaden. Du kan välja att erbjuda din lösning enligt standardavtalet, som kunderna bara behöver kontrollera och acceptera en gång. Detta är ett bra alternativ om du inte vill skapa anpassade villkor.

Mer information om standardkontraktet finns i [Standardkontrakt för Microsoft Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). Du kan också ladda ner [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (se till att popup-blockeraren är avstängd).

Om du vill använda standardkontraktet markerar du kryssrutan **Använd standardkontraktet för Microsofts kommersiella marknadsplats** och klickar sedan på **Acceptera**.

> [!NOTE]
> När du har publicerat ett erbjudande med standardkontraktet för Microsofts kommersiella marknadsplats kan du inte använda dina egna anpassade villkor. Antingen erbjuda din lösning enligt standardavtalet eller enligt dina egna villkor.

![Illustrerar hur standardkontraktet för Microsofts kommersiella marknadsplats visas.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Dina egna villkor

Om du vill ange dina egna anpassade villkor anger du dem i rutan **Regler och villkor.** Du kan ange ett obegränsat antal tecken text i den här rutan. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Offer list.

## <a name="offer-listing"></a>Lista över erbjudande

Här definierar du erbjudandeinformationen som visas på marknadsplatsen. Detta inkluderar erbjudandet namn, beskrivning, bilder och så vidare. Var noga med att följa de principer som beskrivs på Microsofts policysida när du konfigurerar det här erbjudandet.

> [!NOTE]
> Erbjudandeuppgifter behöver inte vara på engelska om erbjudandebeskrivningen börjar med frasen "Denna ansökan är endast tillgänglig på [icke-engelska]." Det är också okej att tillhandahålla en användbar länk för att erbjuda innehåll på ett språk som skiljer sig från det som används i erbjudandena.

### <a name="name"></a>Namn

Namnet du anger här visas som titeln på ditt erbjudande. Det här fältet är förfyllt med den text som du angav i rutan **Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara varumärkesskyddade (och du kan inkludera varumärkes- eller upphovsrättssymboler).
- Kan inte vara mer än 50 tecken lång.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

Ge en kort beskrivning av ditt erbjudande. Detta kan vara upp till 100 tecken långt och används i marketplace-sökresultat.

### <a name="long-summary"></a>Lång sammanfattning

Ge en mer detaljerad beskrivning av ditt erbjudande. Detta kan vara upp till 256 tecken lång och används i marketplace sökresultat.

### <a name="description"></a>Beskrivning

Ge en längre beskrivning av ditt erbjudande, upp till 3000 tecken. Detta visas för kunder i översikten för marknadsplatsannonser.

Inkludera ett eller flera av följande i beskrivningen:

- Värdet och de viktigaste fördelarna med ditt erbjudande ger
- Kategori- eller branschorganisationer, eller båda
- Köpmöjligheter i appen
- Alla nödvändiga upplysningar

IoT Edge-modulerbjudanden måste innehålla ett stycke minimikrav på maskinvarukrav längst ned i beskrivningen. Ett exempel:

*Minimikrav på maskinvara: Linux x64 och arm32 OS, 1 GB RAM, 500 Mb lagringsutrymme*

Här är några tips för att skriva din beskrivning:

- Beskriv tydligt värdet av ditt erbjudande i de första meningarna i din beskrivning. Inkludera följande:
    - Beskrivning av erbjudandet.
    - Den typ av användare som drar nytta av erbjudandet.
    - Kundens behov eller utfärdar erbjudandeadresserna.
- Kom ihåg att de första meningarna kan visas i sökresultaten.
- Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde som ditt erbjudande ger.
- Försök att använda branschspecifika ordförråd eller förmånsbaserad ordalydelse.

Om du vill göra erbjudandet **Beskrivning** mer engagerande använder du RTF-redigeraren för att formatera beskrivningen. Med RTF-redigeraren kan du lägga till tal, punkter, fetstil, kursiv stil och indrag för att göra beskrivningen mer läsbar.

:::image type="content" source="media/text-editor2.png" alt-text="Illustrerar RTF-redigeraren." border="false":::

- Om du vill ändra formatet på innehållet markerar du den text som du vill formatera och markerar ett textformat, som visas i den här skärmbilden:

     :::image type="content" source="media/text-editor3.png" alt-text="Illustrerar textformatkontrollen i RTF-redigeraren." border="false":::

- Om du vill lägga till en punktlista eller numrerad lista i texten använder du alternativen som visas i den här skärmbilden:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Illustrerar kontrollerna för punktlistor och nummerlistor i RTF-redigeraren." border="false":::

- Om du vill lägga till eller ta bort indrag i texten använder du alternativen som visas i den här skärmbilden:

    :::image type="content" source="media/text-editor5.png" alt-text="Illustrerar indragskontrollerna i RTF-redigeraren." border="false":::

#### <a name="privacy-policy-url"></a>Url till sekretesspolicy

Ange webbadressen till organisationens sekretesspolicy. Du är ansvarig för att ditt erbjudande följer sekretesslagar och sekretessregler. Du är också ansvarig för att publicera en giltig sekretesspolicy på din webbplats.

#### <a name="useful-links"></a>Användbara länkar

Tillhandahålla kompletterande onlinedokument om ditt erbjudande. Du kan lägga till upp till 25 länkar. Om du vill lägga till en länk väljer du **+ Lägg till en länk** och fyller sedan i följande fält:

- **Titel** - Kunderna ser titeln på ditt erbjudandes informationssida.
- **Länk (URL)** – Ange en länk så att kunderna kan visa ditt onlinedokument. Länken måste börja med http:// eller https://.

Se till att lägga till minst en länk till din dokumentation och en länk till kompatibla IoT Edge-enheter från [Azure IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-post och telefonnummer för en **supportkontakt** och en **teknisk kontakt.** Den här informationen visas inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner (Cloud Solution Provider).

- Supportkontakt (obligatoriskt): För allmänna supportfrågor.
- Teknisk kontakt (krävs): För tekniska frågor och certifieringsfrågor.
- CSP-programkontakt (valfritt): För återförsäljarfrågor relaterade till CSP-programmet.

I avsnittet **Supportkontakt** anger du webbadressen **till supportwebbplatsen** där partner kan hitta support för ditt erbjudande baserat på om erbjudandet är tillgängligt i globala Azure, Azure Government eller båda.

I **kontaktsektionen för CSP-programmet** anger du länken **(CSP Program Marketing Materials)** där CSP-partner kan hitta marknadsföringsmaterial för ditt erbjudande.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

Mer information om hur du skapar erbjudanden finns i [Bästa praxis för erbjudandebeskrivning](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

### <a name="marketplace-images"></a>Marketplace-bilder

Ange logotyper och bilder som du kan använda med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

>[!Note]
>Om du har problem med att ladda upp filer https://upload.xboxlive.com kontrollerar du att det lokala nätverket inte blockerar tjänsten som används av Partner Center.

#### <a name="store-logos"></a>Lagra logotyper

Ange PNG-filer av erbjudandets logotyp i var och en av följande fyra pixelstorlekar:

- **Liten (48 x 48)**
- **Medel (90 x 90)**
- **Stor (216 x 216)**
- **Bred (255 x 115)**

Alla fyra logotyper krävs och används på olika platser på marknadsplatsen notering.

#### <a name="screenshots-optional"></a>Skärmdumpar (valfritt)

Lägg till upp till fem skärmbilder som visar hur erbjudandet fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i .png-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som visar ditt erbjudande. Ange videons namn, dess webbadress och en miniatyrbild av videon med en bild på 1280 x 720 pixlar.

#### <a name="offer-examples"></a>Exempel på erbjudanden

Följande exempel visar hur listningsfälten för erbjudande visas på olika ställen i erbjudandet.

Den här skärmbilden visar sidan **Erbjudandelistning** på Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Illustrerar sidan Erbjudandelistning på Azure Marketplace.":::

Den här skärmbilden visar sökresultaten på Azure Marketplace:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Illustrerar sökresultaten på Azure Marketplace.":::

Den här skärmbilden visar sidan **Erbjudandelista** i Azure-portalen.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Illustrerar sidan Erbjudandelistning i Azure-portalen.":::

Den här skärmbilden visar sökresultaten i Azure-portalen.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Illustrerar sidan Erbjudandelistning i Azure-portalen.":::

Välj **Spara utkast** innan du går vidare till nästa avsnitt, Förhandsgranska.

## <a name="preview"></a>Förhandsversion

På **fliken Förhandsgranska**kan du välja en begränsad målgrupp för **att** validera ditt erbjudande innan du publicerar det live för den bredare marknadsplatsens målgrupp.

> [!IMPORTANT]
> När du har visat erbjudandet i förhandsversion måste du välja **Gå live** för att publicera ditt erbjudande för allmänheten.

Ange din förhandsgranskningsmålgrupp med azure-prenumerations-ID-GUID: er, tillsammans med en valfri beskrivning för varje. Inget av dessa fält kan ses av kunder.

> [!NOTE]
> Du hittar ditt Azure-prenumerations-ID på sidan Prenumerationer i Azure-portalen.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID:er definierar du vem som kan förhandsgranska erbjudandet innan det publiceras live. Om ditt erbjudande redan är live kan du definiera en målgrupp för förhandsversionen för att testa ändringar eller uppdateringar av erbjudandet.

> [!NOTE]
> Förhandsgranskningspubliken skiljer sig från en privat målgrupp. En **förhandsgranskningsmål** kan se och bekräfta alla erbjudandeplaner innan de visas på marknadsplatsen, inklusive de som endast publiceras för en **privat** målgrupp (som anges på fliken Tillgänglighet).

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Planera översikt.

### <a name="plan-overview"></a>Översikt över plan

På den här fliken kan du ange olika planalternativ i samma erbjudande i Partner Center. Dessa planer, kallades tidigare SKU: er, eller lagerhållningsenheter. Planer kan skilja sig åt när det gäller vilka moln som är tillgängliga, till exempel globala moln, regeringsmoln och bilden som refereras av planen. Om du vill lista ditt erbjudande på marknadsplatsen måste du ställa in minst en plan.

När du har skapat dina planer visas på fliken **Planera översikt:**

- Planera namn
- Prismodell
- Molntillgänglighet (global eller myndighet)
- Aktuell publiceringsstatus
- Alla tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i översikten Planera varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast**: Om planens status är ett utkast.
- **Stoppa säljplan**: Om planens status publiceras live.

#### <a name="create-new-plan"></a>Skapa ny plan

Välj **Skapa ny plan**. Dialogrutan **Ny plan** visas.

Skapa ett unikt plan-ID för varje plan i det här erbjudandet i rutan **Plan-ID.** Detta ID kommer att vara synligt för kunder i produktens webbadress. Använd bara gemener och siffror, streck eller understreck och högst 50 tecken.

Ange ett namn för den här planen i rutan **Plannamn.** Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas inom ditt erbjudande. Skapa ett unikt namn för varje plan i det här erbjudandet. Du kan till exempel använda ett erbjudandenamn på **Windows Server** med abonnemangen Windows **Server 2016** och **Windows Server 2019**.

> [!NOTE]
> Det går inte att ändra plan-ID:t när du har valt **Skapa**.

Välj **Skapa**.

### <a name="plan-setup"></a>Planera inställningar

På den här fliken kan du konfigurera vilka moln planen är tillgänglig i. Dina svar på den här fliken påverkar vilka fält som visas på andra flikar.

#### <a name="cloud-availability"></a>Molntillgänglighet

Din plan måste vara tillgänglig i minst ett moln med Azure IoT Hub.

Välj alternativet **Azure Global** så att din plan kan användas av kunder i alla globala Azure-regioner som använder marknadsplatsen. Mer information finns i [Geografisk tillgänglighet och valutastöd](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Välj alternativet [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) för att få din lösning att visas här. Detta är en regering gemenskap moln med kontrollerad åtkomst för kunder från amerikanska federala, statliga och lokala eller tribal myndigheter, samt partner som är berättigade att betjäna dem. Som utgivare är du ansvarig för alla efterlevnadskontroller, säkerhetsåtgärder och metodtips för den här molncommunityn. Azure Government använder fysiskt isolerade datacenter och nätverk (endast i USA). Innan [du publicerar](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) till Azure Government testar och bekräftar du din lösning inom det området eftersom resultaten kan vara annorlunda. Om du vill arrangera och testa din lösning begär du ett utvärderingskonto från [testversionen](https://azure.microsoft.com/global-infrastructure/government/request/)av Microsoft Azure Government .

> [!NOTE]
> När ditt abonnemang har publicerats och är tillgängligt i ett visst moln kan du inte ta bort molnet.

#### <a name="azure-government-cloud-certifications"></a>Azure Government Cloud-certifieringar

Det här alternativet visas bara om **Azure Government Cloud** har valts under **Molntillgänglighet**.

Azure Government-tjänster hanterar data som omfattas av vissa myndighetsregler och krav. FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att öka medvetenheten om dina certifieringar för dessa program kan du tillhandahålla upp till 100 länkar som beskriver dina certifieringar. Dessa kan vara länkar till dina listor på programmet direkt eller till din egen webbplats. Dessa länkar är endast synliga för Azure Government-kunder.

## <a name="plan-listing"></a>Planera listning

På den här fliken visas specifik information för varje plan inom samma erbjudande.

### <a name="plan-name"></a>Plannamn

Detta är förfyllt med namnet du gav din plan när du skapade den. Du kan ändra det här namnet efter behov. Det kan vara upp till 50 tecken lång. Det här namnet visas som titeln på den här planen i Azure Marketplace och Azure-portalen. Den används som standardmodulnamn när planen är klar att användas.

### <a name="plan-summary"></a>Sammanfattning av planen

Ge en kort sammanfattning av din plan (inte erbjudandet). Den här sammanfattningen visas i Azure Marketplace-sökresultat och kan innehålla upp till 100 tecken.

### <a name="plan-description"></a>Beskrivning av planen

Beskriv vad som gör den här planen unik, samt skillnader mellan planer inom ditt erbjudande. Beskriv inte erbjudandet, bara planen. Den här beskrivningen visas i Azure Marketplace och i Azure-portalen på sidan Erbjudandelistning. Det kan vara samma innehåll som du angav i plansammanfattningen och innehålla upp till 2 000 tecken.

Välj **Spara utkast** när du har slutfört dessa fält.

#### <a name="plan-examples"></a>Planera exempel

Följande exempel visar hur fälten för listning av planer visas i olika vyer.

Det här är fälten på Azure Marketplace när du visar planinformation:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Illustrerar de fält du ser när du visar planinformation i Azure Marketplace.":::

Det här är planinformation på Azure-portalen:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Illustrerar planinformation på Azure-portalen.":::

## <a name="availability"></a>Tillgänglighet

Om du vill dölja ditt publicerade erbjudande så att kunderna inte kan söka, bläddra eller köpa det på marknadsplatsen markerar du kryssrutan **Dölj abonnemang** på fliken Tillgänglighet.

Det här fältet används ofta när:

- Erbjudandet är endast avsett att användas indirekt när det hänvisas till genom ett annat program.
- Erbjudandet ska inte köpas individuellt.
- Planen användes för inledande testning och är inte längre relevant.
- Planen användes för tillfälliga eller säsongsbetonade erbjudanden och bör inte längre erbjudas.

## <a name="technical-configuration"></a>Teknisk konfiguration

**Erbjudandetypen IoT Edge-modul** är en viss typ av behållare som körs på en IoT Edge-enhet. På fliken **Teknisk konfiguration** anger du referensinformation för behållaravbildningsdatabasen i Azure [Container Registry](https://azure.microsoft.com/services/container-registry/), tillsammans med konfigurationsinställningar som gör att kunderna enkelt kan använda modulen.

När erbjudandet har publicerats kopieras avbildningen av IoT Edge-behållare till Azure Marketplace i ett specifikt offentligt behållarregister. Alla begäranden från Azure-användare som ska använda modulen visas från Azure Marketplaces offentliga behållarregister, inte från ditt privata behållarregister.

Du kan rikta in dig på flera plattformar och tillhandahålla flera versioner av modulbehållarens avbildning med hjälp av taggar. Mer information om taggar och versionshantering finns i [Förbereda tekniska resurser för IoT Edge-modulen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset).

### <a name="image-repository-details"></a>Information om bilddatabas

Du anger följande information på fliken **Information om bilddatabasen.**

**Välj bildkälla:** Välj alternativet **Azure Container Registry.**

**Azure-prenumerations-ID:** Ange prenumerations-ID där resursanvändning rapporteras och tjänster faktureras för Azure Container Registry som innehåller din behållaravbildning. Du hittar det här ID:t på [sidan Prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

**Namn på Azure-resursgrupp**: Ange [resursgruppsnamnet](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) som innehåller Azure Container-registret med behållaravbildningen. Resursgruppen måste vara tillgänglig i prenumerations-ID (ovan). Du hittar namnet på sidan [Resursgrupper](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i Azure-portalen.

**Azure-behållarregisternamn**: Ange namnet på [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) som har din behållaravbildning. Behållarregistret måste finnas i den Azure-resursgrupp som du angav tidigare. Ange endast registernamnet, inte det fullständiga inloggningsservernamnet. Var noga med att utelämna **azurecr.io** från namnet. Du hittar registernamnet på [sidan Behållarregister](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) i Azure-portalen.

**Administratörsanvändarnamn för Azure Container Registry:** Ange [administratörsanvändarnamnet](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) som är associerat med Azure Container-registret som har din behållaravbildning. Användarnamnet och lösenordet krävs för att säkerställa att ditt företag har tillgång till registret. Om du vill hämta administratörsanvändarnamnet och lösenordet anger du egenskapen **för adminaktiverad** till **True** med hjälp av AZURE Command-Line Interface (CLI). Du kan också ange **admin-användare** till **Aktivera** i Azure-portalen.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Illustrerar dialogrutan Uppdatera behållarregister.":::

**Lösenord för Azure Container Registry:** Ange lösenordet för administratörsanvändarnamnet som är associerat med Azure Container Registry och har din behållaravbildning. Användarnamnet och lösenordet krävs för att säkerställa att ditt företag har tillgång till registret. Du kan hämta lösenordet från Azure-portalen genom att gå till **Container Registry** > **Access Keys** eller med Azure CLI med kommandot [Visa.](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Illustrerar skärmen för åtkomstnyckel i Azure-portalen.":::

**Databasnamn i Azure Container Registry**. Ange namnet på azure container-registerdatabasen som har avbildningen. Du anger namnet på databasen när du skickar avbildningen till registret. Du hittar namnet på databasen genom att gå till sidan [Behållarregisterdatabaser](https://azure.microsoft.com/services/container-registry/) > **Repositories page**. Mer information finns [i Visa behållarregisterdatabaser i Azure-portalen](https://docs.microsoft.com/azure/container-registry/container-registry-repositories). Observera att när namnet har angetts kan det inte ändras. Använd ett unikt namn för varje erbjudande i ditt konto.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Bildtaggar för nya versioner av ditt erbjudande

Kunder måste kunna hämta uppdateringar automatiskt från Azure Marketplace när du publicerar en uppdatering. Om de inte vill uppdatera måste de kunna behålla en viss version av bilden. Du kan göra detta genom att lägga till nya bildtaggar varje gång du gör en uppdatering av bilden.

**Bildtagg**. Det här fältet måste innehålla en **senaste** tagg som pekar på den senaste versionen av avbildningen på alla plattformar som stöds. Den måste också innehålla en versionstagg (till exempel från xx.xx.xx, där xx är ett tal). Kunder bör använda [manifesttaggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla taggar som refereras av en manifesttagg måste också läggas till så att vi kan ladda upp dem. Alla manifesttaggar (utom den senaste taggen) måste börja med antingen X.Y- eller X.Y.Z- där X, Y och Z är heltal. Om till exempel en senaste tagg pekar på 1.0.1-linux-x64, 1.0.1-linux-arm32 och 1.0.1-windows-arm32, måste dessa sex taggar läggas till i det här fältet. Mer information om taggar och versionshantering finns i [Förbereda tekniska resurser för IoT Edge-modulen.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)

### <a name="default-deployment-settings-optional"></a>Standardinställningar för distribution (valfritt)

Definiera de vanligaste inställningarna för att distribuera IoT Edge-modulen. Optimera kunddistributioner genom att låta dem starta din IoT Edge-modul direkt med dessa standardinställningar.

**Standardvägar**. IoT Edge Hub hanterar kommunikation mellan moduler, IoT Hub och enheter. Du kan ange vägar för datainmatning och datautdata mellan moduler och IoT Hub, vilket ger dig flexibiliteten att skicka meddelanden där de behöver gå utan att behöva ytterligare tjänster för att bearbeta meddelanden eller skriva ytterligare kod. Rutter konstrueras med namn/värdepar. Du kan definiera upp till fem standardvägnamn, var och en upp till 512 tecken långa.

Var noga med att använda rätt [vägsyntax](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) i flödesvärdet (vanligtvis definierad som FROM/message/* INTO $upstream). Det innebär att alla meddelanden som skickas av moduler går till din IoT Hub. Om du vill referera till modulen använder du standardmodulnamnet, som blir ditt **erbjudandenamn**, utan mellanslag eller specialtecken. Om du vill referera till andra moduler som ännu inte är kända använder du <FROM_MODULE_NAME> konvention för att informera dina kunder om att de behöver uppdatera den här informationen. Mer information om IoT Edge-vägar finns i [Deklarera vägar](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

Om modul ContosoModule till exempel lyssnar efter indata på ContosoInput och utdata på ContosoOutput, är det vettigt att definiera följande två standardvägar:

- Namn #1: ToContosoModule
- Värde #1: FRÅN /messages/modules/<FROM_MODULE_NAME>/outputs/* Into BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Namn #2: FromContosoModuleToCloud
- Värde #2: FRÅN /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Standardmodul dubbla önskade egenskaper**. En modultvilling är ett JSON-dokument i IoT Hub som lagrar tillståndsinformationen för en modulinstans, inklusive önskade egenskaper. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera modulkonfiguration eller villkor. Lösningens backend kan ställa in önskade egenskaper och modulen kan läsa dem. Modulen kan också ta emot ändringsmeddelanden i önskade egenskaper. Önskade egenskaper skapas med upp till fem namn-/värdepar och varje standardvärde måste vara mindre än 512 tecken. Du kan definiera upp till fem önskade egenskaper för namn/värdetvilling. Värden för dubbla önskade egenskaper måste vara giltiga JSON, som inte har rymts, utan matriser med en maximal kapslad hierarki på fyra nivåer. I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds server) kan du lägga till en parameter som standardvärde. Mer information om dubbla önskade egenskaper finns i [Definiera eller uppdatera önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

Om en modul till exempel stöder en dynamiskt konfigurerbar uppdateringsfrekvens med dubbla önskade egenskaper, är det klokt att definiera följande önskade standardtvillingegenskaper:

- Namn #1: RefreshRate
- Värde #1: 60

**Standardmiljövariabler**. Miljövariabler ger kompletterande information till en modul som hjälper konfigurationsprocessen. Miljövariabler skapas med namn-/värdepar. Varje standardnamn och värde för miljövariabeln måste vara mindre än 512 tecken och du kan definiera upp till fem. När en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds server) kan du lägga till en parameter som standardvärde.

Om en modul till exempel måste acceptera användningsvillkor innan den startas kan du definiera följande miljövariabel:

- Namn #1: ACCEPT_EULA
- Värde #1: Y

**Alternativ för att skapa standardbehållare**. Alternativ för att skapa behållare direkt för att skapa IoT Edge-modulen Docker-behållaren. IoT Edge stöder alternativen för Docker-motorns API Create Container. Se alla alternativ på [List containers.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Fältet Skapa alternativ måste vara giltigt JSON, som inte har escapes och färre än 512 tecken.

Om en modul till exempel kräver portbindning definierar du följande skapa alternativ:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in det för att granska och publicera.

I det övre högra hörnet på portalen väljer du **Granska och publicera**.

På granskningssidan kan du se publiceringsstatus:

- Se slutförandestatusen för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har markerats som slutförd.
    - **Inte startat** - Avsnittet har inte startats och måste slutföras.
    - **Ofullständig** - Avsnittet har fel som måste åtgärdas eller kräver att du ger mer information. Mer information finns i avsnitten tidigare i det här dokumentet.
    - **Komplett** - Avsnittet har alla nödvändiga data och det finns inga fel. Alla delar av erbjudandet måste vara fullständiga innan du kan skicka erbjudandet.
- Ge testinstruktioner till certifieringsteamet för att säkerställa att ditt erbjudande testas korrekt. Också, ge eventuella kompletterande anteckningar som är till hjälp för att förstå ditt erbjudande.

Om du vill skicka erbjudandet för publicering väljer du **Publicera**.

Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet finns tillgänglig för granskning och godkännande. Om du vill publicera ditt erbjudande för allmänheten (eller om ett privat erbjudande, till en privat målgrupp) går du till Partnercenter och väljer **Go-live**.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande på den kommersiella marknaden](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)