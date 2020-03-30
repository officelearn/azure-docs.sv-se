---
title: Datalager som stöds i Azure Data Share
description: Lär dig mer om de datalager som stöds för användning av Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501804"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Datalager som stöds i Azure Data Share

Azure Data Share ger öppen och flexibel datadelning, inklusive möjligheten att dela från och till olika datalager. Dataleverantörer kan dela data från en typ av datalager, och deras datakonsumenter kan välja vilket datalager som ska ta emot data till. 

I den här artikeln får du lära dig mer om den omfattande uppsättningen Azure-datalager som stöds i Azure Data Share. Du kan också hitta information om kombinationer av datalager som kan utnyttjas av dataleverantörer och datakonsumenter. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Vilka datalager stöds i Azure Data Share? 

I tabellen nedan beskrivs de datakällor som stöds för Azure Data Share. 

| Datalager | Ögonblicksbildbaserad delning | På plats dela 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Offentlig förhandsversion | |
| Azure Synapse Analytics (tidigare Azure SQL DW) |Offentlig förhandsversion | |
| Azure-datautforskaren | |Offentlig förhandsversion |

## <a name="data-store-support-matrix"></a>Matris för stödmatris för datalager

Azure Data Share ger datakonsumenter flexibilitet när de beslutar om ett datalager att acceptera data i. Data som delas från Azure SQL Database kan till exempel tas emot i Azure Data Lake Store Gen2, Azure SQL Database eller Azure Synapse Analytics. Kunderna kan välja vilket format som ska tas emot när de konfigurerar en mottagen dataresurs. 

I tabellen nedan beskrivs olika kombinationer och val som datakonsumenterna har när de accepterar och konfigurerar sin dataresurs. Mer information om hur du konfigurerar datauppsättningsmappningar finns i [konfigurera datauppsättningsmappningar](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (tidigare Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Dela från ett lagringskonto
Azure Data Share stöder delning av filer, mappar och filsystem från Azure Data Lake Gen1 och Azure Data Lake Gen2. Den stöder också delning av blobbar, mappar och behållare från Azure Blob Storage. Endast blockblob stöds för närvarande. När mappar delas i ögonblicksbildbaserad delning kan datakonsumenten välja att göra en fullständig kopia av resursdata eller utnyttja inkrementell ögonblicksbildkapacitet för att bara kopiera nya eller uppdaterade filer. Befintliga filer med samma namn skrivs över.

## <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Azure Data Share stöder delning av tabeller eller vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL DW). Datakonsumenter kan välja att acceptera data i Azure Data Lake Store Gen2 eller Azure Blob Storage som csv- eller parkettfil. Observera att som standard är filformat csv. Datakonsumenten kan välja att ta emot data i parkettformat om så önskas. Detta kan göras i datauppsättningsmappningsinställningarna när du tar emot data. 

När du accepterar data till Azure Data Lake Store Gen2 eller Azure Blob Storage skriver fullständiga ögonblicksbilder över innehållet i målfilen. 

En datakonsument kan välja att ta emot data i en tabell som de själva väljer. I det här fallet om måltabellen inte redan finns, azure data share skapar SQL-tabellen med källschemat. Om det redan finns en måltabell med samma namn kommer den att tas bort och skrivas över med den senaste fullständiga ögonblicksbilden. När måltabellen mappas kan ett alternativt schema och tabellnamn anges. Inkrementella ögonblicksbilder stöds för närvarande inte. 

Delning från SQL-baserade källor har förutsättningar relaterade till brandväggsregler och behörigheter. Mer information finns i avsnittet om förordnande av delningen av [din data.](share-your-data.md)

## <a name="share-from-azure-data-explorer"></a>Dela från Azure Data Explorer
Azure Data Share stöder möjligheten att dela databaser på plats från Azure Data Explorer-kluster. Dataprovidern kan dela på databas- eller klusternivå. När datakonsumenten delas på databasnivå kan de bara komma åt de specifika databaser som delas av uppgiftslämnaren. När datakonsumenten delas på klusternivå kan de komma åt alla databaser från providerns kluster, inklusive eventuella framtida databaser som skapats av dataleverantören.

För att komma åt delade databaser måste datakonsumenten ha ett eget Azure Data Explorer-kluster. Datakonsumentens Azure Data Explorer-kluster måste hitta i samma Azure-datacenter som dataproviderns Azure Data Explorer-kluster. När delningsrelation upprättas skapar Azure Data Share en symbolisk länk mellan providern och konsumentens Azure Data Explorer-kluster.

Azure Data Explorer stöder två lägen för datainmatning: batch och direktuppspelning. Data som tas emot från batch i den delade databasen visas mellan några sekunder till några minuter på datakonsumentsidan. Det kan ta upp till 24 timmar innan datakonsumentsidan visas i upp till 24 timmar. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du börjar dela data fortsätter du till [att dela din datahandledning.](share-your-data.md)
