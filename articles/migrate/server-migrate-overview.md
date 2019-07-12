---
title: Välj ett alternativ för VMware-migrering med Azure Migrate servermigrering | Microsoft Docs
description: Översikt över alternativ för att migrera virtuella VMware-datorer till Azure med Azure Migrate-servermigrering
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811575"
---
# <a name="select-a-vmware-migration-option"></a>Välj ett alternativ för VMware-migrering

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget Azure Migrate servermigrering. Det här verktyget erbjuder ett par alternativ för VMware-VM-migrering:

- Migrering med utan Agent replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.


Agentlös replikeringen har enklare från en distributionsperspektiv, men den har för närvarande ett antal begränsningar.

## <a name="agentless-migration-limitations"></a>Begränsningar för migrering utan agenter

Begränsningar är följande:

- **Samtidiga replikering**: Upp till 50 virtuella datorer kan replikeras samtidigt från en vCenter-Server.<br/> Om du har fler än 50 virtuella datorer för migrering kan du skapa flera batchar av virtuella datorer.<br/> Replikera mer på en gång kommer att påverka prestanda.
- **VM-diskar**: En virtuell dator som du vill migrera måste ha 60 eller färre diskar.
- **VM-operativsystem**: I allmänhet Azure Migrate kan migrera en Windows Server eller Linux-operativsystem, men det kan kräva ändringar på virtuella datorer så att de kan köras i Azure. Azure Migrate gör ändringarna automatiskt för dessa operativsystem:
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - För andra operativsystem som du behöver justera manuellt före migreringen. Den [migrera självstudien](tutorial-migrate-vmware.md) förklarar hur du gör detta.
- **Linux-Start**: Om/Boot finns på en särskild partition, bör finnas på OS-disken och sprids inte över flera diskar.<br/> Om/Boot är en del av rotpartitionen (/), ska sedan '/' partitionen vara på OS-disken och inte omfatta andra diskar.
- **UEFI-startmetod**: Virtuella datorer med UEFI boot stöds inte för migrering.
- **Krypterade diskar/volymer (BitLocker, cryptfs)** : Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
- **RDM/genomströmningsdiskar**: Om virtuella datorer har RDM eller genomströmning, vilket kan replikeras de här diskarna inte till Azure
- **NFS**: NFS-volymer monterade volymer på de virtuella datorerna replikeras inte.
- **Mållagringskontot**: Du kan bara migrera virtuella VMware-datorer till virtuella Azure-datorer med hanterade diskar (HDD-Standard, Premium SSD).



## <a name="deployment-steps-comparison"></a>Distribution steg jämförelse

När du har granskat begränsningarna, Förstå steg som ingår i distributionen av varje lösning kan hjälpa dig att avgöra vilket alternativ du väljer.

**Aktivitet** | **Detaljer** |**Utan Agent** | **Agentbaserad**
--- | --- | --- | ---
**Förbereda VMware-servrar och virtuella datorer för migrering** | Konfigurera ett antal inställningar på VMware-servrar och virtuella datorer. | Krävs | Krävs
**Lägg till Server-Migreringsverktyget** | Lägg till Migreringsverktyget Azure Migrate Server i Azure Migrate-projektet. | Krävs | Krävs
**Distribuera Azure Migrate-installation** | Konfigurera en enkel installation på en VMware-VM för VM-identifiering och utvärdering. | Krävs | Krävs inte.
**Installera mobilitetstjänsten på virtuella datorer** | Installera mobilitetstjänsten på varje virtuell dator som du vill replikera | Krävs inte | Krävs
**Distribuera installationen för replikering av Azure Migrate-servermigrering** | Konfigurera en installation på en VMware-VM för att identifiera virtuella datorer och brygga mellan den mobilitetstjänstversion som körs på virtuella datorer och Azure Migrate-servermigrering | Krävs inte | Krävs
**Replikera virtuella datorer**. Aktivera replikering av virtuella datorer. | Konfigurera replikeringsinställningar och välj virtuella datorer för replikering | Krävs | Krävs
**Kör en testmigrering** | Kör en testmigrering för att kontrollera att allt fungerar som förväntat. | Krävs | Krävs
**Kör en fullständig migrering** | Migrera de virtuella datorerna. | Krävs | Krävs




## <a name="next-steps"></a>Nästa steg

[Migrera virtuella VMware-datorer](tutorial-migrate-vmware.md) med migrering utan agenter.



