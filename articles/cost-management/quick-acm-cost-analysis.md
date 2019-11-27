---
title: Snabb start – utforska Azure-kostnader med kostnads analys | Microsoft Docs
description: Den här snabbstarten hjälper dig att använda kostnadsanalys för att utforska och analysera dina Azure-organisationskostnader.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: cost-management-billing
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: 858672a7fcfcfd5f50c91eb769fdd0d42578195f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229999"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Snabb start: utforska och analysera kostnader med kostnads analys

För att kunna kontrollera och optimera dina Azure-kostnader på rätt sätt behöver du förstå var kostnaderna har sitt ursprung i organisationen. Det är också användbart att veta hur mycket pengar dina tjänster kostar och vilka som har stöd för vilka miljöer och system. Insyn i hela spektrumet av kostnader är mycket viktigt för kunna förstå organisationens utgiftsmönster. Du kan använda utgifts mönster för att tillämpa mekanismer för styrning av kostnader, till exempel budgetar.

I den här snabbstarten använder du kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader sker över tid och för att identifiera utgiftstrender. Du kan se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget. En budget gör det lättare att hålla sig till ekonomiska begränsningar. En budget används även till att visa dagliga och månatliga kostnader för att isolera oregelbundenheter vad gäller utgifter. Dessutom kan du ladda ned den aktuella rapportens data för ytterligare analys eller för användning i ett externt system.

I den här snabbstarten lär du dig att:

- Granska kostnader i kostnadsanalys
- Anpassa kostnadsvyer
- Ladda ned kostnadsanalysdata


## <a name="prerequisites"></a>Krav

Kostnads analys stöder olika typer av typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto.

Information om hur du tilldelar åtkomst till Azure Cost Management data finns i [tilldela åtkomst till data](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Granska kostnader i kostnadsanalys

Om du vill granska kostnaderna i kostnads analyser öppnar du omfånget i Azure Portal och väljer **kostnads analys** i menyn. Gå till exempel till **prenumerationer**, Välj en prenumeration i listan och välj sedan **kostnads analys** i menyn. Använd reglaget **Omfång** för att växla till ett annat omfång i kostnadsanalysen. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

Det omfång du väljer används i hela Cost Management för att tillhandahålla data konsolidering och kontrol lera åtkomsten till kostnads information. När du använder omfång så använder du inte flerval för dem. I stället väljer du ett större omfång, som andra samlar in till och filtrerar sedan ned till de kapslade omfattningar som du behöver. Den här metoden är viktig att förstå eftersom vissa personer kanske inte har åtkomst till en enda överordnad omfattning, som täcker flera kapslade områden.

Vyn inledande kostnads analys innehåller följande områden.

**Ackumulerad kostnadsallokering**: representerar konfigurationen för den fördefinierade vyn för kostnads analys. Varje vy innehåller inställningar för datum intervall, granularitet, gruppera efter och filter. Standardvyn visar ackumulerade kostnader för den aktuella fakturerings perioden, men du kan ändra till andra inbyggda vyer. Mer information finns i [Anpassa Cost views](#customize-cost-views).

**Verklig kostnad**: visar den totala förbrukningen och inköps kostnaden för den aktuella månaden, allteftersom de påförs och visas på fakturan.

**Prognos**: visar den totala beräknade kostnaden för den tids period som du väljer. (Prognosen är i för hands version.)

**Budget**: visar den planerade utgifts gränsen för det valda omfånget, om det är tillgängligt.

**Ackumulerad granularitet**: visar total summan för dagliga kostnader, från början av fakturerings perioden. När du har [skapat en budget](tutorial-acm-create-budgets.md) för ditt faktureringskonto eller din prenumeration kan du snabbt se din utgiftstrend jämfört med budgeten. Hovra över ett datum om du vill visa den ackumulerade kostnaden för den dagen.

**Pivot-diagram (Ring diagram)** : ge dynamiska Pivot-scheman och dela upp den totala kostnaden med en gemensam uppsättning standard egenskaper. De visar de största till minsta kostnaderna för den aktuella månaden. Du kan ändra pivotdiagram när som helst genom att välja en annan pivot. Kostnaderna kategoriseras efter tjänst (mätar kategori), plats (region) och underordnad omfattning som standard. Registrerings konton är till exempel under fakturerings konton, resurs grupper under prenumerationer och resurser under resurs grupper.

![Startvyn för kostnadsanalys på Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Anpassa kostnadsvyer

Kostnads analys har fyra inbyggda vyer som är optimerade för de vanligaste målen:

Visa | Besvara frågor som
--- | ---
Ackumulerad kostnad | Hur mycket har jag använt hittills den här månaden? Följer jag budgeten?
Daglig kostnad | Har det funnits några ökade kostnader per dag under de senaste 30 dagarna?
Kostnad per tjänst | Hur skiljer sig min månatliga användning över de tre senaste fakturorna?
Kostnad efter resurs | Vilka resurser kostar mest hittills den här månaden?

![Visa väljare som visar ett exempel val för den här månaden](./media/quick-acm-cost-analysis/view-selector.png)

Det finns dock många fall där du behöver djupare analys. Anpassning startar överst på sidan med valet av datum.

Kostnadsanalys visar data för den aktuella månaden som standard. Använd datum väljaren för att snabbt växla till vanliga datum intervall. Exempel omfattar de senaste sju dagarna, den senaste månaden, det aktuella året eller ett anpassat datum intervall. Prenumerationer enligt principen betala per användning omfattar även datum intervall baserat på din fakturerings period, som inte är kopplat till kalender månaden, till exempel den aktuella fakturerings perioden eller senaste fakturan. Använd **< föregående** och **Nästa >** länkar överst på menyn för att hoppa till föregående eller nästa period. **< föregående** växlar till exempel från de **senaste 7 dagarna** till **8-14 dagar sedan** eller **15-21 dagar sedan**.

![Datumväljare som visar ett exempelurval för den här månaden](./media/quick-acm-cost-analysis/date-selector.png)

Kostnadsanalysen visar **ackumulerade** kostnader som standard. De ackumulerade kostnaderna omfattar alla kostnader för varje dag plus föregående dagar, för en ständigt växande vy av dina dagliga sammanlagda kostnader. Den här vyn är optimerad för att visa hur du trendar mot en budget för det valda tidsintervallet.

Använd prognos diagram vyn för att identifiera potentiella budget överträdelser. När det finns en potentiell budget överträdelse visas projekt överförbrukningen i rött. En indikator symbol visas också i diagrammet. Om du hovrar över symbolen visas det beräknade datumet för budget brottet.

![Exempel som visar potentiell budget överträdelse](./media/quick-acm-cost-analysis/budget-breach.png)

Det finns även den **dagsvyn**, som visar kostnaderna för varje dag. Dagsvyn visar inte någon tillväxttrend. Vyn har utformats för att visa oregelbundenheter såsom toppar eller dalar i utgifter från dag till dag. Om du har valt en budget visas även en uppskattning av din dagliga budget i dagsvyn.

När dina dagliga kostnader är konsekventa över den uppskattade dagliga budgeten kan du behöva överskrida din månads budget. Den uppskattade dagliga budgeten är ett sätt att hjälpa dig att visualisera budgeten på en lägre nivå. När det förekommer variationer i de dagliga kostnaderna är jämförelsen mellan den uppskattade dagliga budgeten och den månatliga budgeten mindre exakt.

Här är en daglig vy över de senaste utgifterna med utgifts prognos som är aktive rad.
![daglig vy som visar exempel dagliga kostnader för den aktuella månaden](./media/quick-acm-cost-analysis/daily-view.png)

När du stänger av utgifts prognosen visas inte planerade utgifter för framtida datum. När du tittar på kostnader för tidigare tids perioder visar kostnads prognosen inga kostnader.

I allmänhet kan du vänta på att se data eller meddelanden för förbrukade resurser inom 8 till 12 timmar.


**Gruppera efter** gemensamma egenskaper för att dela upp kostnader och identifiera de flesta bidrags givare. Om du till exempel vill gruppera efter resurs Taggar väljer du den etikett nyckel som du vill gruppera efter. Kostnaderna uppdelas efter varje tagg värde, med ett extra segment för resurser som inte har taggen tillämpad.

De flesta [Azure-resurser har stöd för taggning](../azure-resource-manager/tag-support.md). Vissa taggar är dock inte tillgängliga i Cost Management och fakturering. Dessutom stöds inte resursgrupptaggar. Cost Management stöder endast resurstaggar från det datum då taggarna tillämpas direkt på resursen. Se [hur du kan läsa tag-principer med Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) video för att lära dig mer om att använda Azure tag-principer för att förbättra kostnads data synlighet.

Här är en vy över Azure service-kostnader för den aktuella månaden.

![Grupperad daglig ackumulerad vy som visar exempel på kostnader för Azure-tjänster för den senaste månaden](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Som standard visar kostnads analys alla användnings-och inköps kostnader när de påförs och visas på din faktura, även kallat **verklig kostnad**. Det är idealiskt att visa den faktiska kostnaden för att stämma av din faktura. Köp toppar i kostnad kan dock vara Alarme när du håller ett öga på utgifts avvikelser och andra förändringar i kostnad. Om du vill förenkla utökningar som orsakas av reservations inköps kostnader byter du till **periodiserad kostnad**.

![Ändra mellan faktisk och periodiserad kostnad för att se uppslag av reservationer på villkoret och allokeras till de resurser som använde reservationen](./media/quick-acm-cost-analysis/metric-picker.png)

Periodiserad kostnad avbryter reservations köp till dagliga segment och sprider dem under tiden för reservations perioden. I stället för att se ett $365-inköp den 1 januari ser du ett $1 inköp varje dag från 1 januari till 31 december. Utöver den grundläggande amorteringen omallokeras dessutom dessa kostnader och associeras med hjälp av de speciella resurser som använde reservationen. Om till exempel den $1 dagliga avgiften delades mellan två virtuella datorer skulle du se $2 0,50-debitering för dagen. Om en del av reservationen inte används för dagen ser du $1 0,50-avgiften som är kopplad till den aktuella virtuella datorn och en annan $0,50-avgift med en avgifts typ `UnusedReservation`. Observera att oanvända reservations kostnader bara kan visas när du visar den periodiserade kostnaden.

På grund av ändringen i hur kostnader visas, är det viktigt att Observera att vyerna verklig kostnad och förfallna kostnad visar olika totala siffror. I allmänhet minskar den totala kostnaden för månader med ett reservations köp när du visar periodiserade kostnader och månader efter att ett reservations köp ökar. Amortering är bara tillgängligt för reservations köp och gäller inte för Azure Marketplace-köp för tillfället.

Följande bild visar resurs grupp namn. Du kan gruppera efter tagg om du vill visa total kostnad per tagg eller använda vyn **kostnad per resurs** för att se alla Taggar för en viss resurs.

![Fullständiga data för den aktuella vyn som visar namn på resursgrupper](./media/quick-acm-cost-analysis/full-data-set.png)

När du grupperar kostnader med ett speciellt attribut visas de 10 främsta kostnads bidrags deltagarna från högsta till lägsta. Om det finns fler än 10 visas de nio främsta kostnads bidragen med en **annan** grupp som representerar alla återstående grupper tillsammans. När du grupperar efter taggar, visas en **otaggade** grupp för kostnader som inte har tag-nyckeln applicerad. **Otaggade** är alltid sist, även om otaggade kostnader är högre än märkta kostnader. Otaggade kostnader kommer att ingå i **andra**, om 10 eller fler märkes värden finns. Växla till vyn tabell och ändra granularitet till **ingen** om du vill se alla värden rangordnade från högsta till lägsta kostnad.

Klassiska virtuella datorer, nätverk och lagrings resurser delar inte detaljerade fakturerings data. De slås samman som **klassiska tjänster** när kostnader grupperas.

Pivotera diagram under huvud diagrammet visar olika grupperingar, vilket ger dig en bredare bild av dina totala kostnader för den valda tids perioden och filter. Välj en egenskap eller en tagg för att visa samlade kostnader efter valfri dimension.

![Exempel som visar pivot-diagram](./media/quick-acm-cost-analysis/pivot-charts.png)

Du kan Visa fullständig data uppsättning för alla vyer. De val eller filter som du tillämpar påverkar de data som visas. Om du vill se en fullständig data uppsättning väljer du listan **diagram typ** och väljer sedan **tabellvy** .

![Data för aktuell vy i en tabellvy](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Förstå grupperings-och filtrerings alternativ

I följande tabell visas några av de vanligaste grupperings-och filtrerings alternativen och när du ska använda dem.

| Egenskap | När du ska använda detta |
| --- | --- |
| **Fakturerings period** | Dela upp kostnader per faktura månad. Det här alternativet är viktigt för att betala per användning och utveckling/test-prenumerationer som inte är kopplade till kalender månader. EA/MCA-konton kan använda Kalender månader i datum väljaren eller månads kornig het för att utföra samma mål. |
| **Avgifts typ** | Bryt ned användningen, Köp, åter betalning och outnyttjade reservations kostnader. Köp och åter betalningar av reservationer är bara tillgängliga när du använder åtgärds kostnader och inte när du använder periodiserade kostnader. Oanvända reservations kostnader är bara tillgängliga när du tittar på periodiserade kostnader. |
| **Moln** | Dela upp kostnader med AWS och Azure. AWS-kostnader är bara tillgängliga från hanterings grupper, externa fakturerings konton och externa prenumerationer. |
| Avsnittet **avdelnings** / **faktura** | Dela upp kostnader efter EA-eller MCA-faktura-avsnittet. Det här alternativet är endast tillgängligt för EA/MCA-fakturerings konton och MCA-fakturerings profiler. |
| **Registrerings konto** | Dela upp kostnader efter EA-kontots ägare. Det här alternativet är endast tillgängligt för EA-fakturerings konton och-avdelningar. |
| **Frekvens** | Bryt ned användnings-baserade, engångs-och återkommande kostnader. |
| **Mätare** | Dela upp kostnader per användnings mätare i Azure. Det här alternativet är endast tillgängligt för Azure-användning. Alla inköp och Marketplace-användning visas som **ej specificerade** eller **otilldelade**. |
| **Utgivar typ** | Bryt ned AWS-, Azure-och Marketplace-kostnader. |
| **Reservation** | Dela upp kostnader per reservation. All användning som inte inkluderar en reservation visas som **Ej angiven**. |
| **Resurs** | Dela upp kostnader per resurs. Alla inköp visas som **ej specificerade**eftersom de används på ett EA-/PAYG fakturerings konto eller en MCA-fakturerings profil nivå.  |
| **Resursgrupp** | Dela upp kostnader per resurs grupp. Det här alternativet är endast tillgängligt för icke-klassisk användning. Klassisk Resursanvändning visas som **övrigt**och inköp visas som **ej angivet**. |
| **Resurstyp** | Dela upp kostnader efter resurs typ. Det här alternativet är endast tillgängligt för icke-klassisk användning. Klassisk Resursanvändning visas som **övrigt**och inköp visas som **ej angivet**. |
| **Tjänst namn** eller **mätnings kategori** | Dela upp kostnad per Azure-tjänst. Det här alternativet är endast tillgängligt för Azure-användning. Alla inköp och Marketplace-användning visas som **ej specificerade** eller **otilldelade**. |
| **Tjänste nivå** eller **mätar under kategori** | Dela upp kostnaden med under klassificering av Azure-förbruknings mätare. Det här alternativet är endast tillgängligt för Azure-användning. Alla inköp och Marketplace-användning visas som **ej specificerade** eller **otilldelade**. |
| **Prenumeration** | Dela upp kostnader per prenumeration. Alla inköp visas som **ej specificerade**. |
| **Tag** | Dela upp kostnader efter tagg värden för en speciell tagg nyckel. |

Mer information om villkor finns i [förstå de termer som används i filen för Azure-användning och-debitering](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Spara och dela anpassade vyer

Spara och dela anpassade vyer med andra genom att fästa kostnads analys på Azure Portal instrument panelen eller genom att kopiera en länk till kostnads analys.

För att fästa kostnads analys väljer du ikonen fäst i det övre högra hörnet. När du fäster kostnads analys sparas endast huvud diagram-eller tabellvy. Dela instrument panelen för att ge andra åtkomst till panelen. Observera att detta endast delar konfigurationen på instrument panelen och ger inte andra åtkomst till underliggande data. Om du inte har till gång till kostnader men har åtkomst till en delad instrument panel visas ett meddelande om "åtkomst nekad".

Om du vill dela en länk till kostnads analys väljer du **dela** överst på bladet. En anpassad URL visas, som öppnar den här vyn för det aktuella omfånget. Om du inte har kostnads åtkomst och får den här URL: en visas meddelandet "åtkomst nekad".

Läs mer om hur du beviljar åtkomst till kostnader för varje omfattning som stöds genom att läsa [förstå och arbeta med omfattningar](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatisering och offline-analys

Det finns tillfällen när du behöver hämta data för ytterligare analys, sammanfoga dem med dina egna data eller integrera dem i dina egna system. Cost Management erbjuder några olika alternativ. Som start punkt, om du behöver en ad hoc-sammanfattning på hög nivå, t. ex. vad du får inom kostnads analys, bygger du den vy du behöver. Sedan kan du hämta den genom att välja **Exportera** och välja **Hämta data till CSV** eller **Hämta data till Excel**. Excel-nedladdningen ger ytterligare kontext för den vy som du använde för att generera hämtningen, t. ex. omfång, frågeinställningar, total och datum som genereras.

Om du behöver den fullständiga, icke-aggregerade data uppsättningen kan du ladda ned den från fakturerings kontot. Sedan går du till **Cost Management + fakturering**från listan över tjänster i portalens vänstra navigerings fönster. Välj ditt fakturerings konto, om det är tillämpligt. Gå till **förbrukning + kostnader**och välj sedan **nedladdnings** ikonen för önskad fakturerings period.

Använd en liknande metod för att automatisera hanteringen av kostnads data. Använd [fråge-API](/rest/api/cost-management/query) för bättre analys med dynamisk filtrering, gruppering och agg regering, eller Använd UsageDetails- [API: et](/rest/api/consumption/usageDetails) för den fullständiga, icke-aggregerade data uppsättningen. Den allmänna tillgänglighets versionen för dessa API: er är 2019-01-01. Använd **2019-04-01 – för hands version** för att få åtkomst till för hands versionen av reservations-och Marketplace-köp inom dessa API: er.

Följande är till exempel en sammanställd vy över periodiserade kostnader uppdelade efter debiterings typ (användning, inköp eller åter betalning), utgivar typ (Azure eller Marketplace), resurs grupp (tom för inköp) och reservation (tom om ej tillämpligt).

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

Och om du inte behöver agg regeringen och föredrar fullständig data uppsättning för RAW:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Om du behöver faktiska kostnader för att Visa inköp när de påförs, ändra **typ**/**mått** till **ActualCost**. Mer information om dessa API: er finns i [fråge](/rest/api/cost-management/query) -och [UsageDetails](/rest/api/consumption/usageDetails) API-dokumentationen. Observera att de publicerade dokumenten är till för GA-versionen. De båda fungerar dock på samma sätt för *2019-04-01-för hands* versions-API-versionen utanför det nya typ/metriska attributet och ändrade egenskaps namn. (Läs mer om egenskaps namnen nedan.)

Cost Management-API: er fungerar över alla omfattningar över resurser: resurs grupp, prenumeration och hanterings grupp via Azure RBAC-åtkomst, EA-fakturerings konton (registreringar), avdelningar och registrerings konton via EA Portal åtkomst. Lär dig mer om omfattningar, inklusive hur du fastställer ditt scope-ID eller hantera åtkomst, i [förstå och arbeta med omfattningar](understand-work-scopes.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till den första självstudien om du vill lära dig att skapa och hantera budgetar.

> [!div class="nextstepaction"]
> [Skapa och hantera budgetar](tutorial-acm-create-budgets.md)
