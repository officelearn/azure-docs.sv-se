---
title: ta med fil
description: ta med fil
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285609"
---
| Domän namn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av integration runtime med egen värd för att ansluta till data flytt tjänster i Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. |
| `download.microsoft.com`    | 443            | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över detta. |
| `*.core.windows.net`          | 443            | Används av integration runtime med egen värd för att ansluta till Azure Storage-kontot när du använder funktionen för [mellanlagrad kopiering](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Valfritt Krävs när du kopierar från eller till Azure SQL Database eller Azure SQL Data Warehouse. Använd funktionen för mellanlagrad kopiering för att kopiera data till Azure SQL Database eller Azure SQL Data Warehouse utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Valfritt Krävs när du kopierar från eller till Azure Data Lake Store. |
