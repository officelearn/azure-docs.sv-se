---
title: Välj ett alternativ för VMware-migrering med Azure Migrate Server-migrering | Microsoft Docs
description: Innehåller en översikt över alternativ för att migrera virtuella VMware-datorer till Azure med Azure Migrate Server-migrering
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 196b80f600d9895e9fcb6471bfb5df5fb5bbe8d1
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324512"
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
**Samtidig replikering** | Högst 100 virtuella datorer kan replikeras samtidigt från en vCenter Server.<br/> Om du har fler än 50 virtuella datorer för migrering skapar du flera batchar för virtuella datorer.<br/> Att replikera mer i taget påverkar prestanda. | NA
**Distribution av utrustning** | [Azure Migrate](migrate-appliance.md) -installationen distribueras lokalt. | [Azure Migrate Replication](migrate-replication-appliance.md) har distribuerats lokalt.
**Site Recovery kompatibilitet** | Överensstämmelse. | Du kan inte replikera med Azure Migrate Server-migrering om du har konfigurerat replikering för en dator med Site Recovery.
**Mål disk** | Hanterade diskar | Hanterade diskar
**Disk gränser** | OS-disk: 2 TB<br/><br/> Data disk: 4 TB<br/><br/> Maximalt antal diskar: 60 | OS-disk: 2 TB<br/><br/> Data disk: 8 TB<br/><br/> Maximalt antal diskar: 63
**Genom strömnings diskar** | Stöds inte | Stöds
**UEFI-start** | Stöds inte | Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start.<br/><br/> OS-disken bör ha upp till fyra partitioner och volymer ska formateras med NTFS.


## <a name="deployment-steps-comparison"></a>Jämförelse av distributions steg

När du har granskat begränsningarna kan du med hjälp av de steg som ingår i distributionen av varje lösning hjälpa dig att avgöra vilket alternativ du ska välja.

**Uppgift** | **Information** |**Utan agent** | **Agent-baserad**
--- | --- | --- | ---
**Beskrivningar** | Utvärdera servrar innan migreringen.  Utvärderingen är valfri. Vi rekommenderar att du bedömer datorer innan du migrerar dem, men du behöver inte. <br/><br/> Vid utvärdering ställer Azure Migrate in en förenklad installation för att identifiera och utvärdera virtuella datorer. | Om du kör en agent utan migrering efter utvärdering används samma Azure Migrate-installation för utvärdering för att migrera utan agent.  |  Om du kör en agent-baserad migrering efter utvärdering, används inte installationen för utvärdering vid agent-baserad migrering. Du kan lämna enheten på plats eller ta bort den om du inte vill göra ytterligare identifiering och bedömning.
**Förbereda VMware-servrar och virtuella datorer för migrering** | Konfigurera ett antal inställningar på VMware-servrar och virtuella datorer. | Obligatorisk | Obligatorisk
**Lägg till Migreringsverktyg för Server** | Lägg till verktyget för migrering av Azure Migrate server i Azure Migrate projektet. | Obligatorisk | Obligatorisk
**Distribuera Azure Migrate-enheten** | Konfigurera en förenklad installation på en virtuell VMware-dator för identifiering och utvärdering av virtuella datorer. | Obligatorisk | Krävs inte.
**Installera mobilitets tjänsten på virtuella datorer** | Installera mobilitets tjänsten på varje virtuell dator som du vill replikera | Krävs inte | Obligatorisk
**Distribuera replikerings enheten för Azure Migrate Server-migrering** | Konfigurera en installation på en virtuell VMware-dator för att identifiera virtuella datorer och brygga mellan mobilitets tjänsten som körs på virtuella datorer och migrering av Azure Migrate Server | Krävs inte | Obligatorisk
**Replikera virtuella datorer**. Aktivera VM-replikering. | Konfigurera replikeringsinställningar och välj virtuella datorer som ska replikeras | Obligatorisk | Obligatorisk
**Kör en testmigrering** | Kör en testmigrering för att se till att allt fungerar som förväntat. | Obligatorisk | Obligatorisk
**Kör en fullständig migrering** | Migrera de virtuella datorerna. | Obligatorisk | Obligatorisk




## <a name="next-steps"></a>Nästa steg

[Migrera virtuella VMware-datorer](tutorial-migrate-vmware.md) med utan agent migrering.



