---
title: Välj ett VMware-migreringsalternativ med Azure Migrera servermigrering | Microsoft-dokument
description: Innehåller en översikt över alternativ för att migrera virtuella virtuella VMware-datorer till Azure med Azure Migrera servermigrering
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028715"
---
# <a name="select-a-vmware-migration-option"></a>Välj ett VMware-migreringsalternativ

Du kan migrera virtuella virtuella datorer med VMware till Azure med hjälp av verktyget Migrera servermigrering av Azure.You can migrate VMware VMs to Azure using the Azure Migrate Server Migration tool. Det här verktyget erbjuder ett par alternativ för VMware VM-migrering:

- Migrering med hjälp av agentlös replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.




## <a name="compare-migration-methods"></a>Jämföra migreringsmetoder

Använd de här valda jämförelserna för att avgöra vilken metod du ska använda. Du kan också granska fullständiga supportkrav för [agentlös](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) och [agentbaserad](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) migrering.

**Inställning** | **Utan agent** | **Agent-baserade**
--- | --- | ---
**Azure-behörigheter** | Du behöver behörigheter för att skapa ett Azure Migrate-projekt och för att registrera Azure AD-appar som skapats när du distribuerar Azure Migrate-installationen. | Du behöver deltagarbehörigheter för Azure-prenumerationen. 
**Samtidig replikering** | Högst 100 virtuella datorer kan replikeras samtidigt från en vCenter-server.<br/> Om du har fler än 50 virtuella datorer för migrering skapar du flera batchar med virtuella datorer.<br/> Om du replikerar mer vid en enda tidpunkt påverkas prestanda. | Ej tillämpligt
**Distribution av apparater** | [Azure Migrate-enheten](migrate-appliance.md) distribueras lokalt. | [Azure Migrate Replication-enheten](migrate-replication-appliance.md) distribueras lokalt.
**Kompatibilitet för återställning av webbplats** | Kompatibel. | Du kan inte replikera med Azure Migrate Server Migration om du har konfigurerat replikering för en dator med site recovery.
**Måldisk** | Hanterade diskar | Hanterade diskar
**Diskgränser** | OS-disk: 2 TB<br/><br/> Datadisk: 4 TB<br/><br/> Högsta diskar: 60 | OS-disk: 2 TB<br/><br/> Datadisk: 8 TB<br/><br/> Högsta diskar: 63
**Genomströmningsdiskar** | Stöds inte | Stöds
**UEFI-start** | Stöds inte | Den migrerade virtuella datorn i Azure konverteras automatiskt till en VIRTUELL BIOS-start.<br/><br/> OS-disken ska ha upp till fyra partitioner och volymerna ska formateras med NTFS.


## <a name="deployment-steps-comparison"></a>Jämförelse av distributionssteg

När du har granskat begränsningarna kan det hjälpa dig att bestämma vilket alternativ du vill välja om du förstår vilka steg som ingår i distributionen av varje lösning.

**Aktivitet** | **Detaljer** |**Utan agent** | **Agent-baserade**
--- | --- | --- | ---
**Utvärdering** | Utvärdera servrar före migreringen.  Bedömning är valfritt. Vi föreslår att du bedömer maskiner innan du migrerar dem, men du behöver inte. <br/><br/> För bedömning skapar Azure Migrate en lättviktsinstallation för att identifiera och bedöma virtuella datorer. | Om du kör en agentlös migrering efter bedömning används samma Azure Migrate-enhet som konfigurerats för bedömning för agentlös migrering.  |  Om du kör en agentbaserad migrering efter bedömning används inte den uppsättning som är konfigurerad för bedömning under agentlös migrering. Du kan lämna apparaten på plats eller ta bort den om du inte vill göra ytterligare upptäckt och bedömning.
**Förbereda VMware-servrar och virtuella datorer för migrering** | Konfigurera ett antal inställningar på VMware-servrar och virtuella datorer. | Krävs | Krävs
**Lägga till verktyget Servermigrering** | Lägg till verktyget För migrering av Azure Migrate Server i Azure Migrate-projektet. | Krävs | Krävs
**Distribuera Azure Migrate-enheten** | Konfigurera en lättinstallation på en virtuell virtuell dator för virtuell dator för vm-identifiering och utvärdering. | Krävs | Krävs inte.
**Installera mobilitetstjänsten på virtuella datorer** | Installera mobilitetstjänsten på varje virtuell dator som du vill replikera | Krävs inte | Krävs
**Distribuera azure migrate servermigreringsreplikeringsverktyget** | Konfigurera en apparat på en virtuell virtuell VMware-dator för att identifiera virtuella datorer och brygga mellan mobilitetstjänsten som körs på virtuella datorer och Azure Migrera servermigrering | Krävs inte | Krävs
**Replikera virtuella datorer**. Aktivera VM-replikering. | Konfigurera replikeringsinställningar och välj virtuella datorer att replikera | Krävs | Krävs
**Kör en testmigrering** | Kör en testmigrering för att se till att allt fungerar som förväntat. | Krävs | Krävs
**Kör en fullständig migrering** | Migrera de virtuella datorerna. | Krävs | Krävs




## <a name="next-steps"></a>Nästa steg

[Migrera virtuella virtuella datorer](tutorial-migrate-vmware.md) med agentlös migrering.



