---
title: Datainmatning med Azure Data Factory
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en pipeline för datainmatning med Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274793"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Datainmatning med Azure Data Factory

I den här artikeln får du lära dig hur du skapar en datainmatningspipeline med Azure Data Factory (ADF). Den här pipelinen används för att använda data för användning med Azure Machine Learning. Med Azure Data Factory kan du enkelt extrahera, omvandla och läsa in (ETL) data. När data har omvandlats och lästs in i lagring kan de användas för att träna dina maskininlärningsmodeller.

Enkel dataomvandling kan hanteras med inbyggda ADF-aktiviteter och instrument som [dataflöde](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity). När det gäller mer komplicerade scenarier kan data bearbetas med viss anpassad kod. Python- eller R-kod.

Det finns flera vanliga tekniker för att använda Azure Data Factory för att omvandla data under inmatning. Varje teknik har för-och nackdelar som avgör om det är en bra passform för en viss användning fall:

| Teknik | Fördelar | Nackdelar |
| ----- | ----- | ----- |
| ADF + Azure-funktioner | Låg latens, serverlös beräkning</br>Tillståndskänsliga funktioner</br>Återanvändbara funktioner | Endast bra för kort gående bearbetning |
| ADF + anpassad komponent | Storskalig parallell databehandling</br>Passar för tunga algoritmer | Figursättningskod till en körbar</br>Komplexiteten i hantering av beroenden och IO |
| ADF + Azure Databricks anteckningsbok | Apache Spark</br>Inbyggd Python-miljö | Kan bli dyrt</br>Det tar tid att skapa kluster från början och lägger till svarstid

## <a name="adf-with-azure-functions"></a>ADF med Azure-funktioner

![adf-funktion](media/how-to-data-ingest-adf/adf-function.png)

Med Azure Functions kan du köra små bitar av kod (funktioner) utan att behöva oroa dig för programinfrastruktur. I det här alternativet bearbetas data med anpassad Python-kod insvept i en Azure-funktion. 

Funktionen anropas med [ADF Azure Function-aktiviteten](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Den här metoden är ett bra alternativ för lätta dataomvandlingar. 

* Proffsen:
    * Data bearbetas på en serverlös beräkning med en relativt låg latens
    * ADF-pipeline kan anropa en [hållbar Azure-funktion](/azure/azure-functions/durable/durable-functions-overview) som kan implementera ett sofistikerat dataomvandlingsflöde 
    * Informationen om dataomvandlingen abstraheras av Azure-funktionen som kan återanvändas och anropas från andra platser
* Nackdelar:
    * Azure-funktionerna måste skapas före användning med ADF
    * Azure Functions är bara bra för kort databearbetning

## <a name="adf-with-custom-component-activity"></a>ADF med anpassad komponentaktivitet

![adf-anpassad ersättning](media/how-to-data-ingest-adf/adf-customcomponent.png)

I det här alternativet bearbetas data med anpassad Python-kod insvept i en körbar. Den anropas med en [ADF Custom Component-aktivitet](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Detta tillvägagångssätt är en bättre passform för stora data än den tidigare tekniken.

* Proffsen:
    * Data bearbetas på [Azure Batch-pool,](https://docs.microsoft.com/azure/batch/batch-technical-overview) som ger storskalig parallell och högpresterande datoranvändning
    * Kan användas för att köra tunga algoritmer och bearbeta stora mängder data
* Nackdelar:
    * Azure Batch-pool måste skapas före användning med ADF
    * Över teknik relaterad till omslag Python-kod till en körbar. Komplexiteten i hantering av beroenden och in-/utdataparametrar

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF med Azure Databricks Python-anteckningsbok

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) är en Apache Spark-baserad analysplattform i Microsoft-molnet.

I den här tekniken utförs dataomvandlingen av en [Python-anteckningsbok](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)som körs på ett Azure Databricks-kluster. Detta är förmodligen den vanligaste metoden som utnyttjar den fulla kraften i en Azure Databricks-tjänst. Den är utformad för distribuerad databehandling i stor skala.

* Proffsen:
    * Data omvandlas till den mest kraftfulla Azure-tjänsten för databehandling, som backas upp av Apache Spark-miljön
    * Inbyggt stöd för Python tillsammans med datavetenskap ramverk och bibliotek inklusive TensorFlow, PyTorch och scikit-learn
    * Det finns ingen anledning att radbryt Python-koden till funktioner eller körbara moduler. Koden fungerar som den är.
* Nackdelar:
    * Azure Databricks-infrastrukturen måste skapas före användning med ADF
    * Kan vara dyrt beroende på Azure Databricks-konfiguration
    * Att snurra upp beräkningskluster från "kallt" läge tar lite tid som ger hög latens till lösningen 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Använda data i Azure Machine Learning-pipelines

![aml-datauppsättning](media/how-to-data-ingest-adf/aml-dataset.png)

Transformerade data från ADF-pipelinen sparas i datalagring (till exempel Azure Blob). Azure Machine Learning kan komma åt dessa data med hjälp av [datalager](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) och [datauppsättningar](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

Varje gång ADF-pipelinen körs sparas data på en annan plats i lagring. För att överföra platsen till Azure Machine Learning anropar ADF-pipelinen en Azure Machine Learning-pipeline. När ML-pipelinen anropas skickas dataplatsen och kör-ID som parametrar. ML-pipelinen kan sedan skapa ett datalager/datauppsättning med hjälp av dataplatsen. 

> [!TIP]
> Datauppsättningar [stöder versionshantering](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), så ML-pipelinen kan registrera en ny version av datauppsättningen som pekar på de senaste data från ADF-pipelinen.

När data är tillgängliga via ett datalager eller datauppsättning kan du använda den för att träna en ML-modell. Utbildningsprocessen kan vara en del av samma ML-pipeline som anropas från ADF. Eller det kan vara en separat process som experiment i en Jupyter anteckningsbok.

Eftersom datauppsättningar stöder versionshantering och varje körning från pipelinen skapar en ny version är det lätt att förstå vilken version av data som användes för att träna en modell.

## <a name="next-steps"></a>Nästa steg

* [Köra en Databricks-anteckningsbok i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Komma åt data i Azure-lagringstjänster](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Träna modeller med datauppsättningar i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps för en pipeline för datainmatning](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

