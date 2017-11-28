---
title: "Flytta stora mängder data till/från molnlagring i Azure | Microsoft Docs"
description: "En översikt över de olika metoderna för att flytta data till och från Azure Storage."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: db0f09433750a3af2d70039d780a25ad64bb4df1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="moving-data-to-and-from-azure-storage"></a>Flytta data till och från Azure Storage
Om du vill flytta lokala data till Azure Storage (eller vice versa) finns det flera olika sätt att göra detta. Den metod som passar dig bäst beror på ditt scenario. Den här artikeln ger en snabb överblick över olika scenarier och lämpliga erbjudanden för varje kriterium.

## <a name="building-applications"></a>Skapa program
Om du utvecklar ett program som utvecklar mot REST-API eller en av våra många klientbibliotek är ett bra sätt att flytta data till och från Azure Storage.

Azure Storage innehåller omfattande klientbibliotek för .NET, iOS, Java, Android, universella Windowsplattformen (UWP), Xamarin, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Se [Kom igång med Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) vill veta mer.

Dessutom kan vi erbjuder även den [Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) som är ett bibliotek som är utformat för högpresterande kopiering av data till och från Azure. Se vår Data Movement Library [dokumentationen](https://github.com/Azure/azure-storage-net-data-movement) vill veta mer. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Visa/interagera snabbt med dina data
Om du vill att ett enkelt sätt att visa dina Azure Storage data medan du har också möjlighet att ladda upp och hämta data bör du använda en Azure Lagringsutforskaren.

Kolla vår lista över [Azure lagringsutforskare](../storage-explorers.md) vill veta mer.

## <a name="system-administration"></a>Systemadministration
Om du behöver eller inte bekvämare med kommandoradsverktyget (t.ex. systemadministratörer) följer här några alternativ för du bör tänka på:

### <a name="azcopy"></a>AzCopy
AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Du kan också kopiera data inom ett lagringskonto eller mellan olika lagringskonton.

Se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md) vill veta mer.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera tjänster på Azure. Det är ett uppgiftsbaserat kommandoradsgränssnitt och skriptspråk som utformats specifikt för systemadministration.

Se [med hjälp av Azure PowerShell med Azure Storage](storage-powershell-guide-full.md) vill veta mer.

### <a name="azure-cli"></a>Azure CLI
Azure CLI tillhandahåller en uppsättning med öppen källkod, plattformsoberoende kommandon för att arbeta med Azure-tjänster. Azure CLI är tillgänglig på Windows, OSX och Linux.

Se [med hjälp av Azure CLI med Azure Storage](../storage-azure-cli.md) vill veta mer.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Flytta stora mängder data med ett långsamt nätverk
En av de största utmaningarna som är associerade med flytta stora mängder data är överföringstiden. Om du vill hämta data till och från Azure Storage utan att bekymra dig om kostnaderna för nätverk eller skriva kod är en lämplig lösning med Azure Import/Export.

Se [Azure Import/Export](../storage-import-export-service.md) vill veta mer.

## <a name="backing-up-your-data"></a>Säkerhetskopiera dina data
Om du behöver säkerhetskopiera data till Azure Storage, är Azure Backup att. Detta är en kraftfull lösning för att säkerhetskopiera data lokalt och virtuella Azure-datorer.

Se [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) vill veta mer.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Åtkomst till data lokalt och från molnet
Om du behöver en lösning för att komma åt data lokalt och från molnet, bör sedan du använda Azures moln hybridlagringslösning, StorSimple. Den här lösningen består av en fysisk enhet att Intelligent lagrar ofta använda data på SSD, används ibland data på hårddiskar och inaktiva/backup/arkiveringsdata på Azure Storage.

Se [StorSimple](../../storsimple/storsimple-overview.md) vill veta mer.

## <a name="recovering-your-data"></a>Återställning av data
När du har lokala arbetsbelastningar och program, måste en lösning som gör att företaget ska fortsätta att köras vid en katastrof. Azure Site Recovery hanterar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Replikerade data lagras i Azure Storage, så att du kan i stället för en sekundär plats datacenter.

Se [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) vill veta mer.
### <a name="moving-data-faq"></a>Flytta Data vanliga frågor och svar:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Kan jag migrera virtuella hårddiskar från en region till en annan utan att kopiera?
Det enda sättet att kopiera virtuella hårddiskar mellan region används för att kopiera data mellan lagringskonton på varje region. Du kan använda AZCopy för den här. Överföra data med kommandoradsverktyget Azcopy mer information finns i. För mycket stora mängder data kan du också Azure Import/Export. Se [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) vill veta mer.
