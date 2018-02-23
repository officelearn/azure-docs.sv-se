---
title: "Säkerhetskopiering och återställning av SQLServer | Microsoft Docs"
description: "Beskriver överväganden för säkerhetskopiering och återställning för SQL Server-databaser som körs på Azure Virtual Machines."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 16fef048e7c795f3d21fbc4185f6ba31bbc885fb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Säkerhetskopiering och återställning för SQL Server i Azure Virtual Machines
## <a name="overview"></a>Översikt
Azure-lagring underhålls 3 kopior av varje Virtuella Azure-disk att garantera skydd mot dataförlust eller skadade data fysisk data. Därmed, till skillnad från lokalt behöver du inte bekymra dig om dessa. Du bör dock fortfarande säkerhetskopiera SQL Server-databaser att skydda mot program- eller användarspecifik fel (t.ex infoga felaktiga data eller ta bort en tabell) och kunna återställa till en tidpunkt.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Du kan använda interna säkerhetskopieringen och återställa tekniker med anslutna diskar för mål för säkerhetskopian för SQL-servern körs i virtuella Azure-datorer. Det finns dock en gräns för antalet diskar som du kan koppla till en Azure-dator, baserat på de [storlek för den virtuella datorn](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Det finns också arbetet med Diskhantering att tänka på.

Från och med SQL Server 2014 kan du säkerhetskopiera och återställa Microsoft Azure Blob storage. SQL Server 2016 innehåller också förbättringar för det här alternativet. Dessutom för databasfiler som lagras i Microsoft Azure Blob storage, innehåller SQL Server 2016 ett alternativ för nästan omedelbar säkerhetskopieringar och snabb återställning med hjälp av Azure ögonblicksbilder. Den här artikeln innehåller en översikt över de här alternativen och ytterligare information finns på [SQL Server-säkerhetskopiering och återställning med Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx).

> [!NOTE]
> En beskrivning av alternativ för hur du säkerhetskopierar mycket stora databaser finns [flera Terabyte SQL Server-databasen säkerhetskopiering strategier för Azure Virtual Machines](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).
> 
> 

I avsnitten nedan innehåller information som är specifika för olika versioner av SQL Server som stöds i en virtuell Azure-dator.

## <a name="sql-server-virtual-machines"></a>SQL Server-datorer
När SQL Server-instansen körs på en virtuell dator i Azure, finns dina databasfiler redan på datadiskar med i Azure. Diskarna live i Azure Blob storage. Så ta skälen för att säkerhetskopiera databasen och metoderna du ändra något. Tänk på följande. 

* Du behöver inte längre att säkerhetskopiera databasen för att skydda mot maskinvara eller fel eftersom Microsoft Azure tillhandahåller detta skydd som en del av tjänsten Microsoft Azure.
* Du måste säkerhetskopiera databasen för att ge skydd mot fel eller för arkivering, regulatoriska orsaker och administrativa syften.
* Du kan lagra säkerhetskopian direkt i Azure. Mer information finns i följande avsnitt som vägledning för olika versioner av SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016
Har stöd för Microsoft SQL Server 2016 [säkerhetskopiering och återställning med Azure BLOB](https://msdn.microsoft.com/library/jj919148.aspx) funktioner finns i SQL Server 2014. Men den innehåller också följande förbättringar:

| Förbättring av 2016 | Information |
| --- | --- |
| **Striping** |När du säkerhetskopierar till Microsoft Azure blob storage stöder SQL Server 2016 säkerhetskopiera flera BLOB för att aktivera säkerhetskopiering av stora databaser högst 12,8 TB. |
| **Ögonblicksbild av säkerhetskopian** |SQL Server-fil – Ögonblicksbildsäkerhetskopia ger nästan omedelbar säkerhetskopior och snabb återställning för databasfiler med hjälp av Azure Blob storage-tjänst genom att använda Azure ögonblicksbilder. Den här funktionen kan du förenkla din säkerhetskopia och återställa principer. Filen ögonblicksbilder Säkerhetskopiering stöder också punkt tidpunkt för återställning. Mer information finns i [säkerhetskopior av ögonblicksbilder för databasfilerna i Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx). |
| **Hanterade schemaläggning av säkerhetskopiering** |SQL Server-hanterad säkerhetskopiering till Azure stöder nu anpassade scheman. Mer information finns i [SQL Server-hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). |

En genomgång av funktionerna i SQL Server 2016 när du använder Azure Blob storage finns [Självstudier: använda tjänsten Microsoft Azure Blob storage med SQL Server 2016 databaser](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014
SQL Server 2014 innehåller följande förbättringar:

1. **Säkerhetskopiera och återställa till Azure**:
   
   * *SQL Server-säkerhetskopiering till URL: en* har nu stöd i SQL Server Management Studio. Alternativet för att säkerhetskopiera till Azure är nu tillgänglig när du använder Säkerhetskopiering eller återställning av aktivitet eller guiden för underhåll plan i SQL Server Management Studio. Mer information finns i [SQL Server-säkerhetskopiering till URL: en](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
   * *SQL Server hanterad säkerhetskopiering till Azure* har nya funktioner som möjliggör hantering av automatisk säkerhetskopiering. Detta är särskilt användbart för att automatisera hanteringen av säkerhetskopiering för SQL Server 2014-instanser som körs på en Azure-dator. Mer information finns i [SQL Server-hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
   * *Automatisk säkerhetskopiering* ger ytterligare automation för att automatiskt aktivera *SQL Server-hanterad säkerhetskopiering till Azure* på alla befintliga och nya databaser i SQL Server-VM i Azure. Mer information finns i [Automatisk säkerhetskopiering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).
   * En översikt över alla alternativ för SQL Server 2014-säkerhetskopiering till Azure, se [SQL Server-säkerhetskopiering och återställning med Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
2. **Kryptering**: SQL Server 2014 stöder kryptering av data när du skapar en säkerhetskopia. Den stöder flera krypteringsalgoritmer och använda osf ett certifikat eller asymmetriska nyckeln. Mer information finns i [kryptering vid säkerhetskopiering](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012
Detaljerad information om SQL Server-säkerhetskopiering och återställning i SQL Server 2012 finns [säkerhetskopiering och återställning av SQL Server-databaser (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

Med början i SQL Server 2012 SP1 Cumulative Update 2 kan du säkerhetskopiera till och återställa från Azure Blob Storage-tjänsten. Den här förbättringen kan användas för att säkerhetskopiera SQL Server-databaser på en SQL-Server som körs på en virtuell Azure-dator eller en lokal instans. Mer information finns i [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Några av fördelarna med att använda tjänsten Azure Blob storage är möjlighet att kringgå 16 disk gränsen för anslutna diskar enkel hantering, direkt tillgängligheten för säkerhetskopian till en annan instans av SQL Server-instans som körs på en virtuell Azure-dator , eller en lokal instans för migrering eller katastrof återställning. En fullständig lista över fördelar med att använda en Azure blob storage-tjänst för säkerhetskopieringar för SQL Server finns i *fördelar* i avsnittet [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Rekommendationer om bästa praxis och felsökningsinformation finns i [säkerhetskopiering och återställning Metodtips (Azure Blob Storage Service)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008
SQL Server-säkerhetskopiering och återställning i SQL Server 2008 R2 finns [säkerhetskopiera och återställa databaser i SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

SQL Server-säkerhetskopiering och återställning i SQL Server 2008 finns [säkerhetskopiera och återställa databaser i SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Nästa steg
Om du planerar distributionen av SQL Server i en Azure VM, hittar du vägledning för etablering i följande Självstudier: [etablering av en SQL Server-dator på Azure med Azure Resource Manager](virtual-machines-windows-portal-sql-server-provision.md).

Säkerhetskopiering och återställning kan användas för att migrera data, finns det potentiellt enklare migrering datasökvägar till SQL Server på en virtuell dator i Azure. En fullständig beskrivning av migreringsalternativ och rekommendationer finns [migrera en databas till SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

Granska andra [resurser för att köra SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

