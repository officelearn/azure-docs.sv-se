---
title: Skillnader från Azure Data Factory
description: Lär dig hur data integrerings funktionerna i Azure Synapse Analytics skiljer sig från Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345169"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Data integrering i Azure Synapse Analytics kontra Azure Data Factory

I Azure Synapse Analytics baseras data integrerings funktionerna som Synapse-pipeliner och data flöden på Azure Data Factory. Mer information finns i [Vad är Azure Data Factory](../../data-factory/introduction.md). Nästan alla funktioner är identiska eller liknande och dokumentationen delas mellan de två tjänsterna. I den här artikeln beskrivs och identifieras de aktuella skillnaderna mellan Azure Data Factory och Azure-Synapse.

Om du vill se om en Azure Data Factory funktion eller artikel gäller för Azure-Synapse kontrollerar du monikern överst i artikeln.

![Gäller för moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Gäller för moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funktioner i Azure Data Factory som inte har planer ATS för Azure-Synapse

Följande funktioner är tillgängliga i Azure Data Factory, men planeras inte för Azure-Synapse.

* Möjlighet att lyfta och byta SSIS-paket.
* Snö flingor som mottagare i data flödet för kopierings aktiviteten och mappningen.
* Tids inställningen för att mappa data flödet till Real tid i Azure integration Runtime.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Synapse-funktioner stöds inte i Azure Data Factory

Följande funktioner är tillgängliga i Azure Synapse, men planeras inte för Azure Data Factory.

* Spark-jobb övervakning av mappnings data flöden är bara tillgängligt i Synapse. I Synapse finns Spark-motorn i användarens prenumeration så att användarna kan se detaljerade Spark-loggar. I Azure Data Factory sker jobb körningen i ett Azure Data Factory hanterat Spark-kluster. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funktioner som beter sig annorlunda i Synapse

Följande funktioner fungerar antingen annorlunda eller finns inte i Azure-Synapse. 

* Datatransformering data flöden
* Galleriet för lösnings mal len
* Git-integrering och en inbyggd CI/CD-lösning
* Integrering med Azure Monitor
* Byta namn på resurser efter publicering
* Hybrid integration runtime-konfiguration inom en Synapse-arbetsyta. En användare kan inte ha både en hanterad VNet IR och en Azure IR.
* Integration runtime-delning mellan Synapse-arbetsytor

## <a name="next-steps"></a>Nästa steg

Kom igång med data integrering på din Synapse-arbetsyta genom att lära dig att mata [in data i ett Azure Data Lake Storage Gen2-konto](data-integration-data-lake.md).
