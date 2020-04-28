---
title: Migrera en SQL Server databas till SQL Server på en virtuell dator | Microsoft Docs
description: Lär dig mer om hur du migrerar en lokal användar databas till SQL Server på en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646871"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrera en SQL Server-databas till SQL Server i en Azure VM

Det finns ett antal metoder för att migrera en lokal SQL Server användar databas till SQL Server i en virtuell Azure-dator. Den här artikeln handlar kortfattat om olika metoder och rekommenderar den bästa metoden för olika scenarier.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 och SQL Server 2008 R2 närmar [sig support livs cykelns slut](https://www.microsoft.com/sql-server/sql-server-2008) för lokala instanser. Om du vill utöka stödet kan du antingen migrera SQL Server-instansen till en virtuell Azure-dator eller köpa utökade säkerhets uppdateringar för att hålla den lokalt. Mer information finns i [utöka stödet för SQL Server 2008 och 2008 R2 med Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Vilka är metoderna för primär migrering?
Metoderna för den primära migreringen är:

* Utföra lokal säkerhets kopiering med komprimering och kopiera säkerhets kopian manuellt till den virtuella Azure-datorn
* Säkerhetskopiera till en URL och Återställ den till den virtuella Azure-datorn från URL: en
* Koppla från och kopiera sedan data-och loggfilerna till Azure Blob Storage och bifoga sedan till SQL Server i Azure VM från URL
* Konvertera den lokala fysiska datorn till Hyper-V VHD, ladda upp till Azure Blob Storage och distribuera sedan som ny virtuell dator med Uppladdad virtuell hård disk
* Leverera hård disk med tjänsten Windows import/export
* Om du har en hantering av AlwaysOn-tillgänglighetsgrupper lokalt kan du använda [guiden Lägg till Azure-replik](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) för att skapa en replik i Azure och sedan redundansväxla och peka användare till Azure Database-instansen
* Använd SQL Server [transaktionell replikering](https://msdn.microsoft.com/library/ms151176.aspx) för att konfigurera Azure SQL Server-instansen som prenumerant och inaktivera sedan replikering, som pekar på användare till Azure Database-instansen

> [!TIP]
> Du kan också använda samma metoder för att flytta databaser mellan SQL Server virtuella datorer i Azure. Det finns till exempel inget stöd för att uppgradera ett SQL Server Galleri – avbildnings-VM från en version/utgåva till en annan. I så fall bör du skapa en ny SQL Server VM med den nya versionen/versionen och sedan använda en av migrerings teknikerna i den här artikeln för att flytta databaserna. 

## <a name="choosing-your-migration-method"></a>Välja en metod för migrering
För bästa prestanda för data överföring migrerar du databasfilerna till den virtuella Azure-datorn med en komprimerad säkerhets kopia.

Om du vill minimera drift stopp under migreringen av databasen använder du antingen alternativet AlwaysOn eller alternativet transaktionell replikering.

Om det inte går att använda ovanstående metoder kan du migrera databasen manuellt. Med den här metoden börjar du normalt med en databas säkerhets kopia följt av en kopia av säkerhets kopian av databasen i Azure och utför sedan en databas återställning. Du kan också kopiera databasfilerna till Azure och sedan bifoga dem. Det finns flera metoder för att utföra den här manuella processen för att migrera en databas till en virtuell Azure-dator.

> [!NOTE]
> När du uppgraderar till SQL Server 2014 eller SQL Server 2016 från äldre versioner av SQL Server bör du överväga om det behövs några ändringar. Vi rekommenderar att du hanterar alla beroenden för funktioner som inte stöds av den nya versionen av SQL Server som en del av ditt migreringsjobb. Mer information om vilka utgåvor och scenarier som stöds finns i [Uppgradera till SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

I följande tabell visas var och en av de primära metoderna för migrering och information om hur användningen av varje metod är lämpligast.

| Metod | Käll databas version | Mål databas version | Storleks begränsning för säkerhets kopiering av käll databasen | Anteckningar |
| --- | --- | --- | --- | --- |
| [Utföra lokal säkerhets kopiering med komprimering och kopiera säkerhets kopian manuellt till den virtuella Azure-datorn](#backup-and-restore) |SQL Server 2005 eller mer |SQL Server 2005 eller mer |[Lagrings gräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Det här är en mycket enkel och vältestad teknik för att flytta databaser mellan datorer. |
| [Säkerhetskopiera till en URL och Återställ den till den virtuella Azure-datorn från URL: en](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 eller senare |SQL Server 2012 SP1 CU2 eller senare |< 12,8 TB för SQL Server 2016, annars < 1 TB | Den här metoden är bara ett annat sätt att flytta säkerhets kopian till den virtuella datorn med Azure Storage. |
| [Koppla från och kopiera sedan data-och loggfilerna till Azure Blob Storage och bifoga sedan till SQL Server i den virtuella Azure-datorn från URL: en](#detach-and-attach-from-url) |SQL Server 2005 eller mer |SQL Server 2014 eller mer |[Lagrings gräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd den här metoden när du planerar att [lagra filerna med hjälp av Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/dn385720.aspx) och koppla dem till SQL Server som körs i en virtuell Azure-dator, särskilt med mycket stora databaser |
| [Konvertera den lokala datorn till Hyper-V-VHD: er, ladda upp till Azure Blob Storage och distribuera sedan en ny virtuell dator med Uppladdad virtuell hård disk](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 eller mer |SQL Server 2005 eller mer |[Lagrings gräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd när du vill använda [din egen SQL Server-licens](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)när du migrerar en databas som ska köras på en äldre version av SQL Server, eller när du migrerar system-och användar databaser tillsammans som en del av migreringen av databasen som är beroende av andra användar databaser och/eller system databaser. |
| [Leverera hård disk med tjänsten Windows import/export](#ship-hard-drive) |SQL Server 2005 eller mer |SQL Server 2005 eller mer |[Lagrings gräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd [tjänsten Windows import/export](../../../storage/common/storage-import-export-service.md) när metoden för manuell kopiering är för långsam, t. ex. med mycket stora databaser |
| [Använd guiden Lägg till Azure-replik](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 eller mer |SQL Server 2012 eller mer |[Lagrings gräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimerar drift stopp, Använd när du har en lokal distribution som alltid är lokal |
| [Använd SQL Server Transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 eller mer |SQL Server 2005 eller mer |[Lagrings gräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd när du behöver minimera nedtid och inte har en lokal distribution |

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning
Säkerhetskopiera databasen med komprimering, kopiera säkerhets kopian till den virtuella datorn och återställ sedan databasen. Om säkerhets kopian är större än 1 TB måste du ta bort den eftersom den maximala storleken på en virtuell dator disk är 1 TB. Använd följande allmänna steg för att migrera en användar databas med den här manuella metoden:

1. Utför en fullständig säkerhets kopiering av databasen till en lokal plats.
2. Skapa eller ladda upp en virtuell dator med den version av SQL Server som önskas.
3. Konfigurera anslutningen utifrån dina krav. Se [ansluta till en SQL Server virtuell dator på Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Kopiera dina säkerhets kopior till den virtuella datorn med hjälp av fjärr skrivbord, Utforskaren eller kopierings kommandot från en kommando tolk.

## <a name="backup-to-url-and-restore"></a>Säkerhetskopiera till URL och Återställ
I stället för att säkerhetskopiera till en lokal fil kan du använda [säkerhets kopieringen till URL: en](https://msdn.microsoft.com/library/dn435916.aspx) och sedan återställa från webb adressen till den virtuella datorn. Med SQL Server 2016 stöds stripe-säkerhetskopieringar, de är rekommenderade för prestanda och måste överskrida storleks gränserna per blob. För mycket stora databaser rekommenderas användningen av [tjänsten Windows import/export](../../../storage/common/storage-import-export-service.md) .

## <a name="detach-and-attach-from-url"></a>Koppla från och koppla från URL
Koppla från databasen och loggfilerna och överför dem till [Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx). Koppla sedan databasen från URL: en på den virtuella Azure-datorn. Använd detta om du vill att de fysiska databasfilerna ska finnas i Blob Storage. Detta kan vara användbart för mycket stora databaser. Använd följande allmänna steg för att migrera en användar databas med den här manuella metoden:

1. Koppla bort databasfilerna från den lokala databas instansen.
2. Kopiera de frånkopplade databasfilerna till Azure Blob Storage med hjälp av [kommando rads verktyget AzCopy](../../../storage/common/storage-use-azcopy.md).
3. Bifoga databasfilerna från Azure-URL: en till SQL Server-instansen på den virtuella Azure-datorn.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertera till VM och överföra till URL och distribuera som en ny virtuell dator
Använd den här metoden för att migrera alla system-och användar databaser i en lokal SQL Server instans till en virtuell Azure-dator. Använd följande allmänna steg för att migrera en hel SQL Server-instans med hjälp av den här manuella metoden:

1. Konvertera fysiska eller virtuella datorer till Hyper-V-VHD: er.
2. Ladda upp VHD-filer till Azure Storage med hjälp av [cmdleten Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Distribuera en ny virtuell dator med hjälp av den överförda virtuella hård disken.

> [!NOTE]
> Om du vill migrera ett helt program bör du överväga att använda [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Leverera hård disk
Använd [metoden Windows import/export-tjänsten](../../../storage/common/storage-import-export-service.md) för att överföra stora mängder fildata till Azure Blob Storage i situationer där överföring över nätverket är prohibitively dyrt eller inte genomförbart. Med den här tjänsten kan du skicka en eller flera hård diskar som innehåller dessa data till ett Azure-datacenter, där dina data överförs till ditt lagrings konto.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på azure Virtual Machines översikt](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

Anvisningar om hur du skapar en virtuell Azure SQL Server-dator från en avbildning finns i [Tips & trick på kloning av virtuella Azure SQL-datorer från insamlade avbildningar](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) på CSS SQL Server Engineers-bloggen.

