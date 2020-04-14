---
title: Skapa ett Azure-behållarerbjudande i Partner Center - Azure Marketplace
description: I den här artikeln beskrivs hur du skapar och publicerar ett behållarerbjudande för Azure Marketplace.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266323"
---
# <a name="create-an-azure-container-offer"></a>Skapa ett Azure Container-erbjudande

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Azure Container-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) for Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du skapar och publicerar ett behållarerbjudande för Azure Marketplace. Innan du börjar [skapar du ett kommersiellt Marketplace-konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in i [Partnercenter](https://partner.microsoft.com/dashboard/home)och välj sedan **Instrumentpanel**på den övre menyn .
2. Välj **Kommersiell marknadsplats**på den vänstra menyn och sedan **Översikt**.
3. På sidan **Översikt** väljer du **+ Nytt erbjudande**och sedan Azure **Container**. Dialogrutan **Nytt erbjudande** visas.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="Illustrerade översiktssidan i Partnercenter. Knappen Nytt erbjudande och Erbjudandet om konsulttjänster är markerade.":::

> [!TIP]
> När ett erbjudande har publicerats visas ändringar som görs i Partner center endast i skyltfönster efter att erbjudandet har publicerats på nytt. Se till att du alltid publicerar om när du har gjort ändringar.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t kan ses av kunder i webbadressen för marketplace-erbjudandet och Azure Resource Manager-mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Den kan innehålla bindestreck och understreck, men inga blanksteg och är begränsad till 50 tecken. Om du till exempel anger **test-erbjudande-1**blir `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`erbjudandewebbadressen .
- Erbjudande-ID:t kan inte ändras när du har valt **Skapa**.

**Ange ett offeralias** **Offer alias**. Det här är namnet som används för att referera till erbjudandet i Partner Center.

- Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna.
- Detta kan inte ändras när du har valt **Skapa**.

Välj **Skapa** innan du fortsätter.

## <a name="offer-overview"></a>Översikt över erbjudandet

**Översiktssidan Erbjudande** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och kommande) och hur lång tid varje steg ska ta att slutföra.

På den här sidan visas olika länkar baserat på erbjudandets aktuella status. Ett exempel:

- Om erbjudandet är ett utkast - [Ta bort utkast erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Om erbjudandet är live - [Sluta sälja erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Om erbjudandet är i förhandsgranskning - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Om du inte har slutfört utgivningsut logga ut - [Avbryt publicering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Inställningar för erbjudande

Följ dessa steg för att ställa in ditt erbjudande.

### <a name="connect-lead-management--optional"></a>Anslut leadhantering – tillval

När du publicerar ditt erbjudande på marknadsplatsen med Partner Center kan du ansluta det till ditt CRM-system (Customer Relationship Management). På så sätt kan du få information om kundkontakter så snart någon uttrycker intresse för eller använder din produkt.

1. **Välj en leaddestination där du vill att vi ska skicka kundleads.** Partner Center stöder följande CRM-system:

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kundengagemang
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Om ditt CRM-system inte visas ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra leaddata för kunder och exporterar sedan data till CRM-systemet.

2. Anslut ditt erbjudande till leaddestinationen när du publicerar i Partner Center.
3. Bekräfta att anslutningen till leadmålet är korrekt konfigurerad. När du har publicerat den i Partner Center validerar vi anslutningen och skickar en testledare till dig. Medan du förhandsgranskar erbjudandet innan det visas kan du också testa din leadanslutning genom att försöka köpa erbjudandet själv i förhandsversionen.
4. Se till att anslutningen till leaddestinationen förblir uppdaterad så att du inte förlorar några leads.

Här är några ytterligare lead hanteringsresurser:

- [Översikt över leadhantering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över leadhantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Kontrollera att popup-blockeraren är avstängd)

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Egenskaper.

### <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier som används för att gruppera ditt erbjudande på marknadsplatsen och de juridiska kontrakt som stöder ditt erbjudande.

#### <a name="category"></a>Kategori

Välj minst en och högst fem kategorier. Dessa kategorier används för att placera ditt erbjudande i lämpliga marknadsplatssökområden och visas på sidan med erbjudandeinformation. Förklara i erbjudandebeskrivningen hur ditt erbjudande stöder dessa kategorier. Behållare visas under **Behållare** och sedan kategorin **Behållarbilder.**

#### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet. Det finns två alternativ:

- Använd standardkontraktet för Microsofts kommersiella marknadsplats.
- Ange dina egna villkor.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardkontrakt för Microsofts kommersiella marknadsplats

Vi erbjuder en standardavtalsmall för att underlätta transaktioner på den kommersiella marknaden. Du kan välja att erbjuda din lösning enligt standardavtalet, som kunderna bara behöver kontrollera och acceptera en gång. Detta är ett bra alternativ om du inte vill skapa anpassade villkor.

Mer information om standardkontraktet finns i [Standardkontrakt för Microsofts kommersiella marknadsplats](https://docs.microsoft.com/azure/marketplace/standard-contract). Du kan också ladda ner [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (se till att popup-blockeraren är avstängd).

Om du vill använda standardkontraktet markerar du kryssrutan **Använd standardkontraktet för Microsofts kommersiella marknadsplats** och klickar sedan på **Acceptera**.

> [!NOTE]
> När du har publicerat ett erbjudande med standardkontraktet för Microsofts kommersiella marknadsplats kan du inte använda dina egna anpassade villkor. Antingen erbjuda din lösning enligt standardavtalet eller enligt dina egna villkor.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Illustrerar kryssrutan Använda standardkontraktet för Microsofts kommersiella marknadsplats.":::

##### <a name="your-own-terms-and-conditions"></a>Dina egna villkor

Om du vill ange dina egna anpassade villkor anger du dem i rutan **Regler och villkor.** Du kan ange ett obegränsat antal tecken text i den här rutan. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Offer list.

## <a name="offer-listing"></a>Lista över erbjudande

På den här sidan kan du definiera erbjudandeinformation som visas på den kommersiella marknadsplatsen. Detta inkluderar erbjudandets namn, beskrivning och bilder.

> [!NOTE]
> Erbjudandeinformation behöver inte vara på engelska om erbjudandebeskrivningen börjar med frasen"Denna ansökan är endast tillgänglig på [icke-engelska]." Det är också okej att tillhandahålla en användbar länk för att erbjuda innehåll på ett språk som skiljer sig från det som används i erbjudandena.

### <a name="name"></a>Namn

Namnet du anger här visas som titeln på ditt erbjudande. Det här fältet är förfyllt med den text som du angav i rutan **Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara varumärkesskyddade (och du kan inkludera varumärkes- och upphovsrättssymboler).
- Kan inte vara mer än 50 tecken lång.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

En kort beskrivning av ditt erbjudande. Detta kan vara upp till 100 tecken långt och används i marketplace-sökresultat.

### <a name="long-summary"></a>Lång sammanfattning

En mer detaljerad beskrivning av ditt erbjudande. Detta kan vara upp till 256 tecken lång och används i marketplace sökresultat.

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
  - Den typ av användare som drar nytta av erbjudandet
  - Kundens behov eller utfärdar erbjudandeadresserna.
- Kom ihåg att de första meningarna kan visas i sökresultaten.
- Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde som ditt erbjudande ger.
- Försök att använda branschspecifika ordförråd eller förmånsbaserad ordalydelse.

Om du vill göra **erbjudandebeskrivningen** mer engagerande använder du RTF-redigeraren för att formatera beskrivningen. med numrering, punkter, fetstil, kursiv stil och indrag för att göra din beskrivning mer läsbar.

:::image type="content" source="media/text-editor2.png" alt-text="Illustrerar RTF-redigeraren." border="false" :::

- Använd den här listrutan om du vill använda ett styckeformat på text.

    :::image type="content" source="media/text-editor3.png" alt-text="Illustrerar textformatkontrollen i RTF-redigeraren." border="false":::

- Använd dessa ikoner för att använda numrering eller punkter på text.

     :::image type="content" source="media/text-editor4.png" alt-text="Illustrerar kontrollerna för punktlistor och nummerlistor i RTF-redigeraren." border="false":::

- Använd dessa ikoner för att lägga till eller ta bort indrag i eller från text.

    :::image type="content" source="media/text-editor5.png" alt-text="Illustrerar indragskontrollerna i RTF-redigeraren." border="false":::

#### <a name="privacy-policy-link"></a>Länk till sekretesspolicy

Ange webbadressen till organisationens sekretesspolicy. Du är ansvarig för att ditt erbjudande följer sekretesslagar och sekretessregler. Du är också ansvarig för att publicera en giltig sekretesspolicy på din webbplats.

#### <a name="useful-links"></a>Användbara länkar

Tillhandahålla kompletterande onlinedokument om ditt erbjudande. Du kan lägga till upp till 25 länkar. Om du vill lägga till en länk väljer du **+ Lägg till en länk** och fyller sedan i följande fält:

- **Titel** – Kunderna ser detta på ditt erbjudandes informationssida.
- **Länk (URL)** – Ange en länk så att kunderna kan visa ditt onlinedokument. Länken måste börja med http:// eller https://.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-post och telefonnummer för en **supportkontakt** och en **teknisk kontakt.** Den här informationen visas inte för kunder men är tillgänglig för Microsoft. Det kan också tillhandahållas till CSP-partner (Cloud Solution Provider).

- Supportkontakt (obligatoriskt): För allmänna supportfrågor.
- Teknisk kontakt (krävs): För tekniska frågor och certifieringsfrågor.
- CSP-programkontakt (valfritt): För återförsäljarfrågor relaterade till CSP-programmet.

I avsnittet **Supportkontakt** anger du **supportwebbplatsen** där partner kan hitta support för ditt erbjudande baserat på om erbjudandet är tillgängligt i globala Azure, Azure Government eller båda.

I **kontaktsektionen för CSP-programmet** anger du länken **(CSP Program Marketing Materials)** där CSP-partner kan hitta marknadsföringsmaterial för ditt erbjudande.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

Mer information om hur du skapar erbjudanden finns i [Bästa praxis för erbjudandebeskrivning](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Marketplace-bilder

Ange logotyper och bilder som du kan använda med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

#### <a name="store-logos"></a>Lagra logotyper

 Ange PNG-filer av erbjudandets logotyp i var och en av följande fyra pixelstorlekar:

- **Liten** (48 x 48)
- **Medium** (90 X 90)
- **Stor** (216 x 216)
- **Bred** (255 X 115)

Alla fyra logotyper krävs och används på olika platser på marknadsplatsen notering.

#### <a name="screenshots-optional"></a>Skärmdumpar (valfritt)

Lägg till upp till fem skärmbilder som visar hur erbjudandet fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som visar ditt erbjudande. Ange videons namn, dess webbadress och en PNG-miniatyrbild av videon med en storlek på 1 280 x 720 pixlar.

#### <a name="offer-examples"></a>Exempel på erbjudanden

Följande exempel visar hur listningsfälten för erbjudande visas på olika ställen i erbjudandet.

Detta visar sidan **Erbjudandelistning** på Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Illustrerar sidan Erbjudandelistning på Azure Marketplace." :::

Detta visar sökresultat på Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Illustrerar sökresultaten på Azure Marketplace.":::

Detta visar sidan **Erbjudandelistning** i Azure-portalen:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Illustrerar sidan Erbjudandelistning i Azure-portalen.":::

Detta visar sökresultat i Azure-portalen:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Illustrerar sökresultaten i Azure-portalen.":::

## <a name="preview"></a>Förhandsversion

På fliken Förhandsgranska kan du välja en begränsad målgrupp för **att** validera erbjudandet innan du publicerar det live.

> [!IMPORTANT]
> När du har visat erbjudandet i **förhandsversion**måste du välja **Gå live** för att publicera ditt erbjudande för allmänheten.

Ange din förhandsgranskningsmålgrupp med azure-prenumerations-ID-GUID: er, tillsammans med en valfri beskrivning för varje. Inget av dessa fält kan ses av kunder.

> [!NOTE]
> Du hittar ditt Azure-prenumerations-ID på sidan Prenumerationer i Azure-portalen.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID:er bestämmer du vem som kan förhandsgranska erbjudandet innan det publiceras live. Om ditt erbjudande redan är live kan du välja en målgrupp för förhandsversionen för att testa ändringar eller uppdateringar av erbjudandet.

> [!NOTE]
> Förhandsgranskningspubliken skiljer sig från en privat målgrupp. En **förhandsgranskningsmål** kan se och bekräfta alla erbjudandeplaner innan de visas på marknadsplatsen, inklusive de som endast publiceras för en **privat** målgrupp (som anges på fliken Tillgänglighet).

Välj **Spara utkast** innan du fortsätter.

### <a name="plan-overview"></a>Översikt över plan

På den här fliken kan du ange olika planalternativ i samma erbjudande. Dessa planer kallades tidigare SKU:er eller lagerhållningsenheter. Planer kan skilja sig åt när det gäller vilka moln som är tillgängliga, till exempel globala moln, regeringsmoln och bilden som refereras av planen. Om du vill lista ditt erbjudande på den kommersiella marknaden måste du ställa in minst en plan.

När du har skapat dina planer visas på fliken **Planera översikt:**

- Planera namn
- Prismodell
- Molntillgänglighet (global eller myndighet)
- Aktuell publiceringsstatus
- Alla tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i översikten Planera varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast** – Om planstatusen är ett utkast.
- **Stoppa säljplan** – Om planens status publiceras live.

#### <a name="create-new-plan"></a>Skapa ny plan

Välj **Skapa ny plan**. Dialogrutan **Ny plan** visas.

Skapa en unik planidentifierare för varje plan i det här erbjudandet i rutan **Plan-ID.** Detta ID kommer att vara synligt för kunder i produktens webbadress. Använd bara gemener och siffror, streck eller understreck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID:t när du har valt **Skapa**.

Ange ett namn för den här planen i rutan **Plannamn.** Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas inom ditt erbjudande. Skapa ett unikt namn för varje plan i det här erbjudandet. Du kan till exempel använda ett erbjudandenamn på **Windows Server** med abonnemangen Windows **Server 2016** och **Windows Server 2019**.

### <a name="plan-setup"></a>Planera inställningar

På den här fliken kan du välja vilka moln planen är tillgänglig i. Dina svar på den här fliken påverkar vilka fält som visas på andra flikar.

#### <a name="cloud-availability"></a>Molntillgänglighet

Din plan måste vara tillgänglig i minst ett moln.

Välj alternativet **Azure Global** så att din plan kan användas av kunder i alla globala Azure-regioner som använder den kommersiella marknadsplatsen. Mer information finns i [Geografisk tillgänglighet och valutastöd](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Välj alternativet [**Azure Government Cloud**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) för att få din lösning att visas här. Detta är en regering gemenskap moln med kontrollerad åtkomst för kunder från amerikanska federala, statliga och lokala eller tribal myndigheter, samt partner som är berättigade att betjäna dem. Som utgivare är du ansvarig för alla efterlevnadskontroller, säkerhetsåtgärder och metodtips för den här molncommunityn. Azure Government använder fysiskt isolerade datacenter och nätverk (endast i USA).

Innan [du publicerar](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) till Azure Government testar och bekräftar du din lösning inom det området eftersom resultaten kan vara annorlunda. Om du vill skapa och testa din lösning begär du ett utvärderingskonto från [testversionen](https://azure.microsoft.com/global-infrastructure/government/request/)av Microsoft Azure Government .

> [!NOTE]
> När ditt abonnemang har publicerats och är tillgängligt i ett visst moln kan du inte ta bort molnet.

#### <a name="azure-government-cloud-certifications"></a>Azure Government Cloud-certifieringar

Det här alternativet kan bara ses om **Azure Government Cloud** har valts under **Molntillgänglighet**.

Azure Government-tjänster hanterar data som omfattas av vissa myndighetsregler och krav. FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS.

Om du vill visa dina certifieringar för dessa program kan du tillhandahålla upp till 100 länkar som beskriver dem. Dessa kan vara länkar till dina listor på programmet direkt eller till din egen webbplats. Dessa länkar är endast synliga för Azure Government-kunder.

## <a name="plan-listing"></a>Planera listning

På den här fliken visas specifik information för varje plan inom det aktuella erbjudandet.

### <a name="plan-name"></a>Plannamn

Detta är förfyllt med namnet du gav din plan när du skapade den. Du kan ändra det här namnet efter behov. Det kan vara upp till 50 tecken lång. Det här namnet visas som titeln på den här planen i Azure Marketplace och Azure-portalen. Den används som standardmodulnamn när planen är klar att användas.

### <a name="plan-summary"></a>Sammanfattning av planen

En kort sammanfattning av din programvara plan (inte erbjudandet). Den här sammanfattningen visas i Azure Marketplace-sökresultat och kan innehålla upp till 100 tecken.

### <a name="plan-description"></a>Beskrivning av planen

Beskriv vad som gör denna programvaruplan unik, samt skillnader mellan planer inom ditt erbjudande. Beskriv inte erbjudandet, bara planen. Den här beskrivningen visas i Azure Marketplace och i Azure-portalen på sidan **Erbjudandelistning.** Det kan vara samma innehåll som du angav i plansammanfattningen och innehålla upp till 2 000 tecken.

Välj **Spara** när du har slutfört dessa fält.

#### <a name="plan-examples"></a>Planera exempel

Följande exempel visar hur fälten för listning av planer visas i olika vyer.

Det här är fälten på Azure Marketplace när du visar planinformation:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustrerar de fält du ser när du visar planinformation i Azure Marketplace.":::

Det här är planinformation på Azure-portalen:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Illustrerar planinformation på Azure-portalen.":::

## <a name="plan-availability"></a>Planera tillgänglighet

Om du vill dölja ditt publicerade erbjudande så att kunderna inte kan söka, bläddra eller köpa det på marknadsplatsen markerar du kryssrutan **Dölj abonnemang** på fliken **Tillgänglighet.**

Det här fältet används när:

- Erbjudandet är avsett att användas indirekt när det refereras till genom ett annat program.
- Erbjudandet ska inte köpas individuellt.
- Planen användes för inledande testning och är inte längre relevant.
- Planen användes för tillfälliga eller säsongsbetonade erbjudanden och bör inte längre erbjudas.

## <a name="technical-configuration"></a>Teknisk konfiguration

Behållaravbildningar måste finnas i ett privat [Azure-behållarregister](https://azure.microsoft.com/services/container-registry/). På fliken **Teknisk konfiguration** anger du referensinformation för behållaravbildningsdatabasen i Azure Container Registry.

När erbjudandet har publicerats kopieras behållaravbildningen till Azure Marketplace i ett specifikt offentligt behållarregister. Alla begäranden om att använda behållaravbildningen visas från Azure Marketplaces offentliga behållarregister, inte från ditt privata. Mer information finns i [Förbereda dina tekniska resurser för Azure Container](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

### <a name="image-repository-details"></a>Information om bilddatabas

Ange följande information på fliken **Information om bilddatabasen.**

**Azure-prenumerations-ID** – Ange prenumerations-ID där användning rapporteras och tjänster faktureras för Azure Container Registry som innehåller din behållaravbildning. Du hittar det här ID:t på [sidan Prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

**Azure-resursgruppsnamn** – Ange [resursgruppnamnet](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) som innehåller Azure Container-registret med behållaravbildningen. Resursgruppen måste vara tillgänglig i prenumerations-ID (ovan). Du hittar namnet på sidan [Resursgrupper](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i Azure-portalen.

**Azure Container Registry name** – Ange namnet på [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) som har din behållaravbildning. Behållarregistret måste finnas i den Azure-resursgrupp som du angav tidigare. Inkludera endast registernamnet, inte det fullständiga inloggningsservernamnet. Var noga med att utelämna **azurecr.io** från namnet. Du hittar registernamnet på [sidan Behållarregister](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) i Azure-portalen.

**Administratörsanvändarnamn för Azure Container Registry** – Ange [administratörsanvändarnamnet](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) som är länkat till Azure Container-registret som har din behållaravbildning. Användarnamnet och lösenordet krävs för att säkerställa att ditt företag har tillgång till registret. Om du vill hämta administratörsanvändarnamnet och lösenordet anger du egenskapen **för adminaktiverad** till **True** med hjälp av AZURE Command-Line Interface (CLI). Du kan också ange **admin-användare** till **Aktivera** i Azure-portalen.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Illustrerar dialogrutan Uppdatera behållarregister.":::

**Lösenord för Azure Container Registry** – Ange lösenordet för administratörsanvändarnamnet som är associerat med Azure Container Registry och har din behållaravbildning. Användarnamnet och lösenordet krävs för att säkerställa att ditt företag har tillgång till registret. Du kan hämta lösenordet från Azure-portalen genom att gå till **Container Registry** > **Access Keys** eller med Azure CLI med kommandot [Visa](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Illustrerar menyn Åtkomstnyckel.":::

**Databasnamn i Azure Container Registry**. Ange namnet på azure container-registerdatabasen som har avbildningen. Inkludera namnet på databasen när du skickar avbildningen till registret. Du hittar namnet på databasen genom att gå till sidan [Behållarregisterdatabaser.](https://azure.microsoft.com/services/container-registry/) > **Repositories** Mer information finns i [Visa behållarregisterdatabaser i Azure Portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories).

> [!NOTE]
> När namnet har angetts kan det inte ändras. Använd ett unikt namn för varje erbjudande i ditt konto.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Bildtaggar för nya versioner av ditt erbjudande

Kunder måste kunna hämta uppdateringar automatiskt från Azure Marketplace när du publicerar en uppdatering. Om de inte vill uppdatera måste de kunna behålla en viss version av bilden. Du kan göra detta genom att lägga till nya bildtaggar varje gång du gör en uppdatering av bilden.

### <a name="image-tag"></a>Bildtagg

Det här fältet måste innehålla en **senaste** tagg som pekar på den senaste versionen av avbildningen på alla plattformar som stöds. Den måste också innehålla en versionstagg (till exempel från xx.xx.xx, där xx är ett tal). Kunder bör använda [manifesttaggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla taggar som refereras av en manifesttagg måste också läggas till så att vi kan ladda upp dem.

Alla manifesttaggar (utom den senaste taggen) **-** måste börja med antingen X.Y eller X.Y.Z- där X, Y och Z är heltal. Om till exempel en **senaste** tagg pekar på 1.0.1-linux-x64, 1.0.1-linux-arm32 och 1.0.1-windows-arm32, måste dessa sex taggar läggas till i det här fältet. Mer information finns i [Förbereda dina tekniska resurser för Azure Container](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Kom ihåg att lägga till en testtagg i bilden så att du kan identifiera bilden under testningen.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in det för att granska och publicera.

I det övre högra hörnet på portalen väljer du **Granska och** **publicera**.

På granskningssidan kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har markerats som slutförd.
  - **Inte startat** – Har inte startats och måste slutföras.
  - **Ofullständig** – Har fel som måste åtgärdas eller kräver att du ger mer information. Mer hjälp finns i avsnitten tidigare i det här dokumentet.
  - **Slutför** – Innehåller alla nödvändiga data utan fel. Alla delar av erbjudandet måste vara fullständiga innan du kan skicka erbjudandet.
- Ge testinstruktioner till certifieringsteamet för att säkerställa att ditt erbjudande testas korrekt. Också, ge eventuella kompletterande anteckningar som är till hjälp för att förstå ditt erbjudande.

Om du vill skicka erbjudandet för publicering väljer du **Publicera**.

Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet finns tillgänglig för granskning och godkännande.

Om du vill publicera ditt erbjudande för allmänheten (eller om ett privat erbjudande, till en privat målgrupp) går du till Partnercenter och väljer **Go-live**.

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
