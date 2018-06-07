---
title: Säkerhetskopiering och återställning av SQLServer i virtuella Azure-datorer | Microsoft Docs
description: Beskriver överväganden för säkerhetskopiering och återställning för SQL Server-databaser som körs på Azure Virtual Machines.
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
ms.openlocfilehash: 4b90d1b9b2ee64722d3c92bcbd8fa205c9b59ebd
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809615"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Säkerhetskopiering och återställning för SQL Server i Azure Virtual Machines

Den här artikeln ger vägledning om säkerhetskopiering och återställning av alternativen för SQL Server i Windows Azure virtuella datorer som körs. Azure-lagring underhålls tre kopior av varje Virtuella Azure-disk att garantera skydd mot dataförlust eller skadade data fysisk data. Därmed, till skillnad från lokalt behöver du inte att fokusera på maskinvarufel. Du bör dock fortfarande att säkerhetskopiera SQL Server-databaser att skydda mot fel på programmet eller användare som oavsiktligt data infogningar och borttagningar. I det här fallet är det viktigt att kunna återställa till en specifik tidpunkt.

Den första delen av den här artikeln innehåller en översikt över tillgängliga säkerhetskopierings- och alternativ för återställning. Detta följs av avsnitt som innehåller mer information om varje strategi.

## <a name="backup-and-restore-options"></a>Alternativ för säkerhetskopiering och återställning

Följande tabell innehåller information om olika alternativ för säkerhetskopiering och återställning för SQL Server körs på virtuella Azure-datorer:

| Strategi | SQL-versioner | Beskrivning |
|---|---|---|---|
| [Automatisk säkerhetskopiering](#automated) | 2014<br/> 2016<br/> 2017 | Automatisk säkerhetskopiering kan du schemalägga regelbunden säkerhetskopiering för alla databaser på en SQL Server-VM. Säkerhetskopior lagras i Azure-lagring för upp till 30 dagar. Från och med SQL Server 2016, erbjuder automatisk säkerhetskopiering v2 ytterligare alternativ, till exempel konfigurera manuell schemaläggning och frekvensen av fullständig och säkerhetskopior. |
| [Azure-säkerhetskopiering av SQL virtuella datorer](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure-säkerhetskopiering ger en Enterprise-klass säkerhetskopiering för SQL Server körs i virtuella Azure-datorer. Med den här tjänsten kan du centralt hantera säkerhetskopior för flera servrar och tusentals databaser. Databaser kan återställas till en specifik tidpunkt i portalen. Den erbjuder en anpassningsbara bevarandeprincip som kan underhålla säkerhetskopior i år. Den här funktionen är för närvarande i förhandsversion. |
| [Manuell säkerhetskopiering](#manual) | Alla | Beroende på din version av SQL Server finns det olika metoder för att säkerhetskopiera och återställa SQL Server som körs på en Azure VM manuellt. Du är ansvarig för hur dina databaser säkerhetskopieras och lagringsplats och hantering av dessa säkerhetskopieringar i det här scenariot. |

I följande avsnitt beskrivs varje alternativ i detalj. Den sista delen av den här artikeln innehåller en sammanfattning i form av en matris för funktionen.

## <a id="autoamted"></a> Automatisk säkerhetskopiering

Automatisk säkerhetskopiering innehåller en automatisk säkerhetskopiering för SQL Server Standard och Enterprise-utgåvor som körs i en Windows Azure VM. Den här tjänsten tillhandahålls av den [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md), som installeras automatiskt på SQL Server Windows avbildningar av virtuella datorer i Azure-portalen.

Alla databaser säkerhetskopieras till en Azure storage-konto som du konfigurerar. Säkerhetskopieringar kan krypteras och sparas i upp till 30 dagar.

SQL Server 2016 och högre virtuella datorer har fler anpassningsalternativ med automatisk säkerhetskopiering v2. Dessa förbättringar omfattar:

- System databassäkerhetskopieringar
- Manuell schemat för säkerhetskopiering och tidsfönster
- Fullständig och log file frekvens för säkerhetskopiering

Om du vill återställa en databas måste du leta upp krävs säkerhetskopierade filer i lagringskontot och utför en återställning på din SQL-VM med hjälp av SQL Server Management Studio (SSMS) eller Transact-SQL-kommandon.

Mer information om hur du konfigurerar automatisk säkerhetskopiering för virtuella datorer SQL finns i något av följande artiklar:

- **SQL Server 2016/2017**: [automatisk säkerhetskopiering v2 för virtuella Azure-datorer ](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQLServer 2014**: [automatisk säkerhetskopiering för SQL Server 2014 virtuella datorer](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Azure-säkerhetskopiering av SQL virtuella datorer (förhandsversion)

[Azure-säkerhetskopiering](/azure/backup/) ger en Enterprise-klass säkerhetskopiering för SQL Server körs i virtuella Azure-datorer. Alla säkerhetskopior lagras och hanteras i Recovery Services-valvet. Det finns flera fördelar med den här lösningen, särskilt för företag:

- **Noll-infrastruktur säkerhetskopiering**: du behöver inte hantera reservservrar eller lagringsplatser.
- **Skala**: skydda många SQL virtuella datorer och tusentals databaser.
- **Betala per användning**: den här funktionen är en separat tjänst som tillhandahålls av Azure Backup, men precis som med alla Azure-tjänster, du betalar bara för det du använder.
- **Central hantering och övervakning av**: centralt hantera alla dina säkerhetskopieringar, inklusive andra arbetsbelastningar som stöder Azure Backup från en enda instrumentpanel i Azure.
- **Principen som drivs av säkerhetskopiering och kvarhållning**: skapa principer för standard säkerhetskopiering för regelbundna säkerhetskopieringar. Upprätta bevarandeprinciper för att upprätthålla säkerhetskopieringar i år.
- **Stöd för SQL Always On**: identifiera och skydda en SQL Server Always On-konfiguration och respektera säkerhetskopiering säkerhetskopiering Availability Group-inställningen.
- **15 minuter Recovery punkt mål (RPO)**: Konfigurera SQL säkerhetskopieringar av transaktionsloggen upp till var 15: e minut.
- **Punkt tidpunkt för återställning**: använda portalen för att återställa databaser till en specifik tidpunkt utan att manuellt återställa flera differentiell, fullständig och loggsäkerhetskopior.
- **Konsoliderade e-postaviseringar för fel**: Konfigurera konsoliderade e-postaviseringar efter fel.
- **Rollbaserad åtkomstkontroll**: bestämma vem som kan hantera säkerhetskopiering och återställning via portalen.

Titta på följande videoklipp för en snabb överblick över hur det fungerar tillsammans med en demo:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Den här Azure Backup-lösningen för SQL virtuella datorer är för närvarande i förhandsversion. Mer information finns i [säkerhetskopiera SQL Server-databas till Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Manuell säkerhetskopiering

Om du vill hantera säkerhetskopiering och återställning på din SQL virtuella datorer manuellt finns flera alternativ beroende på vilken version av SQL Server som du använder. En översikt över säkerhetskopiering och återställning finns i följande artiklar baserat på din version av SQL Server:

- [Säkerhetskopiering och återställning för SQL Server 2016 och senare](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Säkerhetskopiering och återställning för SQLServer 2014](https://msdn.microsoft.com/en-us/library/ms187048%28v=sql.120%29.aspx)
- [Säkerhetskopiering och återställning för SQLServer 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Säkerhetskopiering och återställning av SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Säkerhetskopiering och återställning för SQLServer 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

I följande avsnitt beskrivs flera manuell säkerhetskopiering och återställningsalternativ i detalj.

### <a name="backup-to-attached-disks"></a>Säkerhetskopieringen till anslutna diskar

Du kan använda interna säkerhetskopieringen och återställa tekniker med anslutna diskar på den virtuella datorn för målet för säkerhetskopian för SQL-servern körs i virtuella Azure-datorer. Det finns dock en gräns för antalet diskar som du kan koppla till en Azure-dator, baserat på de [storlek för den virtuella datorn](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Det finns också arbetet med Diskhantering att tänka på.

Ett exempel på hur du manuellt skapar en fullständig säkerhetskopiering av databasen med hjälp av SQL Server Management Studio (SSMS) eller Transact-SQL finns [skapa en fullständig Database-säkerhetskopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Säkerhetskopiering till URL

Från och med SQL Server 2012 SP1 CU2 kan du säkerhetskopiera och återställa direkt till Microsoft Azure Blob-lagring, som även kallas för säkerhetskopiering till URL: en. SQL Server 2016 infördes också följande förbättringar för den här funktionen:

| Förbättring av 2016 | Information |
| --- | --- |
| **Striping** |När du säkerhetskopierar till Microsoft Azure blob storage stöder SQL Server 2016 säkerhetskopiera flera BLOB för att aktivera säkerhetskopiering av stora databaser högst 12,8 TB. |
| **Ögonblicksbild av säkerhetskopian** |SQL Server-fil – Ögonblicksbildsäkerhetskopia ger nästan omedelbar säkerhetskopior och snabb återställning för databasfiler med hjälp av Azure Blob storage-tjänst genom att använda Azure ögonblicksbilder. Den här funktionen kan du förenkla din säkerhetskopia och återställa principer. Filen ögonblicksbilder Säkerhetskopiering stöder också punkt tidpunkt för återställning. Mer information finns i [säkerhetskopior av ögonblicksbilder för databasfilerna i Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Mer information finns i något av följande artiklar baserat på din version av SQL Server:

- **SQL Server 2016/2017**: [SQL Server-säkerhetskopiering till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQLServer 2014**: [SQL Server 2014-säkerhetskopiering till URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQLServer 2012**: [SQL Server 2012-säkerhetskopiering till URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Hanterad säkerhetskopiering

Från och med SQL Server 2014, automatiserar hanterad säkerhetskopiering skapandet av säkerhetskopieringar till Azure-lagring. I bakgrunden gör hanterad säkerhetskopiering för säkerhetskopiering till URL: en funktion som beskrivs i föregående avsnitt i den här artikeln. Hanterad säkerhetskopiering är också den underliggande funktion som stöder tjänsten SQL Server-VM automatisk säkerhetskopiering.

Från och med SQL Server 2016, har hanterad säkerhetskopiering ytterligare alternativ för schemaläggning av säkerhetskopiering och fullständiga-databas och frekvens för säkerhetskopiering av loggen.

Mer information finns i något av följande artiklar baserat på din version av SQL Server:

- [Hanterad säkerhetskopiering till Microsoft Azure för SQL Server 2016 och senare](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Hanterad säkerhetskopiering till Microsoft Azure för SQLServer 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Beslut matris

I följande tabell sammanfattas funktionerna med varje alternativ för säkerhetskopiering och återställning för SQL Server-datorer i Azure.

|| **Automatisk säkerhetskopiering** | **Azure SQL-säkerhetskopiering** | **Manuell säkerhetskopiering** |
|---|---|---|---|
| Kräver ytterligare Azure-tjänst |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurera princip för säkerhetskopiering i Azure-portalen | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Återställa databaser i Azure-portalen |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hantera flera servrar i en och samma instrumentpanel |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Återställning från tidpunkt | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 minuter Återställningspunktmål (RPO) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Kortsiktig säkerhetskopiering bevarandeprincip (dagar) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Långsiktig säkerhetskopiering bevarandeprincip (månader, år) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Inbyggt stöd för SQL Server alltid aktiverad |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Säkerhetskopiering till Azure Storage-konton | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisk) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisk) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(kund hanteras) |
| Hantering av lagring och säkerhetskopiering av filer | | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Säkerhetskopieringen till anslutna diskar på den virtuella datorn |   |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Central anpassningsbara rapporter |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsoliderade e-postaviseringar för fel |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Anpassa övervakningen baserat på OMS |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Övervaka säkerhetskopieringsjobb med SSMS eller Transact-SQL-skript | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Återställa databaser med SSMS eller Transact-SQL-skript | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Nästa steg

Om du planerar distributionen av SQL Server i en Azure VM, hittar du vägledning för etablering i följande guide: [hur du etablerar en virtuell dator i Windows SQL Server i Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

Säkerhetskopiering och återställning kan användas för att migrera data, finns det potentiellt enklare migrering datasökvägar till SQL Server på en virtuell dator i Azure. En fullständig beskrivning av migreringsalternativ och rekommendationer finns [migrera en databas till SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).