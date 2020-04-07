---
title: Skapa ett Power BI-apperbjudande – Azure Marketplace
description: Lär dig hur du skapar och publicerar ett Power BI-apperbjudande till Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 776311d6e6395cbe462f958bd8685fa0259e1fc2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674194"
---
# <a name="create-a-power-bi-app"></a>Skapa en Power BI-app

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Power BI-apperbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Skapa ett Power BI-apperbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du skapar och publicerar ett Power BI-apperbjudande till Microsoft [AppSource](https://appsource.microsoft.com/).

Innan du kan skapa ett Power BI-apperbjudande måste du ha ett kommersiellt marketplace-konto i Partner Center. Om du inte har skapat ett ännu läser du [Skapa ett kommersiellt Marketplace-konto i Partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) Center-kontot.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in [på Partner Center](https://partner.microsoft.com/dashboard/home).
2. Välj Översikt för kommersiell **marknadsplats** > i menyn till**vänster.**

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="översikt över den kommersiella marknadsplatsen menyn" border="false":::

3. Välj **+ Nytt erbjudande** > **Power BI App**. Dialogrutan **Nytt erbjudande** visas.

> [!IMPORTANT]
> Om alternativet **Power BI App**&#39;inte visas eller inte är aktiverat&#39;inte har ditt konto&#39;inte behörighet att skapa den här erbjudandetypen. Kontrollera att du&#39;har uppfyllt alla [krav](create-power-bi-app-overview.md) för den här erbjudandetypen, inklusive registrering för ett utvecklarkonto

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t är synligt för kunder i webbadressen för marketplace-erbjudande och Azure Resource Manager-mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Den kan innehålla bindestreck och understreck, men inga blanksteg och är begränsad till 50 tecken. Om du till exempel anger **test-erbjudande-1** blir `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`erbjudandewebbadressen .

- Erbjudande-ID:t kan inte ändras när du har valt **Skapa**.

Ange ett **offeralias**. Det här är namnet som används för att referera till erbjudandet i Partner Center.

- Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna.
- Detta kan inte ändras när du har valt **Skapa**.

När du har angett dessa två värden väljer du **Skapa** för att fortsätta till sidan Erbjudandeöversikt.

## <a name="offer-overview"></a>Översikt över erbjudandet

**Översiktssidan Erbjudande** visar en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och kommande) och hur lång tid varje steg ska ta att slutföra.

Den innehåller länkar för att utföra åtgärder på detta erbjudande baserat på det val du gör. Ett exempel:

- Om erbjudandet är ett utkast - [Ta bort utkast erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Om erbjudandet är live - [Sluta sälja erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Om erbjudandet är i förhandsgranskning - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Om du inte har slutfört utgivningsut logga ut - [Avbryt publicering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Inställningar för erbjudande

Följ dessa steg för att ställa in ditt erbjudande.

### <a name="connect-lead-management"></a>Anslut leadhantering

När du publicerar ditt erbjudande på marknadsplatsen med Partner Center måste du ansluta det till ditt CRM-system (Customer Relationship Management). På så sätt kan du få information om kundkontakter så snart någon uttrycker intresse för eller använder din produkt.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kundengagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system inte visas ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra leaddata för kunder. Exportera sedan data till crm-systemet.

2. Anslut ditt erbjudande till leaddestinationen när du publicerar i Partner Center.
3. Kontrollera att anslutningen till lead-målet är korrekt konfigurerad. När du har publicerat den i Partner Center validerar vi anslutningen och skickar en testledare till dig. Medan du förhandsgranskar erbjudandet innan det visas kan du också testa din leadanslutning genom att försöka köpa erbjudandet själv i förhandsversionen.
4. Se till att anslutningen till leaddestinationen förblir uppdaterad så att du inte förlorar några leads.

Här är några ytterligare lead hanteringsresurser:

- [Översikt över leadhantering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Leadhantering – vanliga frågor och svar](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga fel för leadkonfiguration](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över leadhantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (se till att popup-blockeraren är avstängd)

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Egenskaper.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatsen, din appversion och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj minst en och högst tre kategorier. Dessa kategorier används för att placera ditt erbjudande i lämpliga marknadsplats sökområden och visas på din erbjudandeinformation sida. Förklara i erbjudandebeskrivningen hur ditt erbjudande stöder dessa kategorier.

### <a name="industry"></a>Bransch

Välj eventuellt upp till två branscher och två vertikaler under varje bransch. Kategorier används för att visa ditt erbjudande, men industrin och vertikalerna används i sökfilter och används i Storefront. Om ditt erbjudande riktar sig till en viss bransch och/eller vertikal använder du erbjudandebeskrivningen för att förklara hur erbjudandet stöder de valda branscherna eller vertikalerna. Om ditt erbjudande inte är&#39;branschspecifikt lämnar du det här avsnittet tomt.

> [!NOTE]
> När vi arbetar för att introducera nya branscher och vertikaler för att förbättra erbjudandet upptäckt erfarenhet, vissa branscher eller vertikaler kanske ännu inte syns på Skyltfönstret. Industrier och vertikaler markerade med en (*) kommer att vara tillgängliga vid ett framtida datum. Alla publicerade erbjudanden kan upptäckas via sökordssökning.
<p>&nbsp;

| **Bransch** | **Subindustri** |
| --- | --- |
| *Automotive (Automotive) | *Automotive (Automotive) |
| Jordbruk | *Övrigt - Ej segmenterad |
| Distribution | * Partihandel<br>Paket- och paketfrakt |
| Utbildning | *Högre<br> utbildning* Grund- och gymnasieutbildning / K-12<br>*Bibliotek och museer |
| Ekonomiska tjänster | *Bank- och<br> kapitalmarknader* Försäkring |
| Government | *Försvar och intelligens (brukade kallas nationell<br> och allmän säkerhet)* Allmän säkerhet och rättvisa<br>*Civil regering |
| Hälso- och sjukvård (brukade kallas hälsa) | *Hälsa Betalare<br> * Vårdgivare<br>* Läkemedel |
| Tillverkning och resurser (brukade kallas tillverkning) | *Kemiska och agrokemiska<br> * Diskret tillverkning<br>*Energi |
| Detaljhandel och konsumtionsvaror (brukade kallas Detaljhandel) | *Konsumtionsvaror<br> * Återförsäljare |
| * Media och kommunikation (brukade kallas Media och Underhållning) | *Media och<br> underhållning* Telekommunikation |
| Professionella tjänster | *Juridiska<br> * Partner professionella tjänster |
| * Arkitektur och konstruktion (brukade kallas Arkitektur Engineering) | *Övrigt - Ej segmenterad |
| * Gästfrihet och resor | *Hotell och<br> fritid* Resor och transport<br>* Restauranger och mattjänster |
| *Övriga branscher inom den offentliga sektorn | *Skogsbruk och<br> fiske* Ideella organisationer |
| * Fastigheter | *Övrigt - Ej segmenterad |

### <a name="legal"></a>Juridisk information

#### <a name="terms-and-conditions"></a>Villkor

Om du vill ange dina egna anpassade villkor anger du upp till 10 000 tecken i rutan **Regler och villkor.** Om dina villkor kräver en längre beskrivning anger du en enda webblänk till var de finns. Den visas för kunderna som en aktiv länk.

Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Offer list.

## <a name="offer-listing"></a>Lista över erbjudande

Här definierar du erbjudandeinformationen som visas på marknadsplatsen. Detta inkluderar erbjudandet namn, beskrivning, bilder och så vidare.

### <a name="language"></a>Språk

Välj det språk som erbjudandet ska visas på. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Definiera marknadsplatsinformation (till exempel erbjudandenamn, beskrivning och bilder) för varje språk/marknad. Välj språk-/marknadsnamn för att ange den här informationen.

> [!NOTE]
> Erbjudandeuppgifter behöver inte vara på engelska om erbjudandebeskrivningen &quot;börjar med frasen, Denna ansökan är endast tillgänglig på [icke-engelska språket]. &quot; Det&#39;också okej att ge en användbar länk för att erbjuda innehåll på ett språk som&#39;skiljer sig från den som används i erbjudandet notering.

### <a name="name"></a>Namn

Namnet du anger här visas som titeln på ditt erbjudande. Det här fältet är förfyllt med den text som du angav i rutan **Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara varumärkesskyddade (och du kan inkludera varumärkes- eller upphovsrättssymboler).
- Kan inte vara mer än 50 tecken lång.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

Ge en kort beskrivning av ditt erbjudande. Detta kan vara upp till 100 tecken långt och används i marketplace-sökresultat.

### <a name="description"></a>Beskrivning

Ge en längre beskrivning av ditt erbjudande, upp till 3000 tecken. Detta visas för kunder i översikten för marknadsplatsannonser.

Inkludera ett eller flera av följande i beskrivningen:

- Värdet och de viktigaste fördelarna med ditt erbjudande ger.
- Kategori- eller branschorganisationer, eller båda.
- Köpmöjligheter i appen.
- Alla nödvändiga upplysningar.

Här är några tips för att skriva din beskrivning:

- Beskriv tydligt värdet av ditt erbjudande i de första meningarna i din beskrivning. Inkludera följande:
  - Beskrivning av erbjudandet.
  - Den typ av användare som drar nytta av erbjudandet.
  - Kundens behov eller utfärdar erbjudandeadresserna.
- Kom ihåg att de första meningarna kan visas i sökresultaten.
- Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde som ditt erbjudande ger.
- Försök att använda branschspecifika ordförråd eller förmånsbaserad ordalydelse.

Om du vill göra erbjudandet **Beskrivning** mer engagerande använder du RTF-redigeraren för att formatera beskrivningen. Med RTF-redigeraren kan du lägga till tal, punkter, fetstil, kursiv stil och indrag för att göra beskrivningen mer läsbar.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="illustrerar RTF-redigeraren" border="false":::

### <a name="search-keywords"></a>Sök nyckelord

Ange upp till tre valfria sökord för att hjälpa kunderna att hitta ditt erbjudande på marknaden. För bästa resultat, använd även dessa sökord i beskrivningen.

### <a name="helpprivacy-web-addresses"></a>Webbadresser för hjälp/sekretess

Tillhandahålla länkar som hjälper kunderna att bättre förstå ditt erbjudande.

#### <a name="help-link"></a>Länk till hjälp

Ange webbadressen där kunderna kan läsa mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>Url till sekretesspolicy

Ange webbadressen till organisationens sekretesspolicy. Du är ansvarig för att ditt erbjudande följer sekretesslagar och sekretessregler. Du är också ansvarig för att publicera en giltig sekretesspolicy på din webbplats.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-post och telefonnummer för en **supportkontakt** och en **teknisk kontakt.** Den här informationen visas&#39;inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner (Cloud Solution Provider).

- Supportkontakt (obligatoriskt): För allmänna supportfrågor.
- Teknisk kontakt (krävs): För tekniska frågor och certifieringsfrågor.
- CSP-programkontakt (valfritt): För återförsäljarfrågor relaterade till CSP-programmet.

I avsnittet **Supportkontakt** anger du webbadressen **till supportwebbplatsen** där partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Verifikationer

Ange minst ett och tre relaterade marknadsföringsdokument i PDF-format. Till exempel faktablad, broschyrer, checklistor eller presentationer.

### <a name="marketplace-images"></a>Marketplace-bilder

Ange logotyper och bilder som du kan använda med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

#### <a name="store-logos"></a>Lagra logotyper

Ange .png-filer i ditt erbjudande&#39;logotyp i två storlekar: **Liten** (48 x 48 pixlar) och **Stor** (216 x 216 pixlar).

Båda logotyperna krävs och används på olika platser på marknadsplatsen notering.

#### <a name="screenshots"></a>Skärmbilder

Lägg till minst en och upp till fem skärmdumpar som visar hur erbjudandet fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i .png-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som visar ditt erbjudande. Ange videon&#39;s namn, dess webbadress och miniatyrbild av videon med 1 280 x 720 pixlar.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

Mer information om hur du skapar erbjudanden finns i [Bästa praxis för erbjudandebeskrivning](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Teknisk konfiguration

Marknadsför din app i Power BI Service till produktion och ange power bi-appinstallationslänken som gör det möjligt för kunder att installera din app. Mer information finns i [Publicera appar med instrumentpaneler och rapporter i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Supplementellt innehåll

Ge ytterligare information om ditt erbjudande för att hjälpa oss att validera det. Den här informationen visas&#39;inte för kunder eller publiceras på marknadsplatsen.

### <a name="validation-assets"></a>Valideringstillgångar

Du kan också lägga till instruktioner (upp till 3 000 tecken) som hjälper Microsofts valideringsteam att konfigurera, ansluta och testa appen. Inkludera vanliga konfigurationsinställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut data. Den här informationen är endast synlig för valideringsteamet och används endast för valideringsändamål.

## <a name="review-and-publish"></a>Granska och publicera

När du&#39;har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in ditt erbjudande om att granska och publicera.

I det övre högra hörnet på portalen väljer du **Granska och publicera**.

På granskningssidan kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet. Du kan&#39;inte publicera förrän alla delar av erbjudandet har markerats som slutförd.
  - **Inte startat** - Avsnittet har inte startats och måste slutföras.
  - **Ofullständig** - Avsnittet har fel som måste åtgärdas eller kräver att du ger mer information. Mer information finns i avsnitten tidigare i det här dokumentet.
  - **Komplett** - Avsnittet har alla nödvändiga data och det finns inga fel. Alla delar av erbjudandet måste vara fullständiga innan du kan skicka erbjudandet.
- Ge testinstruktioner till certifieringsteamet för att säkerställa att din app testas korrekt. Också, ge eventuella kompletterande anteckningar som är till hjälp för att förstå ditt erbjudande.

Om du vill skicka erbjudandet för publicering väljer du **Publicera**.

Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet finns tillgänglig för granskning och godkännande. Om du vill publicera ditt erbjudande för allmänheten (eller om ett privat erbjudande, till en privat målgrupp) går du till Partnercenter och väljer **Go-live**.
