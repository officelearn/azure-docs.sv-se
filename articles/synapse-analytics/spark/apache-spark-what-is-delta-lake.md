---
title: Vad är Delta Lake
description: Översikt över Delta Lake och hur det fungerar som en del av Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429205"
---
# <a name="what-is-delta-lake"></a>Vad är Delta Lake?

Azure Synapse Analytics är kompatibelt med Linux Foundation Delta Lake. Delta Lake är ett lagringslager med öppen källkod som ger 40 000 transaktioner (atomicitet, konsekvens, isolering och hållbarhet) till Apache Spark och arbetsbelastningar för stordata.

## <a name="key-features"></a>Huvudfunktioner

| Funktion | Beskrivning |
| --- | --- |
| **ACID Transaktioner** | Datasjöar fylls vanligtvis i av flera processer och rörledningar, varav vissa skriver data samtidigt med läsningar. Före Delta Lake och tillägg av transaktioner, dataingenjörer var tvungna att gå igenom en manuell felbenägen process för att säkerställa dataintegritet. Delta Lake ger välbekanta ACID transaktioner till datasjöar. Det ger serialiserbarhet, den starkaste nivån av isolering nivå. Läs mer på [Dykning i Delta Lake: Packa upp transaktionsloggen.](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)|
| **Skalbar metadatahantering** | I stordata kan även själva metadata vara "big data". Delta Lake behandlar metadata precis som data, utnyttja Spark distribuerade processorkraft för att hantera alla dess metadata. Som ett resultat kan Delta Lake hantera petabyte-skala tabeller med miljarder partitioner och filer till mods. |
| **Tidsresor (dataversion)** | Möjligheten att "ångra" en ändring eller gå tillbaka till en tidigare version är en av de viktigaste funktionerna i transaktioner. Delta Lake innehåller ögonblicksbilder av data som gör att du kan återgå till tidigare versioner av data för granskningar, återställningar eller för att återskapa experiment. Läs mer i [Introduktion Delta Lake Tidsresor för storskaliga datasjöar](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Öppna format** | Apache Parquet är baslinjeformatet för Delta Lake, så att du kan utnyttja de effektiva komprimerings- och kodningsscheman som är inbyggda i formatet. |
| **Enhetlig batch och streaming källa och diskbänk** | En tabell i Delta Lake är både en batchtabell, samt en strömmande källa och diskho. Strömmande data som matar in, batch historiska återfyllning och interaktiva frågor alla bara arbeta ur lådan. |
| **Tvingande schema** | Schema verkställighet hjälper till att säkerställa att datatyperna är korrekta och nödvändiga kolumner finns, vilket förhindrar att felaktiga data orsakar datakonsekvens. Mer information finns [i Dykning i Delta Lake: Schema Verkställighet & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **SchemaUtveckling** | Med Delta Lake kan du göra ändringar i ett tabellschema som kan tillämpas automatiskt, utan att behöva skriva migrerings-DDL. Mer information finns [i Dykning i Delta Lake: Schema Verkställighet & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Granskningshistorik** | Delta Lake transaktionslogg registrerar information om varje ändring som görs i data som ger en fullständig granskning spår av ändringarna. |
| **Uppdateringar och borttagningar** | Delta Lake stöder Scala / Java / Python och SQL API: er för en mängd olika funktioner. Stöd för sammanslagning, uppdatering och borttagning av åtgärder hjälper dig att uppfylla efterlevnadskraven. Mer information finns i [Meddela Delta Lake 0.4.0 Release](https://delta.io/news/delta-lake-0-4-0-released/) och [Enkla, tillförlitliga upserts och tar bort på Delta Lake-tabeller med Python-API:er](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), som innehåller kodavsnitt för sammanfogning, uppdatering och ta bort DML-kommandon. |
| **100% kompatibel med Apache Spark API** | Utvecklare kan använda Delta Lake med sina befintliga datapipelpipel med minimal förändring eftersom det är helt kompatibelt med befintliga Spark-implementeringar. |

Fullständig dokumentation finns på [dokumentationssidan](https://docs.delta.io/latest/delta-intro.html) för Delta Lake

Mer information finns i [Delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>Nästa steg

- [.NET för Apache Spark-dokumentation](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
