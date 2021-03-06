---
title: Översikt över avdelningens kontroll-koppling
description: Den här artikeln beskriver de olika data lager och-funktioner som stöds i avdelningens kontroll
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 26efa840bacd3ee542816cf861cf2ef586b0582f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780220"
---
# <a name="supported-data-stores"></a>Datalager som stöds

Avdelningens kontroll har stöd för följande data lager. Klicka på varje data lager för att lära dig vilka funktioner som stöds och motsvarande konfigurationer i detalj.

## <a name="purview-data-sources"></a>Avdelningens kontroll data källor

|**Kategori**|  **Data lager**  |**Extrahering av metadata**|**Fullständig sökning**|**Stegvis skanning**|**Sökning i omfång**|**Klassificering**|**Ursprung**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure-datautforskaren](register-scan-azure-data-explorer.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Ja| Ja| Nej| Ja| Ja| Ja|
||[Hanterad Azure SQL Database-instans](register-scan-azure-sql-database-managed-instance.md)|Ja| Ja| Nej| Ja| Ja| Ja|
||[Azure Synapse Analytics (tidigare SQL DW)](register-scan-azure-synapse-analytics.md)|Ja| Ja| Nej| Ja| Ja| Ja|
|Databas|[SQL Server](register-scan-on-premises-sql-server.md)|Ja| Ja| Nej| Ja| Ja| Ja|
||[Teradata (förhandsversion)](register-scan-teradata-source.md)|Ja| Ja| Nej| Nej| Nej| Ja|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ja| Ja| Nej| Nej| Nej| Ja|

## <a name="next-steps"></a>Nästa steg

- [Registrera och skanna Azure Blob Storage-källa](register-scan-azure-blob-storage-source.md)