---
title: Snabbstart – Utforska Azure-kostnader med kostnadsanalys
description: Den här snabbstarten hjälper dig att använda kostnadsanalys för att utforska och analysera dina Azure-organisationskostnader.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: quickstart
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: seodec18
ms.openlocfilehash: 53e1ef2f5e4faecd3ab2dee1350f3e9087df8e9d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80155961"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Snabbstart: Utforska och analysera kostnader med kostnadsanalys

För att kunna kontrollera och optimera dina Azure-kostnader på rätt sätt behöver du förstå var kostnaderna har sitt ursprung i organisationen. Det är även bra att veta hur mycket pengar dina tjänster kostar och vilka miljöer och system de stödjer. Insyn i hela spektrumet av kostnader är mycket viktigt för kunna förstå organisationens utgiftsmönster. Du kan använda utgiftsmönster för att tillämpa kostnadskontrollmekanismer, till exempel budgetar.

I den här snabbstarten använder du kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader sker över tid och för att identifiera utgiftstrender. Du kan se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget. En budget gör det lättare att hålla sig till ekonomiska begränsningar. En budget används även till att visa dagliga och månatliga kostnader för att isolera oregelbundenheter vad gäller utgifter. Dessutom kan du ladda ned den aktuella rapportens data för ytterligare analys eller för användning i ett externt system.

I den här snabbstarten lär du dig att:

- Granska kostnader i kostnadsanalys
- Anpassa kostnadsvyer
- Ladda ned kostnadsanalysdata

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto.

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../../cost-management/assign-access-acm-data.md).

Om du har en ny prenumeration kan du inte använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Granska kostnader i kostnadsanalys

Om du vill granska kostnaderna i kostnadsanalys öppnar du omfånget i Azure-portalen och väljer **Kostnadsanalys** på menyn. Du kan till exempel gå till **Prenumerationer**, välja en prenumeration i listan och sedan välja **Kostnadsanalys** på menyn. Använd reglaget **Omfång** för att växla till ett annat omfång i kostnadsanalysen. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

Det omfång som du väljer används i hela Cost Management för att ge datakonsolidering och styra åtkomsten till kostnadsinformation. När du använder omfång så använder du inte flerval för dem. I stället väljer du ett större omfång som andra ackumuleras till, och sedan filtrerar du ned till de kapslade omfång du behöver. Den här metoden är viktig att förstå eftersom vissa personer kanske inte har åtkomst till ett enda överordnat omfång som täcker flera kapslade omfång.

Lär dig hur du arbetar med kostnadsanalyser i videon om [Cost Management på Azure-portalen](https://www.youtube.com/watch?v=mfxysF-kTFA).

>[!VIDEO https://www.youtube.com/embed/mfxysF-kTFA]

Den initiala kostnadsanalysvyn innehåller följande områden.

**Vy över ackumulerade kostnader**: Representerar konfigurationen för den fördefinierade vyn för kostnadsanalys. Varje vy innehåller inställningar för datumintervall, kornighet, gruppera efter och filter. Standardvyn visar ackumulerade kostnader för den aktuella faktureringsperioden, men du kan ändra till andra inbyggda vyer. Mer information finns i [Anpassa kostnadsvyer](#customize-cost-views).

**Faktisk kostnad**: Visar den totala förbruknings- och inköpskostnaden för den aktuella månaden, allteftersom de påförs och visas på fakturan.

**Prognos**: Visar de totala prognostiserade kostnaderna för den tidsperiod som du väljer.

**Budget**: Visar den planerade utgiftsgränsen för det valda omfånget, om en sådan är tillgänglig.

**Ackumulerad kornighet**: Visar totalsumman för dagliga kostnader från början av faktureringsperioden. När du har [skapat en budget](tutorial-acm-create-budgets.md) för ditt faktureringskonto eller din prenumeration kan du snabbt se din utgiftstrend jämfört med budgeten. Hovra över ett datum om du vill visa den ackumulerade kostnaden för den dagen.

**Pivotdiagram (ringdiagram)** : Visar dynamiska pivoter som delar upp den totala kostnaden enligt en gemensam uppsättning standardegenskaper. De visar de största till minsta kostnaderna för den aktuella månaden. Du kan ändra pivotdiagram när som helst genom att välja en annan pivot. Kostnaderna kategoriseras efter tjänst (mätarkategori), plats (region) och underordnat omfång som standard. Exempel: registreringskonton är under faktureringskonton, resursgrupper är under prenumerationer och resurser är under resursgrupper.

![Startvyn för kostnadsanalys på Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Förstå en prognos

En kostnadsprognos visar en uppskattning av kostnaderna för den valda tidsperioden. Modellen baseras på en regressionsmodell med en tidsserie. Kostnads- och användningsdata för minst de senaste tio dagarna krävs för att få en korrekt prognostisering av kostnaderna. För en viss tidsperiod behöver prognosmodellen lika delar med träningsdata för prognosperioden. För att göra en uppskattning för tre månader krävs kostnads- och användningsdata för minst de senaste tre månaderna.

Modellen använder maximalt sex månaders träningsdata för att uppskatta kostnaderna för ett år. Den behöver minst sju dagars träningsdata för att ändra förutsägelsen. Förutsägelsen baseras på stora förändringar, till exempel topp- och bottenvärden, i kostnads- och användningsmönster. Prognosen skapar inte enskilda uppskattningar för varje objekt i **Gruppera efter**-egenskaper. Den ger bara en prognos för ackumulerade kostnader totalt. Om du använder flera valutor anger modellen en prognos för kostnaderna endast i USD.

## <a name="customize-cost-views"></a>Anpassa kostnadsvyer

Kostnadsanalys har fyra inbyggda vyer som är optimerade för de vanligaste målen:

Visa | Besvara frågor som
--- | ---
Ackumulerad kostnad | Hur mycket har jag använt hittills den här månaden? Följer jag budgeten?
Daglig kostnad | Har det funnits några ökade kostnader per dag under de senaste 30 dagarna?
Kostnad efter tjänst | Hur skiljer sig min månatliga användning för de tre senaste fakturorna?
Kostnad efter resurs | Vilka resurser kostar mest hittills den här månaden?

![Vyväljare som visar ett exempelurval för den här månaden](./media/quick-acm-cost-analysis/view-selector.png)

Det finns dock många fall där du behöver djupare analys. Anpassning startar överst på sidan med valet av datum.

Kostnadsanalys visar data för den aktuella månaden som standard. Använd datumväljaren för att snabbt växla till vanliga datumintervall. Exempel omfattar de senaste sju dagarna, den senaste månaden, det aktuella året eller ett anpassat datumintervall. Prenumerationer enligt principen betala per användning omfattar även datumintervall baserat på din faktureringsperiod, som inte är kopplad till kalendermånaden, till exempel den aktuella faktureringsperioden eller senaste fakturan. Använd länkarna **<FÖREGÅENDE** och **NÄSTA>** överst på menyn för att gå vidare till föregående eller nästa period. **<FÖREGÅENDE** kommer till exempel att växla från **Senaste 7 dagarna** till **8-14 days ago** (8–14 dagar sedan) eller **15-21 days ago** (15–21 dagar sedan).

![Datumväljare som visar ett exempelurval för den här månaden](./media/quick-acm-cost-analysis/date-selector.png)

Kostnadsanalysen visar **ackumulerade** kostnader som standard. Ackumulerade kostnader omfattar alla kostnader för varje dag plus föregående dagar, för en ständigt växande vy över dina dagliga sammanställda kostnader. Den här vyn är optimerad för att visa hur du trendar mot en budget för det valda tidsintervallet.

Använd prognosdiagramvyn för att identifiera potentiella budgetöverträdelser. När det finns en potentiell budgetöverträdelse visas projektets för höga utgifter i rött. En indikatorsymbol visas också i diagrammet. Om du hovrar över symbolen visas det beräknade datumet för budgetöverträdelsen.

![Exempel som visar potentiell budgetöverträdelse](./media/quick-acm-cost-analysis/budget-breach.png)

Det finns även den **dagsvyn**, som visar kostnaderna för varje dag. Dagsvyn visar inte någon tillväxttrend. Vyn har utformats för att visa oregelbundenheter såsom toppar eller dalar i utgifter från dag till dag. Om du har valt en budget visar dagsvyn även en uppskattning av din dagliga budget.

När dina dagliga kostnader konsekvent är över den beräknade dagliga budgeten kan du förvänta dig att du överskrider din månatliga budget. Den beräknade dagliga budgeten är ett sätt att visualisera din budget på en lägre nivå. När det förekommer variationer i de dagliga kostnaderna är jämförelsen mellan den uppskattade dagliga budgeten och den månatliga budgeten mindre exakt.

Här är en daglig vy över de senaste utgifterna med aktiverad utgiftsprognos.
![Daglig vy som visar exempel på dagliga kostnader för den aktuella månaden](./media/quick-acm-cost-analysis/daily-view.png)

När du stänger av utgiftsprognosen visas inte planerade utgifter för framtida datum. När du tittar på kostnader för tidigare tidsperioder visar kostnadsprognosen inga kostnader.

Normalt kan du förvänta dig att se data eller meddelanden för förbrukade resurser inom åtta till tolv timmar.


**Gruppera efter** vanliga egenskaper för att dela upp kostnader och identifiera de flesta deltagarna. Om du till exempel grupperar efter resurstaggar väljer du den taggnyckel som du vill gruppera efter. Kostnaderna visas uppdelade efter varje taggvärde, med ett extra segment för resurser som inte har den taggen tillämpad.

De flesta [Azure-resurser stöder taggning](../../azure-resource-manager/management/tag-support.md). Vissa taggar är emellertid inte tillgängliga inom Cost Management (kostnadshantering) och fakturering. Dessutom stöds inte resursgrupptaggar. Stöd för taggar gäller för användning som rapporterats *efter* taggen tillämpades på resursen. Taggar tillämpas inte retroaktivt för insamlade kostnader.

Titta på videon [How to review tag policies with Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) (Så här granskar du taggprinciper med Azure Cost Management) för att lära dig mer om att använda Azures taggprincip för att förbättra synligheten för kostnadsdata.

Här är en vy över Azure-tjänstkostnaderna för den aktuella månaden.

![Grupperad daglig ackumulerad vy som visar exempel på kostnader för Azure-tjänster för den senaste månaden](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Som standard visar kostnadsanalys alla användnings- och inköpskostnader när de påförs och visas på din faktura. Det här kallas även **Faktisk kostnad**. Det är idealiskt att visa den faktiska kostnaden för att stämma av din faktura. Köptoppar i kostnad kan dock vara alarmerande när du håller ett öga på utgiftsavvikelser och andra kostnadsförändringar. Om du vill förenkla toppar som orsakas av kostnader för reservationsköp växlar du till **Amorterad kostnad**.

![Ändra mellan faktisk och amorterad kostnad för att se hur reservationsköp sprids ut över villkoret och allokeras till de resurser som använde reservationen](./media/quick-acm-cost-analysis/metric-picker.png)

Amorterad kostnad avbryter reservationsköp till dagliga segment och sprider dem över tid för reservationsperioden. I stället för att se ett inköp på 365 USD den 1 januari ser du ett inköp på 1,00 USD varje dag från 1 januari till 31 december. Utöver den grundläggande amorteringen omallokeras dessutom kostnaderna och associeras med hjälp av de speciella resurser som använde reservationen. Om till exempel den dagliga kostnaden 1,00 USD delades mellan två virtuella datorer skulle du se två debiteringar på 0,50 USD för dagen. Om en del av reservationen inte används för dagen ser du en kostnad på USD 0,50 som är kopplad till den aktuella virtuella datorn och en annan avgift på USD 0,50 med kostnadstypen `UnusedReservation`. Lägg märke till att oanvända reservationskostnader endast kan visas när du visar amorterade kostnader.

På grund av ändringen av hur kostnader visas är det viktigt att observera att vyerna för faktisk kostnad och amorterad kostnad visar olika totala siffror. I allmänhet minskar den totala kostnaden för månader med ett reservationsköp när du visar amorterade kostnader och månader efter att ett reservationsköp ökar. Amortering är bara tillgängligt för reservationsköp och gäller inte för Azure Marketplace-köp för tillfället.

På följande bild visas resursgruppnamn. Du kan gruppera efter tagg om du vill visa total kostnad per tagg eller använda vyn **Kostnad efter resurs** för att se alla taggar för en viss resurs.

![Fullständiga data för den aktuella vyn som visar namn på resursgrupper](./media/quick-acm-cost-analysis/full-data-set.png)

När du grupperar kostnader efter ett specifikt attribut visas de tio viktigaste kostnadsfaktorerna från högsta till lägsta. Om det finns fler än tio deltagare visas de nio viktigaste deltagarna plus gruppen **Others** (Övriga), där alla övriga grupper ingår. När du grupperar efter taggar visas en **Otaggad** grupp för kostnader som inte har taggnyckeln tillämpad. **Otaggad** är alltid är sist även om otaggade kostnader är högre än taggade kostnader. Om det finns minst 10 taggvärden ingår otaggade kostnader i **Others** (Övriga). Växla till tabellvyn och ändra kornighet till **Ingen** för att se alla värden i rangordning från högsta till lägsta kostnad.

Klassiska virtuella datorer, nätverk och lagringsresurser delar inte detaljerad faktureringsinformation. De slås samman som **klassiska tjänster** när kostnader grupperas.

Pivotdiagram under huvuddiagram visar olika grupperingar för att ge dig en bredare bild av de totala kostnaderna för den valda tidsperioden och filtren. Välj en egenskap eller en tagg för att visa samlade kostnader efter valfri dimension.

![Exempel som visar pivotdiagram](./media/quick-acm-cost-analysis/pivot-charts.png)

Du kan visa den fullständiga datauppsättningen för alla vyer. Oavsett vilka val och filter som du använder påverkar de data som visas. Om du vill se en fullständig uppsättning data väljer du listan **diagramtyp** och därefter **tabellvyn**.

![Data för aktuell vy i en tabellvy](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Förstå alternativ för gruppering och filtrering

I följande tabell visas några av de vanligaste alternativen för gruppering och filtrering och när du ska använda dem.

| Egenskap | När du ska använda detta | Anteckningar |
| --- | --- | --- |
| **Tillgänglighetszoner** | Bryt ned AWS-kostnader per tillgänglighetszon. | Gäller endast för AWS-omfång och -hanteringsgrupper. Azure data inkluderar inte tillgänglighetszon och visas som **Ej tillämpligt**. |
| **Faktureringsperiod** | Bryt ned PAYG-kostnader efter den månad som de (eller kommer att) fakturerades. | Använd **Faktureringsperiod** för att få en korrekt representation av fakturerade PAYG-avgifter. Inkludera 2 extra dagar före och efter faktureringsperioden om du filtrerar ned till ett anpassat datumintervall. Begränsning av de exakta datumen för faktureringsperioden stämmer inte överens med fakturan. Visar kostnader från alla fakturor under faktureringsperioden. Använd **Faktura-ID** för att filtrera ned till en speciell faktura. Endast tillgängligt för PAYG-prenumerationer eftersom EA och MCA faktureras per kalendermånad. EA/MCA-konton kan använda kalendermånader i datumväljaren eller månadsvis kornighet för att utföra samma mål. |
| **Kostnadstyp** | Dela upp användning, köp, återbetalning och outnyttjade reservationskostnader. | Köp och återbetalningar av reservationer är bara tillgängliga när du använder faktiska kostnader och inte när du använder amorterade kostnader. Oanvända reservationskostnader är bara tillgängliga när du tittar på amorterade kostnader. |
| **Avdelning** | Dela upp kostnader efter EA-avdelning. | Endast tillgängligt för EA- och hanteringsgrupper. PAYG-prenumerationer har ingen avdelning och visas som **Ej tillämpligt** eller **ej tilldelade**. |
| **Registreringskonto** | Dela upp kostnader efter EA-kontots ägare. | Endast tillgängligt för EA-faktureringskonton, avdelningar och hanteringsgrupper. PAYG-prenumerationer har inga EA-registreringskonton och visas som **Ej tillämpligt** eller **ej tilldelade**. |
| **Frekvens** | Bryt ned användningsbaserade, engångs- och återkommande kostnader. | |
| **Faktura-ID** | Dela upp kostnader per fakturerad faktura. | Ofakturerade avgifter har ännu inget faktura-ID och EA-kostnader inkluderar inte fakturainformation och visas som **Inte tillämpligt**.  |
| **Mätare** | Dela upp kostnader per användningsmätare. | Köp och Marketplace-användning visas som **Ej tillämpligt**. Se **Kostnadstyp** för att identifiera inköp och **Typ av utgivare** för att identifiera Marketplace-avgifter. |
| **Åtgärd** | Bryt ned AWS-kostnader per åtgärd. | Gäller endast för AWS-omfång och -hanteringsgrupper. Azure data inkluderar inte åtgärd och visas som **Ej tillämpligt** – använd **Mätare** i stället. |
| **Prismodell** | Dela upp kostnader per begäran, reservation eller dekor användning. | Köp visas som **OnDemand**. Om du ser **Inte tillämpligt**kan du gruppera efter **Reservation** för att avgöra om användningen är en reservation eller användning på begäran och **Kostnadstyp** för att identifiera köp.
| **Leverantör** | Dela upp kostnader per AWS och Azure. | Endast tillgängligt för hanteringsgrupper. |
| **Typ av utgivare** | Dela upp AWS-, Azure- och Marketplace-kostnader. |  |
| **Reservation** | Dela upp kostnader per reservation. | All användning eller alla inköp som inte är associerade med en reservation visas som **Inte tillämpligt**. Gruppera efter **Typ av utgivare** för att identifiera andra Azure-, AWS- eller Marketplace-köp. |
| **Resurs** | Dela upp kostnader per resurs. | Köp visas som **Inte tillämpligt** eftersom de används på ett EA-/PAYG-faktureringskonto eller en MCA-faktureringsprofilnivå och inte är associerade med en speciell resurs. Gruppera efter **Typ av utgivare** för att identifiera andra Azure-, AWS- eller Marketplace-köp. |
| **Resursgrupp** | Dela upp kostnader per resursgrupp. | Köp, klientresurser som inte är associerade med prenumerationer, prenumerationsresurser som inte har distribuerats till en resursgrupp och klassiska resurser som inte har någon resursgrupp visas som **övriga**, **klassiska tjänster**, **$system**eller **Inte tillämpligt**. |
| **Resurstyp** | Dela upp kostnader per resurstyp. | Inköps och klassiska tjänster har ingen Azure Resource Manager-resurstyp och visas som **andra**, **klassiska tjänster**eller **Inte tillämpligt**. |
| **Resursplats** | Dela upp kostnader efter plats eller region. | Köp- och Marketplace-användning kan visas som **ej tilldelad**, **okänd**, **omappad**eller **Inte tillämpligt**. |
| **Tjänstnamn** eller **Mätarkategori** | Dela upp kostnad per Azure-tjänst. | Köp och Marketplace-användning visas som **Inte tillämpligt** eller **Otilldelad**. |
| **Tjänstnivå** eller **Mätarunderkategori** | Dela upp kostnaden efter Azure-användningsmätarens underklassificering. | Köp och Marketplace-användning visas som **Inte tillämpligt** eller **Otilldelad**. |
| **Prenumeration** | Dela upp kostnader per Azure-prenumeration och AWS-länkat konto. | Köp- och klientresurser kan visas som **Ej tillämpligt**. |
| **Tag** | Dela upp kostnader efter taggvärden för en speciell taggnyckel. | Taggar är inte tillgängliga för köp, klientresurser som inte är associerade med prenumerationer, prenumerationsresurser som inte har distribuerats till en resursgrupp eller klassiska resurser. Observera att vissa tjänster inte innehåller taggar i användningsdata. Läs mer om [stöd för taggar för varje resurstyp](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Mer information om villkor finns i [Understand the terms used in the Azure usage and charges file](../understand/understand-usage.md) (Förstå villkoren som används i filen för Azure-användning och avgifter).


## <a name="saving-and-sharing-customized-views"></a>Spara och dela anpassade vyer

Spara och dela anpassade vyer med andra genom att fästa kostnadsanalys på Azure-portalens instrumentpanel eller genom att kopiera en länk till kostnadsanalys.

Titta på videon om att [dela och spara vyer i Azure Cost Management](https://www.youtube.com/watch?v=kQkXXj-SmvQ) och se hur du kan använda portalen för att dela kunskap om kostnadshantering med andra i organisationen.

>[!VIDEO https://www.youtube.com/embed/kQkXXj-SmvQ]

För att fästa kostnadsanalys väljer du Fäst-ikonen i det övre högra hörnet. När du fäster kostnadsanalys sparas endast huvuddiagram- eller tabellvyn. Dela instrumentpanelen för att ge andra åtkomst till panelen. Observera att detta endast delar konfigurationen på instrumentpanelen och ger inte andra åtkomst till underliggande data. Om du inte har tillgång till kostnader men har åtkomst till en delad instrumentpanel visas ett meddelande om ”nekad åtkomst”.

Om du vill dela en länk till kostnadsanalys väljer du **Dela** överst på bladet. En anpassad URL visas, som öppnar den specifika vyn för just det här omfånget. Om du inte har kostnadsåtkomst och ser den här URL:en visas meddelandet ”åtkomst nekad”.

Läs mer om hur du beviljar åtkomst till kostnader för varje omfång som stöds i [Förstå och arbeta med omfång](understand-work-scopes.md).



## <a name="automation-and-offline-analysis"></a>Automatisering och offlineanalys

Det finns tillfällen när du behöver hämta data för ytterligare analys, sammanfoga dem med dina egna data eller integrera dem i dina egna system. Cost Management erbjuder några olika alternativ. Som startpunkt, om du behöver en ad hoc-sammanfattning på hög nivå, t. ex. vad du får inom kostnadsanalys, skapar du den vy du behöver. Sedan kan du ladda ned den genom att välja **Exportera** och välja **Ladda ned rapport till CSV** eller **Ladda ned data till Excel**. Excel-nedladdningen ger ytterligare kontext för vyn du använde för att generera nedladdningen, som omfång, frågekonfiguration, summa och datum för generering.

Om du behöver den fullständiga, icke-aggregerade datauppsättningen kan du ladda ned den från faktureringskontot. I listan över tjänster i portalens vänstra navigeringsfönster går du till **Kostnadshantering och fakturering**. Välj ditt faktureringskonto, om det är tillämpligt. Gå till **Användning och avgifter**och välj sedan **nedladdningsikonen** för önskad faktureringsperiod.

Använd en liknande metod för att automatisera hanteringen av kostnadsdata. Använd [fråge-API](/rest/api/cost-management/query) för bättre analys med dynamisk filtrering, gruppering och aggregering, eller använd [UsageDetails-API:et](/rest/api/consumption/usageDetails) för den fullständiga, icke-aggregerade datauppsättningen. Den allmänna tillgänglighetsversionen för dessa API:er är 2019-01-01. Använd **2019-04-01-preview** för att få åtkomst till förhandsversionen av reservations- och Marketplace-köp inom dessa API:er.

Följande är till exempel en sammanställd vy över amorterade kostnader uppdelade efter kostnadstyp (användning, köp eller återbetalning), typ av utgivare (Azure eller Marketplace), resursgrupp (tom för inköp) och reservation (tom om ej tillämpligt).

```
POST https://management.azure.com/{scope}/providers/Microsoft.CostManagement/query?api-version=2019-04-01-preview
Content-Type: application/json

{
  "type": "AmortizedCost",
  "timeframe": "Custom",
  "timePeriod": { "from": "2019-04-01", "to": "2019-04-30" },
  "dataset": {
    "granularity": "None",
    "aggregation": {
      "totalCost": { "name": "PreTaxCost", "function": "Sum" }
    },
    "grouping": [
      { "type": "dimension", "name": "ChargeType" },
      { "type": "dimension", "name": "PublisherType" },
      { "type": "dimension", "name": "Frequency" },
      { "type": "dimension", "name": "ResourceGroup" },
      { "type": "dimension", "name": "SubscriptionName" },
      { "type": "dimension", "name": "SubscriptionId" },
      { "type": "dimension", "name": "ReservationName" },
      { "type": "dimension", "name": "ReservationId" },
    ]
  },
}
```

Och om du inte behöver aggregeringen och föredrar den fullständiga rådatauppsättningen:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Om du behöver de faktiska kostnaderna för att visa inköp när de påförs kan du ändra **typ**/**mått** till **ActualCost**. Mer information om dessa API:er finns i API-dokumentationen [Fråga](/rest/api/cost-management/query) och [UsageDetails](/rest/api/consumption/usageDetails). Observera att de publicerade dokumenten är till för GA-versionen. De båda fungerar dock på samma sätt för API-versionen *2019-04-01-preview* utanför det nya typ-/måttattributet och ändrade egenskapsnamn. (Läs mer om egenskapsnamnen nedan.)

API:er för Cost Management fungerar över alla omfång över resurser: resursgrupp, prenumeration och hanteringsgrupp via Azure RBAC-åtkomst, EA-faktureringskonton (registreringar), avdelningar och registreringskonton via åtkomst till EA-portalen. Lär dig mer om omfång, till exempel hur du fastställer ditt omfångs-ID eller hanterar åtkomst, i [Förstå och arbeta med omfång](understand-work-scopes.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till den första självstudien om du vill lära dig att skapa och hantera budgetar.

> [!div class="nextstepaction"]
> [Skapa och hantera budgetar](tutorial-acm-create-budgets.md)
