---
title: Exempel på jupyter-anteckningsböcker med NOAA-data
titleSuffix: Azure Open Datasets
description: Använd exempelvis Jupyter-anteckningsböcker för Azure Open Dataset för att lära dig hur du läser in öppna datauppsättningar och använder dem för att berika demodata. Tekniker inkluderar användning av Spark och Pandor för att bearbeta data.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606150"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exempel på jupyter-anteckningsböcker visar hur du berikar data med öppna datauppsättningar 
Exemplet Jupyter-anteckningsböcker för Azure Open Dataset visar hur du läser in öppna datauppsättningar och använder dem för att berika demodata. Tekniker inkluderar användning av Apache Spark och Pandor för att bearbeta data.

>[!IMPORTANT]
>När du arbetar i en icke-Spark-miljö tillåter Open Dataset nedladdning endast en månad data åt gången med vissa klasser för att undvika MemoryError med stora datauppsättningar.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Ladda IN ISD-data (Integrated Surface Database) 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Ladda en månad med väderdata i en Pandas-dataram](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Lär dig hur du läser in historiska väderdata i din favoritpandas dataram. |
|[Ladda en månad med väderdata i en Spark-dataram](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Lär dig hur du läser in historiska väderdata i din favorit spark-dataram.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Gå med i demodata med NOAA ISD-data 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Gå med i demodata med väderdata - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Gå med i en 1-månaders demodatauppsättning av sensorplatser med väderavläsningar i en Pandas-dataram.  |
|[Gå med i demodata med väderdata – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Gå med i en demodatauppsättning med sensorplatser med väderavläsningar i en Spark-dataram. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Gå med i NYC-taxidata med NOAA ISD-data 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Taxi resa data berikad med väderdata - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Ladda NYC grön taxi data (över 1 månad) och berika den med väderdata i en Pandas dataframe. Det här exemplet `get_pandas_limit` åsidosätter metoden och balanserar datainläsningsprestanda med mängden data.|
|[Taxi resa data berikad med väderdata - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Ladda NYC grön taxi data och berika den med väderdata, i Spark dataram.  |

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Regressionsmodellering med automatiserad maskininlärning och en öppen datauppsättning](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK för öppna datauppsättningar](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure Open Datasets-katalog](https://azure.microsoft.com/services/open-datasets/catalog/)
