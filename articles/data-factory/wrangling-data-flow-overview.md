---
title: Datatransformering data flöden i Azure Data Factory
description: En översikt över datatransformering data flöden i Azure Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 607ed6167ec761a6bf380a2cc420eb72f808f0a3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494876"
---
# <a name="what-are-wrangling-data-flows"></a>Vad är datatransformering data flöden?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Organisationer behöver utföra förberedelse av data och datatransformering för korrekt analys av komplexa data som fortsätter att växa varje dag. Förberedelse av data krävs så att organisationer kan använda data i olika affärs processer och minska tiden till värde.

Med datatransformering data flöden i Azure Data Factory kan du göra kod fria data förberedelser i moln skala upprepade gånger. Datatransformering data flöden integreras med [Power Query online](/power-query/) och gör Power Query M-funktioner tillgängliga för Data Factory-användare.

Datatransformering Data Flow översätter M som genereras av Power Query online mashup-redigeraren till Spark-kod för moln skalnings körning.

Datatransformering data flöden är särskilt användbara för data tekniker eller "medborgarnas data integratorer".

> [!NOTE]
> Datatransformering data flöde är för närvarande tillgängligt i en offentlig för hands version

## <a name="use-cases"></a>Användningsfall

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snabb interaktiv data utforskning och förberedelse

Flera data tekniker och medborgarnas Data Integrator kan interaktivt utforska och förbereda data uppsättningar i moln skala. Med ökningen av volym, variation och hastighet av data i data sjöar behöver användarna ett effektivt sätt att utforska och förbereda data uppsättningar. Du kan till exempel behöva skapa en data uppsättning som har all kund demografisk information för nya kunder sedan 2017. Du mappar inte till ett känt mål. Du är utforska, datatransformering och förberedelser data uppsättningar för att uppfylla ett krav innan du publicerar det i sjön. Datatransformering data flöden används ofta för mindre formella analys scenarier. Beredd-datauppsättningarna kan användas för att utföra omvandlingar och maskin inlärnings åtgärder.

### <a name="code-free-agile-data-preparation"></a>Kod – kostnads fri förberedelse av smidig data behandling

Medborgarnas data integratorer ägnar mer än 60% av tiden att söka efter och förbereda data. De vill göra det med en kod gratis för att förbättra drift produktiviteten. Genom att tillåta att medborgarna integrerar, formar och publicerar data med hjälp av kända verktyg som Power Query online på ett skalbart sätt förbättrar produktiviteten avsevärt. Datatransformering data flöde i Azure Data Factory gör att den välbekanta Power Querys mashup-redigeraren kan göra det möjligt för medborgarna att åtgärda fel snabbt, standardisera data och skapa data med hög kvalitet för att stödja affärs beslut.

### <a name="data-validation"></a>Datavalidering

Genomsök data visuellt i ett kod fritt sätt för att ta bort eventuella avvikare, avvikelser och anpassa dem till en form för snabb analys.

## <a name="supported-sources"></a>Källor som stöds

| Anslutning | Dataformat | Autentiseringstyp |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Konto nyckel |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Tjänstens huvudnamn |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Konto nyckel, tjänstens huvud namn |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-autentisering |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-autentisering |

## <a name="the-mashup-editor"></a>Mashup-redigeraren

När du skapar ett datatransformering-dataflöde blir alla käll data uppsättningar av data uppsättnings frågor och placeras i mappen **ADFResource** . Som standard pekar UserQuery på den första data uppsättnings frågan. Alla transformeringar bör utföras på UserQuery eftersom ändringar av data uppsättnings frågor inte stöds och inte kommer att vara kvar. Det finns för närvarande inte stöd för att byta namn på, lägga till och ta bort frågor.

![Datatransformering](media/wrangling-data-flow/editor.png)

För närvarande stöds inte alla Power Query M-funktioner för data datatransformering trots att de är tillgängliga under redigeringen. När du skapar dina datatransformering-dataflöden uppmanas du att ange följande fel meddelande om en funktion inte stöds:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Mer information om omvandlingar som stöds finns i [datatransformering Data Flow Functions](wrangling-data-flow-functions.md).

För närvarande stöder datatransformering Data Flow endast skrivning till en mottagare.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett datatransformering-dataflöde](wrangling-data-flow-tutorial.md).