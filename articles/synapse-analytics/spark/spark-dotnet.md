---
title: Använda .NET för Apache Spark med Azure Synapse Analytics
description: Lär dig mer om hur du använder .NET och Apache Spark för att göra batchbearbetning, direktuppspelning i realtid, maskininlärning och skriva ad hoc-frågor i Azure Synapse Analytics-anteckningsböcker.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430518"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Använda .NET för Apache Spark med Azure Synapse Analytics

[.NET för Apache Spark](https://dot.net/spark) är ledigt, öppet källkod och plattformsoberoende .NET-stöd för Spark. .NET för Apache Spark tillhandahåller .NET-bindningar för Spark som gör att du kan komma åt Spark API:er via C# och F#. Med .NET för Apache Spark kan du skriva och köra användardefinierade funktioner för Spark med .NET. Med .NET-API:erna för Spark kan du komma åt alla aspekter av Spark som hjälper dig att analysera dina data, inklusive Spark SQL och structured streaming.

Du kan analysera data med .NET för Apache Spark genom Spark-batchjobbdefinitioner eller med interaktiva Azure Synapse Analytics-anteckningsböcker. I den här artikeln får du lära dig hur du använder .NET för Apache Spark med Azure Synapse med båda teknikerna. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Skicka batchjobb med spark-jobbdefinitionen

Besök självstudien om du vill lära dig hur du använder Azure Synapse Analytics för att [skapa Apache Spark-jobbdefinitioner för Synapse Spark-pooler](apache-spark-job-definitions.md). Om du inte har paketerat din app för att skicka till Azure Synapse gör du följande steg.

1. Kör följande kommandon för att publicera appen. Var noga med att ersätta *mySparkApp* med sökvägen till din app.

   **I Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **På Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET för Apache Spark i Azure Synapse Analytics-anteckningsböcker

När du skapar en ny anteckningsbok väljer du en språkkärna som du vill uttrycka din affärslogik. Det finns kärnstöd för flera språk, inklusive C#.

Om du vill använda .NET för Apache Spark i din Azure Synapse Analytics-anteckningsbok väljer du **.NET Spark (C#)** som kärna och bifogar anteckningsboken till en befintlig Spark-pool.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET för Apache Spark i Azure Synapse Analytics-anteckningsböcker 

Anteckningsböcker är ett bra alternativ för att prototypera dina .NET för Apache Spark-pipelines och scenarier. Du kan börja arbeta med, förstå, filtrera, visa och visualisera dina data snabbt och effektivt. Datatekniker, datavetare, affärsanalytiker och maskininlärningsingenjörer kan alla samarbeta över ett delat, interaktivt dokument. Du ser omedelbara resultat från datautforskning och kan visualisera dina data i samma anteckningsbok.

### <a name="how-to-use-notebooks"></a>Så här använder du anteckningsböcker

När du skapar en ny anteckningsbok väljer du en språkkärna som du vill uttrycka din affärslogik. Det finns kärnstöd för flera språk, inklusive C#. 

Om du vill använda .NET för Apache Spark i din Azure Synapse Analytics-anteckningsbok väljer du **.NET Spark (C#)** som kärna och bifogar anteckningsboken till en befintlig Spark-pool. 

.NET Spark-anteckningsboken baseras på .NET-interaktiva upplevelser och ger interaktiva C#-upplevelser med möjligheten att `spark` använda .NET för Spark out of the box med spark-sessionsvariabeln redan fördefinierad.

### <a name="sparknet-c-kernel-features"></a>Spark.NET C#-kärnfunktioner

Följande funktioner är tillgängliga när du använder .NET för Apache Spark i azure Synapse Analytics-anteckningsboken:

* Deklarativ HTML: Generera utdata från cellerna med HTML-syntax, till exempel rubriker, punktlistor och till och med visa bilder.
* Enkla C#-satser (t.ex. tilldelningar, utskrift till konsol, undantag och så vidare).
* Flerradig C#-kodblock (till exempel om satser, förföra loopar, klassdefinitioner och så vidare).
* Tillgång till standard-C#-biblioteket (till exempel System, LINQ, Uppräkningsbara och så vidare).
* Stöd för [språkfunktioner för C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* "gnista" som en fördefinierad variabel för att ge dig tillgång till din Apache Spark-session.
* Stöd för att definiera [.NET användardefinierade funktioner som kan köras i Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Stöd för visualisering av utdata från Spark-jobb med hjälp av olika diagram (till exempel linje, stapel eller histogram) och layouter (till exempel enkel, överlagrad och så vidare) med `XPlot.Plotly` hjälp av biblioteket.
* Möjlighet att inkludera NuGet-paket i din C#-anteckningsbok.

## <a name="next-steps"></a>Nästa steg

* [.NET för Apache Spark-dokumentation](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interaktiv](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)