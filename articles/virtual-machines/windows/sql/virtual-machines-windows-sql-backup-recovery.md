---
title: Säkerhetskopiering och återställning av SQLServer i virtuella Azure-datorer | Microsoft Docs
description: Beskriver överväganden för säkerhetskopiering och återställning för SQL Server-databaser som körs på Azure virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ec147355d6f61a282a0e6b0950fe03ba0e2341f5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530390"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Säkerhetskopiering och återställning för SQL Server i Azure Virtual Machines

Den här artikeln innehåller råd om säkerhetskopiering och återställning av tillgängliga alternativ för SQL Server körs på en Windows-dator i Azure. Azure-lagring underhålls tre kopior av varje virtuell dator i Azure-disk att garantera skydd mot dataförlust eller skadade fysisk data. Därför till skillnad från lokalt behöver du inte fokusera på maskinvarufel. Du bör dock fortfarande att säkerhetskopiera SQL Server-databaser som skydd mot fel på program eller användare som oavsiktligt data infogningar och borttagningar. I så fall kan är det viktigt för att kunna återställa till en specifik tidpunkt.

Den första delen av den här artikeln innehåller en översikt över tillgängliga säkerhetskopiering och återställningsalternativ. Detta åtföljs av avsnitt som innehåller mer information om varje strategi.

## <a name="backup-and-restore-options"></a>Alternativ för säkerhetskopiering och återställning

I följande tabell innehåller information om olika alternativ för säkerhetskopiering och återställning för SQL Server som körs på virtuella Azure-datorer:

| Strategi | SQL-versioner | Beskrivning |
|---|---|---|---|
| [Automatisk säkerhetskopiering](#automated) | 2014<br/> 2016<br/> 2017 | Automatisk säkerhetskopiering kan du schemalägga regelbundna säkerhetskopieringar för alla databaser på en SQL Server VM. Säkerhetskopior lagras i Azure-lagring för upp till 30 dagar. Från och med SQL Server 2016, erbjuder automatisk säkerhetskopiering v2 ytterligare alternativ, till exempel konfigurera manuell schemaläggning och frekvensen av fullständig eller säkerhetskopior. |
| [Azure Backup för SQL-datorer](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup ger en Enterprise-klass säkerhetskopiering för SQL Server som körs i virtuella Azure-datorer. Med den här tjänsten kan du centralt hantera säkerhetskopior för flera servrar och tusentals databaser. Databaser kan återställas till en viss punkt i tiden i portalen. Den erbjuder en anpassningsbar bevarandeprincip som kan underhålla säkerhetskopior i flera år. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. |
| [Manuell säkerhetskopiering](#manual) | Alla | Det finns olika metoder manuellt säkerhetskopiera och återställa SQL Server som körs på en Azure VM beroende på din version av SQL Server. I det här scenariot ansvarar du för hur dina databaser som säkerhetskopieras och den lagringsplats och hantering av dessa säkerhetskopior. |

I följande avsnitt beskrivs varje alternativ i detalj. Den sista delen av den här artikeln innehåller en sammanfattning i form av en funktion-matris.

## <a id="automated"></a> Automatisk säkerhetskopiering

Automatisk säkerhetskopiering innehåller en automatisk säkerhetskopiering för SQL Server Standard och Enterprise-versioner som körs i en Windows-dator i Azure. Den här tjänsten tillhandahålls av den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md), som installeras automatiskt på SQL Server Windows-avbildningar i Azure-portalen.

Alla databaser som säkerhetskopieras till en Azure storage-konto som du konfigurerar. Säkerhetskopieringar kan krypteras och bibehålls i upp till 30 dagar.

SQL Server 2016 och högre VMs erbjuder fler anpassade alternativ med automatisk säkerhetskopiering v2. Dessa förbättringar omfattar:

- System-databassäkerhetskopieringar
- Manuell Säkerhetskopieringsschemat och tidsperioden
- Fullständig och log file säkerhetskopieringsfrekvens

Om du vill återställa en databas, måste du leta upp de säkerhetskopiera filerna som krävs i storage-konto och utför en återställning på din SQL-VM med hjälp av SQL Server Management Studio (SSMS) eller Transact-SQL-kommandon.

Mer information om hur du konfigurerar automatisk säkerhetskopiering för virtuella SQL-datorer, finns i följande artiklar:

- **SQL Server 2016/2017**: [Automatisk säkerhetskopiering v2 för virtuella Azure-datorer](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatiserad säkerhetskopiering för virtuella datorer med SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Azure Backup för SQL-datorer (förhandsversion)

[Azure Backup](/azure/backup/) ger en Enterprise-klass säkerhetskopiering för SQL Server som körs i virtuella Azure-datorer. Alla säkerhetskopior lagras och hanteras i Recovery Services-valvet. Det finns flera fördelar med den här lösningen, särskilt för företag:

- **Kräver ingen infrastruktur backup**: Du behöver inte hantera eller lagringsplatser som reservservrar.
- **Skala**: Skydda många virtuella datorer med SQL och tusentals databaser.
- **Betala per användning**: Den här funktionen är en separat tjänst som tillhandahålls av Azure Backup, men som med alla Azure-tjänster, du betalar bara för det du använder.
- **Central hantering och övervakning**: Centralt hantera alla dina säkerhetskopior, inklusive andra arbetsbelastningar som stöder Azure Backup, från en enda instrumentpanel i Azure.
- **Princip för driven säkerhetskopiering och kvarhållning**: Skapa standard säkerhetskopieringsprinciper för regelbundna säkerhetskopieringar. Upprätta principer för kvarhållning om du vill behålla säkerhetskopior i flera år.
- **Stöd för SQL alltid på**: Identifiera och skydda en SQL Server Always On-konfiguration och respekterar säkerhetskopiering säkerhetskopieringsinställningen för Tillgänglighetsgruppen.
- **15 minuters mål för återställningspunkt (RPO)**: Konfigurera SQL säkerhetskopieringar av transaktionsloggen upp till 15 minuter.
- **Tidpunkt för återställning till tidpunkt**: Använda portalen för att återställa databaser till en viss tidpunkt i tid utan att manuellt återställa flera fullständig, differentiell och säkerhetskopior.
- **Konsoliderade e-postaviseringar för fel**: Konfigurera konsoliderade e-postmeddelanden för eventuella fel.
- **Rollbaserad åtkomstkontroll**: Bestämma vem som kan hantera säkerhetskopiering och återställning via portalen.

Titta på följande videoklipp för en snabb överblick över hur det fungerar tillsammans med en demo:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Den här Azure Backup-lösningen för SQL VM är för närvarande i offentlig förhandsversion. Mer information finns i [säkerhetskopiera SQL Server-databas till Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Manuell säkerhetskopiering

Om du vill hantera säkerhetskopiering och återställning på din virtuella SQL-datorer manuellt finns flera alternativ beroende på vilken version av SQL Server som du använder. En översikt över säkerhetskopiering och återställning finns i följande artiklar baserat på din version av SQL Server:

- [Säkerhetskopiering och återställning för SQL Server 2016 och senare](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Säkerhetskopiering och återställning för SQLServer 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Säkerhetskopiering och återställning för SQLServer 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Säkerhetskopiering och återställning av SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Säkerhetskopiering och återställning för SQLServer 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

I följande avsnitt beskrivs flera manuell säkerhetskopiering och återställningsalternativ i detalj.

### <a name="backup-to-attached-disks"></a>Säkerhetskopieringen till anslutna diskar

För SQL Server som körs i virtuella Azure-datorer, kan du använda interna säkerhetskopieringen och återställa tekniker med anslutna diskar på den virtuella datorn för målet för de säkerhetskopiera filerna. Det finns dock en gräns för antalet diskar som du kan koppla till en Azure-dator, baserat på den [storleken på den virtuella datorn](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Det finns också arbetet med Diskhantering att tänka på.

Ett exempel på hur du manuellt skapar en fullständig säkerhetskopiering av databasen med hjälp av SQL Server Management Studio (SSMS) eller Transact-SQL finns i [skapa en fullständig Database-säkerhetskopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Säkerhetskopiering till URL

Från och med SQL Server 2012 SP1 CU2 kan du säkerhetskopiera och återställa direkt till Microsoft Azure Blob storage, som även kallas för säkerhetskopiering till URL: en. SQL Server 2016 introduceras också följande förbättringar för den här funktionen:

| Förbättring av 2016 | Information |
| --- | --- |
| **Striping** |När du säkerhetskopierar till Microsoft Azure blob storage, kan SQL Server 2016 sparas till flera BLOB-och aktivera säkerhetskopiering av stora databaser, upp till högst 12,8 TB. |
| **Ögonblicksbildsäkerhetskopia** |Genom att använda Azure ögonblicksbilder ger SQL Server av säkerhetskopiering stort sett ögonblickliga säkerhetskopior och snabba återställningar för databasfiler som lagras med Azure Blob storage-tjänsten. Den här funktionen kan du förenkla säkerhetskopieringen och Återställ principer. Av backup stöder också punkt återställning till tidpunkt. Mer information finns i [säkerhetskopior av Filögonblicksbilder för databasfiler i Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Mer information finns i ett av följande artiklar baserat på din version av SQL Server:

- **SQL Server 2016/2017**: [SQL Server-säkerhetskopiering till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014-säkerhetskopiering till URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012-säkerhetskopiering till URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Hanterad säkerhetskopiering

Från och med SQL Server 2014, automatiserar hanterad säkerhetskopiering skapandet av säkerhetskopieringar till Azure storage. I bakgrunden gör hanterad säkerhetskopiering för säkerhetskopiering till URL-funktionen som beskrivs i föregående avsnitt i den här artikeln. Hanterad säkerhetskopiering är också den underliggande funktion som stöd för automatisk säkerhetskopiering för SQL Server VM-tjänsten.

Från och med SQL Server 2016 kan har Managed backup ytterligare alternativ för schemaläggning och systemdatabasen säkerhetskopiering och fullständig och frekvens för loggsäkerhetskopiering.

Mer information finns i någon av följande artiklar baserat på din version av SQL Server:

- [Hanterad säkerhetskopiering till Microsoft Azure för SQL Server 2016 och senare](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Hanterad säkerhetskopiering till Microsoft Azure för SQLServer 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Beslutet matris

I följande tabell sammanfattas funktionerna med varje alternativ för säkerhetskopiering och återställning för SQL Server-datorer i Azure.

|| **Automatisk säkerhetskopiering** | **Azure Backup för SQL** | **Manuell säkerhetskopiering** |
|---|---|---|---|
| Kräver ytterligare Azure-tjänst |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurera princip för säkerhetskopiering i Azure portal | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Återställa databaser i Azure-portalen |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hantera flera servrar i en enda instrumentpanel |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Återställning från tidpunkt | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 minuters mål för återställningspunkt (RPO) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Princip för kortsiktig kvarhållning av säkerhetskopior (dagar) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Långsiktig kvarhållning av säkerhetskopior princip (månader, år) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Inbyggt stöd för SQL Server Always On |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Säkerhetskopiering till Azure Storage-konton | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisk) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisk) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(som hanteras av kunden) |
| Hantering av lagring och säkerhetskopiering av filer | | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Säkerhetskopieringen till anslutna diskar på den virtuella datorn |   |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Central anpassningsbara backup-rapporter |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsoliderade e-postaviseringar för fel |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Anpassa övervakning utifrån Azure Monitor-loggar |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Övervaka säkerhetskopieringsjobb med SSMS eller Transact-SQL-skript | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Återställ databasen med SSMS eller Transact-SQL-skript | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Nästa steg

Om du planerar distributionen av SQL Server i en Azure VM, hittar du etablering vägledning i följande guide: [Hur du etablerar en Windows SQL Server-dator i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

Säkerhetskopiering och återställning kan användas för att migrera dina data, finns men det potentiellt enklare migrering datasökvägar till SQL Server på en Azure-dator. En fullständig beskrivning av migreringsalternativ och rekommendationer finns [migrera en databas till SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).
