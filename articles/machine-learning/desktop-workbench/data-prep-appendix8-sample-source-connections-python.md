---
title: Exempel ytterligare data datakällanslutningar möjligt med Azure Machine Learning databearbetning | Microsoft Docs
description: Det här dokumentet innehåller en uppsättning exempel på käll-dataanslutningar som är möjliga med Azure Machine Learning förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ed0e6bc4c506535fcb679fc2b4015a61274a77f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967701"
---
# <a name="sample-of-custom-source-connections-python"></a>Exempel på anpassade datakällanslutningar (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Innan du läser den här bilagan läsa [Python extensibility översikt](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Läsa in data från data.world

### <a name="prerequisites"></a>Förutsättningar

#### <a name="register-yourself-at-dataworld"></a>Registrera dig på data.world
Behöver du en API-token från data.world-webbplatsen.

#### <a name="install-dataworld-library"></a>Installera data.world-biblioteket

Öppna kommandoradsgränssnittet i Azure Machine Learning Workbench genom att välja **filen** > **öppna kommandoradsgränssnittet**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Kör `dw configure` på kommandoraden, som uppmanar dig att din token. När du anger din token, en .dw / directory skapas i arbetskatalogen och din token lagras där.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Du bör nu kunna importera data.world-bibliotek.

#### <a name="load-data-into-data-preparation"></a>Läs in data till förberedelse av data

Skapa en transformering transformera dataflöde (skript). Använd sedan följande skript för att läsa in data från data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos DB som en anslutning till datakälla
Ett exempel på Azure Cosmos DB som en dataanslutning läsa [belastningen Azure Cosmos DB som en anslutning till en datakälla](data-prep-load-azure-cosmos-db.md)
