---
title: Azure Migrate-stödmatris
description: Innehåller en sammanfattning av inställningar för stöd och begränsningar för tjänsten Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811562"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate-stödmatris

Du kan använda den [Azure Migrate-tjänsten](migrate-overview.md) att utvärdera och migrera datorer till Microsoft Azure-molnet. Den här artikeln sammanfattas stöd för allmänna inställningar och begränsningar för Azure Migrate scenarier och distributioner.


## <a name="azure-migrate-versions"></a>Azure Migrate-versioner

Det finns två versioner av Azure Migrate-tjänsten:

- **Aktuell version**: Med den här versionen som du kan skapa nya Azure Migrate-projekt kan identifiera lokal utvärderar och dirigera bedömning och migrering. [Läs mer](whats-new.md#azure-migrate-new-version).
- **Tidigare version**: För kunder som använder den tidigare versionen av Azure Migrate (endast bedömning av lokala virtuella VMware-datorer har stöds), bör du nu använda den aktuella versionen. Du kan inte skapa nya Azure Migrate-projekt eller utföra nya identifieringar i den tidigare versionen.

## <a name="supported-migration-scenarios"></a>Migreringsscenarier som stöds

Tabellen sammanfattar migreringsscenarier som stöds.

**Distribution** | **Information om*** 
--- | --- 
**Lokal utvärdering** | Utvärdera lokala arbetsbelastningar och data som körs på virtuella VMware-datorer och Hyper-V-datorer. Utvärdera med Azure Migrate Server-utvärdering och Microsoft Data Migration Assistant (DMA) samt verktyg från tredje part som innehåller Cloudamize och Corent teknisk Turbonomic Server.
**Lokala migrering till Azure** | Migrera arbetsbelastningar och data som körs på fysiska servrar, virtuella VMware-datorer, Hyper-V-datorer och på AWS/GCP-instanser till Azure. Migrera med hjälp av Azure Migrate Server-utvärdering och Azure Database Migration Service (DMS) och samt med hjälp av verktyg från tredje part som omfattar Carbonite och CorentTech.

Specifika verktygsstöd sammanfattas på följande sätt.

**Verktyg** | **Utvärdering/migrering** | **Detaljer**
--- | --- | ---
Azure Migrate Server-utvärdering | Utvärdering | Prova att använda server-utvärdering för [Hyper-V](tutorial-prepare-hyper-v.md) och [VMware](tutorial-prepare-vmware.md).
Cloudamize | Utvärdering | [Läs mer](https://www.cloudamize.com/platform#tab-0).
CorentTech | Utvärdering | [Läs mer](https://www.corenttech.com/).
Turbonomic | Utvärdering | [Läs mer](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure Migrate-servermigrering | Migrering | Prova att använda servermigrering för [Hyper-V](tutorial-migrate-hyper-v.md) och [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrering | [Läs mer](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migrering | [Läs mer](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate-projekt

**Support** | **Detaljer**
--- | ---
Subscription | Du kan ha en enda Azure Migrate-projekt i en prenumeration.
Azure-behörigheter | Du behöver deltagare eller ägare behörigheter i prenumerationen för att skapa ett Azure Migrate-projekt.
VMwares virtuella datorer  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt.
Hyper-V:s virtuella datorer | Utvärdera upp till 10 000 Hyper-V-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och Hyper-V-datorer till en gräns för utvärdering.


## <a name="vmware-assessment-and-migration"></a>VMware-bedömning och migrering

[Granska](migrate-support-matrix-vmware.md) Azure Migrate Server-utvärdering och servermigrering supportmatris för virtuella VMware-datorer.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V-bedömning och migrering

[Granska](migrate-support-matrix-hyper-v.md) Azure Migrate Server-utvärdering och servermigrering supportmatris för Hyper-V-datorer.


## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella VMware-datorer](tutorial-assess-vmware.md) för migrering.
- [Utvärdera Hyper-V-datorer](tutorial-assess-hyper-v.md) för migrering.

