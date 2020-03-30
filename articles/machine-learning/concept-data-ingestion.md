---
title: Alternativ för inmatning av data
titleSuffix: Azure Machine Learning
description: Läs mer om alternativ för datainmatning för att träna dina maskininlärningsmodeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086886"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Datainmatning i Azure Machine Learning

I den här artikeln får du lära dig för- och nackdelarna med följande datainmatningsalternativ som är tillgängliga med Azure Machine Learning. 

1. [Azure Data](#use-azure-data-factory) Factory-pipelines
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

Datainmatning är den process där ostrukturerade data extraheras från en eller flera källor och sedan förbereds för utbildning av maskininlärningsmodeller. Det är också tidskrävande, särskilt om det görs manuellt, och om du har stora mängder data från flera källor. Genom att automatisera den här insatsen frigörs resurser och dina modeller använder de senaste och gällande data.

Azure Data Factory (ADF) är speciellt byggd för att extrahera, läsa in och omvandla data, men Python SDK låter dig utveckla en anpassad kodlösning för grundläggande datainmatningsuppgifter. Om ingen av dem är riktigt vad du behöver kan du också använda ADF och Python SDK tillsammans för att skapa ett övergripande arbetsflöde för datainmatning som uppfyller dina behov. 

## <a name="use-azure-data-factory"></a>Använda Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) erbjuder inbyggt stöd för övervakning av datakällor och utlösare för datainmatningspipelor.  

I följande tabell sammanfattas för- och nackdelarna med att använda Azure Data Factory för dina arbetsflöden för datainmatning.

|Fördelar|Nackdelar
---|---
Speciellt byggd för att extrahera, läsa in och omvandla data.|Erbjuder för närvarande en begränsad uppsättning Azure Data Factory pipeline-uppgifter 
Gör att du kan skapa datadrivna arbetsflöden för att dirigera dataförflyttningar och omvandlingar i stor skala.|Dyrt att bygga och underhålla. Mer information finns på Azure Data Factorys [prissida.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
Integrerad med olika Azure-verktyg som [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) och [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Kör inte skript internt, förlitar sig istället på separat beräkning för skriptkörningar 
Stöder inbyggt datakälla utlöst datainmatning| 
Databeredning och modell utbildningsprocesser är separata.|
Inbäddad datalinjekapacitet för Azure Data Factory-dataflöden|
Ger ett [användargränssnitt](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) för låg kodupplevelse för metoder som inte är skript |

De här stegen och följande diagram illustrerar Azure Data Factorys arbetsflöde för datainmatning.

1. Hämta data från källorna
1. Omvandla och spara data i en utdatablolobbehållare, som fungerar som datalagring för Azure Machine Learning
1. Med förberedda data lagrade anropar Azure Data Factory-pipelinen en machine learning-pipeline som tar emot förberedda data för modellutbildning


    ![Inmatning av ADF-data](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Lär dig hur du skapar en pipeline för datainmatning för Machine Learning med [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>Använda Python SDK 

Med [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)kan du införliva datainmatningsuppgifter i ett [Azure Machine Learning pipeline-steg.](how-to-create-your-first-pipeline.md)

I följande tabell sammanfattas proffsen och kon för att använda steget SDK och en ML-pipelines för datainmatningsaktiviteter.

Fördelar| Nackdelar
---|---
Konfigurera dina egna Python-skript | Stöder inte inbyggt utlösande av ändring av datakällan. Kräver Logic App- eller Azure-funktionsimplementeringar
Dataförberedelse som en del av varje modell utbildning utförande|Kräver utvecklingsförmåga för att skapa ett skript för datainmatning
Stöder skript för dataförberedelse på olika beräkningsmål, inklusive [Azure Machine Learning-beräkning](concept-compute-target.md#azure-machine-learning-compute-managed) |Tillhandahåller inte ett användargränssnitt för att skapa inmatningsmekanismen

I följande diagram består Azure Machine Learning-pipelinen av två steg: datainmatning och modellutbildning. Datainmatningssteget omfattar uppgifter som kan utföras med Python-bibliotek och Python SDK, till exempel extrahera data från lokala/webbkällor och grundläggande dataomvandlingar, som värdeimputering saknas. Utbildningssteget använder sedan de förberedda data som indata till ditt träningsskript för att träna din maskininlärningsmodell. 

![Inmatning av Azure-pipeline + SDK-data](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en pipeline för datainmatning för Machine Learning med [Azure Data Factory](how-to-data-ingest-adf.md)
* Lär dig hur du automatiserar och hanterar utvecklingslivscyklerna för dina datainmatningspipelider med [Azure Pipelines](how-to-cicd-data-ingestion.md).
