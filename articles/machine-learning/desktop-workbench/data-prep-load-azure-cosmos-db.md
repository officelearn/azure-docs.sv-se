---
title: "Ansluta till Azure Cosmos DB som en datakälla i Azure Machine Learning-arbetsstationen | Microsoft Docs"
description: "Det här dokumentet innehåller ett exempel på hur du ansluter till Azure Cosmos DB via Azure Machine Learning arbetsstationen"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: d36b394a528dc4bc1b6e0a9e0e5dbde728cbee1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Ansluter till Azure Cosmos DB som en datakälla
Den här artikeln innehåller en python exemplet kan du ansluta till Cosmos-DB i Azure Machine Learning-arbetsstationen.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Läs in Azure Cosmos DB data till förberedelse av data

Skapa ett nytt skript-baserad dataflöde och sedan använda följande skript för att läsa in data från Azure Cosmos DB. 

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

## <a name="other-data-source-connections"></a>Andra anslutningar till datakällor
Andra exempel läsa [exempel ytterligare data datakällanslutningar](data-prep-appendix8-sample-source-connections-python.md)
