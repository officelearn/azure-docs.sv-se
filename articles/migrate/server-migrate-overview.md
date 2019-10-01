---
title: Välj ett alternativ för VMware-migrering med Azure Migrate Server-migrering | Microsoft Docs
description: Innehåller en översikt över alternativ för att migrera virtuella VMware-datorer till Azure med Azure Migrate Server-migrering
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9ddb63fcf4d960d2d8ed2671eba83fc38cddcbe4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710054"
---
# <a name="select-a-vmware-migration-option"></a>Välj ett alternativ för VMware-migrering

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget för migrering av Azure Migrate Server. Det här verktyget erbjuder ett par alternativ för migrering av virtuella VMware-datorer:

- Migrering med hjälp av replikering utan agent. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.




## <a name="compare-migration-methods"></a>Jämför metoder för migrering

Använd de här valda jämförelserna för att bestämma vilken metod som ska användas. Du kan också granska fullständiga support krav för [agent utan agent](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) och [agent-baserad](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) migrering.

**Inställning** | **Utan agent** | **Agent-baserad**
--- | --- | ---
**Azure-behörigheter** | Du måste ha behörighet att skapa ett Azure Migrate-projekt och för att registrera Azure AD-appar som skapats när du distribuerar Azure Migrate-enheten. | Du behöver deltagar behörighet för Azure-prenumerationen. 
**Samtidig replikering** | Högst 100 virtuella datorer kan replikeras samtidigt från en vCenter Server.<br/> Om du har fler än 50 virtuella datorer för migrering skapar du flera batchar för virtuella datorer.<br/> Att replikera mer i taget påverkar prestanda. | Ej tillämpligt
**Distribution av utrustning** | [Azure Migrate](migrate-appliance.md) -installationen distribueras lokalt. | [Azure Migrate Replication](migrate-replication-appliance.md) har distribuerats lokalt.
**Site Recovery kompatibilitet** | Överensstämmelse. | Du kan inte replikera med Azure Migrate Server-migrering om du har konfigurerat replikering för en dator med Site Recovery.
**Mål disk** | Hanterade diskar | Hanterade diskar
**Disk gränser** | OS-disk: 2 TB<br/><br/> Datadisk: 4 TB<br/><br/> Maximalt antal diskar: 60 | OS-disk: 2 TB<br/><br/> Datadisk: 8 TB<br/><br/> Maximalt antal diskar: 63
**Genom strömnings diskar** | Stöds inte | Stöds
**UEFI-start** | Stöds inte | Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start.<br/><br/> OS-disken bör ha upp till fyra partitioner och volymer ska formateras med NTFS.


## <a name="deployment-steps-comparison"></a>Jämförelse av distributions steg

När du har granskat begränsningarna kan du med hjälp av de steg som ingår i distributionen av varje lösning hjälpa dig att avgöra vilket alternativ du ska välja.

**Aktivitet** | **Detaljer** |**Utan agent** | **Agent-baserad**
--- | --- | --- | ---
**Förbereda VMware-servrar och virtuella datorer för migrering** | Konfigurera ett antal inställningar på VMware-servrar och virtuella datorer. | Obligatorisk | Obligatorisk
**Lägg till Migreringsverktyg för Server** | Lägg till verktyget för migrering av Azure Migrate server i Azure Migrate projektet. | Obligatorisk | Obligatorisk
**Distribuera Azure Migrate-enheten** | Konfigurera en förenklad installation på en virtuell VMware-dator för identifiering och utvärdering av virtuella datorer. | Obligatorisk | Krävs inte.
**Installera mobilitets tjänsten på virtuella datorer** | Installera mobilitets tjänsten på varje virtuell dator som du vill replikera | Inte obligatoriskt | Obligatorisk
**Distribuera replikerings enheten för Azure Migrate Server-migrering** | Konfigurera en installation på en virtuell VMware-dator för att identifiera virtuella datorer och brygga mellan mobilitets tjänsten som körs på virtuella datorer och migrering av Azure Migrate Server | Inte obligatoriskt | Obligatorisk
**Replikera virtuella datorer**. Aktivera VM-replikering. | Konfigurera replikeringsinställningar och välj virtuella datorer som ska replikeras | Obligatorisk | Obligatorisk
**Kör en testmigrering** | Kör en testmigrering för att se till att allt fungerar som förväntat. | Obligatorisk | Obligatorisk
**Kör en fullständig migrering** | Migrera de virtuella datorerna. | Obligatorisk | Obligatorisk




## <a name="next-steps"></a>Nästa steg

[Migrera virtuella VMware-datorer](tutorial-migrate-vmware.md) med utan agent migrering.



