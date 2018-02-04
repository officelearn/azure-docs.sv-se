---
title: "Datamål och utdata som är tillgängliga med Azure Machine Learning förberedelse av data som stöds | Microsoft Docs"
description: "Det här dokumentet innehåller en fullständig lista över stöds mål och matar ut för Azure Machine Learning förberedelse av data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 18db3caf487375252ca50f3942d7bdf538367ba6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="supported-data-exports-for-this-preview"></a>Stöd för export av data för den här förhandsversionen 
Det är möjligt att exportera till flera olika format. Du kan använda dessa format för att behålla mellanresultat för förberedelse av data innan du integrera resultaten i resten av Machine Learning-arbetsflöde.

## <a name="types"></a>Typer 
### <a name="csv-file"></a>CSV-fil 
Skriva ett kommatecken-värdefil till lagring.

#### <a name="options"></a>Alternativ
- Radbrytningar
- Ersätt med null-värden
- Ersätt fel med 
- Avgränsare


### <a name="parquet"></a>Parkettgolv 
Skriv en datamängd till lagring som parkettgolv.

Parkettgolv som ett format som kan ha olika format i lagringen. En enda .parquet-fil används ibland för mindre datamängder. Olika Python-bibliotek stöder läsning och skrivning till enkel .parquet filer. 

För närvarande använder Azure Machine Learning arbetsstationen PyArrow Python-bibliotek för att skriva ut parkettgolv under lokala interaktiv användning. Detta innebär att filen parkettgolv är för närvarande endast parkettgolv utdataformatet stöds lokala interaktiv användning.

Under skalbar körs (på Spark) använder Azure Machine Learning arbetsstationen Sparks parkettgolv läsning och skrivning av funktioner. Sparks standardformatet för utdata för parkettgolv (för närvarande den enda stöds) liknar i strukturen för en Hive-datamängd. Det innebär att en mapp innehåller många .parquet-filer som var en mindre partition på en större datamängd. 

#### <a name="caveats"></a>Varningar 
Parkettgolv som ett format är relativt små och har vissa inkonsekvenser i implementering över olika bibliotek. Till exempel begränsar Spark som tecken är giltiga i kolumnnamn när du skriver till parkettgolv. PyArrow göra inte detta. Följande tecken får inte finnas i ett kolumnnamn: 
- ,
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- För att säkerställa kompatibilitet med Spark skrivning av data till parkettgolv, förekomster av dessa tecken i kolumnnamn ersätts med och understreck (_).
>- Om du vill garantera konsekvens över lokala och skalbar körs alla data som skrivs till parkettgolv, via appen, Python eller Spark har dess kolumnnamn språkoberoende för att säkerställa kompatibilitet i Spark. Ta bort det ogiltiga ange kolumner innan de skrivs för att säkerställa förväntade kolumnnamn vid skrivning till parkettgolv tecken i Spark.



## <a name="locations"></a>Platser 
### <a name="local"></a>Lokal 
Hårddisken eller mappade nätverksenheter lagringsplats.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob storage kräver en Azure-prenumeration.

