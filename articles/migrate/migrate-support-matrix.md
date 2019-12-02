---
title: Azure Migrate support mat ris
description: Innehåller en översikt över support inställningar och begränsningar för tjänsten Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: raynew
ms.openlocfilehash: 13a4fbb119ae9aceb8731e53f7123375a6e5c106
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666281"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate support mat ris

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. I den här artikeln sammanfattas allmänna support inställningar och begränsningar för Azure Migrate scenarier och distributioner.


## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: om du använder den här versionen kan du skapa nya Azure Migrate projekt, identifiera lokala utvärderingar och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md#release-version-july-2019).
- **Tidigare version**: för kund som använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöddes) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte skapa nya Azure Migrate projekt eller utföra nya identifieringar.

## <a name="supported-assessmentmigration-scenarios"></a>Scenarier för bedömning/migrering som stöds

Tabellen sammanfattar scenarier för identifiering, utvärdering och migrering som stöds.

**Distribution** | **Detaljer** 
--- | --- 
**App-Specific Discovery** | Du kan identifiera appar, roller och funktioner som körs på virtuella VMware-datorer. För närvarande är den här funktionen begränsad till enbart identifiering. Utvärderingen är för närvarande på dator nivå. Vi erbjuder ännu inte app-, roll-eller funktions-/regionsspecifika bedömning. 
**Lokal utvärdering** | Utvärdera lokala arbets belastningar och data som körs på virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar. Utvärdera med hjälp av Azure Migrate Server utvärdering och Microsoft Data Migration Assistant (DMA), samt andra verktyg och ISV-erbjudanden.
**Lokal migrering till Azure** | Migrera arbets belastningar och data som körs på fysiska servrar, virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och molnbaserade virtuella datorer till Azure. Migrera med hjälp av Azure Migrate Server Assessment and Azure Database Migration Service (DMS) och även andra verktyg och ISV-erbjudanden.


## <a name="supported-tools"></a>Verktyg som stöds

Stöd för särskilt verktyg sammanfattas i tabellen.

**Verktyg** | **Beräknas** | **Migrera** 
--- | --- | ---
Utvärdering av Azure Migrate Server | Utvärdera virtuella [VMware-datorer](tutorial-prepare-vmware.md), [virtuella Hyper-V-datorer](tutorial-prepare-hyper-v.md)och [fysiska servrar](tutorial-prepare-physical.md). |  Inte tillgängligt (NA)
Migrering av Azure Migrate Server | Ej tillämpligt | Migrera [virtuella VMware-datorer](tutorial-migrate-vmware.md), [virtuella Hyper-V-datorer](tutorial-migrate-hyper-v.md)och [fysiska servrar](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Ej tillämpligt | Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar. | Ej tillämpligt
[Samhyres teknik](https://go.microsoft.com/fwlink/?linkid=2084928) | Utvärdera och migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar. |  Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar.
[Enhet 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar.| Ej tillämpligt
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Utvärdera lokala SQL Server databaser. | Ej tillämpligt
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | Ej tillämpligt | Migrera SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Utvärdera VDI (Virtual Desktop Infrastructure) | Ej tillämpligt
[Förflyttat](https://go.microsoft.com/fwlink/?linkid=2109528) | Utvärdera virtuella VMWare-datorer, virtuella Hyper-V-datorer, virtuella Xen-datorer, fysiska datorer, arbets stationer (inklusive VDI), offentliga moln arbets belastningar | Ej tillämpligt
[Rack](https://www.rackwareinc.com/cloud-migration) | Ej tillämpligt | Migrera virtuella VMWare-datorer, virtuella Hyper-V-datorer, virtuella Xen-datorer, KVM-VM, fysiska datorer, offentliga moln arbets belastningar 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar. | Ej tillämpligt
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar och SQL Server databaser. | Ej tillämpligt
[Webapp-Migration Assistant](https://appmigration.microsoft.com/) | Utvärdera webbappar | Migrera webbappar.


## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Detaljer**
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
Azure Government | USA Gov Virginia
Asien och stillahavsområdet | Asien, östra eller Sydostasien
Australien | Östra Australien eller Australien, sydöstra
Brasilien | Brasilien, södra
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

