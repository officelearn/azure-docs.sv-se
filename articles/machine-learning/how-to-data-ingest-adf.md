---
title: Datainmatning med Azure Data Factory
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en pipeline för data inmatning med Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8cf0abdeaf3a7fe71213b6fa4f78f057bf2f92eb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307359"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Datainmatning med Azure Data Factory

I den här artikeln får du lära dig hur du skapar en pipeline för data inmatning med Azure Data Factory (ADF). Den här pipelinen används för att mata in data som ska användas med Azure Machine Learning. Med Azure Data Factory kan du enkelt extrahera, transformera och läsa in data (ETL). När data har omvandlats och lästs in i lagringen kan du använda dem för att träna dina maskin inlärnings modeller.

Enkel datatransformering kan hanteras med ursprungliga ADF-aktiviteter och instrument som [data flöde](../data-factory/control-flow-execute-data-flow-activity.md). När det kommer till mer komplicerade scenarier kan data bearbetas med en anpassad kod. Till exempel python eller R-kod.

Det finns flera vanliga metoder för att använda Azure Data Factory för att transformera data under inmatningen. Varje teknik har för-och nack delar som avgör om den passar bra för ett särskilt användnings fall:

| Teknik | Fördelar | Nackdelar |
| ----- | ----- | ----- |
| ADF + Azure Functions | Låg latens, Server lös beräkning</br>Tillstånds känsliga funktioner</br>Återanvändbara funktioner | Endast lämpat för kortvarig bearbetning |
| ADF + anpassad komponent | Storskalig parallell data behandling</br>Lämpligt för stora algoritmer | Figursätta kod till en körbar fil</br>Komplexitet vid hantering av beroenden och i/o |
| ADF + Azure Databricks Notebook | Apache Spark</br>Inbyggd python-miljö | Kan vara dyrt</br>Att skapa kluster från början tar tid och lägger till latens

## <a name="adf-with-azure-functions"></a>ADF med Azure Functions

![I diagrammet visas en Azure Data Factory pipeline, med Azure Function och kör M L pipeline, och en Azure Machine Learning pipeline, med träna modell, och hur de interagerar med rå data och för beredda data.](media/how-to-data-ingest-adf/adf-function.png)

Med Azure Functions kan du köra små delar av kod (funktioner) utan att oroa dig för program infrastrukturen. I det här alternativet bearbetas data med anpassad python-kod inkapslad i en Azure-funktion. 

Funktionen anropas med [aktiviteten ADF Azure Function](../data-factory/control-flow-azure-function-activity.md). Den här metoden är ett lämpligt alternativ för lätta data transformationer. 

* Proffs
    * Data bearbetas med en server lös beräkning med en relativt låg latens
    * ADF pipeline kan anropa en [varaktig Azure-funktion](../azure-functions/durable/durable-functions-overview.md) som kan implementera ett avancerat data omvandlings flöde 
    * Informationen om Datatransformeringen har sammanställts av Azure-funktionen som kan återanvändas och anropas från andra platser
* Nack delar
    * Azure Functions måste skapas före användning med ADF
    * Azure Functions är bara lämpat för kort körning av data behandling

## <a name="adf-with-custom-component-activity"></a>ADF med anpassad komponent aktivitet

![Diagrammet visar en Azure Data Factory pipeline, med en anpassad komponent och kör M L pipeline, och en Azure Machine Learning pipeline, med träna modell, och hur de interagerar med rå data och för beredda data.](media/how-to-data-ingest-adf/adf-customcomponent.png)

I det här alternativet bearbetas data med anpassad python-kod som är figursatt till en körbar fil. Den anropas med en [anpassad ADM-komponent-aktivitet](../data-factory/transform-data-using-dotnet-custom-activity.md). Den här metoden passar bättre för stora data än den tidigare tekniken.

* Proffs
    * Data bearbetas på [Azure Batch](../batch/batch-technical-overview.md) pool, vilket ger storskalig parallell och högpresterande data behandling
    * Kan användas för att köra tungt frekventa algoritmer och bearbeta stora mängder data
* Nack delar
    * Azure Batch pool måste skapas före användning med ADF
    * Över-teknik som är relaterad till att figursätta python-kod till en körbar fil. Komplexitet vid hantering av beroenden och indata/utdata-parametrar

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF med Azure Databricks python Notebook

![Diagrammet visar en Azure Data Factory pipeline, med Azure Databricks python och kör M L pipeline, och en Azure Machine Learning pipeline, med träna modell, och hur de interagerar med rå data och för beredda data.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) är en Apache Spark-baserad analys plattform i Microsoft-molnet.

I den här tekniken utförs Datatransformeringen av en [python-anteckningsbok](../data-factory/transform-data-using-databricks-notebook.md), som körs på ett Azure Databricks kluster. Detta är förmodligen den vanligaste metoden som utnyttjar en Azure Databrickss tjänsts fulla kapacitet. Den är utformad för distribuerad data bearbetning i stor skala.

* Proffs
    * Data omvandlas till den mest kraftfulla Azure-tjänsten för data bearbetning, som säkerhets kopie ras av Apache Sparks miljö
    * Inbyggt stöd för python tillsammans med ramverk och bibliotek för data vetenskap, inklusive TensorFlow, PyTorch och scikit – lära
    * Det finns inget behov av att figursätta python-koden till Functions eller körbara moduler. Koden fungerar som den är.
* Nack delar
    * Azure Databricks-infrastrukturen måste skapas före användning med ADF
    * Kan vara kostsam beroende på Azure Databricks konfiguration
    * Det tar en stund att snurra beräknings kluster från "kall" läge med hög latens för lösningen 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Använda data i Azure Machine Learning pipelines

![Diagrammet visar en Azure Data Factory pipeline och en Azure Machine Learning pipeline och hur de interagerar med rå data och för beredda data. Data Factory pipeline-data till den för beredda data databasen, som feedar ett data lager, som feed-datauppsättningar i arbets ytan Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

Transformerade data från ADF-pipeline sparas i data lagring (till exempel Azure-Blob). Azure Machine Learning kan komma åt dessa data med hjälp av data [lager](./how-to-access-data.md#create-and-register-datastores) och [data uppsättningar](./how-to-create-register-datasets.md).

Varje gången ADF-pipeline körs, sparas data på en annan plats i lagringen. För att skicka platsen till Azure Machine Learning anropar ADF-pipeline en Azure Machine Learning pipeline. När du anropar en ML-pipeline skickas data platsen och körnings-ID: t som parametrar. ML-pipeline kan sedan skapa ett data lager/data uppsättning med hjälp av data platsen. 

> [!TIP]
> Data uppsättningar [stöder versions hantering](./how-to-version-track-datasets.md), så den ml-pipelinen kan registrera en ny version av data uppsättningen som pekar på de senaste data från ADF-pipeline.

När data är tillgängliga via ett data lager eller en data uppsättning kan du använda det för att träna en ML-modell. Inlärnings processen kan vara en del av samma ML-pipeline som anropas från ADF. Eller så kan det vara en separat process som experimentering i en Jupyter Notebook.

Eftersom data uppsättningar stöder versions hantering och varje körning från pipelinen skapar en ny version, är det enkelt att förstå vilken version av data som användes för att träna en modell.

## <a name="next-steps"></a>Nästa steg

* [Kör en Databricks Notebook i Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Få åtkomst till data i Azure Storage-tjänster](./how-to-access-data.md#create-and-register-datastores)
* [Träna modeller med data uppsättningar i Azure Machine Learning](./how-to-train-with-datasets.md)
* [DevOps i en pipeline för datainmatning](./how-to-cicd-data-ingestion.md)