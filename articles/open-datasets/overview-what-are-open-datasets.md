---
title: Vad är öppna datauppsättningar? Granskad offentliga datauppsättningar
titleSuffix: Azure Open Datasets (preview)
description: Läs mer om Azure öppna datauppsättningar (förhandsversion), granskade datauppsättningar från den offentliga domänen som är klara att använda i machine learning och Analyslösningar. Datauppsättningar innehåller offentliga data, till exempel väder, inventering, helgdagar och plats för att utöka förutsägande lösningar.
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 439c25363d4c3b24b391b49811d3806c98171034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026951"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>Vad är Azure öppna datauppsättningar (förhandsversion) och hur kan du använda dem?

[Azure öppna datauppsättningar](https://azure.microsoft.com/services/open-datasets/) är granskad offentliga datauppsättningar som du kan använda för att lägga till scenariot-specifika funktioner i machine learning-lösningar för mer exakta modeller. Öppna datauppsättningar finns i molnet på Microsoft Azure och är lätt tillgängliga för Azure Databricks, Machine Learning-tjänsten och Machine Learning Studio. Du kan också använda datauppsättningarna via API: er och använda dem i andra produkter, t.ex Power BI och Azure Data Factory.

Datauppsättningar är allmän egendom data för väder, inventering, helger, allmän säkerhet och plats som hjälper dig att träna machine learning-modeller och berika förutsägande lösningar. Du kan också dela dina offentliga datauppsättningar i Azure öppna datauppsättningar. 

![Azure öppna datauppsättningar-komponenter](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Granskad, förberedda datauppsättningar
Granskad öppna offentliga datauppsättningar i Azure öppna datauppsättningar är optimerade för användning i machine learning-arbetsflöden. 

Dataexperter ofta att tillbringa merparten av sin tid för rensning och förbereda data för avancerade analyser. Öppna datauppsättningar kopieras till Azure-molnet och förbearbetats för att spara tid. Med jämna mellanrum data hämtas från källorna som parsas till ett strukturerat format med en FTP-anslutning till National Oceanic and Atmospheric Administration (NOAA), och sedan berikats efter behov med funktioner som postnummer eller platsen för den den närmaste väder station.

Datauppsättningar är cohosted med molnet beräkning i Azure och åtkomst och bearbetning enklare.  

Följande är exempel på datauppsättningar som är tillgängliga. 

### <a name="weather-data"></a>Weather-data
 
|Datauppsättning         | Notebooks     | Beskrivning                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA integrerade Surface-Data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | I hela världen per timme väderdata från amerikanska NOAA med bäst spatial täckning i Nordamerika, Europa, Australien och delar av Asien. Uppdateras dagligen. |
|[NOAA globala Prognostisera System (offentlig sektor GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-dagars USA per timme väderprognos data från amerikanska NOAA. Uppdateras dagligen. |

### <a name="calendar-data"></a>Kalenderdata

|Datauppsättning         | Notebooks     | Beskrivning                                    |
|----------------|---------------|------------------------------------------------|
|[Helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | I hela världen helgdag data, som täcker 41 länder eller regioner från 1970 till 2099. Innehåller land och om de flesta har lönat tid. |

## <a name="access-to-datasets"></a>Åtkomst till datauppsättningar  
Öppna datauppsättningar med hjälp av kod med en Azure-konto du kan komma åt eller via Azure-tjänsten gränssnitt. Data är samordnat med Azure-molnberäkningsresurser för användning i din machine learning-lösning.  

Öppna datauppsättningar ger Azure-datorer och Azure Databricks-anteckningsböcker som du kan använda för att ansluta data till Azure Machine Learning-tjänsten och Azure Databricks. Datauppsättningar kan även nås via en Python-SDK. 

Men behöver du ett Azure-konto till öppna datauppsättningar; de kan vara åtkomst i alla Python-miljöer utan att eller Spark.

## <a name="request-or-contribute-datasets"></a>Begär eller bidra med datauppsättningar

Om du inte kan hitta data du vill kan skicka e-post till [begär en datauppsättning](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) eller [bidra med en datauppsättning](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Nästa steg
* [Exempel-anteckningsbok](samples.md)
* [Självstudie: Regression modellering med NY taxi-data](tutorial-opendatasets-automl.md)
* [Python SDK för öppna datauppsättningar](https://aka.ms/open-datasets-api)
