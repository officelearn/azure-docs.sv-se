---
title: "Exempel ytterligare data datakällanslutningar möjligt med Azure Machine Learning förberedelse av data | Microsoft Docs"
description: "Det här dokumentet innehåller en uppsättning exempel på datakällanslutningar för data som är möjliga med Azure Machine Learning förberedelse av data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 3ffe0e385f9dd71d8341305f42ceb10e0ea49af4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
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

Skapa ett nytt skript-baserad dataflöde. Använd sedan följande skript för att läsa in data från data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Läs in Azure Cosmos DB data till förberedelse av data

Skapa ett nytt skript-baserad dataflöde och sedan använda följande skript för att läsa in data från Azure Cosmos DB. (Bibliotek måste installeras först. Mer information finns i föregående referensdokumentet som vi länkar till.)

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
