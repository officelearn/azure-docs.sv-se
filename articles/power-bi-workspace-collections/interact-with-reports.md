---
title: Interagera med rapporter med JavaScript API | Microsoft Docs
description: Med Power BI JavaScript API kan du enkelt bädda in Power BI-rapporter i dina program.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
origin.date: 09/26/2018
ms.date: 03/05/2019
ms.author: v-junlch
ms.openlocfilehash: 252296af8b2065ae22bed8b421d4d00718b78287
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110471"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagera med Power BI-rapporter med JavaScript API

Med Power BI JavaScript API kan du enkelt bädda in Power BI-rapporter i dina program. Med API, kan dina program interagera med andra rapportelement som sidor och filter. Den här interaktiviteten gör Power BI-rapporter till en mer integrerad del av ditt program.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Du bäddar in en Power BI-rapport i ditt program med hjälp av en iframe som finns som en del av programmet. Iframe fungerar som en gräns mellan programmet och rapporten som du ser i följande bild:

![iframe för Power BI-arbetsyta utan Javascript API](./media/interact-with-reports/iframe-without-javacript.png)

Iframe gör inbäddningsprocessen mycket enklare, men utan JavaScript API kan rapporten och programmet inte samverka med varandra. Den här bristande interaktionen kan kännas som att rapporten egentligen inte är en del av programmet. Rapporten och programmet behöver verkligen kommunicera fram och tillbaka som i följande bild:

![iframe för Power BI-arbetsyta med Javascript API](./media/interact-with-reports/iframe-with-javascript.png)

Med Power BI JavaScript API kan du skriva kod som på ett säkert sätt kan passera iframe-gränsen. Detta gör att programmet programmässigt kan utföra en åtgärd i en rapport och lyssna efter händelser från åtgärder som användare gör i rapporten.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Vad kan du göra med Power BI JavaScript API?

Med JavaScript API kan du hantera rapporter, navigera till sidor i en rapport, filtrera en rapport och hantera inbäddade händelser. Följande diagram visar strukturen för API:en.

![Power BI JavaScript API-diagram](./media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Hantera rapporter
Med Javascript API kan du hantera beteende på rapporten och sidan:

- Bädda in en Power BI-rapport på ett säkert sätt i ditt program, prova med [inbäddade demoprogram](https://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Ange åtkomst-token
- Konfigurera rapporten
  - Aktivera och inaktivera filterfönstret och sidnavigeringsfönstret, och prova med [uppdatera inställningsdemoprogram](https://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Ange standard för sidor och filter, prova med [ange standarddemo](https://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Gå in och ur fullskärmsläge

[Läs mer om att bädda in en rapport](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Navigera till sidor i en rapport
Med JavaScript API kan du identifiera alla sidor i en rapport och ange den aktuella sidan. Prova med [navigeringsdemoprogram](https://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Läs mer om sidnavigering](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrera en rapport
JavaScript API innehåller grundläggande och avancerade filtreringsfunktioner för inbäddade rapporter och rapportsidor. Prova med [filtreringsdemoprogram](https://azure-samples.github.io/powerbi-angular-client/#/scenario4), och granska grundläggande koder här.

#### <a name="basic-filters"></a>Grundläggande filter
Ett grundläggande filter placeras på en kolumn eller hierarkinivå och innehåller en lista med värden som ska tas med eller undantas.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Avancerade filter
Avancerade filter använder logisk operator OCH eller ELLER, och godkänner ett eller två villkor, vart och ett med sin egen operator och värde. Villkor som stöds är:

- Ingen
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- Contains
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Is
- IsNot
- IsBlank
- IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Läs mer om filtrering](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Hantera händelser

Förutom att skicka information till iframe, kan programmet även ta emot information om följande händelser från iframe:

- Bädda in
  - inläst
  - fel
- Rapporter
  - pageChanged
  - dataSelected (kommer snart)

[Läs mer om att hantera händelser](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Nästa steg

Mer information om Power BI JavaScript API finns under följande länkar:

- [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Referens för objektmodell](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- [Live-demo](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!-- Update_Description: update metedata properties -->