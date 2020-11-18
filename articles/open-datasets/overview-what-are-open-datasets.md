---
title: Vad är öppna data uppsättningar? Granskade offentliga data uppsättningar
titleSuffix: Azure Open Datasets
description: Lär dig mer om Azure Open data uppsättningar, granskade data uppsättningar från den offentliga domänen, till exempel väder, räkning, helgdagar och plats för att utöka förutsägelse lösningar.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 2ab713da87bd67053505289f5c9c8e7b747fd6bd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654855"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Vad är Azure Open-datauppsättningar och hur kan du använda dem?

[Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/) är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Öppna data uppsättningar finns i molnet på Microsoft Azure och är integrerade i Azure Machine Learning och är tillgängliga för Azure Databricks och Machine Learning Studio (klassisk). Du kan också komma åt data uppsättningarna via API: er och använda dem i andra produkter, till exempel Power BI och Azure Data Factory.

Data uppsättningar omfattar data från offentliga domäner för väder, räkning, helger, offentlig säkerhet och plats som hjälper dig att träna maskin inlärnings modeller och utöka förutsägelse lösningar. Du kan också dela dina offentliga data uppsättningar i Azure Open-datauppsättningar. 

![Azure Open data uppsättnings komponenter](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Granskade, för beredda data uppsättningar
Granskade öppna offentliga data uppsättningar i Azure Open-datauppsättningar är optimerade för användning i Machine Learning-arbetsflöden. 

Om du vill se alla tillgängliga data uppsättningar går du till [Azures öppna data uppsättnings katalog](https://azure.microsoft.com/services/open-datasets/catalog/).

Data forskare lägger ofta till majoriteten av tiden som rensas och förbereder data för avancerad analys. Öppna data uppsättningar kopieras till Azure-molnet och förbearbetas för att spara tid. Med jämna mellanrum hämtas data från källorna, t. ex. via en FTP-anslutning till National ensamt-NOAA (ensamt atmosfär administration). Därefter tolkas data i ett strukturerat format och berikas sedan som det är lämpligt med funktioner som post nummer eller plats för närmaste väder Station.

Data uppsättningar är samvärdna med moln beräkning i Azure och underlättar åtkomst och manipulering.  

Följande är exempel på data uppsättningar som är tillgängliga. 

### <a name="weather-data"></a>Väder data
 
|Datamängd         | Notebooks     | Description                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA-integrerade Surface-data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Worldwide varje timmes väder data från NOAA med den bästa spatiala täckningen i Nordamerika, Europa, Australien och delar av Asien. Uppdateras varje dag. |
|[NOAA global Forecast system (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 dagars amerikanska väder prognoser från NOAA. Uppdateras varje dag. |

### <a name="calendar-data"></a>Kalender data

|Datamängd         | Notebooks     | Description                                    |
|----------------|---------------|------------------------------------------------|
|[Offentliga helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Världs omfattande offentliga helgdags data som täcker 41 länder eller regioner från 1970 till 2099. Inkluderar land och om de flesta har betalat tids gräns. |

## <a name="access-to-datasets"></a>Åtkomst till data uppsättningar  
Med ett Azure-konto kan du komma åt öppna data uppsättningar med hjälp av kod eller via Azure-tjänstens gränssnitt. Data är samplacerade med Azures moln beräknings resurser för användning i din Machine Learning-lösning.  

Öppna data uppsättningar är tillgängliga via Azure Machine Learning användar gränssnitt och SDK. Öppna data uppsättningar innehåller också Azure Notebooks och Azure Databricks antecknings böcker som du kan använda för att ansluta data till Azure Machine Learning och Azure Databricks. Data uppsättningar kan också nås via en python SDK. 

Du behöver dock inget Azure-konto för att få åtkomst till öppna data uppsättningar. Du kan komma åt dem från valfri python-miljö med eller utan Spark.

## <a name="request-or-contribute-datasets"></a>Begär ande eller Contribute-datauppsättningar

Om du inte kan hitta de data som du vill ha, kan du skicka e-post till [en data uppsättning](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) eller [bidra med en data uppsättning](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Nästa steg
* [Exempel på Notebook](samples.md)
* [Självstudie: Regressions modellering med NY taxi data](../machine-learning/tutorial-auto-train-models.md?context=azure%252fopen-datasets%252fcontext%252fopen-datasets-context)
* [Python SDK för öppna data uppsättningar](/python/api/azureml-opendatasets/)
