---
title: Käbbel dataflöden i Azure Data Factory
description: En översikt över käbbel dataflöden i Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408991"
---
# <a name="what-are-wrangling-data-flows"></a>Vad är käbbel dataflöden?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Organisationer måste göra dataförberedelser och käbbel för korrekt analys av komplexa data som fortsätter att växa varje dag. Dataförberedelse krävs så att organisationer kan använda data i olika affärsprocesser och minska tiden till värde.

Med käbbeldataflöden i Azure Data Factory kan du göra kodfria dataförberedelser i molnskala iterativt. Käbbel dataflöden integreras med [Power Query Online](https://docs.microsoft.com/power-query/) och gör Power Query M-funktioner tillgängliga för datafabriksanvändare.

Käbbel dataflöde översätter M genereras av Power Query Online Mashup Editor till spark-kod för molnskala körning.

Käbbel dataflöden är särskilt användbara för dataingenjörer eller "medborgare dataintegratörer".

## <a name="use-cases"></a>Användningsfall

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snabb interaktiv datautforskning och förberedelse

Flera datatekniker och medborgare dataintegratörer kan interaktivt utforska och förbereda datauppsättningar i molnskala. Med ökningen av volym, variation och hastighet av data i datasjöar, användare behöver ett effektivt sätt att utforska och förbereda datamängder. Du kan till exempel behöva skapa en datauppsättning som "har all kunddemografisk information för nya kunder sedan 2017". Du mappar inte till ett känt mål. Du utforskar, käbbel och prepping datauppsättningar för att uppfylla ett krav innan du publicerar den i sjön. Käbbel dataflöden används ofta för mindre formella analytics scenarier. De förberedda datauppsättningarna kan användas för att göra omvandlingar och maskininlärningsåtgärder nedströms.

### <a name="code-free-agile-data-preparation"></a>Code-free agile data förberedelse

Citizen data integratörer tillbringar mer än 60% av sin tid att leta efter och förbereda data. De är ute efter att göra det på ett kodfritt sätt för att förbättra den operativa produktiviteten. Att tillåta medborgare dataintegratörer att berika, forma och publicera data med hjälp av kända verktyg som Power Query Online på ett skalbart sätt drastiskt förbättrar deras produktivitet. Med ett käbbeldataflöde i Azure Data Factory kan den välbekanta Power Query Online-mashup-redigeraren tillåta medborgaredataintegratörer att åtgärda fel snabbt, standardisera data och producera data av hög kvalitet för att stödja affärsbeslut.

### <a name="data-validation"></a>Datavalidering

Skanna dina data visuellt på ett kodfritt sätt för att ta bort eventuella avvikare, avvikelser och anpassa dem till en form för snabb analys.

## <a name="supported-sources"></a>Källor som stöds

| Anslutningsprogram | Dataformat | Autentiseringstyp |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parkett | Kontonyckel |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Tjänstens huvudnamn |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parkett | Kontonyckel, Tjänstens huvudman |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-autentisering |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-autentisering |

## <a name="the-mashup-editor"></a>Den mashup redaktör

När du skapar ett käbbeldataflöde blir alla källdatauppsättningar datauppsättningsfrågor och placeras i mappen **ADFResource.** Som standard pekar UserQuery på den första datauppsättningsfrågan. Alla omvandlingar bör göras på UserQuery som ändringar i datauppsättningsfrågor stöds inte heller kommer de att bevaras. Det går inte att byta namn på, lägga till och ta bort frågor.

![Gräl](media/wrangling-data-flow/editor.png)

För närvarande stöds inte alla Power Query M-funktioner för datakäbbel trots att de är tillgängliga under redigeringen. När du skapar dina käbbeldataflöden uppmanas du att få följande felmeddelande om en funktion inte stöds:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Mer information om omvandlingar som stöds finns i [käbbel med dataflödesfunktioner](wrangling-data-flow-functions.md).

För närvarande käbbel dataflödet stöder bara skriva till en diskbänk.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett käbbeldataflöde](wrangling-data-flow-tutorial.md).