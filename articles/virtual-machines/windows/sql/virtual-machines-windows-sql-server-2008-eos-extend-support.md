---
title: Utöka stöd för SQL Server 2008 och SQL Server 2008 R2 med Azure
description: Lär dig hur du utökar stödet för SQL Server 2008 och SQL Server 2008 R2 genom att migrera din SQL Server-instans till Azure eller köp av utökad support om du vill behålla instanser på lokalt.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b16fecd6db1f4bed319c832795a2252f8de96c6c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607125"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Utöka stöd för SQL Server 2008 och SQL Server 2008 R2 med Azure

SQL Server 2008 och SQL Server 2008 R2 båda närmar sig den [slutet av sin livscykel för support (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Eftersom många av våra kunder fortfarande använder båda versionerna, tillhandahåller vi flera alternativ för att fortsätta få support. Du kan migrera dina lokala SQL Server-instanser till virtuella Azure-datorer (VM), migrera till Azure SQL Database, eller vara på plats och köpa utökade säkerhetsuppdateringar.

Till skillnad från med en hanterad instans kräver migrera till en Azure-dator inte recertifying dina program. Och till skillnad från med vistas på plats, får du kostnadsfria utökade säkerhetsuppdateringar genom att migrera till en Azure-dator.

Resten av den här artikeln innehåller överväganden för att migrera SQL Server-instansen till en Azure-dator.

## <a name="provisioning"></a>Etablering

Det finns en betala per användning `SQL Server 2008 R2 on Windows Server 2008 R2` avbildning som är tillgängliga på Azure marketplace.

Kunder som finns på SQL Server 2008 måste antingen att själv installera eller uppgradera till SQL Server 2008 R2. På samma sätt kan behöver kunder på Windows Server 2008 antingen distribuera sina virtuella datorer från en anpassad virtuell Hårddisk eller uppgradera till Windows Server 2008 R2.

Avbildningar som distribueras via Marketplace medföljer SQL IaaS-tillägget som redan är installerat. SQL IaaS-tillägget är ett krav för automatisk uppdatering och flexibla licensiering. Kunder som distribuerar egen installerade virtuella datorer behöver du installera SQL IaaS-tillägget manuellt. SQL IaaS-tillägget stöds inte på Windows 2008.

  > [!NOTE]
  > När SQL-servern `Create` och `Manage` blad fungerar med SQL Server 2008R2-avbildning i Azure-portalen, följande funktioner är _stöds inte_: Automatisk säkerhetskopiering, Azure Key Vault-integrering, R Services och lagringskonfiguration.

## <a name="licensing"></a>Licensiering
Betala per användning SQL Server 2008R2-distributioner kan konvertera till [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Om du vill konvertera en licens med Software Assurance (SA) baserat till betala per användning, kunder bör registreras med SQL-VM [resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). När registrerad hos resursprovidern SQL VM, att licenstyp SQL utbytbara mellan AHB och betala per användning.

Lokal installerade instanser av SQL Server 2008 eller SQL Server 2008 R2 på Azure VM kan registrera med SQL-resursprovidern och konvertera sina licenstyp till betala per användning.

## <a name="migration"></a>Migrering
Du kan migrera EOS SQL Server-instanser till en Azure-dator med manuell säkerhetskopiering/återställning metoder. Det här är de vanligaste migreringsmetod från en lokal plats till en Azure virtuell dator.

### <a name="azure-site-recovery"></a>Azure Site Recovery

För bulk-migrering, rekommenderar vi att [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) service. Kunder kan replikera den hela den virtuella datorn, inklusive SQL Server från en lokal plats till en Azure virtuell dator med Azure Site Recovery.

SQL Server kräver appkonsekventa ögonblicksbilder för Azure Site Recovery att garantera recovery; och Azure Site Recovery stöder appkonsekventa ögonblicksbilder med minimiintervall för 1 timme. Minsta möjliga för SQL Server med Azure Site Recovery-migreringar Återställningspunktmålet är 1 timme och RTO är 2 timmar plus tiden för återställning av SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

Den [Database Migration Service](/azure/dms/dms-overview) är ett alternativ för kunder om migrering från en lokal plats till en Azure virtuell dator genom att uppgradera SQL Server till SQL Server 2012 och senare.

## <a name="disaster-recovery"></a>Haveriberedskap

Lösningar för haveriberedskap för EOS SQL Server på Azure VM är följande:

- **SQL Server-säkerhetskopieringar**: Använda Azure Backup för att skydda din SQL-Server för EOS mot utpressningstrojaner, oavsiktlig borttagning och skador. Lösningen är för närvarande i förhandsversion för EOS SQL Server och har stöd för SQL Server 2008 och 2008 R2 körs på Windows 2008 R2 SP1. Mer information finns i det här [artikel](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)
- **Loggöverföring**: Du kan skapa en replik för leverans av loggen i en annan zon eller Azure-region med kontinuerlig återställningar för att minska RTO. Kunder måste du manuellt konfigurerar loggöverföring.
- **Azure Site Recovery**: Du kan replikera den virtuella datorn mellan zoner och -regioner via Azure Site Recovery-replikering. SQL Server kräver App programkonsekventa ögonblicksbilder att garantera rör katastrofåterställning. Azure Site Recovery erbjuder minst 1 timme RPO och två timmar + SQL Server-återställningstid RTO för EOS SQL Server DR.

## <a name="security-patching"></a>Säkerhetskorrigeringar
Utökade säkerhetsuppdateringar för SQL Server-datorer kommer att levereras via Microsoft Update-kanaler när SQL Server-dator har registrerats med SQL [resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). Antingen du kan hämta korrigeringar manuellt eller automatiskt.

**Automatisk uppdatering** är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Du kan ange en dag i veckan, tid och varaktighet för en underhållsperiod om SQL IaaS-tillägget har installerats. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema.  Mer information finns i [Automatisk uppdatering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Nästa steg

Migrera dina SQLServer-dator till Azure

* [Migrera en SQL Server-databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)

Kom igång med SQL Server på Azure Virtual Machines:

* [Skapa en virtuell SQL Server-dator på Azure Portal](quickstart-sql-vm-create-portal.md)

Få svar på vanliga frågor om virtuella SQL-datorer:

* [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
