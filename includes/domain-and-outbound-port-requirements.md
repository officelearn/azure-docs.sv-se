---
title: inkludera fil
description: inkludera fil
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596130"
---
| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av integration runtime med egen värd för att ansluta till data flytt tjänster i Azure Data Factory. |
| `{datafactory}.{region}.datafactory.azure.net`<br> eller `*.frontend.clouddatahub.net` | 443            | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. <br>För nya Data Factory som skapats ska du hitta det fullständiga domän namnet från din egen värd Integration Runtime nyckel som är i formatet {DataFactory}. {region}. DataFactory. Azure. net. Om du inte ser det fullständiga domän namnet i den egna värdbaserade integrerings nyckeln använder du *. frontend.clouddatahub.net i stället för gammal data fabrik. |
| `download.microsoft.com`    | 443            | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över konfigurationen av den här domänen. |
| `*.core.windows.net`          | 443            | Används av integration runtime med egen värd för att ansluta till Azure Storage-kontot när du använder funktionen för [mellanlagrad kopiering](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Krävs endast när du kopierar från eller till Azure SQL Database eller Azure Synapse Analytics och valfritt annat. Använd funktionen för mellanlagrad kopiering för att kopiera data till SQL Database-eller Synapse-analys utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Krävs endast när du kopierar från eller till Azure Data Lake Store och valfritt annat. |
