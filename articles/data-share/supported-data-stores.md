---
title: Data lager som stöds i Azure Data Share
description: Lär dig mer om de data lager som stöds för användning av Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e0daa2b02c16d8d5a65b5e7e0f983a4f47181d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84635976"
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
| Azure SQL Database |Offentlig för hands version | |
| Azure Synapse Analytics (tidigare Azure SQL DW) |Offentlig för hands version | |
| Azure-datautforskaren | |Offentlig för hands version |

## <a name="data-store-support-matrix"></a>Support mat ris för data lager

Azure Data Share ger flexibilitet för data konsumenter när de bestämmer sig för ett data lager för att ta emot data i. Till exempel kan data som delas från Azure SQL Database tas emot i Azure Data Lake Store Gen2, Azure SQL Database eller Azure Synapse Analytics. Kunder kan välja vilket format de vill ta emot data i när de konfigurerar en mottagen data resurs. 

Tabellen nedan innehåller information om olika kombinationer och alternativ som data konsumenter har när de godkänner och konfigurerar data resursen. Mer information om hur du konfigurerar mappningar för data uppsättningar finns i [så här konfigurerar du mappningar för data uppsättningar](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Azure-datautforskaren
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (tidigare Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure-datautforskaren |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Dela från ett lagrings konto
Azure data sharing stöder delning av filer, mappar och fil system från Azure Data Lake gen1 och Azure Data Lake Gen2. Det stöder också delning av blobbar, mappar och behållare från Azure Blob Storage. Endast Block-Blob stöds för närvarande. När fil system, behållare eller mappar delas i ögonblicks bilds-baserad delning, kan data konsumenten välja att göra en fullständig kopia av delnings data eller utnyttja en stegvis ögonblicks bilds kapacitet för att bara kopiera nya eller uppdaterade filer. En stegvis ögonblicks bild baseras på filernas senaste ändrings tid. Befintliga filer med samma namn kommer att skrivas över.

## <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Azure Data Share stöder delning av tabeller eller vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL DW). Data konsumenter kan välja att acceptera data till Azure Data Lake Store Gen2 eller Azure Blob Storage som CSV-eller Parquet-fil. Observera att fil formaten som standard är CSV. Data konsumenten kan välja att ta emot data i Parquet-format om du vill. Detta kan göras i inställningarna för data uppsättnings mappning när data tas emot. 

När du accepterar data i Azure Data Lake Store Gen2 eller Azure Blob Storage skrivs innehållet i målfilen över av fullständiga ögonblicks bilder. 

En data konsument kan välja att ta emot data i en valfri tabell. I det här scenariot skapar Azure Data Share SQL-tabellen med käll schemat om mål tabellen inte redan finns. Om det redan finns en mål tabell med samma namn kommer den att tas bort och skrivas över med den senaste fullständiga ögonblicks bilden. När du mappar mål tabellen kan ett alternativt schema och tabell namn anges. Stegvisa ögonblicks bilder stöds inte för närvarande. 

Att dela från SQL-baserade källor har krav som rör brand Väggs regler och-behörigheter. Mer information finns i avsnittet krav i artikeln [dela din data](share-your-data.md) .

## <a name="share-from-azure-data-explorer"></a>Dela från Azure Datautforskaren
Azure Data Share stöder möjligheten att dela databaser på plats från Azure Datautforskaren-kluster. Data leverantören kan dela på databas-eller kluster nivå. Vid delning på databas nivå kommer data konsumenten bara att kunna komma åt de enskilda databaserna som delas av data leverantören. Vid delning på kluster nivå kan data konsumenten komma åt alla databaser från leverantörens kluster, inklusive alla framtida databaser som skapats av data leverantören.

För att få åtkomst till delade databaser måste data konsumenten ha ett eget Azure Datautforskaren-kluster. Data konsumentens Azure Datautforskaren-kluster måste hitta i samma Azure-datacenter som data leverantörens Azure Datautforskaren-kluster. När du har upprättat relationen skapas en symbolisk länk mellan providern och användarens Azure Datautforskaren-kluster vid Azure-dataresursen.

Azure Datautforskaren stöder två lägen för data inmatning: batch och strömning. Data som tas emot från batch i den delade databasen kommer att visas mellan några sekunder till några minuter på data konsument sidan. Data som tas emot från strömningen kan ta upp till 24 timmar innan de visas på data konsument sidan. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
