---
title: Azure Synapse Analytics-modell för delad metadata
description: Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och tabeller mellan dess Spark-pooler (för hands version), SQL på begäran-motor (för hands version) och SQL-pooler.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7bd44a350b3c3eeb723b73b0ec416cdd1c0ec643
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698767"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Delade Azure Synapse Analytics-metadata

Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och tabeller mellan dess Spark-pooler (för hands version) och SQL på begäran-motor (för hands version).

[!INCLUDE [preview](../includes/note-preview.md)]

Delningen har stöd för det s.k. moderna informations lager mönstret och ger arbets ytans SQL-motorer åtkomst till databaser och tabeller som skapats med Spark. Det gör det också möjligt för SQL-motorer att skapa egna objekt som inte delas med andra motorer.

## <a name="support-the-modern-data-warehouse"></a>Stöd för modern informations lager

Den delade metadata-modellen stöder det moderna informations lagrets mönster på följande sätt:

1. Data från data Lake förbereds och struktureras effektivt med Spark genom att lagra de förberedda data i (eventuellt partitionerade) Parquet tabeller som finns i möjligen flera databaser.

2. Spark skapade databaser och alla deras tabeller blir synliga i någon av instanserna i Azure Synapse-arbets ytan Spark-pool och kan användas från alla Spark-jobb. Den här funktionen omfattas av [behörigheterna](#security-model-at-a-glance) eftersom alla Spark-pooler i en arbets yta delar samma underliggande katalog-meta-arkiv.

3. Spark-skapade databaser och deras Parquet tabeller blir synliga i arbets ytans SQL-motor på begäran. [Databaser](database.md) skapas automatiskt i SQL-metadata på begäran, och både de [externa och hanterade tabellerna](table.md) som skapas av ett Spark-jobb blir tillgängliga som externa tabeller i SQL on-demand-metadata i `dbo` schemat för motsvarande databas. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synkronisering av objekt sker asynkront. Objekt får en liten fördröjning på några sekunder tills de visas i SQL-kontexten. När de visas kan de frågas, men inte uppdateras eller ändras inte av de SQL-motorer som har åtkomst till dem.

## <a name="which-metadata-objects-are-shared"></a>Vilka metadata-objekt som delas

Med Spark kan du skapa databaser, externa tabeller, hanterade tabeller och vyer. Eftersom Spark-vyer kräver en spark-motor för att bearbeta definitionen av Spark SQL-instruktionen och inte kan bearbetas av en SQL-motor, delas bara databaser och deras externa och hanterade tabeller som använder lagrings Parquet lagrings utrymme med SQL-motorn för arbets ytan. Spark-vyer delas bara mellan instanserna i Spark-poolen.

## <a name="security-model-at-a-glance"></a>Säkerhets modell snabbt

Spark-databaser och-tabeller, tillsammans med deras synkroniserade representationer i SQL-motorn, skyddas på den underliggande lagrings nivån. När en fråga skickas från någon av de motorer som den som skickar frågan har rätt att använda, skickas frågans säkerhets objekt till de underliggande filerna. Behörigheter kontrol leras på fil system nivå.

Mer information finns i den [delade Azure Synapse Analytics-databasen](database.md).

## <a name="change-maintenance"></a>Ändra underhåll

Om ett metadataobjekt tas bort eller ändras med Spark, hämtas ändringarna och sprids till SQL-motorn på begäran. Synkroniseringen är asynkron och ändringar visas i SQL-motorn efter en kort fördröjning.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om databaserna för delade metadata i Azure Synapse Analytics](database.md)
- [Läs mer om delade metadata-tabeller för Azure Synapse Analytics](table.md)

