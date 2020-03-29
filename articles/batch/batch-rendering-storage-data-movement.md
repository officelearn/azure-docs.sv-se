---
title: Lagring och dataförflyttning för rendering - Azure Batch
description: Lär dig mer om de olika lagrings- och dataförflyttningsalternativen för återgivning av arbetsbelastningar för tillgångs- och utdatafiler.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 1076fc152ddf2c3a2d4f2346262ca90215d68ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390391"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Lagrings- och dataförflyttningsalternativ för rendering av tillgångs- och utdatafiler

Det finns flera alternativ för att göra scenen och tillgångsfiler tillgängliga för renderingsprogrammen på pooldamparna:

* [Azure blob-lagring:](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
  * Scen- och tillgångsfiler överförs till blob-lagring från ett lokalt filsystem. När programmet körs av en uppgift kopieras de nödvändiga filerna från blob-lagring till den virtuella datorn så att de kan nås av renderingsprogrammet. Utdatafilerna skrivs av renderingsprogrammet till VM-disken och kopieras sedan till blob-lagring.  Om det behövs kan utdatafilerna hämtas från blob-lagring till ett lokalt filsystem.
  * Azure blob storage är ett enkelt och kostnadseffektivt alternativ för mindre projekt.  Eftersom alla tillgångsfiler krävs på varje pool VM, sedan när antalet och storleken på tillgångsfiler ökar försiktighet måste vidtas för att säkerställa filöverföringar är så effektiva som möjligt.  
* Azure-lagring som filsystem med [blobfuse:](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
  * För virtuella Linux-datorer kan ett lagringskonto exponeras och användas som ett filsystem när drivrutinen för blobfuse-virtuella filsystem används.
  * Det här alternativet har fördelen att det är mycket kostnadseffektivt, eftersom inga virtuella datorer krävs för filsystemet, plus blobfuse caching på de virtuella datorerna undviker upprepade nedladdningar av samma filer för flera jobb och uppgifter.  Dataförflyttning är också enkelt eftersom filerna helt enkelt blobbar och standard-API:er och verktyg, till exempel azcopy, kan användas för att kopiera filer mellan ett lokalt filsystem och Azure-lagring.
* Filsystem eller filresurs:
  * Beroende på vm-operativsystem och prestanda/skalningskrav, inkluderar alternativen [Azure-filer](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), med hjälp av en virtuell dator med anslutna diskar för NFS, med flera virtuella datorer med anslutna diskar för ett distribuerat filsystem som GlusterFS eller med hjälp av ett tredjepartserbjudande.
  * [Avere Systems](https://www.averesystems.com/) är nu en del av Microsoft och kommer att ha lösningar inom en snar framtid som är idealiska för storskalig, högpresterande rendering.  Avere-lösningen gör det möjligt att skapa en Azure-baserad NFS- eller SMB-cache som fungerar tillsammans med blob-lagring eller med lokala NAS-enheter.
  * Med ett filsystem kan filer läsas eller skrivas direkt till filsystemet eller kopieras mellan filsystemet och pool-datorerna.
  * Ett delat filsystem gör att ett stort antal resurser som delas mellan projekt och jobb kan användas, med återgivningsuppgifter som bara får åtkomst till det som krävs.

## <a name="using-azure-blob-storage"></a>Använda Azure blob storage

Ett blob-lagringskonto eller ett allmänt v2-lagringskonto ska användas.  Dessa två lagringskontotyper kan konfigureras med betydligt högre gränser jämfört med ett allmänt v1-lagringskonto, som beskrivs i [det här blogginlägget](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  När de högre gränserna är konfigurerade aktiveras mycket bättre prestanda och skalbarhet, särskilt när det finns många virtuella pool-datorer som använder lagringskontot.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiera filer mellan klient- och bloblagring

Om du vill kopiera filer till och från Azure-lagring kan olika mekanismer användas, inklusive api:et för lagringsblobb, [Azure Storage Data Movement Library](https://github.com/Azure/azure-storage-net-data-movement), kommandoradsverktyget för azkopior för [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) eller [Linux,](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)och Azure Batch [Explorer](https://azure.github.io/BatchExplorer/).

Med hjälp av azoskopi kan till exempel alla tillgångar i en mapp överföras på följande sätt:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Om du bara vill kopiera ändrade filer kan /XO-parametern användas:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgångsfiler från blob storage till batchpool virtuella datorer

Det finns ett par olika metoder för att kopiera filer med den bästa metoden bestäms av storleken på jobbtillgångarna.
Den enklaste metoden är att kopiera alla tillgångsfiler till pool-datorerna för varje jobb:

* När det finns filer som är unika för ett jobb, men krävs för alla uppgifter i ett jobb, kan en [jobbförberedelseuppgift](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) anges för att kopiera alla filer.  Jobbförberedelseaktiviteten körs en gång när den första jobbaktiviteten körs på en virtuell dator men inte körs igen för efterföljande jobbaktiviteter.
* En [jobbfrisättningsaktivitet](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) bör anges för att ta bort filerna per jobb när jobbet har slutförts. Detta kommer att undvika att vm-disken fylls i av alla jobbtillgångsfiler.
* När det finns flera jobb som använder samma tillgångar, med endast inkrementella ändringar i tillgångarna för varje projekt, kopieras alla tillgångsfiler fortfarande, även om bara en delmängd har uppdaterats.  Detta skulle vara ineffektivt när det finns massor av stora tillgångsfiler.

När tillgångsfiler återanvänds mellan jobb, med endast inkrementella ändringar mellan jobb, då en mer effektiv men något mer involverad metod är att lagra tillgångar i den delade mappen på den virtuella datorn och synkronisera ändrade filer.

* Jobbförberedelseuppgiften utför kopian med azkopia med parametern /XO till den delade mappen FÖR virtuella datorer som anges av AZ_BATCH_NODE_SHARED_DIR miljövariabel.  Detta kopierar bara ändrade filer till varje virtuell dator.
* Man måste tänka på storleken på alla tillgångar för att säkerställa att de får plats på den tillfälliga enheten för pool-virtuella datorer.

Azure Batch har inbyggt stöd för att kopiera filer mellan ett lagringskonto och virtuella batchpool-datorer.  [Aktivitetsresursfiler kopierar](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) filer från lagring till pool virtuella datorer och kan anges för jobbförberedelseaktiviteten.  Tyvärr, när det finns hundratals filer är det möjligt att träffa en gräns och uppgifter att misslyckas.  När det finns ett stort antal tillgångar rekommenderas att använda kommandoraden askakopi i jobbförberedelseuppgiften, som kan använda jokertecken och inte har någon gräns.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiera utdatafiler till blob-lagring från virtuella batchpool-datorer

[Utdatafiler](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) kan användas kopiera filer från en pool-VM till lagring.  En eller flera filer kan kopieras från den virtuella datorn till ett angivet lagringskonto när uppgiften har slutförts.  Den renderade utdata ska kopieras, men det kan också vara önskvärt att lagra loggfiler.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Använda ett virtuellt filsystem för blobfuse för Linux VM-pooler

Blobfuse är en virtuell filsystemdrivrutin för Azure Blob Storage, som gör att du kan komma åt filer som lagras som blobbar i ett lagringskonto via Linux-filsystemet.

Poolnoder kan montera filsystemet när det startas eller så kan fästet ske som en del av en jobbförberedelseaktivitet – en aktivitet som bara körs när den första aktiviteten i ett jobb körs på en nod.  Blobfuse kan konfigureras för att utnyttja både en ramdisk och de virtuella datorernas lokala SSD för cachelagring av filer, vilket ökar prestanda avsevärt om flera uppgifter på en nod kommer åt några av samma filer.

[Exempelmallar finns tillgängliga](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) för att köra fristående V-Ray-renderingar med hjälp av ett blobfuse-filsystem och kan användas som grund för mallar för andra program.

### <a name="accessing-files"></a>Komma åt filer

Jobbuppgifter anger sökvägar för indatafiler och utdatafiler med hjälp av det monterade filsystemet.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgångsfiler från blob storage till batchpool virtuella datorer

Eftersom filer helt enkelt blobbar i Azure Storage kan standard-blob API:er, verktyg och UIs användas för att kopiera filer mellan ett lokalt filsystem och blob-lagring. till exempel azcopy, Storage Explorer, Batch Explorer, etc.

## <a name="using-azure-files-with-windows-vms"></a>Använda Azure-filer med virtuella Windows-datorer

[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet.  Azure-filer baseras på Azure blob-lagring. Den är [kostnadseffektiv](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med datareplikering till en annan region så globalt redundant.  [Skalningsmål](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) bör granskas för att avgöra om Azure-filer ska användas med tanke på prognospoolens storlek och antal tillgångsfiler.

Det finns ett [blogginlägg](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) och [dokumentation](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) som beskriver hur du monterar en Azure File-resurs.

### <a name="mounting-an-azure-files-share"></a>Montera en Azure-filresurs

Om du vill använda i Batch måste en monteringsåtgärd utföras varje gång en aktivitet körs eftersom det inte går att bevara anslutningen mellan aktiviteter.  Det enklaste sättet att göra detta är att använda cmdkey för att spara autentiseringsuppgifter med startuppgiften i poolkonfigurationen och sedan montera resursen före varje aktivitet.

Exempel på användning av cmdkey i en poolmall (som kan användas i JSON-filen) – observera att när cmdkey-anropet separeras från nettoanvändningsanropet måste användarkontexten för startuppgiften vara samma som för att köra aktiviteterna:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Exempel på kommandorad för jobbaktivitet:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Komma åt filer

Jobbuppgifter anger sökvägar för indatafiler och utdatafiler med hjälp av det monterade filsystemet, antingen med hjälp av en mappad enhet eller en UNC-sökväg.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgångsfiler från blob storage till batchpool virtuella datorer

Azure-filer stöds av alla de viktigaste API:erna och verktygen som har Azure Storage-stöd. t.ex. azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer, etc.

[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) är tillgängligt för automatisk synkronisering av filer mellan ett lokalt filsystem och en Azure File-resurs.

## <a name="next-steps"></a>Nästa steg

Mer information om lagringsalternativen finns i den djupgående dokumentationen:

* [Azure blob lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blus](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Azure-filer](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
