---
title: "Migrera en SQL Server-databas till SQL Server på en virtuell dator | Microsoft Docs"
description: "Läs mer om hur du migrerar en lokal användardatabas till SQL Server i en virtuell Azure-dator."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jroth
ms.openlocfilehash: 64245a968eca94518d2e4238b4bc5c93c952563a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrera en SQL Server-databas till SQL Server i en Azure VM

Det finns ett antal metoder för att migrera en användardatabas för lokala SQL Server till SQL Server i en Azure VM. Den här artikeln kort diskuterar olika metoder och rekommenderar den bästa metoden för olika scenarier.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Vad är de primära metoderna?
De primära migreringsmetoderna är:

* Utför lokal säkerhetskopiering med komprimering och manuellt kopiera säkerhetskopian till den virtuella Azure-datorn
* Utför en säkerhetskopiering till URL och återställning till den virtuella Azure-datorn från URL
* Koppla från och sedan kopiera data och loggfilen filerna till Azure blob storage och anslut sedan till SQL Server i Azure VM från URL
* Konvertera lokala fysiska datorn till Hyper-V virtuell, ladda upp till Azure Blob storage och sedan distribuera som nya virtuella datorn med hjälp av överföra VHD
* Leverera hårddisken med hjälp av Windows Import/Export Service
* Om du har en AlwaysOn-distribution lokalt, Använd den [guiden för Lägg till Azure-replik](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) pekar användare på Azure-databasinstans för att skapa en replik i Azure och växling vid fel
* Använda SQL Server [Transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) för att konfigurera Azure SQL Server-instansen som en prenumerant och inaktivera replikering, pekar du användare till Azure-databasinstans

> [!TIP]
> Du kan också använda samma metoder för att flytta databaser mellan SQL Server-datorer i Azure. T.ex, går det inte stöds så här uppgraderar du en SQL Server-bild galleriet virtuell dator från en version/utgåva till en annan. I det här fallet bör du skapa en ny SQL Server-VM med den nya version/utgåvan och använda någon av migreringen tekniker i den här artikeln du flyttar databaser. 

## <a name="choosing-your-migration-method"></a>Välja din migreringsmetod
För optimala dataöverföringsprestanda migrera databasfilerna till en komprimerad säkerhetskopia Azure VM.

Använd alternativet AlwaysOn eller Transaktionsreplikering alternativet för att minimera driftstopp under migreringen av databasen.

Om det inte går att använda metoderna ovan, manuellt migrera din databas. Med den här metoden vanligtvis börja med en säkerhetskopia av databasen följt av en kopia av säkerhetskopierade databasen till Azure och sedan utför en databasåterställning av. Du kan också kopiera databasfilerna sig själva till Azure och sedan koppla dem. Det finns flera metoder som du kan utföra den manuella processen för att migrera en databas till en Azure VM.

> [!NOTE]
> När du uppgraderar till SQL Server 2014 eller SQL Server 2016 från äldre versioner av SQL Server, bör du överväga om ändringar krävs. Vi rekommenderar att du hanterar alla beroenden på funktioner som inte stöds av den nya versionen av SQL Server som en del av migreringen. Mer information om versioner som stöds och scenarier finns [uppgraderar till SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

I följande tabell listar de primära migrering metoderna och beskriver om varje metod som passar bäst.

| Metod | Databasversionen för källa | Målversionen av databasen | Källan databasen säkerhetskopieringsstorlek begränsning | Anteckningar |
| --- | --- | --- | --- | --- |
| [Utför lokal säkerhetskopiering med komprimering och manuellt kopiera säkerhetskopian till den virtuella Azure-datorn](#backup-and-restore) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Lagringsgränsen för Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Detta är en mycket enkel och väl testade teknik för att flytta databaser mellan datorer. |
| [Utför en säkerhetskopiering till URL och återställning till den virtuella Azure-datorn från URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 eller större |SQL Server 2012 SP1 CU2 eller större |< 12,8 TB för SQL Server 2016, annars < 1 TB | Den här metoden är bara ett annat sätt att flytta säkerhetskopian till den virtuella datorn med hjälp av Azure storage. |
| [Koppla från och sedan kopiera data och loggfilen filerna till Azure blob storage och ansluta till SQL Server i Azure-dator från URL](#detach-and-attach-from-url) |SQLServer 2005 eller senare |SQLServer 2014 eller högre |[Lagringsgränsen för Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd den här metoden när du planerar att [lagrar dessa filer med Azure Blob storage-tjänst](https://msdn.microsoft.com/library/dn385720.aspx) och koppla dem till SQL Server som körs i en Azure VM, särskilt med mycket stora databaser |
| [Konvertera lokal dator för Hyper-V virtuella hårddiskar, ladda upp till Azure Blob storage och distribuera en ny virtuell dator med överförda virtuell Hårddisk](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Lagringsgränsen för Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd när [ta din egen SQL Server-licens](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), när du migrerar en databas som ska köras på en äldre version av SQL Server, eller när du migrerar system- och databaser tillsammans som en del av migreringen av databasen som är beroende av andra användardatabaser och/eller systemdatabaser. |
| [Leverera hårddisken med hjälp av Windows Import/Export Service](#ship-hard-drive) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Lagringsgränsen för Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd den [Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) när manuell copy-metoden är för långsamt, såsom med mycket stora databaser |
| [Använd den Azure replik guiden Lägg till](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQLServer 2012 eller senare |SQLServer 2012 eller senare |[Lagringsgränsen för Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimerar avbrottstid, Använd när du har en Always On-premises distribution |
| [Använda SQL Server-Transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Lagringsgränsen för Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd när du vill minimera driftavbrott och inte har en alltid på lokal distribution |

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning
Säkerhetskopiera databasen med komprimering, kopiera säkerhetskopian till den virtuella datorn och sedan återställa databasen. Om din säkerhetskopia är större än 1 TB, måste den stripe eftersom den maximala storleken för en virtuell disk är 1 TB. Använd följande allmänna steg för att migrera en databas med hjälp av den här manuella metoden:

1. Utför en fullständig säkerhetskopiering av databasen till en lokal plats.
2. Skapa eller ladda upp en virtuell dator med versionen av SQL Server som önskas.
3. Konfigurera anslutning baserat på dina krav. Se [ansluta till en SQL Server-dator i Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Kopiera dina säkerhetskopierade filer till den virtuella datorn med fjärrskrivbord, Utforskaren eller kopieringskommandot från en kommandotolk.

## <a name="backup-to-url-and-restore"></a>Säkerhetskopiering till URL och återställning
I stället för att säkerhetskopiera till en lokal fil som du kan använda den [säkerhetskopiering till URL: en](https://msdn.microsoft.com/library/dn435916.aspx) och Återställ sedan från URL: en till den virtuella datorn. Med SQL Server 2016 som stripe säkerhetskopior stöds, rekommenderas för prestanda och krävs för att överskrida den storlek per blob. För mycket stora databaser, användning av den [Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) rekommenderas.

## <a name="detach-and-attach-from-url"></a>Koppla från och koppla från URL
Koppla från databasen och loggfilerna filerna och överför dem till [Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx). Sedan koppla databasen från URL-Adressen på Azure VM. Använd det här alternativet om du vill använda de befintliga databasfilerna finns i Blob storage. Detta kan vara användbart för mycket stora databaser. Använd följande allmänna steg för att migrera en databas med hjälp av den här manuella metoden:

1. Koppla bort databasfiler från lokala databasinstansen.
2. Kopiera filerna frånkopplad databas till Azure blob storage med hjälp av den [kommandoradsverktyget azcopy](../../../storage/common/storage-use-azcopy.md).
3. Bifoga databasfilerna från Azure-Webbadressen till SQL Server-instansen i Azure-VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertera till VM och överföra till URL och distribuera som en ny virtuell dator
Använd den här metoden för att migrera alla system- och databaser i en lokal SQL Server-instans till virtuella Azure-datorn. Använd följande allmänna steg för att migrera en hela SQL Server-instans med den här manuella metoden:

1. Omvandla fysiska eller virtuella datorer till Hyper-V virtuella hårddiskar med hjälp av [Microsoft Virtual Machine Converter](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx).
2. Överföra VHD-filer till Azure Storage med hjälp av den [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Distribuera en ny virtuell dator med hjälp av den överförda virtuella Hårddisken.

> [!NOTE]
> Överväg att använda för att migrera en hela programmet [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Leverera hårddisk
Använd den [Windows Import/Export Service metoden](../../../storage/common/storage-import-export-service.md) att överföra stora mängder data i filen till Azure Blob storage i situationer där överför via nätverket är orimligt hög eller inte är möjligt. Med den här tjänsten kan du skicka en eller flera hårddiskar som innehåller dessa data till en Azure-datacenter där data överförs till ditt lagringskonto.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Om du har frågor om SQL Server-datorer finns i [vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md).

Anvisningar om hur du skapar en Azure SQL Server-dator från en avbildning finns [Tips och trick om kloning Azure SQL virtuella datorer från avbildningar](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) på CSS tekniker som SQL Server-bloggen.

