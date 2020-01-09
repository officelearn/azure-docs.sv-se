---
title: Data lager som stöds i Azure Data Share
description: Lär dig mer om de data lager som stöds för användning av Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438693"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Data lager som stöds i Azure Data Share

Azure Data Share ger öppen och flexibel data delning, inklusive möjligheten att dela från och till olika data lager. Data leverantörer kan dela data från en typ av data lager och deras data konsumenter kan välja vilket data lager som ska ta emot data i. 

I den här artikeln får du lära dig mer om den omfattande uppsättningen Azure-datalager som stöds i Azure Data Share. Du kan också hitta information om kombinationer av data lager som kan utnyttjas av data leverantörer och data konsumenter. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Vilka data lager stöds i Azure Data Share? 

Tabellen nedan innehåller information om de data källor som stöds för Azure Data Share. 

| Datalager | Ögonblicks bild baserad delning | Delning på plats 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Offentlig förhandsversion | |
| Azure Synapse Analytics (tidigare Azure SQL DW) |Offentlig förhandsversion | |
| Datautforskaren i Azure | |[Begränsad för hands version](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Support mat ris för data lager

Azure Data Share ger flexibilitet för data konsumenter när de bestämmer sig för ett data lager för att ta emot data i. Till exempel kan data som delas från Azure SQL Database tas emot i Azure Data Lake Store Gen2, Azure SQL Database eller Azure Synapse Analytics. Kunder kan välja vilket format de vill ta emot data i när de konfigurerar en mottagen data resurs. 

Tabellen nedan innehåller information om olika kombinationer och alternativ som data konsumenter har när de godkänner och konfigurerar data resursen. Mer information om hur du konfigurerar mappningar för data uppsättningar finns i [så här konfigurerar du mappningar för data uppsättningar](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure SQL-Data Lake gen1 | Azure SQL-Data Lake Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
