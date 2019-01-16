---
title: Migrera en SQL Server-databas till SQL Server på en virtuell dator | Microsoft Docs
description: Läs mer om hur du migrerar en lokal användare-databas till SQL Server i virtuella Azure-datorer.
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
ms.devlang: na
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95acda60935e82b226a1a0e860b5fa8effb8e47e
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332140"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrera en SQL Server-databas till SQL Server i en Azure VM

Det finns ett antal metoder för att migrera en användardatabas för en lokal SQL Server till SQL Server i en Azure VM. Den här artikeln beskrivs olika metoder kort och rekommenderar den bästa metoden för olika scenarier.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Vad är de primära metoderna?
De första migreringen metoderna är:

* Utför en lokal säkerhetskopiering genom att använda komprimering och manuellt kopiera säkerhetskopian till virtuella Azure-datorer
* Utföra en säkerhetskopiering till URL: en och återställning i Azure-dator från URL
* Koppla bort och kopiera sedan filerna för data och loggfiler till Azure blob storage och anslut sedan till SQL Server i Azure VM från URL
* Konvertera en lokal fysisk dator till Hyper-V virtuell Hårddisk, överföra till Azure Blob storage och sedan distribuera som ny virtuell dator med överfört VHD
* Skicka hårddisken med hjälp av Windows-tjänsten Import/Export
* Om du har en AlwaysOn Availability Group-distribution på plats, använder den [guiden för Lägg till Azure-replik](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) för att skapa en replik i Azure och sedan redundans, pekar du användare till Azure database-instans
* Använda SQL Server [Transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) för att konfigurera Azure SQL Server-instansen som en prenumerant och inaktivera replikering, pekar du användare till Azure database-instans

> [!TIP]
> Du kan också använda samma metoder för att flytta databaser mellan SQL Server-datorer i Azure. Exempel: Det går inte stöds kan uppgradera en SQL Server image z galerie virtuell dator från en version/utgåva till en annan. I det här fallet bör du skapa en ny SQL Server VM med den nya version/utgåvan och sedan använda en av metoder som migrering i den här artikeln för att flytta dina databaser. 

## <a name="choosing-your-migration-method"></a>Välja din migreringsmetod
För optimala dataöverföringsprestanda, migrera databasfilerna till den virtuella Azure-datorer med hjälp av en komprimerad säkerhetskopia.

Använd AlwaysOn-alternativet eller alternativet Transaktionsreplikering för att minimera driftstopp under migreringen.

Om det inte går att använda metoderna ovan, manuellt migrera din databas. Med den här metoden kan du vanligtvis börjar med en databassäkerhetskopia följt av en kopia av säkerhetskopian av databasen till Azure och sedan utför en databasåterställning av. Du kan också kopiera databasfilerna sig själva till Azure och sedan koppla dem. Det finns flera metoder som du kan utföra den manuella processen för att migrera en databas i en Azure virtuell dator.

> [!NOTE]
> När du uppgraderar till SQL Server 2014 eller SQL Server 2016 från äldre versioner av SQL Server, bör du överväga om ändringar som behövs. Vi rekommenderar att du hanterar alla beroenden på funktioner som inte stöds av den nya versionen av SQL Server som en del av ditt migreringsprojekt. Mer information om versioner som stöds och scenarier finns i [uppgradera till SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

I följande tabell visar vart och ett av de primära metoderna och beskriver när användningen av varje metod som passar bäst.

| Metod | Källversion för databasen | Mål-databasversion | Källa databasen säkerhetskopieringsstorlek begränsning | Anteckningar |
| --- | --- | --- | --- | --- |
| [Utför en lokal säkerhetskopiering genom att använda komprimering och manuellt kopiera säkerhetskopian till virtuella Azure-datorer](#backup-and-restore) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Azure VM-gränsen för lagring](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Det här är en mycket enkel och väl beprövad teknik för att flytta databaser mellan datorer. |
| [Utföra en säkerhetskopiering till URL: en och återställning i Azure-dator från URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 eller större |SQL Server 2012 SP1 CU2 eller större |< 12,8 TB för SQL Server 2016, annars < 1 TB | Den här metoden är bara ett annat sätt att flytta den säkerhetskopiera filen till den virtuella datorn med hjälp av Azure storage. |
| [Koppla bort och kopiera sedan filerna för data och loggfiler till Azure blob storage och anslut sedan till SQL Server på Azure-dator från URL](#detach-and-attach-from-url) |SQLServer 2005 eller senare |SQLServer 2014 eller senare |[Azure VM-gränsen för lagring](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd den här metoden när du planerar att [lagrar dessa filer med hjälp av Azure Blob storage-tjänsten](https://msdn.microsoft.com/library/dn385720.aspx) och bifogar dem i SQL Server som körs i en Azure VM, särskilt med mycket stora databaser |
| [Konvertera en lokal dator till Hyper-V virtuella hårddiskar, ladda upp till Azure Blob storage och sedan distribuera en ny virtuell dator med hjälp av överförda virtuella Hårddisken](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Azure VM-gränsen för lagring](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd när [tar med din egen SQL Server-licens](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), när du migrerar en databas som du ska köras på en äldre version av SQL Server eller när du migrerar system- och användardatabaserna tillsammans som en del av migreringen av databasen som är beroende av andra användardatabaser och/eller systemdatabaser. |
| [Skicka hårddisken med hjälp av Windows-tjänsten Import/Export](#ship-hard-drive) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Azure VM-gränsen för lagring](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd den [Windows Import/Export-tjänsten](../../../storage/common/storage-import-export-service.md) när manuella kopierings-metoden är för långsamma, till exempel med mycket stora databaser |
| [Använd den Azure-replik guiden Lägg till](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQLServer 2012 eller senare |SQLServer 2012 eller senare |[Azure VM-gränsen för lagring](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimerar avbrottstid, används när du har en Always On-premises distribution |
| [Använd Transaktionsreplikering för SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQLServer 2005 eller senare |SQLServer 2005 eller senare |[Azure VM-gränsen för lagring](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Använd när du behöver att minimera driftavbrott och som inte har en Always On-premises distribution |

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning
Säkerhetskopiera databasen med komprimering, kopiera säkerhetskopian till den virtuella datorn och sedan återställa databasen. Om din säkerhetskopia är större än 1 TB, måste den stripe eftersom den maximala storleken för en virtuell datordisk är 1 TB. Du kan använda dessa allmänna steg för att migrera en användardatabas som använder den här manuella metoden:

1. Utför en fullständig säkerhetskopiering av databasen till en lokal plats.
2. Skapa eller ladda upp en virtuell dator med versionen av SQL Server som önskas.
3. Ställ in anslutning efter dina behov. Se [ansluta till en SQL Server-dator på Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Kopiera dina säkerhetskopierade filerna till den virtuella datorn med fjärrskrivbord, Windows Explorer eller kopieringskommandot från en kommandotolk.

## <a name="backup-to-url-and-restore"></a>Säkerhetskopiering till URL: en och återställning
I stället för att säkerhetskopiera till en lokal fil som du kan använda den [säkerhetskopiering till URL: en](https://msdn.microsoft.com/library/dn435916.aspx) och sedan återställa från URL: en till den virtuella datorn. Med SQL Server 2016 är stripe säkerhetskopior stöds, rekommenderas för prestanda och krävs för att överskrida den storlek per blob. För mycket stora databaser, användning av den [Windows Import/Export-tjänsten](../../../storage/common/storage-import-export-service.md) rekommenderas.

## <a name="detach-and-attach-from-url"></a>Koppla från och koppla från URL
Koppla från databasen och loggfiler filer och överför dem till [Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx). Sedan koppla databasen från URL: en på den virtuella Azure-datorn. Använd det här alternativet om du vill att de befintliga databasfilerna ska finnas i Blob storage. Det kan vara användbart för mycket stora databaser. Du kan använda dessa allmänna steg för att migrera en användardatabas som använder den här manuella metoden:

1. Koppla bort databasfiler från lokala-databasinstans.
2. Kopiera filerna frånkopplad databas till Azure blob storage med hjälp av den [kommandoradsverktyget azcopy](../../../storage/common/storage-use-azcopy.md).
3. Bifoga databasfilerna från Azure-URL: en till SQL Server-instans i Azure-VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertera till VM och överföra till URL och distribuera som en ny virtuell dator
Använd den här metoden för att migrera alla system- och databaser i en lokal SQL Server-instans till Azure-dator. Du kan använda dessa allmänna steg för att migrera en hela SQL Server-instans med den här manuella metoden:

1. Omvandla fysiska eller virtuella datorer till Hyper-V virtuella hårddiskar.
2. Överför VHD-filer till Azure Storage med hjälp av den [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Distribuera en ny virtuell dator med hjälp av den överförda virtuella Hårddisken.

> [!NOTE]
> För att migrera ett helt program måste du överväga att använda [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Skicka hårddisken
Använd den [Windows-tjänsten Import/Export-metoden](../../../storage/common/storage-import-export-service.md) att överföra stora mängder fildata till Azure Blob storage i situationer där överför över nätverket är oöverkomligt dyra eller inte är möjligt. Med den här tjänsten kan du skicka en eller flera hårddiskar som innehåller dessa data till ett Azure-datacenter, där dina data ska överföras till ditt lagringskonto.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

Anvisningar om hur du skapar en Azure SQL Server-dator från en avbildning finns i [Tips och trick om kloning Azure SQL virtuella datorer från avbildningar](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) på CSS SQL Server Engineers-bloggen.

