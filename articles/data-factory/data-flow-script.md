---
title: Skript för data flöde för Azure Data Factory mappning
description: Översikt över Data Factory data flödes skript kod – bakom språk
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010166"
---
# <a name="data-flow-script-dfs"></a>Data flödes skript (DFS)

Data flödes skriptet (DFS) är underliggande metadata, ungefär som ett kodnings språk, som används för att köra omvandlingar som ingår i ett data flöde för mappning. Varje omvandling representeras av en serie egenskaper som ger den information som krävs för att köra jobbet på rätt sätt. Skriptet är synligt och redigerbart från ADF genom att klicka på knappen "skript" i det övre menyfliksområdet i webb läsar gränssnittet.

![Skript knapp](media/data-flow/scriptbutton.png "Skript knapp")

`allowSchemaDrift: true,` i en käll omvandling till exempel instruerar tjänsten att ta med alla kolumner från käll data uppsättningen i data flödet även om de inte ingår i schema projektionen.

## <a name="use-cases"></a>Användningsfall
DFS skapas automatiskt av användar gränssnittet. Du kan klicka på skript knappen för att visa och anpassa skriptet. Du kan också generera skript utanför ADF-ANVÄNDARGRÄNSSNITTET och sedan skicka det till PowerShell-cmdleten. När du felsöker komplexa data flöden, kan det vara lättare att söka igenom skript koden – bakom i stället för att skanna in UI-diagrammets representation av dina flöden.

Här är några exempel på användnings fall:
- Program mässigt skapar många data flöden som är ganska identiska, d.v.s. "stämpla ut"-data flöden.
- Komplexa uttryck som är svåra att hantera i användar gränssnittet eller som resulterar i verifierings problem.
- Fel sökning och bättre förståelse för olika fel som returneras under körningen.

När du skapar ett data flödes skript som ska användas med PowerShell eller ett API måste du komprimera den formaterade texten till en enda rad. Du kan behålla TABB-och newlines som escape-tecken. Men texten måste vara formaterad för att få plats i en JSON-egenskap. Det finns en knapp i gränssnittet för skript redigeraren längst ned som formaterar skriptet som en enskild rad åt dig.

![Kopierings knapp](media/data-flow/copybutton.png "Knappen Kopiera")

## <a name="how-to-add-transforms"></a>Lägga till transformeringar
Det krävs tre grundläggande steg för att lägga till transformeringar: lägga till kärn omvandlings data, dirigera om indataströmmen och sedan dirigera om utdataströmmen. Detta kan ses enklast i ett exempel.
Låt oss säga att vi börjar med en enkel källa för att ta med data flödet som följande:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Om vi bestämmer dig för att lägga till en härledd omvandling måste du först skapa Core Transformation text, som har ett enkelt uttryck för att lägga till en ny versal kolumn med namnet `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Sedan tar vi befintliga DFS och lägger till omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Och nu dirigerar vi om den inkommande strömmen genom att identifiera vilken omvandling vi vill att den nya omvandlingen ska komma efter (i det här fallet `source1`) och kopiera namnet på data strömmen till den nya omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Slutligen identifierar vi den omvandling som vi vill komma efter den nya omvandlingen och ersätter dess indataströmmen (i det här fallet `sink1`) med namnet på den resulterande data strömmen för vår nya omvandling:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Grundläggande om DFS
DFS består av en serie anslutna omvandlingar, inklusive källor, mottagare och andra som kan lägga till nya kolumner, filtrera data, koppla data och mycket mer. Vanligt vis är skriptet med start med en eller flera källor följt av många omvandlingar och slutar med en eller flera handfat.

Alla källor har samma grundläggande konstruktion:
```
source(
  source properties
) ~> source_name
```

En enkel källa med tre kolumner (movieId, title, genrer) skulle till exempel vara:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alla transformeringar än källor har samma grundläggande konstruktion:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Till exempel är en enkel härledd omvandling som tar en kolumn (rubrik) och skriver över den med en versal version så här:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

Och en mottagare utan schema skulle bara vara:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Nästa steg

Utforska data flöden genom att starta med [översikts artikeln för data flöden](concepts-data-flow-overview.md)
