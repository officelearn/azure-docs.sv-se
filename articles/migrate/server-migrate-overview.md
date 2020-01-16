---
title: Välj ett alternativ för VMware-migrering med Azure Migrate Server-migrering | Microsoft Docs
description: Innehåller en översikt över alternativ för att migrera virtuella VMware-datorer till Azure med Azure Migrate Server-migrering
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028715"
---
# <a name="select-a-vmware-migration-option"></a>Välj ett alternativ för VMware-migrering

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget för migrering av Azure Migrate Server. Det här verktyget erbjuder ett par alternativ för migrering av virtuella VMware-datorer:

- Migrering med hjälp av replikering utan agent. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.




## <a name="compare-migration-methods"></a>Jämför metoder för migrering

Använd de här valda jämförelserna för att bestämma vilken metod som ska användas. Du kan också granska fullständiga support krav för [agent utan agent](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) och [agent-baserad](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) migrering.

**Inställning** | **Utan agent** | **Agent-baserad**
--- | --- | ---
**Azure-behörigheter** | Du måste ha behörighet att skapa ett Azure Migrate-projekt och för att registrera Azure AD-appar som skapats när du distribuerar Azure Migrate-enheten. | Du behöver deltagar behörighet för Azure-prenumerationen. 
**Samtidig replikering** | Högst 100 virtuella datorer kan replikeras samtidigt från en vCenter Server.<br/> Om du har fler än 50 virtuella datorer för migrering skapar du flera batchar för virtuella datorer.<br/> Att replikera mer i taget påverkar prestanda. | Ej tillämpligt
**Distribution av utrustning** | [Azure Migrate](migrate-appliance.md) -installationen distribueras lokalt. | [Azure Migrate Replication](migrate-replication-appliance.md) har distribuerats lokalt.
**Site Recovery kompatibilitet** | Kompatibelt. | Du kan inte replikera med Azure Migrate Server-migrering om du har konfigurerat replikering för en dator med Site Recovery.
**Mål disk** | Managed Disks | Managed Disks
**Disk gränser** | OS-disk: 2 TB<br/><br/> Data disk: 4 TB<br/><br/> Maximalt antal diskar: 60 | OS-disk: 2 TB<br/><br/> Data disk: 8 TB<br/><br/> Maximalt antal diskar: 63
**Genom strömnings diskar** | Stöds inte | Stöds
**UEFI-start** | Stöds inte | Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start.<br/><br/> OS-disken bör ha upp till fyra partitioner och volymer ska formateras med NTFS.


## <a name="deployment-steps-comparison"></a>Jämförelse av distributions steg

När du har granskat begränsningarna kan du med hjälp av de steg som ingår i distributionen av varje lösning hjälpa dig att avgöra vilket alternativ du ska välja.

**Aktivitet** | **Detaljer** |**Utan agent** | **Agent-baserad**
--- | --- | --- | ---
**Utvärdering** | Utvärdera servrar innan migreringen.  Utvärderingen är valfri. Vi rekommenderar att du bedömer datorer innan du migrerar dem, men du behöver inte. <br/><br/> Vid utvärdering ställer Azure Migrate in en förenklad installation för att identifiera och utvärdera virtuella datorer. | Om du kör en agent utan migrering efter utvärdering används samma Azure Migrate-installation för utvärdering för att migrera utan agent.  |  Om du kör en agent-baserad migrering efter utvärdering används inte installationen för utvärdering vid migrering utan agent. Du kan lämna enheten på plats eller ta bort den om du inte vill göra ytterligare identifiering och bedömning.
**Förbereda VMware-servrar och virtuella datorer för migrering** | Konfigurera ett antal inställningar på VMware-servrar och virtuella datorer. | Krävs | Krävs
**Lägg till Migreringsverktyg för Server** | Lägg till verktyget för migrering av Azure Migrate server i Azure Migrate projektet. | Krävs | Krävs
**Distribuera Azure Migrate-enheten** | Konfigurera en förenklad installation på en virtuell VMware-dator för identifiering och utvärdering av virtuella datorer. | Krävs | Krävs inte.
**Installera mobilitets tjänsten på virtuella datorer** | Installera mobilitets tjänsten på varje virtuell dator som du vill replikera | Krävs inte | Krävs
**Distribuera replikerings enheten för Azure Migrate Server-migrering** | Konfigurera en installation på en virtuell VMware-dator för att identifiera virtuella datorer och brygga mellan mobilitets tjänsten som körs på virtuella datorer och migrering av Azure Migrate Server | Krävs inte | Krävs
**Replikera virtuella datorer**. Aktivera VM-replikering. | Konfigurera replikeringsinställningar och välj virtuella datorer som ska replikeras | Krävs | Krävs
**Kör en testmigrering** | Kör en testmigrering för att se till att allt fungerar som förväntat. | Krävs | Krävs
**Kör en fullständig migrering** | Migrera de virtuella datorerna. | Krävs | Krävs




## <a name="next-steps"></a>Nästa steg

[Migrera virtuella VMware-datorer](tutorial-migrate-vmware.md) med utan agent migrering.



