---
title: Azure Synapse Analytics delad metadatamodell
description: Azure Synapse Analytics gör det möjligt för olika beräkningsmotorer på arbetsytan att dela databaser och tabeller mellan sina Spark-pooler (förhandsversion), SQL on-demand-motor (förhandsversion) och SQL-pooler.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424120"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Delade metadata för Azure Synapse Analytics

Azure Synapse Analytics gör det möjligt för olika beräkningsmotorer på arbetsytan att dela databaser och tabeller mellan sina Spark-pooler (förhandsversion), SQL on-demand-motor (förhandsversion) och SQL-pooler.

[!INCLUDE [preview](../includes/note-preview.md)]



Delningen stöder det så kallade moderna datalagermönstret och ger arbetsytans SQL-motorer åtkomst till databaser och tabeller som skapats med Spark. Det gör det också möjligt för SQL-motorer att skapa sina egna objekt som inte delas med de andra motorerna.

## <a name="support-the-modern-data-warehouse"></a>Stöd det moderna datalagret

Den delade metadatamodellen stöder det moderna informationslagermönstret på följande sätt:

1. Data från datasjön förbereds och struktureras effektivt med Spark genom att lagra de förberedda data i (eventuellt partitionerade) Parkett-stödda tabeller som finns i eventuellt flera databaser.

2. Spark skapade databaser och alla deras tabeller blir synliga i någon av Azure Synapse-arbetsyte spark-poolinstanserna och kan användas från alla Spark-jobb. Den här funktionen omfattas av [behörigheterna](#security-model-at-a-glance) eftersom alla Spark-pooler på en arbetsyta har samma underliggande katalogmetabutik.

3. Spark skapade databaser och deras Parkett-backed tabeller blir synliga i arbetsytan SQL on-demand motor. [Databaser skapas](database.md) automatiskt i SQL-metadata på begäran och både de [externa och hanterade tabeller som](table.md) skapas av ett Spark-jobb görs tillgängliga som externa tabeller i SQL-metadata på begäran i `dbo` schemat för motsvarande databas. <!--For more details, see [ADD LINK].-->

4. Om det finns SQL-poolinstanser på arbetsytan som har sin metadatasynkronisering aktiverad <!--[ADD LINK]--> Eller om en ny SQL-poolinstans skapas med metadatasynkroniseringen aktiverad mappas de Spark-skapade databaserna och deras parettstödda tabeller automatiskt till SQL-pooldatabasen enligt beskrivningen i [Azure Synapse Analytics delade databas](database.md).

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Objektsynkronisering sker asynkront. Objekt kommer att ha en liten fördröjning på några sekunder tills de visas i SQL-kontexten. När de visas kan de efterfrågas, men inte uppdateras eller ändras av SQL-motorer som har åtkomst till dem.

## <a name="which-metadata-objects-are-shared"></a>Vilka metadataobjekt som delas

Med Spark kan du skapa databaser, externa tabeller, hanterade tabeller och vyer. Eftersom Spark-vyer kräver en Spark-motor för att bearbeta det definierande Spark SQL-uttrycket och inte kan bearbetas av en SQL-motor, delas endast databaser och deras externa och hanterade tabeller som använder parettlagringsformat med SQL-jobben på arbetsytan. Spark-vyer delas bara mellan Spark-poolinstanserna.

## <a name="security-model-at-a-glance"></a>Säkerhetsmodell i korthet

Spark-databaserna och tabellerna, tillsammans med deras synkroniserade representationer i SQL-motorerna, är säkrade på den underliggande lagringsnivån. När tabellen efterfrågas av någon av de motorer som frågelämnaren har rätt att använda skickas frågelämnarens säkerhetsobjekt till de underliggande filerna. Behörigheter kontrolleras på filsystemnivå.

Mer information finns i [den delade databasen För Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Ändra underhåll

Om ett metadataobjekt tas bort eller ändras med Spark, plockas ändringarna upp och sprids till SQL on-demand-motorn och SQL-poolerna som har objekten synkroniserade. Synkronisering är asynkron och ändringar återspeglas i SQL-motorerna efter en kort fördröjning.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Synapse Analytics delade metadatadatabaser](database.md)
- [Läs mer om Azure Synapse Analytics delade metadatatabeller](table.md)

