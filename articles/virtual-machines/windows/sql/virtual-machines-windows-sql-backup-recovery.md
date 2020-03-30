---
title: Säkerhetskopiering och återställning för SQL Server i virtuella Azure-datorer | Microsoft-dokument
description: Beskriver säkerhetskopiering och återställning av överväganden för SQL Server-databaser som körs på virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249781"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Säkerhetskopiering och återställning för SQL Server i virtuella Azure-datorer

Den här artikeln innehåller vägledning om alternativ för säkerhetskopiering och återställning som är tillgängliga för SQL Server som körs på en virtuell Windows-dator i Azure. Azure Storage underhåller tre kopior av varje Azure VM-disk för att garantera skydd mot dataförlust eller fysisk dataskada. Till skillnad från lokala, behöver du inte fokusera på maskinvarufel. Du bör dock fortfarande säkerhetskopiera SQL Server-databaser för att skydda mot program- eller användarfel, till exempel oavsiktliga datainfogningar eller borttagningar. I denna situation är det viktigt att kunna återställa till en viss tidpunkt.

Den första delen av den här artikeln innehåller en översikt över tillgängliga alternativ för säkerhetskopiering och återställning. Detta följs av avsnitt som ger mer information om varje strategi.

## <a name="backup-and-restore-options"></a>Alternativ för säkerhetskopiering och återställning

I följande tabell finns information om olika alternativ för säkerhetskopiering och återställning för SQL Server som körs på virtuella Azure-datorer:

| Strategi | SQL-versioner | Beskrivning |
|---|---|---|
| [Automatisk säkerhetskopiering](#automated) | 2014<br/> 2016<br/> 2017 | Med automatisk säkerhetskopiering kan du schemalägga regelbundna säkerhetskopior för alla databaser på en VIRTUELL SQL Server. Säkerhetskopior lagras i Azure-lagring i upp till 30 dagar. Från och med SQL Server 2016 erbjuder Automatisk säkerhetskopiering v2 ytterligare alternativ som att konfigurera manuell schemaläggning och frekvensen av fullständiga och logga säkerhetskopior. |
| [Azure Backup för virtuella SQL-datorer](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup tillhandahåller en säkerhetskopieringsfunktion i företagsklass för SQL Server som körs i virtuella Azure-datorer. Med den här tjänsten kan du centralt hantera säkerhetskopior för flera servrar och tusentals databaser. Databaser kan återställas till en viss tidpunkt i portalen. Den erbjuder en anpassningsbar bevarandeprincip som kan upprätthålla säkerhetskopior i flera år. |
| [Manuell säkerhetskopiering](#manual) | Alla | Beroende på din version av SQL Server finns det olika tekniker för att manuellt säkerhetskopiera och återställa SQL Server som körs på en Azure VM. I det här fallet är du ansvarig för hur dina databaser säkerhetskopieras och lagringsplatsen och hanteringen av dessa säkerhetskopior. |

I följande avsnitt beskrivs varje alternativ mer i detalj. Det sista avsnittet i den här artikeln innehåller en sammanfattning i form av en funktionsmatris.

## <a name="automated-backup"></a><a id="automated"></a>Automatisk säkerhetskopiering

Automatisk säkerhetskopiering tillhandahåller en automatisk säkerhetskopieringstjänst för SQL Server Standard- och Enterprise-utgåvor som körs i en Windows VM i Azure. Den här tjänsten tillhandahålls av [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md), som installeras automatiskt på virtuella SQL Server Windows-avbildningar i Azure-portalen.

Alla databaser säkerhetskopieras till ett Azure-lagringskonto som du konfigurerar. Säkerhetskopior kan krypteras och behållas i upp till 30 dagar.

SQL Server 2016 och högre virtuella datorer erbjuder fler anpassningsalternativ med Automatisk säkerhetskopiering v2. Dessa förbättringar inkluderar:

- Säkerhetskopiering av systemdatabaser
- Schema för manuell säkerhetskopiering och tidsfönster
- Fullständig frekvens för säkerhetskopiering av filer och loggfiler

Om du vill återställa en databas måste du hitta de nödvändiga säkerhetskopiorna i lagringskontot och utföra en återställning på sql VM med sql server management studio (SSMS) eller Transact-SQL-kommandon.

Mer information om hur du konfigurerar automatisk säkerhetskopiering för virtuella SQL-datorer finns i någon av följande artiklar:

- **SQL Server 2016/2017**: [Automatisk säkerhetskopiering v2 för virtuella Azure-datorer](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatisk säkerhetskopiering för virtuella SQL Server 2014-datorer](virtual-machines-windows-sql-automated-backup.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Azure Backup för virtuella SQL-datorer

[Azure Backup](/azure/backup/) tillhandahåller en säkerhetskopieringsfunktion i företagsklass för SQL Server som körs i virtuella Azure-datorer. Alla säkerhetskopior lagras och hanteras i ett Recovery Services-valv. Det finns flera fördelar som den här lösningen ger, särskilt för företag:

- **Säkerhetskopiering av noll infrastruktur**: Du behöver inte hantera säkerhetskopieringsservrar eller lagringsplatser.
- **Skala:** Skydda många virtuella SQL-datorer och tusentals databaser.
- **Pay-As-You-Go:** Den här funktionen är en separat tjänst som tillhandahålls av Azure Backup, men som med alla Azure-tjänster betalar du bara för det du använder.
- **Central hantering och övervakning**: Hantera centralt alla dina säkerhetskopior, inklusive andra arbetsbelastningar som Azure Backup stöder, från en enda instrumentpanel i Azure.
- **Principdriven säkerhetskopiering och kvarhållning**: Skapa standardsäkerhetsprinciper för säkerhetskopiering för regelbundna säkerhetskopieringar. Upprätta bevarandeprinciper för att upprätthålla säkerhetskopior i flera år.
- **Stöd för SQL Always On:** Identifiera och skydda en SQL Server Always On-konfiguration och respekterar säkerhetskopieringsinställningarna för tillgänglighetsgruppen för tillgänglighet.
- **15-minuters återställningspunkt mål (RPO)**: Konfigurera SQL transaktionslogg säkerhetskopior upp till var 15 minuter.
- **Tidsåterställning**: Använd portalen för att återställa databaser till en viss tidpunkt utan att behöva återställa flera fullständiga, differentiella och logga säkerhetskopior manuellt.
- **Konsoliderade e-postmeddelanden för fel**: Konfigurera konsoliderade e-postmeddelanden för eventuella fel.
- **Rollbaserad åtkomstkontroll**: Bestäm vem som kan hantera säkerhetskopiering och återställning via portalen.

En snabb översikt över hur det fungerar tillsammans med en demo kan du titta på följande video:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Den här Azure Backup-lösningen för virtuella SQL-datorer är allmänt tillgänglig. Mer information finns i [Säkerhetskopiera SQL Server-databas till Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a>Manuell säkerhetskopiering

Om du vill hantera säkerhetskopierings- och återställningsåtgärder manuellt på sql-datorer finns det flera alternativ beroende på vilken version av SQL Server du använder. En översikt över säkerhetskopiering och återställning finns i någon av följande artiklar som baseras på din version av SQL Server:

- [Säkerhetskopiering och återställning för SQL Server 2016 och senare](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Säkerhetskopiering och återställning för SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Säkerhetskopiering och återställning för SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Säkerhetskopiering och återställning för SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Säkerhetskopiering och återställning för SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

I följande avsnitt beskrivs flera alternativ för manuell säkerhetskopiering och återställning mer i detalj.

### <a name="backup-to-attached-disks"></a>Säkerhetskopiering till anslutna diskar

För SQL Server som körs i virtuella Azure-datorer kan du använda inbyggda säkerhetskopierings- och återställningstekniker med anslutna diskar på den virtuella datorn för målet för säkerhetskopieringsfilerna. Det finns dock en gräns för hur många diskar du kan koppla till en virtuell Azure-dator, baserat på storleken på [den virtuella datorn](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Det finns också overhead diskhantering att överväga.

Ett exempel på hur du manuellt skapar en fullständig säkerhetskopia av databasen med SQL Server Management Studio (SSMS) eller Transact-SQL finns i [Skapa en fullständig säkerhetskopiering av databasen](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Säkerhetskopiera till URL

Från och med SQL Server 2012 SP1 CU2 kan du säkerhetskopiera och återställa direkt till Microsoft Azure Blob-lagring, som också kallas säkerhetskopiering till URL. SQL Server 2016 introducerade också följande förbättringar för den här funktionen:

| 2016 Förbättring | Information |
| --- | --- |
| **Striping** |När SQL Server 2016 säkerhetskopierar till Microsoft Azure blob storage stöder sql Server 2016 säkerhetskopiering till flera blobbar för att möjliggöra säkerhetskopiering av stora databaser, upp till maximalt 12,8 TB. |
| **Säkerhetskopiering av ögonblicksbilder** |Med hjälp av Ögonblicksbilder av Azure tillhandahåller SQL Server File-Snapshot Backup nästan momentana säkerhetskopior och snabba återställningar för databasfiler som lagras med azure blob-lagringstjänsten. Med den här funktionen kan du förenkla principerna för säkerhetskopiering och återställning. Säkerhetskopiering av filer och ögonblicksbilder stöder också tidsåterställning. Mer information finns i [Ögonblicksbildsäkerhetskopior för databasfiler i Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Mer information finns i någon av följande artiklar som baseras på din version av SQL Server:

- **SQL Server 2016/2017**: [SQL Server Backup till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Säkerhetskopiering till URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 Säkerhetskopiering till URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Hanterad säkerhetskopiering

Från och med SQL Server 2014 automatiserar Managed Backup skapandet av säkerhetskopior till Azure-lagring. Bakom kulisserna använder Managed Backup funktionen Backup to URL som beskrivs i föregående avsnitt i den här artikeln. Hanterad säkerhetskopiering är också den underliggande funktionen som stöder tjänsten SQL Server VM Automated Backup.

Från och med SQL Server 2016 fick Managed backup ytterligare alternativ för schemaläggning, säkerhetskopiering av systemdatabaser och fullständig och loggsäkerhetsfrekvens.

Mer information finns i någon av följande artiklar baserat på din version av SQL Server:

- [Hanterad säkerhetskopiering till Microsoft Azure för SQL Server 2016 och senare](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Hanterad säkerhetskopiering till Microsoft Azure för SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Beslutsmatris

I följande tabell sammanfattas funktionerna för varje säkerhetskopiering och återställningsalternativ för virtuella SQL Server-datorer i Azure.

|| **Automatisk säkerhetskopiering** | **Azure Backup för SQL** | **Manuell säkerhetskopiering** |
|---|---|---|---|
| Kräver ytterligare Azure-tjänst |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurera principer för säkerhetskopiering i Azure Portal | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Återställa databaser i Azure Portal |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hantera flera servrar i en instrumentpanel |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Återställning från tidpunkt | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15-minuters mål för återställningspunkt (RPO) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Kortsiktig princip för lagring av säkerhetskopiering (dagar) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Långsiktig princip för lagring av säkerhetskopiering (månader, år) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Inbyggt stöd för SQL Server Always On |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Säkerhetskopiering till Azure Storage-konton | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisk) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisk) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(hanterad kund) |
| Hantering av lagrings- och säkerhetskopieringsfiler | | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Säkerhetskopiering till anslutna diskar på den virtuella datorn |   |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centrala anpassningsbara säkerhetskopieringsrapporter |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsoliderade e-postmeddelanden för fel |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Anpassa övervakning baserat på Azure Monitor-loggar |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Övervaka säkerhetskopieringsjobb med SSMS- eller Transact-SQL-skript | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Återställa databaser med SSMS- eller Transact-SQL-skript | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Nästa steg

Om du planerar distributionen av SQL Server i en virtuell Azure-dator kan du hitta etableringsvägledning i följande guide: Så här etablerar du [en virtuell Windows SQL Server-dator i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

Även om säkerhetskopiering och återställning kan användas för att migrera dina data, finns det potentiellt enklare datamigreringssökvägar till SQL Server på en Virtuell Azure.Although backup and restore can be used to migrate your data, there are potentially easier data migration paths to SQL Server on an Azure VM. En fullständig diskussion om migreringsalternativ och rekommendationer finns i [Migrera en databas till SQL Server på en Virtuell Azure.](virtual-machines-windows-migrate-sql.md)
