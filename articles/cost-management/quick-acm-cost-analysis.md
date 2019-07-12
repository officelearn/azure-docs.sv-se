---
title: Snabbstart – utforska Azure-kostnader med kostnadsanalys | Microsoft Docs
description: Den här snabbstarten hjälper dig att använda kostnadsanalys för att utforska och analysera dina Azure-organisationskostnader.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: d07a52a3cd07c69b9b7e2ef43331ddd9fb455b10
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827819"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Snabbstart: Utforska och analysera kostnaderna med kostnadsanalys

För att kunna kontrollera och optimera dina Azure-kostnader på rätt sätt behöver du förstå var kostnaderna har sitt ursprung i organisationen. Det är också bra att veta hur mycket pengar dina tjänster kostnad, och stöd för vilka miljöer och system. Insyn i hela spektrumet av kostnader är mycket viktigt för kunna förstå organisationens utgiftsmönster. Du kan använda utgiftsgränsen mönster för att genomdriva kostnaden kontrollmekanismer som din budget.

I den här snabbstarten använder du kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader sker över tid och för att identifiera utgiftstrender. Du kan se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget. En budget gör det lättare att hålla sig till ekonomiska begränsningar. En budget används även till att visa dagliga och månatliga kostnader för att isolera oregelbundenheter vad gäller utgifter. Dessutom kan du ladda ned den aktuella rapportens data för ytterligare analys eller för användning i ett externt system.

I den här snabbstarten lär du dig att:

- Granska kostnader i kostnadsanalys
- Anpassa kostnadsvyer
- Ladda ned kostnadsanalysdata


## <a name="prerequisites"></a>Förutsättningar

Kostnadsanalys stöder olika typer av Azure kontotyper. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto.

För [EA-kunder (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/) måste du minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata.

- Faktureringskonto
- Avdelning
- Registreringskonto
- Hanteringsgrupp
- Subscription
- Resource group

Läs mer om att tilldela åtkomst till data i Azure Cost Management [tilldela åtkomst till data](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Granska kostnader i kostnadsanalys

Öppna området i Azure-portalen och välj om du vill granska dina kostnader i cost analysis **analys av kostnader** på menyn. Till exempel Gå till **prenumerationer**, Välj en prenumeration i listan och välj sedan **analys av kostnader** på menyn. Använd den **omfång** pill att växla till ett annat omfång i kostnadsanalys. Mer information om scope finns [förstå och arbeta med omfattningar](understand-work-scopes.md).

Omfång som du väljer används i hela Cost Management för att tillhandahålla data konsolidering och kontrollera åtkomsten till kostnadsinformation. När du använder omfång så använder du inte flerval för dem. I stället du väljer ett större område som andra summera, och sedan filtrera ner till kapslade omfång som du behöver. Den här metoden är viktigt att förstå eftersom vissa personer inte kan ha åtkomst till en enda överordnad omfattning, som omfattar flera kapslade omfång.

Initial kostnad analysvy innehåller följande områden.

**Ackumulerade kostnaden visa**: Representerar fördefinierade cost analysis visa konfiguration. Varje vy innehåller datumintervall, kornighet, gruppera efter och filtrera inställningar. Standardvyn visar ackumulerade kostnaderna för den aktuella faktureringsperioden, men du kan ändra till andra inbyggda vyer. Mer information finns i [anpassa kostnad vyer](#customize-cost-views).

**Verklig kostnad**: Visar de totala kostnaderna för användning och köp för den aktuella månaden, eftersom de är ackumuleras och visas på fakturan.

**Skapa prognoser för**: Visar de totala prognostiserade kostnaderna för tidsperioden som du väljer. (Prognos är i förhandsversion.)

**Budget**: Visar planerade utgiftsgränsen för det valda omfånget, om det är tillgängligt.

**Ackumulerade kornighet**: Visar de totala sammanställda dagliga kostnaderna från början av faktureringsperioden. När du har [skapat en budget](tutorial-acm-create-budgets.md) för ditt faktureringskonto eller din prenumeration kan du snabbt se din utgiftstrend jämfört med budgeten. Hovra över ett datum om du vill visa den ackumulerade kostnaden för den dagen.

**Pivotera (ringdiagram)** : Ange dynamiska pivoteringsmöjligheter, bryta ned den totala kostnaden med en gemensam uppsättning standardegenskaper. De visar den största till minsta kostnaderna för den aktuella månaden. Du kan ändra pivotdiagram när som helst genom att välja en annan pivot. Kostnaderna kategoriseras efter service (mätningskategori), plats (region) och underordnade omfång som standard. Till exempel registreringskonton finns under fakturering konton, resursgrupper är under prenumerationerna och resurserna är under resursgrupper.

![Startvyn för kostnadsanalys på Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Anpassa kostnadsvyer

Kostnadsanalys har fyra inbyggda vyer, optimerat för de flesta vanliga mål:

Visa | Besvara frågor som
--- | ---
Ackumulerade kostnaden | Hur mycket under har hittills den här månaden? Följer jag budgeten?
Kostnad per dag | Har det varit höjs kostnader per dag under de senaste 30 dagarna?
Kostnad per tjänst | Hur har Mina månatlig användning som kan variera under de senaste tre fakturorna?
Kostnad per resurs | Vilka resurser kosta mest hittills den här månaden?

![Vyväljare som visar ett exempel val för den här månaden](./media/quick-acm-cost-analysis/view-selector.png)

Det finns dock många fall där du behöver djupare analys. Anpassning startar överst på sidan med valet av datum.

Kostnadsanalys visar data för den aktuella månaden som standard. Använd Väljaren datum för att växla till vanliga datumintervall snabbt. Exempel är de senaste sju dagarna, den senaste månaden, det aktuella året eller ett eget datumintervall. Prenumerationer med användningsbaserad betalning även innehålla datumintervall baserat på din faktureringsperiod, som inte är bunden till kalendermånad, som den aktuella faktureringsperioden eller senaste fakturan. Använd den **< föregående** och **Nästa >** länkar överst i menyn för att hoppa till föregående eller nästa period, respektive. Till exempel **< föregående** växlar från den **senaste 7 dagarna** till **8 – 14 dagar sedan** eller **15-21 dagar sedan**.

![Datumväljare som visar ett exempelurval för den här månaden](./media/quick-acm-cost-analysis/date-selector.png)

Kostnadsanalysen visar **ackumulerade** kostnader som standard. Ackumulerade kostnaderna omfattar alla kostnader för varje dag plus de senaste dagarna, för en ständigt växande vy över dina dagliga sammanställd kostnader. Den här vyn är optimerad för att visa hur du trendar mot en budget för det valda tidsintervallet.

Använd prognoser diagramvy för att identifiera potentiella överträdelser av budget. Om det finns potentiella budget intrång, visas planerade intressenterna i rött. En symbolen visas också i diagrammet. Hovra över symbolen visar datumet då budget överträdelser.

![Exempel som visar potentiella budget intrång](./media/quick-acm-cost-analysis/budget-breach.png)

Det finns även den **dagsvyn**, som visar kostnaderna för varje dag. Dagsvyn visar inte någon tillväxttrend. Vyn har utformats för att visa oregelbundenheter såsom toppar eller dalar i utgifter från dag till dag. Om du har valt en budget, visar dagsvyn också en uppskattning av din dagliga budget.

När dina dagliga kostnader är konsekvent över beräknade dagliga budget, kan du förväntar dig ska du överskrider din månatliga budget. Den beräknade dagliga budgeten är ett sätt för att visualisera din budget på lägre nivå. När det förekommer variationer i de dagliga kostnaderna är jämförelsen mellan den uppskattade dagliga budgeten och den månatliga budgeten mindre exakt.

Här är en daglig vy över de senaste utgifter i utgifter Prognostisera aktiveras.
![Dagliga vyn visar exempel dagliga kostnader för den aktuella månaden](./media/quick-acm-cost-analysis/daily-view.png)

När stänger av den utgifter prognoser kan du se inte beräknade utgifter för framtida datum. När du tittar på kostnaderna för senaste tidsperioder visar inte också kostnaden prognosen kostnaderna.

I allmänhet bör du kan förvänta dig att se data eller meddelanden för förbrukade resurser inom 8 och 12 timmar.


**Gruppera efter** gemensamma egenskaper att bryta ned kostnader och identifiera de främsta deltagare. Om du vill gruppera efter resurstaggar, t.ex, välja taggnyckeln som du vill gruppera efter. Kostnaderna är uppdelade efter varje tagg-värde med ett extra segment för resurser som inte har den tagg som tillämpas.

De flesta [Azure-resurser stöd för taggning](../azure-resource-manager/tag-support.md). Men vissa taggar är inte tillgängliga i kostnadshantering och fakturering. Dessutom stöds inte resursgrupptaggar. Cost Management stöder endast resurstaggar från det datum då taggarna tillämpas direkt på resursen. Titta på den [granska taggprinciper med Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) video och lär dig använda Azure taggen principer för att förbättra synligheten för kostnad för data.

Här är en vy över kostnaderna för Azure-tjänsten för den aktuella månaden.

![Grupperad daglig ackumulerad vy som visar exempel på kostnader för Azure-tjänster för den senaste månaden](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Som standard visas alla kostnader för användning och köp i kostnadsanalys ackumuleras och visas på din faktura, även kallat **verklig kostnad**. Visa verklig kostnad är perfekt för att synkronisera fakturan. Köp toppar i kostnad kan dock vara oroväckande när du håller ett öga för utgifter avvikelser och andra ändringar i kostnad. Om du vill förenkla ut toppar som orsakas av kostnader för köp av reservation, växla till **Amorterad kostnad**. 

![Ändra mellan faktiska och amorterade kostnaden för att se reservation köp sprids över termen och är allokerad till de resurser som används för reservationen](./media/quick-acm-cost-analysis/metric-picker.png)

Amorterad kostnad eliminerar reservation inköp i dagliga segment och sprider ut dem under reservationsperioden. Till exempel i stället för ett $-365 köpa den 1 januari, visas ett $-1 köpa varje dag från den 1 januari till 31 December. Förutom grundläggande amorteringen dessa kostnader också tilldelas på nytt och som är associerade med hjälp av de enskilda resurser som används för reservationen. Till exempel om den daglig avgiften 1 USD delades mellan två virtuella datorer, visas två 0,50 avgifter för dagen. Om en del av reservationen inte är används för dagen, ser du en 0,50 debiterar som är associerade med den tillämpliga virtuella datorn och en annan 0,50 betalt med en avgift typ av `UnusedReservation`. Observera att oanvända reserverade kostnader visas endast när visning amorterad kostnad.

Det är viktigt att Observera att verklig kostnad och amorterad kostnad vyer visar olika Totalt antal på grund av ändring i hur kostnader representeras. I allmänhet den totala kostnaden för månader med en reservationsköp sänks när du visar amorterade kostnader och ökar månader efter en reservationsköp. Amorteringen är bara tillgängligt för köp av reservation och gäller inte för Azure Marketplace-köp just nu.

Följande bild visar resource gruppnamn. Du kan gruppera efter-taggen för att visa totala kostnader per tagg eller använda den **kostnad per resurs** vy för att se alla taggar för en viss resurs.

![Fullständiga data för den aktuella vyn som visar namn på resursgrupper](./media/quick-acm-cost-analysis/full-data-set.png)

När du grupperar kostnader genom att ett specifikt attribut, visas de översta 10 kostnadsfaktorer från högsta till lägsta. Om det finns fler än 10, kostnadsfaktorer upp nio visas i en **andra** grupp. Den här gruppen innehåller alla återstående grupperna tillsammans. När du grupperar efter taggar, en **Untagged** gruppen visas för kostnader som inte har taggnyckeln tillämpas. **Ej taggade** alltid är den sista även om otaggade kostnader är högre än taggade kostnaderna. Otaggade kostnader kommer att ingå i **andra**, om det finns minst 10 taggvärden.

Dela inte detaljerad faktureringsinformation klassiska virtuella datorer, nätverk och lagringsresurser. De slås samman som **klassiska tjänster** när kostnader grupperas.

Pivot-diagram under de huvuddiagram visa olika grupperingar, vilket ger dig en bredare överblick över de totala kostnaderna för den valda tidsperioden och filter. Välj en egenskap eller en tagg för att visa samlade kostnader efter valfri dimension.

![Exempel som visar pivot-diagram](./media/quick-acm-cost-analysis/pivot-charts.png)

Du kan visa hela datauppsättningen för alla vyer. Oavsett vilket val eller filter som du använder påverkar data visas. Om du vill se hela datauppsättningen, Välj den **diagramtyp** listan och välj sedan **tabell** vy.

![Data för aktuell vy i en tabellvy](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Förstå gruppering och filtrering alternativ

I följande tabell visas några av de vanligaste gruppering och filtrering alternativ och när de ska använda.

| Egenskap | När du ska använda detta |
| --- | --- |
| **Faktureringsperiod** | Dela upp kostnader per Fakturamånad. Det här alternativet är viktigt för betala per användning och utveckling/test-prenumerationer, som inte är bundna till kalendermånaderna. EA/MCA konton kan använda kalendermånaderna i datumväljare eller månatliga kornighet för att utföra samma mål. |
| **Tilläggstyp** | Dela upp användning, köp, återbetalning och oanvända reserverade kostnader. Köp av reservation och återbetalningar finns endast när du använder åtgärden kostnader och inte när du använder amorterade kostnader. Oanvända reserverade kostnader är tillgängliga när du tittar på amorterade kostnader. |
| **Molnet** | Dela upp kostnader efter AWS och Azure. AWS-kostnaderna är tillgängliga från hanteringsgrupper, externa konton för fakturering och externa prenumerationer. |
| **Avdelning** / **faktura avsnittet** | Dela upp kostnader efter EA-avdelningen eller MCA faktura avsnittet. Det här alternativet är endast tillgängligt för EA/MCA fakturering konton och MCA fakturering profiler. |
| **Konto för enhetsregistreringshanterare** | Dela upp kostnader efter EA-Kontoägare. Det här alternativet är endast tillgängligt för EA fakturering konton och avdelningar. |
| **Frekvens** | Dela upp användningsbaserad gång och återkommande kostnaderna. |
| **Meter** | Dela upp kostnader med Azure-användningsmätaren. Det här alternativet är endast tillgängligt för Azure-användning. Alla inköp och användning av Marketplace visas som **angetts** eller **otilldelade**. |
| **Publisher-typ** | Dela upp kostnader för AWS och Azure Marketplace. |
| **Reservation** | Dela upp kostnader genom att reservationen. All användning som inte innehåller en reservation visas som **angetts**. |
| **Resurs** | Dela upp kostnader efter resurs. Alla inköp visas som **angetts**, eftersom de är tillämpas på ett EA/PAYG faktureringskonto eller MCA fakturering profilnivå.  |
| **Resursgrupp** | Dela upp kostnader per resursgrupp. Det här alternativet är endast tillgänglig för användning av icke-klassisk. Klassiska Resursanvändning visas som **andra**, och inköp visas som **angetts**. |
| **Resurstyp** | Dela upp kostnader efter resurstyp. Det här alternativet är endast tillgänglig för användning av icke-klassisk. Klassiska Resursanvändning visas som **andra**, och inköp visas som **angetts**. |
| **Tjänstnamnet** eller **mätningskategori** | Dela upp kostnader efter Azure-tjänst. Det här alternativet är endast tillgängligt för Azure-användning. Alla inköp och användning av Marketplace visas som **angetts** eller **otilldelade**. |
| **Tjänstnivå** eller **mätarunderkategori** | Dela upp kostnader genom användningen av Azure mätaren etablering. Det här alternativet är endast tillgängligt för Azure-användning. Alla inköp och användning av Marketplace visas som **angetts** eller **otilldelade**. |
| **Prenumeration** | Dela upp kostnader per prenumeration. Alla inköp visas som **angetts**. |
| **Tag** | Dela upp kostnader genom att taggvärden för en specifik tagg-nyckel. |

Mer information om villkor finns i [förstår de termer som används i Azure-användning och avgifter](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Spara och dela anpassade vyer

Spara och dela anpassade vyer med andra genom att fästa kostnadsanalys till instrumentpanelen för Azure portal eller genom att kopiera en länk till kostnadsanalys. 

Välj Fäst-ikonen i det övre högra hörnet för att fästa kostnadsanalys. Fästa kostnadsanalys sparas bara på diagrammet eller tabellen Huvudvy. Dela instrumentpanelen som du vill ge andra användare åtkomst till panelen. Observera att detta delar bara den instrumentpanel-konfigurationen och inte bevilja andra åtkomst till underliggande data. Om du inte har åtkomst till kostnaderna, men du har åtkomst till en delad instrumentpanel, visas meddelandet ”åtkomst nekad”.

Om du vill dela en länk för att analys av kostnader väljer **dela** överst på bladet. En anpassad URL visas som du öppnar den här specifika vyn för den här specifikt område. Om du inte har kostnad komma åt och hämta den här URL: en, visas meddelandet ”åtkomst nekad”. 

Mer information om hur du beviljar åtkomst till kostnader för varje område som stöds, [förstå och arbeta med omfattningar](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatisering och offlineanalys

Det finns tillfällen när du behöver hämta data för ytterligare analys kan slås samman med dina egna data, eller integrerar dem i dina egna system. Kostnadshantering erbjuder några olika alternativ. Som utgångspunkt, om du behöver en ad hoc-sammanfattning på hög nivå, som du får i kostnadsanalys, kan du bygga vyn som du behöver. Ladda ned den genom att välja **exportera** och välja **ladda ned data till CSV** eller **ladda ned data till Excel**. Excel-nedladdningen tillhandahåller ytterligare kontext för vyn som du använde för att generera download som omfång, fråga konfigurationen, totalt, och datum genereras.

Om du behöver fullständiga, unaggregated datauppsättningen kan du hämta det från faktureringskontot. Sedan, i listan över tjänster i portalens vänstra navigeringsfönstret, gå till **kostnadshantering + fakturering**. Välj ditt faktureringskonto, om tillämpligt. Gå till **användning och kostnader**, och välj sedan den **hämta** ikonen för den önskade faktureringsperioden.

Ta ett liknande sätt att automatisera mottagande kostnadsdata. Använd den [fråge-API](/rest/api/cost-management/query) för mer omfattande analys med dynamisk filtrering, gruppering, och aggregering eller Använd den [UsageDetails API](/rest/api/consumption/usageDetails) unaggregated för fullständiga datauppsättningen. Allmän tillgänglighet (GA)-versionen av dessa API: er är 2019-01-01. Använd **2019-04-01-preview** att få åtkomst till förhandsversionen av reservationen och Marketplace-köp inom dessa API: er. 

Följande är till exempel en sammanställd vy av amorterade kostnaderna uppdelade efter tilläggstyp (användning, inköp eller återbetalning) utgivartyp (Azure eller Marketplace), resursgrupp (tom för inköp) och reservation (tom om ej tillämpligt).

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

Och om du inte behöver sammansättning och föredrar fullständiga, raw datauppsättningen:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Om du behöver faktiska kostnader för att visa inköp som de ackumuleras, ändra **typ**/**mått** till **ActualCost**. Mer information om dessa API: er finns i den [fråga](/rest/api/cost-management/query) och [UsageDetails](/rest/api/consumption/usageDetails) API-dokumentationen. Observera att publicerade dokumenten för GA-versionen. Men de båda fungerar på samma sätt för den *2019-04-01-preview* API-versionen utanför nytt attribut av typen/mått och ändrade egenskapsnamn. (Läs mer om egenskapsnamnen nedan).
 
Cost Management-API: er fungerar över alla omfattningar ovan resurser: resursgrupp, prenumeration och hanteringsgruppen via Azure RBAC-åtkomst, EA fakturering konton (registreringar), avdelningar och registreringskonton via EA portalåtkomst. Mer information om scope, inklusive hur du avgör scope-ID eller hantera åtkomst i [förstå och arbeta med omfattningar](understand-work-scopes.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till den första självstudien om du vill lära dig att skapa och hantera budgetar.

> [!div class="nextstepaction"]
> [Skapa och hantera budgetar](tutorial-acm-create-budgets.md)
