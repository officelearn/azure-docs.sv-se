---
title: Azure Migrate support mat ris
description: Innehåller en översikt över support inställningar och begränsningar för tjänsten Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: 49bd193303255cdf7d18fd5da9dec8d84c50a829
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019192"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate support mat ris

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. I den här artikeln sammanfattas allmänna support inställningar och begränsningar för Azure Migrate scenarier och distributioner.


## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: Med den här versionen kan du skapa nya Azure Migrate projekt, identifiera lokala utvärderingar och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md#azure-migrate-new-version).
- **Tidigare version**: För kunder som använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte skapa nya Azure Migrate projekt eller utföra nya identifieringar.

## <a name="supported-migration-scenarios"></a>Scenarier för migrering som stöds

Tabellen sammanfattar migrerings scenarier som stöds.

**Distribution** | **Information*** 
--- | --- 
**Lokal utvärdering** | Utvärdera lokala arbets belastningar och data som körs på virtuella VMware-datorer och virtuella Hyper-V-datorer. Utvärdera med hjälp av Azure Migrate Server utvärdering och Microsoft Data Migration Assistant (DMA) och verktyg från tredje part som omfattar Cloudamize, samhyres teknik och Turbonomic-Server.
**Lokal migrering till Azure** | Migrera arbets belastningar och data som körs på fysiska servrar, virtuella VMware-datorer, virtuella Hyper-V-datorer och på AWS/GCP-instanser till Azure. Migrera med hjälp av Azure Migrate Server Assessment and Azure Database Migration Service (DMS) och med verktyg från tredje part som innehåller Carbonite och CorentTech.

Stöd för ett speciellt verktyg sammanfattas på följande sätt.

**Verktyg** | **Bedömning/migrering** | **Detaljer**
--- | --- | ---
Utvärdering av Azure Migrate Server | Utvärdering | Prova Server utvärdering för [Hyper-V](tutorial-prepare-hyper-v.md) och [VMware](tutorial-prepare-vmware.md).
Cloudamize | Utvärdering | [Läs mer](https://www.cloudamize.com/platform#tab-0).
CorentTech | Utvärdering | [Läs mer](https://www.corenttech.com/).
Turbonomic | Utvärdering | [Läs mer](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migrering av Azure Migrate Server | Migrering | Prova Server migrering för [Hyper-V](tutorial-migrate-hyper-v.md) och [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrering | [Läs mer](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migrering | [Läs mer](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Detaljer**
--- | ---
Subscription | Du kan ha ett enda Azure Migrate-projekt i en prenumeration.
Azure-behörigheter | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
VMwares virtuella datorer  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt.
Hyper-V:s virtuella datorer | Utvärdera upp till 10 000 virtuella Hyper-V-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.


## <a name="vmware-assessment-and-migration"></a>VMware-bedömning och migrering

[Granska](migrate-support-matrix-vmware.md) support matrisen för Azure Migrate Server utvärdering och Server migration för virtuella VMware-datorer.

## <a name="hyper-v-assessment-and-migration"></a>Utvärdering och migrering av Hyper-V

[Granska](migrate-support-matrix-hyper-v.md) den Azure Migrate Server utvärderingen och stöd matrisen för Server migration för virtuella Hyper-V-datorer.


## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella VMware-datorer](tutorial-assess-vmware.md) för migrering.
- [Utvärdera virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md) för migrering.

