---
title: Exempel ytterligare data datakällanslutningar möjligt med Azure Machine Learning förberedelse av data | Microsoft Docs
description: Det här dokumentet innehåller en uppsättning exempel på datakällanslutningar för data som är möjliga med Azure Machine Learning förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: b87d88a5bb7846894c425e701a073707ddd1f3be
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831197"
---
# <a name="sample-of-custom-source-connections-python"></a>Exempel på anpassade källan anslutningar (Python) 
Innan du läser den här bilagan läsa [Python utökningsbarhet översikt](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Läs in data från data.world

### <a name="prerequisites"></a>Förutsättningar

#### <a name="register-yourself-at-dataworld"></a>Registrera dig på data.world
Du behöver en API-token från webbplatsen data.world.

#### <a name="install-dataworld-library"></a>Installera data.world bibliotek

Öppna Azure Machine Learning arbetsstationen kommandoradsgränssnittet genom att välja **filen** > **öppna kommandoradsgränssnittet**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Kör därefter `dw configure` på kommandoraden som efterfrågar din token. När du anger din token en .dw / katalogen skapas i arbetskatalogen och din token lagras där.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Du bör nu kunna importera data.world bibliotek.

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
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos-DB som anslutning till en datakälla
Ett exempel på Azure Cosmos DB som en dataanslutning läsa [belastningen Azure Cosmos DB som en anslutning till en datakälla](data-prep-load-azure-cosmos-db.md)
