---
title: "Använda Azure Data Factory med SQL Data Warehouse | Microsoft Docs"
description: "Tips för att använda Azure Data Factory (ADM) med Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Använda Azure Data Factory med SQL Data Warehouse
Azure Data Factory tillhandahåller en helt hanterad metod för att samordna överföring av data och körning av lagrade procedurer i SQL Data Warehouse.  Detta gör att du kan enkelt inställning och schema komplexa extrahera Transform och Load (ETL) procedurer med SQL Data Warehouse. En fullständig översikt över Azure Data Factory finns på [dokumentation för Azure Data Factory][Azure Data Factory documentation].

## <a name="data-movement"></a>Dataförflyttning
Azure Data Factory gör det möjligt för flytt av data mellan både lokala källor och olika Azure-tjänster.  Övergripande, aktuell integrering med Azure Data Factory har stöd för flytt av data till och från följande platser:

* Azure-blobblagring
* Azure SQL Database
* Lokal SQLServer
* SQLServer på IaaS

Information om hur du ställer in en data kopieringsaktiviteten finns [kopiera data med Azure Data Factory][Copy data with Azure Data Factory]

## <a name="stored-procedures"></a>Lagrade procedurer
 På samma sätt som kan användas för att schemalägga dataöverföring kan Azure Data Factory också användas för att dirigera körningen av lagrade procedurer.  Detta gör mer komplexa pipelines skapas och utökar Azure Data Factory möjlighet att utnyttja dataresurser i SQL Data Warehouse.

## <a name="next-steps"></a>Nästa steg
En översikt över integrering finns [översikt över SQL Data Warehouse-integration][SQL Data Warehouse integration overview].
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

