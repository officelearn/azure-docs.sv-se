---
title: Exempel Jupyter-anteckningsböcker med hjälp av en NOAA öppna datauppsättning
titleSuffix: Azure Open Datasets
description: Använd exempel Jupyter-anteckningsböcker för öppna datauppsättningar i Azure och lär dig att läsa in öppna datauppsättningar och använda dem för att utöka demodata. Tekniken omfattar användning av Spark och Pandas för att bearbeta data.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: c1b86199f13454f4785a6737b25e489d45dd53f8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026846"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exempel Jupyter-anteckningsböcker visar hur du utöka data med öppna datauppsättningar 
Exempel Jupyter-anteckningsböcker för Azure öppna datauppsättningar visar hur du hämtar öppna datauppsättningar och använda dem för att utöka demodata. Tekniken omfattar användning av Apache Spark och Pandas för att bearbeta data.

>[!IMPORTANT]
>När du arbetar i en icke-Spark-miljö kan öppna datauppsättningar hämtar bara en månad med data i taget med vissa klasser för att undvika MemoryError med stora datauppsättningar.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Läsa in data för NOAA integrerad Surface databasen (ISD) 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Läsa in en senaste månad från weather-data i en Pandas-dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Lär dig mer om att läsa in historiska väderdata till dina favorit Pandas-dataframe. |
|[Läsa in en senaste månad från weather-data i en Spark dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Lär dig mer om att läsa in historiska väderdata till dina favorit Spark dataframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Ansluta till demodata med NOAA ISD data 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Ansluta till demodata med väderdata - Pandas ](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Ansluta till en datauppsättning för 1 månad demo av sensor platser med väder värdena i en Pandas-dataframe.  |
|[Ansluta till demodata med weather-data – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Ansluta till en datauppsättning för demonstration av sensor platser med väder värdena i en Spark dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Ansluta till NYC taxi-data med NOAA ISD data 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Taxi resedata berikats med väderdata - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Läs in NYC grön taxi-data (över 1 månad) och berika med weather-data i en Pandas-dataframe. Det här exemplet åsidosätter metoden `get_pandas_limit` och balanserar belastningen dataprestanda med mängden data.|
|[Taxi resedata berikats med weather-data – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Läs in NYC grön taxi-data och berika med weather-data i Spark dataframe.  |

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Regression modellering med automatiserade machine learning och en öppen datauppsättning](tutorial-opendatasets-automl.md)
* [Python SDK för öppna datauppsättningar](https://aka.ms/open-datasets-api)
* [Azure öppna datauppsättningar catalog](https://azure.microsoft.com/services/open-datasets/catalog/)
