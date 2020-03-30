---
title: ta med fil
description: ta med fil
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559309"
---
| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av den självvärderade integrationskörningen för att ansluta till dataförflyttningstjänster i Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Krävs av den självvärderade integrationskörningen för att ansluta till datafabrikstjänsten. |
| `download.microsoft.com`    | 443            | Krävs av den självvärderade integrationskörningen för att hämta uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över att konfigurera den här domänen. |
| `*.core.windows.net`          | 443            | Används av den självvärderade integrationskörningen för att ansluta till Azure-lagringskontot när du använder den [mellanslagna](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) kopieringsfunktionen. |
| `*.database.windows.net`      | 1433           | Krävs endast när du kopierar från eller till Azure SQL Database eller Azure SQL Data Warehouse och tillval annars. Använd funktionen mellan stegvis kopia för att kopiera data till SQL Database eller SQL Data Warehouse utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Krävs endast när du kopierar från eller till Azure Data Lake Store och tillval annars. |
