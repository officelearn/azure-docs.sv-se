---
title: Automatisera migreringen av stort antal virtuella datorer till Azure | Microsoft Docs
description: Beskriver hur du migrerar ett stort antal virtuella datorer med Azure Site Recovery med hjälp av skript
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 02/07/2019
ms.author: snehaa
ms.openlocfilehash: 74dabc49dd3d0e38f43dc758204c35ea1c0efd99
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438490"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Skala migrering av virtuella datorer med Azure Site Recovery

Den här artikeln hjälper dig att förstå processen med att använda skript för att migrera stort antal virtuella datorer med Azure Site Recovery. Dessa skript är din hämtas på [Azure PowerShell-exempel](https://github.com/Azure/azure-docs-powershell-samples) lagringsplatsen på GitHub. Skripten kan användas för att migrera VMware, AWS, GCP virtuella datorer och fysiska servrar till Azure. Du kan också använda dessa skript för att migrera Hyper-V-datorer om du migrerar de virtuella datorerna som fysiska servrar. Skripten utnyttjar Azure Site Recovery PowerShell dokumenterade [här](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuella begränsningar:
- Skripten stöd för närvarande för migrering till icke-hanterade diskar endast
- Stöd för migrering till Standard disks
- Stöd för att ange den statiska IP-adressen för det primära nätverkskortet för den Virtuella måldatorn
- Skripten inte vidtar Azure Hybrid-förmånen relaterade indata, måste du manuellt uppdatera egenskaperna för den replikerade virtuella datorn i portalen

## <a name="how-does-it-work"></a>Hur fungerar det?

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du göra följande:
- Se till att Site Recovery-valvet har skapats i din Azure-prenumeration
- Se till att konfigurationsservern och Processervern är installerade i källmiljön och valvet är inte identifiera miljön
- Se till att en replikeringsprincip har skapats och associerats med konfigurationsservern
- Se till att du har lagt till VM-administratörskonto till den config-server (som används för att replikera lokala virtuella datorer)
- Kontrollera att mål-artefakter i Azure skapas
    - Målresursgrupp
    - Rikta Storage-konto (och dess resursgrupp)
    - Rikta virtuellt nätverk för redundans (och dess resursgrupp)
    - Målundernät
    - Rikta virtuellt nätverk för redundanstest (och dess resursgrupp)
    - Tillgänglighetsuppsättning (vid behov)
    - Mål för Nätverkssäkerhetsgrupp och en resursgrupp
- Se till att du har valt i egenskaperna för den Virtuella måldatorn
    - Namn på virtuell måldator
    - Storleken i Azure (kan göras med hjälp av Azure Migrate-utvärdering)
    - Privata IP-adressen för det primära nätverkskortet på den virtuella datorn
- Ladda ned skripten från [Azure PowerShell-exempel](https://github.com/Azure/azure-docs-powershell-samples) lagringsplatsen på GitHub

### <a name="csv-input-file"></a>CSV-indatafil
När du har alla förutsättningar har slutförts kan behöva du skapa en CSV-fil som innehåller data för varje källdatorn som du vill migrera. Indata CSV måste ha en rubrikrad med de inkommande information och en rad med information för varje dator som ska migreras. Alla skript är utformade att fungera på samma CSV-filen. En exempelmall för CSV finns i skriptmappen som referens.

### <a name="script-execution"></a>Körning av skript
När CSV-filen är klar kan köra du följande steg för att utföra migreringen av lokala virtuella datorer:

**Steg #** | **Skriptets namn** | **Beskrivning**
--- | --- | ---
1 | asr_startmigration.ps1 | Aktivera replikering för alla virtuella datorer visas i CSV-filen, skriptet skapar en CSV-utdata med Jobbinformationen för varje virtuell dator
2 | asr_replicationstatus.ps1 | Kontrollera status för replikering, skriptet skapar en CSV-fil med statusen för varje virtuell dator
3 | asr_updateproperties.ps1 | När de virtuella datorerna är replikerade/den skyddade kan använda det här skriptet för att uppdatera målegenskaperna för den virtuella datorn (beräkning och nätverk-Egenskaper)
4 | asr_propertiescheck.ps1 | Kontrollera om uppdatera egenskaperna på rätt sätt
5 | asr_testmigration.ps1 |  Starta redundanstestningen för de virtuella datorerna i CSV-filen, skriptet skapar en CSV-utdata med Jobbinformationen för varje virtuell dator
6 | asr_cleanuptestmigration.ps1 | När du manuellt verifiera de virtuella datorerna som testa över kan du använda det här skriptet för att rensa redundanstestningen virtuella datorer
7 | asr_migration.ps1 | Utför en oplanerad redundansväxling för de virtuella datorerna i CSV-filen, skriptet skapar en CSV-utdata med Jobbinformationen för varje virtuell dator. Skriptet har inte stänga av lokalt virtuella datorer innan du utlöser redundansväxlingen för programkonsekvens, rekommenderar vi att du manuellt stänga av de virtuella datorerna innan du kör skriptet.
8 | asr_completemigration.ps1 | Utföra commit-åtgärden på de virtuella datorerna och ta bort ASR-entiteter
9 | asr_postmigration.ps1 | Du kan använda det här skriptet för att göra det om du tänker tilldela nätverkssäkerhetsgrupper till nätverkskort efter en redundansväxling. Det tilldelar en NSG till alla ett nätverkskort på den Virtuella måldatorn.

## <a name="next-steps"></a>Nästa steg

[Läs mer](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) om migrera servrar till Azure med Azure Site Recovery
