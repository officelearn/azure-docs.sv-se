---
title: Azure Migrate support mat ris
description: Innehåller en översikt över support inställningar och begränsningar för tjänsten Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: 18032250bc5c321d638ad46204738f49f1a0c744
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480141"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate support mat ris

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. I den här artikeln sammanfattas allmänna support inställningar och begränsningar för Azure Migrate scenarier och distributioner.


## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: om du använder den här versionen kan du skapa nya Azure Migrate projekt, identifiera lokala utvärderingar och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md#release-version-july-2019).
- **Tidigare version**: för kund som använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöddes) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte skapa nya Azure Migrate projekt eller utföra nya identifieringar.

## <a name="supported-assessmentmigration-scenarios"></a>Scenarier för bedömning/migrering som stöds

Tabellen sammanfattar scenarier för identifiering, utvärdering och migrering som stöds.

**Distribution** | **Information** 
--- | --- 
**App-Specific Discovery** | Du kan identifiera appar, roller och funktioner som körs på virtuella VMware-datorer. För närvarande är den här funktionen begränsad till enbart identifiering. Utvärderingen är för närvarande på dator nivå. Vi erbjuder ännu inte app-, roll-eller funktions-/regionsspecifika bedömning. 
**Lokal utvärdering** | Utvärdera lokala arbets belastningar och data som körs på virtuella VMware-datorer och virtuella Hyper-V-datorer. Utvärdera med hjälp av Azure Migrate Server utvärdering och Microsoft Data Migration Assistant (DMA) och verktyg från tredje part som omfattar Cloudamize, samhyres teknik och Turbonomic-Server.
**Lokal migrering till Azure** | Migrera arbets belastningar och data som körs på fysiska servrar, virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och molnbaserade virtuella datorer till Azure. Migrera med hjälp av Azure Migrate Server Assessment and Azure Database Migration Service (DMS) och med verktyg från tredje part som innehåller Carbonite och CorentTech.

Stöd för ett speciellt verktyg sammanfattas på följande sätt.

**Verktyg** | **Bedömning/migrering** | **Information**
--- | --- | ---
Utvärdering av Azure Migrate Server | Utvärdering | Prova Server utvärdering för [Hyper-V](tutorial-prepare-hyper-v.md) och [VMware](tutorial-prepare-vmware.md).
Cloudamize | Utvärdering | [Läs mer](https://www.cloudamize.com/platform#tab-0).
CorentTech | Utvärdering | [Läs mer](https://www.corenttech.com/).
Turbonomic | Utvärdering | [Läs mer](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migrering av Azure Migrate Server | Migrering | Prova Server migrering för [Hyper-V](tutorial-migrate-hyper-v.md) och [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrering | [Läs mer](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migrering | [Läs mer](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Information**
--- | ---
Prenumeration | Du kan ha flera Azure Migrate projekt i en prenumeration.
Azure-behörigheter | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
VMwares virtuella datorer  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt.
Hyper-V:s virtuella datorer | Utvärdera upp till 35 000 virtuella Hyper-V-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.

## <a name="supported-geographies"></a>Geografiska områden som stöds

Du kan skapa ett Azure Migrate-projekt i ett antal geografiska områden. Även om du bara kan skapa projekt i dessa geografiska områden kan du utvärdera eller migrera datorer för andra mål platser. Projektets geografi används bara för att lagra identifierade metadata.

**Geografi** | **Lagrings plats för metadata**
--- | ---
Azure Government | Virginia (USA-förvaltad region)
Asien och stillahavsområdet | Asien, östra eller Sydostasien
Australien | Östra Australien eller Australien, sydöstra
Brasilien | Södra Brasilien
Kanada | Kanada, centrala eller Kanada, öst
Europa | Europa, norra eller Europa, västra
Frankrike | Frankrike, centrala
Indien | Centrala Indien eller södra Indien
Japan |  Japan, östra eller Japan, väst
Korea | Korea, centrala eller Korea, södra
Storbritannien | Storbritannien, södra eller Storbritannien, västra
USA | USA, centrala eller västra USA 2


 > [!NOTE]
 > Stöd för Azure Government är för närvarande bara tillgängligt för den [äldre versionen](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>VMware-bedömning och migrering

[Granska](migrate-support-matrix-vmware.md) support matrisen för Azure Migrate Server utvärdering och Server migration för virtuella VMware-datorer.

## <a name="hyper-v-assessment-and-migration"></a>Utvärdering och migrering av Hyper-V

[Granska](migrate-support-matrix-hyper-v.md) den Azure Migrate Server utvärderingen och stöd matrisen för Server migration för virtuella Hyper-V-datorer.


## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella VMware-datorer](tutorial-assess-vmware.md) för migrering.
- [Utvärdera virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md) för migrering.

