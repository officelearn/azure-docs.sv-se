---
title: Migrera en SQL Server-databas till SQL Server på en virtuell dator | Microsoft-dokument
description: Lär dig mer om hur du migrerar en lokal användardatabas till SQL Server på en virtuell Azure-dator.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646871"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrera en SQL Server-databas till SQL Server i en Azure VM

Det finns ett antal metoder för att migrera en lokal SQL Server-användardatabas till SQL Server i en Virtuell Azure.There are a number of methods to migrate an on-premises SQL Server user database to SQL Server in an Azure VM. Den här artikeln kommer kortfattat att diskutera olika metoder och rekommendera den bästa metoden för olika scenarier.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 och SQL Server 2008 R2 närmar sig [slutet av sin supportlivscykel](https://www.microsoft.com/sql-server/sql-server-2008) för lokala instanser. Om du vill utöka supporten kan du antingen migrera SQL Server-instansen till en Virtuell Azure-fil eller köpa utökade säkerhetsuppdateringar för att hålla den lokalt. Mer information finns i [Utöka stöd för SQL Server 2008 och 2008 R2 med Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Vilka är de primära migreringsmetoderna?
De primära migreringsmetoderna är:

* Utför lokala säkerhetskopieringar med komprimering och kopiera säkerhetskopian manuellt till den virtuella Azure-datorn
* Utföra en säkerhetskopia till URL och återställa till den virtuella Azure-datorn från URL:en
* Koppla från och kopiera sedan data och loggfiler till Azure blob storage och bifoga sedan till SQL Server i Azure VM från URL
* Konvertera lokal fysisk dator till Hyper-V VHD, ladda upp till Azure Blob-lagring och distribuera sedan som ny virtuell dator med uppladdad virtuell hårddisk
* Skicka hårddisk med Windows Import/Export Service
* Om du har en distribution av AlwaysOn-tillgänglighetsgrupp lokalt använder du [guiden Lägg till Azure-replik](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) för att skapa en replik i Azure och sedan redundans, vilket pekar användare på Azure-databasinstansen
* Använd SQL Server [transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) för att konfigurera Azure SQL Server-instansen som prenumerant och inaktivera sedan replikering, vilket pekar användare på Azure-databasinstansen

> [!TIP]
> Du kan också använda samma tekniker för att flytta databaser mellan virtuella SQL Server-datorer i Azure. Det finns till exempel inget sätt att uppgradera en virtuell virtuell SQL Server-galleriavbildning från en version/utgåva till en annan. I det här fallet bör du skapa en ny virtuell SQL Server-dator med den nya versionen/utgåvan och sedan använda en av migreringsteknikerna i den här artikeln för att flytta databaserna. 

## <a name="choosing-your-migration-method"></a>Välja migreringsmetod
För bästa prestanda för dataöverföring migrerar du databasfilerna till Den virtuella Azure-datorn med hjälp av en komprimerad säkerhetskopia.

Om du vill minimera driftstopp under databasmigreringsprocessen använder du alternativet AlwaysOn eller alternativet transaktionsreplikering.

Om det inte är möjligt att använda ovanstående metoder migrerar du databasen manuellt. Med den här metoden börjar du vanligtvis med en säkerhetskopia av databasen följt av en kopia av databassäkerhetskopian till Azure och utför sedan en databasåterställning. Du kan också kopiera själva databasfilerna till Azure och sedan bifoga dem. Det finns flera metoder som du kan utföra den här manuella processen för att migrera en databas till en Azure VM.

> [!NOTE]
> När du uppgraderar till SQL Server 2014 eller SQL Server 2016 från äldre versioner av SQL Server bör du överväga om ändringar behövs. Vi rekommenderar att du åtgärdar alla beroenden på funktioner som inte stöds av den nya versionen av SQL Server som en del av migreringsprojektet. Mer information om utgåvor och scenarier som stöds finns i [Uppgradera till SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

I följande tabell visas var och en av de primära migreringsmetoderna och diskuteras när användningen av varje metod är lämpligast.

| Metod | Version av källdatabas | Måldatabasversion | Storleksbegränsning för säkerhetskopiering av källdatabas | Anteckningar |
| --- | --- | --- | --- | --- |
| [Utför lokala säkerhetskopieringar med komprimering och kopiera säkerhetskopian manuellt till den virtuella Azure-datorn](#backup-and-restore) |SQL Server 2005 eller senare |SQL Server 2005 eller senare |[Lagringsgräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Detta är en mycket enkel och väl beprövad teknik för att flytta databaser över maskiner. |
| [Utföra en säkerhetskopia till URL och återställa till den virtuella Azure-datorn från URL:en](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 eller senare |SQL Server 2012 SP1 CU2 eller senare |< 12,8 TB för SQL Server 2016, annars < 1 TB | Den här metoden är bara ett annat sätt att flytta säkerhetskopian till den virtuella datorn med Azure-lagring. |
| [Koppla från och kopiera sedan data och loggfiler till Azure blob storage och bifoga sedan till SQL Server i virtuell Azure-dator från URL](#detach-and-attach-from-url) |SQL Server 2005 eller senare |SQL Server 2014 eller senare |[Lagringsgräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd den här metoden när du planerar att [lagra dessa filer med hjälp av Azure Blob storage-tjänsten](https://msdn.microsoft.com/library/dn385720.aspx) och bifoga dem till SQL Server som körs i en virtuell Azure-dator, särskilt med mycket stora databaser |
| [Konvertera lokala datorer till Hyper-V-hårddiskar, ladda upp till Azure Blob-lagring och distribuera sedan en ny virtuell dator med uppladdad virtuell hårddisk](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 eller senare |SQL Server 2005 eller senare |[Lagringsgräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd när [du tar med din egen SQL Server-licens](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)när du migrerar en databas som du ska köra på en äldre version av SQL Server, eller när du migrerar system- och användardatabaser tillsammans som en del av migreringen av databasen beroende på andra användardatabaser och/eller systemdatabaser. |
| [Skicka hårddisk med Windows Import/Export Service](#ship-hard-drive) |SQL Server 2005 eller senare |SQL Server 2005 eller senare |[Lagringsgräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd [Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) när manuell kopieringsmetod är för långsam, till exempel med mycket stora databaser |
| [Använda guiden Lägg till Azure-replik](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 eller senare |SQL Server 2012 eller senare |[Lagringsgräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimerar stilleståndstiden, använder när du har en alltid på lokal distribution |
| [Använda SQL Server transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 eller senare |SQL Server 2005 eller senare |[Lagringsgräns för Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Använd när du behöver minimera driftstopp och inte har en alltid på lokal distribution |

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning
Säkerhetskopiera databasen med komprimering, kopiera säkerhetskopian till den virtuella datorn och återställ sedan databasen. Om säkerhetskopian är större än 1 TB måste du stripe det eftersom den maximala storleken på en VM-disk är 1 TB. Använd följande allmänna steg för att migrera en användardatabas med den här manuella metoden:

1. Utför en fullständig säkerhetskopiering av databasen till en lokal plats.
2. Skapa eller ladda upp en virtuell dator med den version av SQL Server som önskas.
3. Installationsanslutning baserat på dina behov. Se [Anslut till en virtuell SQL Server-dator på Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Kopiera säkerhetskopian till den virtuella datorn med fjärrskrivbord, Utforskaren eller kopieringskommandot från en kommandotolk.

## <a name="backup-to-url-and-restore"></a>Säkerhetskopiera till URL och återställa
I stället för att säkerhetskopiera till en lokal fil kan du använda [säkerhetskopian till URL](https://msdn.microsoft.com/library/dn435916.aspx) och sedan återställa från URL till den virtuella datorn. Med SQL Server 2016 stöds stripe-säkerhetskopieringsuppsättningar, rekommenderas för prestanda och krävs för att överskrida storleksgränserna per blob. För mycket stora databaser rekommenderas användning av [Windows Import/Export Service.](../../../storage/common/storage-import-export-service.md)

## <a name="detach-and-attach-from-url"></a>Koppla bort och bifoga från URL
Koppla från databasen och loggfilerna och överför dem till [Azure Blob-lagring](https://msdn.microsoft.com/library/dn385720.aspx). Bifoga sedan databasen från URL:en på din Virtuella Azure-dator. Använd detta om du vill att de fysiska databasfilerna ska finnas i Blob-lagring. Detta kan vara användbart för mycket stora databaser. Använd följande allmänna steg för att migrera en användardatabas med den här manuella metoden:

1. Koppla bort databasfilerna från den lokala databasinstansen.
2. Kopiera de fristående databasfilerna till Azure blob storage med [kommandoradsverktyget AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Bifoga databasfilerna från Azure-URL:en till SQL Server-instansen i Azure VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertera till VM och överföra till URL och distribuera som en ny virtuell dator
Använd den här metoden för att migrera alla system- och användardatabaser i en lokal SQL Server-instans till virtuell Azure-dator. Använd följande allmänna steg för att migrera en hel SQL Server-instans med den här manuella metoden:

1. Konvertera fysiska eller virtuella datorer till Virtuella hårddiskar med Hyper-V.
2. Ladda upp VHD-filer till Azure Storage med hjälp av [cmdleten Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Distribuera en ny virtuell dator med hjälp av den uppladdade virtuella hårddisken.

> [!NOTE]
> Om du vill migrera ett helt program bör du överväga att använda [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Skicka hårddisk
Använd [metoden Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) för att överföra stora mängder fildata till Azure Blob-lagring i situationer där det är oöverkomligt dyrt eller inte möjligt att ladda över nätverket. Med den här tjänsten skickar du en eller flera hårddiskar som innehåller dessa data till ett Azure-datacenter, där dina data kommer att överföras till ditt lagringskonto.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

Instruktioner om hur du skapar en virtuell Azure SQL Server-dator från en tagen avbildning finns i [Tips & tricks om "kloning" av virtuella Azure SQL-datorer från tagna avbildningar](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) på bloggen CSS SQL Server Engineers.

