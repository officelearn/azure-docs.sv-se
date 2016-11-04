---
title: Interagera med rapporter med JavaScript API | Microsoft Docs
description: Power BI Embedded, interagera med rapporter med JavaScript API
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# Interagera med Power BI-rapporter med JavaScript API
Med Power BI JavaScript API kan du enkelt bädda in Power BI-rapporter i dina program. Med API, kan dina program interagera med andra rapportelement som sidor och filter. Den här interaktiviteten gör Power BI-rapporter till en mer integrerad del av ditt program.

Du bäddar in en Power BI-rapport i ditt program med hjälp av en iframe som finns som en del av programmet. Iframe fungerar som en gräns mellan programmet och rapporten som du ser i följande bild. 

![Power BI inbäddad iframe utan Javascript API](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-1.png)

Iframe gör inbäddningsprocessen mycket enklare, men utan JavaScript API kan rapporten och programmet inte samverka med varandra. Den här bristande interaktionen kan kännas som att rapporten egentligen inte är en del av programmet. Rapporten och programmet behöver verkligen kommunicera fram och tillbaka som i följande bild.

![Power BI inbäddad iframe med Javascript API](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-2.png)

Med Power BI JavaScript API kan du skriva kod som på ett säkert sätt kan passera iframe-gränsen. Detta gör att programmet programmässigt kan utföra en åtgärd i en rapport och lyssna efter händelser från åtgärder som användare gör i rapporten.

## Vad kan du göra med Power BI JavaScript API?
Med JavaScript API kan du hantera rapporter, navigera till sidor i en rapport, filtrera en rapport och hantera inbäddade händelser. Följande diagram visar strukturen för API:en.

![Power BI JavaScript API-diagram](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-3.png)

### Hantera rapporter
Med Javascript API kan du hantera beteende på rapporten och sidan:

* Bädda in en Power BI-rapport på ett säkert sätt i ditt program, prova med [inbäddade demoprogram](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Ange åtkomst-token
* Konfigurera rapporten
  * Aktivera och inaktivera filterfönstret och sidnavigeringsfönstret, och prova med [uppdatera inställningsdemoprogram](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Ange standard för sidor och filter, prova med [ange standarddemo](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Gå in och ur fullskärmsläge

[Läs mer om att bädda in en rapport](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### Navigera till sidor i en rapport
Med JavaScript API kan du identifiera alla sidor i en rapport och ange den aktuella sidan. Prova med [navigeringsdemoprogram](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Mer information om sidnavigering](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### Filtrera en rapport
JavaScript API innehåller grundläggande och avancerade filtreringsfunktioner för inbäddade rapporter och rapportsidor. Prova med [filtreringsdemoprogram](http://azure-samples.github.io/powerbi-angular-client/#/scenario4), och granska grundläggande koder här.  

#### Grundläggande filter
Ett grundläggande filter placeras på en kolumn eller hierarkinivå och innehåller en lista med värden som ska tas med eller undantas.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### Avancerade filter
Avancerade filter använder logisk operator OCH eller ELLER, och godkänner ett eller två villkor, vart och ett med sin egen operator och värde. Villkor som stöds är:

* Ingen
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
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
[Mer information om filtrering](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### Hantera händelser
Förutom att skicka information till iframe, kan programmet även ta emot information om följande händelser från iframe:

* Bädda in
  * inläst
  * fel
* Rapporter
  * pageChanged
  * dataSelected (kommer snart)

[Läs mer om att hantera händelser](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## Nästa steg
Mer information om Power BI JavaScript API finns under följande länkar:

* [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Referens för objektmodell](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* Exempel
  * [Angular](http://azure-samples.github.io/powerbi-angular-client)
  * [Ember](https://github.com/Microsoft/powerbi-ember)
* [Live-demo](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!--HONumber=Oct16_HO3-->


