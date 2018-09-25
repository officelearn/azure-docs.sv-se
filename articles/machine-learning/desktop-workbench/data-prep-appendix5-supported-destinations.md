---
title: Datamål och utdata som är tillgängliga med Azure Machine Learning förberedelse av data som stöds | Microsoft Docs
description: Det här dokumentet innehåller en fullständig lista över stöds mål och visar tillgängliga för förberedelse av data i Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: f3d7d10db8cdeb4a1a409650f17d2fc39bf7971f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969520"
---
# <a name="supported-data-exports-for-this-preview"></a>Export av data som stöds för den här förhandsversionen 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Det går att exportera till flera olika format. Du kan använda dessa format för att behålla mellanresultat för förberedelse av data innan du integrera resultaten i resten av Machine Learning-arbetsflöde.

## <a name="types"></a>Typer 
### <a name="csv-file"></a>CSV-fil 
Skriv ett kommatecken-värdefil till lagring.

#### <a name="options"></a>Alternativ
- Radbrytningar
- Ersätt null-värden med
- Ersätt fel med 
- Avgränsare


### <a name="parquet"></a>Parquet 
Skriv en datauppsättning till lagring som Parquet.

Parquet eftersom ett format som kan ta olika former i lagring. En enda .parquet fil används ibland för mindre datauppsättningar. Olika Python-biblioteken stöder läsning och skrivning till enkel .parquet filer. 

För närvarande använder Azure Machine Learning Workbench PyArrow Python-bibliotek för att skriva ut Parquet under lokal interaktiva användning. Det innebär att Parquet fil för närvarande är endast Parquet utdataformat som stöds under lokal interaktiva användning.

Under skalbar körs (i Spark), Azure Machine Learning Workbench förlitar sig på Spark's Parquet läsning och skrivning av funktioner. Sparks Standardutdataformatet för Parquet (för närvarande den enda stöds) liknar i strukturen för en Hive-datauppsättning. Det innebär att en mapp innehåller många .parquet-filer som var och en mindre partition av en större datauppsättning. 

#### <a name="caveats"></a>Varningar 
Parquet-formatet är relativt unga och innehåller vissa implementering inkonsekvenser mellan olika bibliotek. Exempelvis begränsar Spark som tecken får användas i kolumnnamn när du skriver till Parquet. PyArrow gör inte detta. Följande tecken får inte vara i ett kolumnnamn: 
- , 
- ;
- {}
- ()
- \\n
- \\T
- =

>[!NOTE]
>- För att säkerställa kompatibilitet med Spark, som helst som du skriver data till Parquet, förekomster av dessa tecken i kolumnnamn har ersatts med och understreck (_).
>- Om du vill garantera konsekvensen mellan lokala och skala ut körningar, alla data som skrivs till Parquet, via appen, Python eller Spark, har dess kolumnnamn språkoberoende så Spark-kompatibilitet. Ta bort det ogiltiga angetts från kolumnerna innan skrivs för att säkerställa förväntade kolumnnamn vid skrivning till Parquet-tecken i Spark.



## <a name="locations"></a>Platser 
### <a name="local"></a>Lokal 
Lokal hårddisk eller mappade nätverksenheter lagringsplats.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob-lagring kräver en Azure-prenumeration.

