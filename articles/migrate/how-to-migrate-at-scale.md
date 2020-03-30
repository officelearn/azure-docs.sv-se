---
title: Automatisera migreringsmaskinmigrering i Azure Migrate
description: Beskriver hur du använder skript för att migrera ett stort antal datorer i Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196365"
---
# <a name="scale-migration-of-vms"></a>Skala migrering av virtuella datorer 

Den här artikeln hjälper dig att förstå hur du använder skript för att migrera ett stort antal virtuella datorer (VMs). Om du vill skala migreringen använder du [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Site Recovery-skript är tillgängliga för nedladdning på [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo på GitHub. Skripten kan användas för att migrera VMware, AWS, GCP-virtuella datorer och fysiska servrar till hanterade diskar i Azure. Du kan också använda dessa skript för att migrera virtuella hyper-virtuella datorer om du migrerar de virtuella datorerna som fysiska servrar. Skripten som utnyttjar Azure Site Recovery PowerShell dokumenteras [här](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuella begränsningar
- Stöd för att ange den statiska IP-adressen endast för det primära nätverkskortet för måldatorn
- Skripten tar inte Azure Hybrid Benefit relaterade indata, måste du manuellt uppdatera egenskaperna för den replikerade virtuella datorn i portalen

## <a name="how-does-it-work"></a>Hur fungerar det?

### <a name="prerequisites"></a>Krav
Innan du börjar måste du göra följande:
- Se till att site recovery-valvet skapas i din Azure-prenumeration
- Kontrollera att konfigurationsservern och processservern är installerade i källmiljön och att valvet kan identifiera miljön
- Kontrollera att en replikeringsprincip skapas och associeras med konfigurationsservern
- Kontrollera att du har lagt till vm-administratörskontot på konfigurationsservern (som ska användas för att replikera de lokala virtuella datorerna)
- Se till att målartefakterna i Azure skapas
    - Målgrupp för målresurs
    - Target Storage Account (och dess resursgrupp) – Skapa ett premiumlagringskonto om du planerar att migrera till premiumhanterade diskar
    - Cachelagringskonto (och dess resursgrupp) – Skapa ett standardlagringskonto i samma region som valvet
    - Rikta virtuellt nätverk för redundans (och dess resursgrupp)
    - Målundernät
    - Målvirtivt nätverk för test redundans (och dess resursgrupp)
    - Tillgänglighetsuppsättning (om det behövs)
    - Target Network Security Group och dess resursgrupp
- Se till att du har bestämt dig för egenskaperna för måldatorn
    - Namn på mål-VM
    - Mål-VM-storlek i Azure (kan beslutas med Azure Migrate-utvärdering)
    - Privat IP-adress för det primära nätverkskortet i den virtuella datorn
- Ladda ned skripten från [Azure PowerShell-exempel](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo på GitHub

### <a name="csv-input-file"></a>Indatafil för CSV
När du har slutfört alla förutsättningar måste du skapa en CSV-fil som innehåller data för varje källdator som du vill migrera. Indata-CSV måste ha en rubrikrad med indatadetaljer och en rad med information för varje dator som måste migreras. Alla skript är utformade för att fungera på samma CSV-fil. Det finns ett exempel på CSV-mall i skriptmappen som referens.

### <a name="script-execution"></a>Körning av skript
När CSV är klart kan du utföra följande steg för att utföra migreringen av lokala virtuella datorer:

**Steg #** | **Skriptnamn** | **Beskrivning**
--- | --- | ---
1 | asr_startmigration.ps1 | Aktivera replikering för alla virtuella datorer som anges i csv, skapar skriptet en CSV-utdata med jobbinformation för varje virtuell dator
2 | asr_replicationstatus.ps1 | Kontrollera status för replikering, skapar skriptet en csv med status för varje virtuell dator
3 | asr_updateproperties.ps1 | När de virtuella datorerna har replikerats/skyddats använder du det här skriptet för att uppdatera målegenskaperna för den virtuella datorn (egenskaper för beräkning och nätverk)
4 | asr_propertiescheck.ps1 | Kontrollera om egenskaperna uppdateras på rätt sätt
5 | asr_testmigration.ps1 |  Starta test failover av de virtuella datorer som anges i csv, skapar skriptet en CSV-utdata med jobbinformation för varje virtuell dator
6 | asr_cleanuptestmigration.ps1 | När du manuellt har validerat de virtuella datorerna som har testats misslyckades kan du använda det här skriptet för att rensa test failover-virtuella datorer
7 | asr_migration.ps1 | Utför en oplanerad redundans för de virtuella datorerna som anges i csv, skriptet skapar en CSV-utdata med jobbinformation för varje virtuell dator. Skriptet stänger inte av de lokala virtuella datorerna innan redundansen utlöses, för programkonsekvens, rekommenderar vi att du stänger av de virtuella datorerna manuellt innan du kör skriptet.
8 | asr_completemigration.ps1 | Utföra genomförandeåtgärden på de virtuella datorerna och ta bort Azure Site Recovery-entiteterna
9 | asr_postmigration.ps1 | Om du planerar att tilldela nätverkssäkerhetsgrupper till nätverkskorten efter redundansen kan du använda det här skriptet för att göra det. Den tilldelar en NSG till ett nätverkskort i måldatorn.

## <a name="how-to-migrate-to-managed-disks"></a>Hur migrerar man till hanterade diskar?
Skriptet migrerar som standard de virtuella datorerna till hanterade diskar i Azure. Om det angivna mållagringskontot är ett premiumlagringskonto skapas premiumhanterade diskar efter migreringen. Cachelagringskontot kan fortfarande vara ett standardkonto. Om mållagringskontot är ett standardlagringskonto skapas standarddiskar efter migreringen. 

## <a name="next-steps"></a>Nästa steg

[Läs mer](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) om att migrera servrar till Azure med Hjälp av Azure Site Recovery
