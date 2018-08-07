---
title: Flytta stora mängder data till/från molnlagring i Azure | Microsoft Docs
description: En översikt över de olika metoderna för att flytta data till och från Azure Storage.
services: storage
author: JarrettRenshaw
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.component: common
ms.openlocfilehash: 81d7b5cf03e56ecc54db71b09af335d6cb794806
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525543"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Flytta data till och från Azure Storage
Om du vill flytta lokala data till Azure Storage (eller vice versa) finns en mängd olika sätt att göra detta. Den metod som passar dig bäst beror på ditt scenario. Den här artikeln ger en snabb överblick över olika scenarier och lämpliga erbjudanden för vart och ett.

## <a name="building-applications"></a>Skapa program
Om du skapar ett program som utvecklar mot REST-API: et eller någon av våra många klientbibliotek är ett bra sätt att flytta data till och från Azure Storage.

Azure Storage tillhandahåller rika klientbibliotek för .NET, iOS, Java, Android, Universal Windows Platform (UWP), Xamarin, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Se [Kom igång med Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) vill veta mer.

Dessutom erbjuder vi också den [Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) som är ett bibliotek som är utformat för högpresterande kopiering av data till och från Azure. Se våra Data Movement Library [dokumentation](https://github.com/Azure/azure-storage-net-data-movement) vill veta mer. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Visa/interagera snabbt med dina data
Om du vill att ett enkelt sätt att visa dina Azure Storage-data medan du har också möjlighet att överföra och hämta dina data, Överväg att använda ett Azure Storage Explorer.

Kolla in vår lista med [Azure Storage-Utforskare](../storage-explorers.md) vill veta mer.

## <a name="system-administration"></a>Systemadministration
Om du behöver eller är bekvämare med ett kommandoradsverktyg (t.ex. systemadministratörer), är här några alternativ att tänka på:

### <a name="azcopy"></a>AzCopy
AzCopy är ett kommandoradsverktyg som utformats för högpresterande kopiering av data till och från Azure Storage. Du kan också kopiera data i ett lagringskonto eller mellan olika lagringskonton. AzCopy är tillgänglig på [Windows](storage-use-azcopy.md) på [Linux](storage-use-azcopy-linux.md).

Se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md) eller [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md) vill veta mer.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera tjänster på Azure. Det är ett uppgiftsbaserat kommandoradsgränssnitt och skriptspråk som utformats specifikt för systemadministration.

Se [med hjälp av Azure PowerShell med Azure Storage](storage-powershell-guide-full.md) vill veta mer.

### <a name="azure-cli"></a>Azure CLI
Azure CLI innehåller en uppsättning med öppen källkod plattformsoberoende kommandon för att arbeta med Azure-tjänster. Azure CLI finns i Windows, OSX och Linux.

Se [med Azure CLI med Azure Storage](../storage-azure-cli.md) vill veta mer.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Flytta stora mängder data med ett långsamt nätverk
En av de största utmaningarna som är associerade med att flytta stora mängder data är överföringstiden. Om du vill hämta data till och från Azure Storage utan att behöva bekymra dig om kostnader för nätverk eller skriva kod är en lämplig lösning med Azure Import/Export.

Se [Azure Import/Export](../storage-import-export-service.md) vill veta mer.

## <a name="backing-up-your-data"></a>Säkerhetskopiera dina data
Om du vill säkerhetskopiera dina data till Azure Storage, är Azure Backup bättre att. Det här är en kraftfull lösning för att säkerhetskopiera lokala data och virtuella Azure-datorer.

Se [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) vill veta mer.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Åtkomst till dina data lagras lokalt och från molnet
Om du behöver en lösning för att komma åt dina data lagras lokalt och från molnet, bör sedan du använda Azures hybridmolnlagringslösning, StorSimple. Den här lösningen består av en fysisk enhet att Smart butiker ofta använda data på SSD-enheter, ibland används för data på hårddiskar och inaktiva/backup/arkiveringsdata på Azure Storage.

Se [StorSimple](../../storsimple/storsimple-overview.md) vill veta mer.

## <a name="recovering-your-data"></a>Återställa dina data
När du har lokala arbetsbelastningar och program, måste en lösning som gör ditt företag genom att fortsätta att köra vid ett haveri. Azure Site Recovery hanterar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Replikerade data lagras i Azure Storage, så att du kan i stället för ett sekundärt lokalt datacenter.

Se [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) vill veta mer.
### <a name="moving-data-faq"></a>Flytta Data vanliga frågor och svar:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Kan jag migrera virtuella hårddiskar från en region till en annan utan att kopiera?
Det enda sättet att kopiera virtuella hårddiskar mellan regionen är att kopiera data mellan lagringskonton på varje region. Du kan använda AZCopy för detta. Se överföra data med kommandoradsverktyget Azcopy vill veta mer. För mycket stora mängder data kan du också Azure Import/Export. Se [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) vill veta mer.
