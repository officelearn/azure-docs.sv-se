---
title: Alternativ för data inmatning
titleSuffix: Azure Machine Learning
description: Lär dig mer om alternativen för data inmatning för att träna dina Machine Learning-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 35c133a4d9d95dc6e942d5b8c3e8d21debb9493b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268549"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Data inmatning i Azure Machine Learning

I den här artikeln får du lära dig om för-och nack delar med följande data inmatnings alternativ som är tillgängliga med Azure Machine Learning. 

1. [Azure Data Factory](#use-azure-data-factory) pipelines
2. [Python-SDK för Azure Machine Learning](#use-the-python-sdk)

Data inmatning är den process i vilken ostrukturerade data extraheras från en eller flera källor och sedan förbereds för utbildning av maskin inlärnings modeller. Det är också tids krävande, särskilt om du gör det manuellt, och om du har stora mängder data från flera källor. Att automatisera den här ansträngningen frigör resurser och ser till att dina modeller använder de senaste och aktuella data.

Vi rekommenderar att du börjar med att utvärdera med Azure Data Factory (ADF), eftersom det är specifikt utformat för att extrahera, läsa in och transformera data. Om du inte uppfyller dina krav med hjälp av ADF kan du använda python SDK för att utveckla en anpassad kod lösning, eller använda ADF och python SDK tillsammans för att skapa ett sammanfattande arbets flöde för data inmatning som uppfyller dina behov.

## <a name="use-azure-data-factory"></a>Använda Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) erbjuder inbyggt stöd för övervakning av data källor och utlösare för data inmatnings pipeliner.  

I följande tabell sammanfattas de tekniker och nack delar som används för att använda Azure Data Factory för arbets flöden för data inmatning.

|Experter|Nackdelar
---|---
Konstruerad för att extrahera, läsa in och transformera data.|Erbjuder för närvarande en begränsad uppsättning Azure Data Factory pipeline-uppgifter 
Gör att du kan skapa data drivna arbets flöden för att dirigera data förflyttning och omvandlingar i skala.|Dyrt att konstruera och underhålla. Mer information finns på [sidan med pris](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) information för Azure Data Factory.
Integrerat med olika Azure-verktyg som [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) och [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Skript körs inte internt i stället beroende på separat beräkning för skript körningar 
Har inbyggt stöd för data inmatning som utlöses av data Källa| 
Processerna för data förberedelse och modell inlärning är separata.|
Härkomst-kapacitet för inbäddad data för Azure Data Factory data flöden|
Innehåller ett [användar gränssnitt](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) med låg kod upplevelse för metoder som inte är skriptbaserade |

De här stegen och följande diagram illustrerar Azure Data Factory data inmatnings flödet.

1. Hämta data från sina källor
1. Transformera och spara data i en BLOB-behållare för utgående data som fungerar som data lagring för Azure Machine Learning
1. Med för beredda data lagrade, anropar Azure Data Factory pipelinen en utbildnings Machine Learning pipeline som tar emot de data som för bereddes för modell träning


    ![ADF-datainmatning](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>Använda python SDK 

Med [python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)kan du inkludera data inmatnings uppgifter i ett [Azure Machine Learning pipeline](how-to-create-your-first-pipeline.md) -steg.

I följande tabell sammanfattas de tekniker och con för att använda SDK: n och ett ML-pipeline-steg för data inmatnings uppgifter.

Experter| Nackdelar
---|---
Konfigurera egna Python-skript | Har inte inbyggt stöd för att aktivera ändringar av data källan. Kräver Logic app-eller Azure Function-implementeringar
Förberedelse av data som en del av alla modell utbildnings körningar|Kräver utvecklings kunskaper för att skapa ett skript för data inmatning
Stöder skript för förberedelse av data på olika beräknings mål, inklusive [Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) |Tillhandahåller inget användar gränssnitt för att skapa inmatnings mekanismen

I följande diagram består Azure Machine Learning pipelinen av två steg: data inmatning och modell träning. Steget för data inmatning omfattar uppgifter som kan utföras med python-bibliotek och python SDK, till exempel för att extrahera data från lokala/webb källor och grundläggande data transformationer, som t. ex. värde som saknas Imputation. I övnings steget används för beredda data som indata till ditt utbildnings skript för att träna din Machine Learning-modell. 

![Azure pipeline + SDK-datainmatning](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du automatiserar och hanterar utvecklings livs cykeln för dina data inmatnings pipeliner med [Azure-pipelines](how-to-cicd-data-ingestion.md).