---
title: Kostnads hanterings guide för Azure Lab Services
description: Förstå de olika sätten att Visa kostnader för labb tjänster.
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445312"
---
# <a name="cost-management-for-azure-lab-services"></a>Kostnads hantering för Azure Lab Services

Kostnads hantering kan delas upp i två olika områden: kostnads uppskattning och kostnads analys.  Kostnads uppskattning inträffar när du konfigurerar labbet för att se till att den inledande strukturen i labbet får plats inom den förväntade budgeten.  Kostnads analys sker vanligt vis i slutet av månaden för att analysera kostnaderna och fastställa de åtgärder som krävs för nästa månad.

## <a name="estimating-the-lab-costs"></a>Uppskatta labb kostnaderna

Varje labb instrument panel har ett **kostnads & fakturerings** avsnitt som ger en grov uppskattning av vad labbet kostar för månaden.  Kostnads uppskattningen sammanfattar användningen av timmen med det maximala antalet användare med den uppskattade kostnaden per timme.  För att få den mest exakta uppskattningen att ställa in labbet, inklusive [schemat](how-to-create-schedules.md), och instrument panelen visar den uppskattade kostnaden.  

Den här uppskattningen kanske inte är alla möjliga kostnader, det finns några resurser som inte ingår.  Mallens förberedelse kostnad är inte faktor för kostnads uppskattningen.  Det kan variera avsevärt i hur lång tid det tar att skapa mallen. Kostnaden för att köra mallen är samma som den övergripande labb kostnaden per timme. Eventuella kostnader för [delade avbildnings gallerier](how-to-use-shared-image-gallery.md) ingår inte i labb instrument panelen eftersom ett galleri kan delas mellan flera labb.  Slutligen kan timmar som uppstått när labb skaparen startar en dator uteslutas från den här beräkningen.

> [!div class="mx-imgBorder"]
> ![Kostnads uppskattning för instrument panelen](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>Analysera användning av föregående månad

Kostnads analysen är att granska föregående månads användning för att hjälpa till att fastställa eventuella justeringar för labbet.  Nedbrytning av kostnader i förflutna finns i [prenumerations kostnads analysen](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  I Azure Portal kan du skriva "prenumerationer" i det övre Sök fältet och sedan välja alternativet prenumerationer.  

> [!div class="mx-imgBorder"]
> ![Prenumerations sökning](./media/cost-management-guide/subscription-search.png)

Välj den prenumeration som ska granskas.

> [!div class="mx-imgBorder"]
> ![Val av prenumeration](./media/cost-management-guide/subscription-select.png)

 Välj "cost Analysis" i den vänstra rutan under **Cost Management**.

 > [!div class="mx-imgBorder"]
> ![Kostnads analys av prenumeration](./media/cost-management-guide/subscription-cost-analysis.png)

Den här instrument panelen kommer att möjliggöra djupgående kostnads analys, inklusive möjligheten att exportera till olika filtyper enligt ett schema.  Cost Management har flera funktioner för mer information, se [Cost Management fakturerings översikt](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtrering av resurs typen: `microsoft.labservices/labaccounts` visar bara kostnaden som är associerad med labb tjänsterna.

## <a name="understanding-the-usage"></a>Förstå användningen

Nedan visas ett exempel på kostnads analys.

> [!div class="mx-imgBorder"]
> ![Kostnads analys av prenumeration](./media/cost-management-guide/cost-analysis.png)

Som standard finns det sex kolumner: resurs, resurs typ, plats, resurs grupp namn, taggar, kostnad.  Resurs kolumnen innehåller information om labb kontot, labb namnet och den virtuella datorn.  Raderna med labb konto/labb namn/standard är kostnaden för labbet, som kan visas på den andra och tredje raden.  De använda virtuella datorerna har en kostnad under labb kontots/Labbets namn/standard/VM-namn.  I det här exemplet får du lägga till den första raden med en andra rad, både från och med "aaalab/dockerlab", och den totala kostnaden för labbet "dockerlab" i labb kontot "aaalab".

Om du vill hämta information om det delade bild galleriet ändrar du resurs typen till `Microsoft.Compute/Galleries` , som ger den övergripande kostnaden för avbildnings galleriet.  Gallerien för resurs avbildningar kanske inte visas i kostnaderna beroende på var galleriet lagras.

> [!NOTE]
> Det delade avbildnings galleriet är anslutet till labb kontot.  Det innebär att flera labb kan använda samma bild.

## <a name="separating-costs"></a>Åtskilj kostnader

Vissa universitet har använt labb kontot och resurs gruppen som olika sätt att skilja ut olika klasser.  Varje klass har sitt eget labb konto och en annan resurs grupp. I fönstret kostnads analys lägger du till ett filter baserat på resurs gruppens namn med lämpligt resurs grupp namn för klassen och endast kostnaderna för den klassen visas.  På så sätt kan du göra en tydligare avslutning mellan olika klasser när du visar kostnaderna.  Med den [schemalagda export](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) funktionen i kostnads analysen kan kostnaden för varje klass hämtas i separata filer.

## <a name="managing-costs"></a>Hantera kostnader

Beroende på typen av klass finns det olika sätt att hantera kostnader för att minska antalet virtuella datorer som körs utan student användning av datorn.

### <a name="auto-shutdown-on-disconnect"></a>Automatisk avstängning vid från koppling

När labbet skapas kan labb ägaren ställa in de virtuella datorerna i labbet så att de [stängs av när RDP-anslutningen till den virtuella datorn är frånkopplad](how-to-enable-shutdown-disconnect.md).  Den här inställningen minskar scenariot där studenten kopplas bort men glömmer att stoppa den virtuella datorn.

### <a name="quota-vs-scheduled-time"></a>Kvot jämfört med schemalagd tid

När du förstår [kvot tiden](classroom-labs-concepts.md#quota) vs den [schemalagda tiden](classroom-labs-concepts.md#schedules) får labb ägaren möjlighet att konfigurera labbet så att det bättre passar behoven hos lärare och studenter.  Schemalagd tid är en angiven tid där alla virtuella student datorer har startats och är tillgängliga för anslutning till.  Vanligt vis används ofta i situationen när alla studenter har sin egen virtuella dator och följer anvisningarna i en angiven tid under dagen som klass timmar.  Nack delen är att alla elev-VM: ar startas och att kostnaderna påförs, även om en student inte loggar in på den virtuella datorn.  Kvot tiden allokeras till varje elev som de kan använda för att avgöra om och ofta används för oberoende studerande. De virtuella datorerna startas inte förrän studenten startar den virtuella datorn.  

Ett labb kan använda antingen kvot tid, schemalagd tid eller en kombination av båda. Om en klass inte behöver den schemalagda tiden använder du bara kvot tiden för den mest effektiva användningen av de virtuella datorerna.

### <a name="scheduled-event---stop-only"></a>Schemalagd händelse-stoppa bara

I schemat kan du lägga till en händelse typ som inte är i topp, vilket stoppar alla datorer vid en angiven tidpunkt.  Vissa labb ägare har angett en stopp händelse för varje dag vid midnatt för att minska kostnaderna och kvot användningen när en student glömmer att stänga av den virtuella dator som de använder.  Nack delen med den här typen av händelse är att alla virtuella datorer stängs av även om studenten använder den virtuella datorn.

### <a name="other-costs-related-to-labs"></a>Andra kostnader relaterade till labb 

Det finns kostnader som inte har registrerats i labb tjänsterna, men kan vara knutna till en labb tjänst.  Ett delat avbildnings Galleri kan vara anslutet till labb men visas inte under kostnader för labb tjänster och har kostnader.  För att hjälpa till att hålla totalkostnaderna bör du ta bort eventuella oanvända avbildningar från galleriet eftersom avbildningarna har en ärvd lagrings kostnad.  Labb kan ha anslutningar till andra Azure-resurser av ett virtuellt nätverk (VNet) när ett labb tas bort, du bör ta bort det virtuella nätverket och de andra resurserna.

## <a name="conclusion"></a>Slutsats

Förhoppnings vis ger informationen ovan en bättre förståelse för användnings kostnaderna och hur du använder verktygen för att minska överskotts kostnaderna.
