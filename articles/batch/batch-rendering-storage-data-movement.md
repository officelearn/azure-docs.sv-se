---
title: Lagring och förflyttning för rendering – Azure Batch
description: Alternativ för lagring och förflyttning för arbetsbelastningar
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 5a0d4dc82995e63697cc673bc54695c9c6d586df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774003"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Alternativ för lagring och data movement för återgivning av tillgången och utgående filer

Det finns flera alternativ för att göra scen- och tillgångsnivå filer tillgängliga för rendering programmen på poolen med virtuella datorer:

* [Azure-bloblagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Scen- och tillgångsnivå laddas upp till blob storage från ett lokalt filsystem. När programmet körs av en aktivitet, kopieras de nödvändiga filerna från blob storage till den virtuella datorn så att de kan nås av programmet för rendering. Utdatafilerna skrivs av rendering programmet till den Virtuella datordisken och kopieras sedan till blob storage.  Om det behövs laddas utdatafilerna ned från blob storage till ett lokalt filsystem.
  * Azure blob storage är ett enkelt och kostnadseffektivt alternativ för mindre projekt.  Som alla tillgång filer krävs på varje pool VM, sedan när antalet och storleken på tillgångsfiler ökar är försiktighet måste vidtas för att se till att filöverföringar så effektivt som möjligt.  
* Azure storage som ett filsystem med [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Ett lagringskonto kan visas och användas som ett filsystem när blobfuse virtuella filsystemsdrivrutin används för virtuella Linux-datorer.
  * Det här alternativet har fördelen att den är mycket kostnadseffektivt, som det krävs inga virtuella datorer för filsystem, plus blobfuse cachelagring på de virtuella datorerna förhindrar upprepade hämtningar av samma filer för flera jobb och uppgifter.  Dataförflyttning är också enkelt eftersom filerna är helt enkelt blobar och standard-API: er och verktyg, till exempel azcopy, som kan användas för att kopiera filen mellan ett lokalt filsystem och Azure storage.
* Filsystem eller filresurs:
  * Beroende på VM-operativsystem och prestanda/skalningskrav kan sedan alternativ inkluderar [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), med hjälp av en virtuell dator med anslutna diskar för NFS, med flera virtuella datorer med anslutna diskar i ett distribuerat filsystem som GlusterFS, eller med hjälp av ett erbjudande från tredje part.
  * [Avere system](https://www.averesystems.com/) är nu en del av Microsoft och kommer att ha lösningar inom en snar framtid som lämpar sig för storskaliga, högpresterande rendering.  Avere-lösningen kan en Azure-baserade NFS eller SMB-cache skapas som fungerar tillsammans med blob storage eller med lokala NAS-enheter.
  * Med ett filsystem filer kan läsas eller skrivas direkt till filsystemet eller kopieras mellan filsystem och pool med virtuella datorer.
  * Ett delat filsystem kan ett stort antal tillgångar som delas mellan projekt och jobb som kan användas med rendering uppgifter endast åtkomst till vad som krävs.

## <a name="using-azure-blob-storage"></a>Med Azure blob storage

Du bör använda ett blob storage-konto eller ett gpv2-lagringskonto.  Dessa två typer av konton kan konfigureras med avsevärt högre gränser jämfört med ett general-purpose v1 storage-konto som beskrivs i [det här blogginlägget](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  När konfigurerad, kan de högre gränserna mycket bättre prestanda och skalbarhet, särskilt när det finns många pool virtuella datorer åtkomst till lagringskontot.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiera filer mellan klienten och blob storage

Kopiera filer till och från Azure storage genom olika metoder kan användas, inklusive lagringsblob API, den [Azure Storage Data Movement Library](https://github.com/Azure/azure-storage-net-data-movement), azcopy kommandoradsverktyg för [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) eller [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), och [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Till exempel kan med hjälp av azcopy, alla tillgångar i en mapp överföras på följande sätt:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Parametern /XO kan användas för att kopiera endast ändrade filer:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgången filer från blob storage till Batch pool med virtuella datorer

Det finns ett par olika sätt att kopiera filer med den bästa metoden bestäms av storleken på jobbet tillgångar.
Det enklaste sättet är att kopiera alla tillgångsfiler till pool med virtuella datorer för varje jobb:

* När det finns filer som är unika för ett jobb, men krävs för alla uppgifter i ett jobb, en [jobbförberedelseaktiviteten](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) kan anges för att kopiera alla filer.  Jobbförberedelseaktiviteten körs en gång när den första projektaktiviteten körs på en virtuell dator men körs inte igen för efterföljande projektaktiviteter.
* En [jobbpubliceringsaktivitet](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) bör anges för att ta bort filer per jobb när jobbet har slutförts, så sätt undviker du den Virtuella datordisken komma fylls av alla resursfiler för jobbet.
* När det finns flera jobb med samma tillgångar med enbart stegvisa ändringar till tillgångar för varje jobb sedan kopieras alla tillgångsfiler fortfarande, även om endast en del har uppdaterats.  Detta kan vara ineffektiv när det finns många stora tillgångsfiler.

När tillgångsfiler återanvänds mellan jobb, med bara inkrementella ändringar mellan jobb, är en metod för effektivare men något mer komplicerad att lagra tillgångar i den delade mappen på den virtuella datorn och synkronisera ändrade filer.

* Jobbförberedelseaktiviteten skulle utföra kopieringen med hjälp av azcopy med parametern /XO till den virtuella dator delade mappen som anges av miljövariabeln AZ_BATCH_NODE_SHARED_DIR.  Det kopieras bara ändrade filer till varje virtuell dator.
* Ansedda måste ges till storleken på alla resurser för att se till att de får plats på den temporära enheten på poolen med virtuella datorer.

Azure Batch har inbyggt stöd för att kopiera filer mellan en storage-konto och Batch pool med virtuella datorer.  Uppgiften [resursfiler](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) kopiera filer från storage till pool med virtuella datorer och kan anges för jobbförberedelseaktiviteten.  Tyvärr är det möjligt att nå en gräns och aktiviteter misslyckas när det finns hundratals filer.  När det finns stora mängder tillgångar rekommenderas att använda azcopy från kommandoraden i jobbförberedelseaktiviteten, som kan använda jokertecken och har ingen gräns.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiera utdatafilerna till blob storage från Batch pool med virtuella datorer

[Utdatafiler](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) kan vara använda kopiera filer från en pool VM till lagring.  En eller flera filer kan kopieras från den virtuella datorn till ett angivet lagringskonto när uppgiften har slutförts.  Den renderade utdatan ska ha kopierats, men det kan också vara önskvärt att lagra loggfilerna.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Med hjälp av ett virtuellt filsystem blobfuse för Linux VM-pooler

Blobfuse är en virtuell filsystemsdrivrutin för Azure Blob Storage, vilket gör att du kan komma åt filer som lagras som blobar i ett lagringskonto via filsystemet Linux.

Poolnoder kan montera filsystemet när igång eller monterings kan inträffa som en del av en jobbförberedelseaktivitet – en aktivitet som körs endast när den första aktiviteten i ett jobb som körs på en nod.  Blobfuse kan konfigureras för att använda både en ramdisk och lokal SSD virtuella datorer för cachelagring av filer, vilket ökar prestandan avsevärt om flera aktiviteter på en nod har åtkomst till några av samma filer.

[Exempelmallar finns](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) att köra fristående V-Ray visas med hjälp av ett blobfuse filsystem och kan användas som grund för mallar för andra program.

### <a name="accessing-files"></a>Åtkomst till filer

Jobbuppgifter ange sökvägar för indata och utdatafiler med hjälp av det monterade filsystemet.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgången filer från blob storage till Batch pool med virtuella datorer

Filerna är helt enkelt blobar i Azure Storage kan sedan för standard blob API: er, verktyg och gränssnitt att kopiera filer mellan en lokal fil system- och blob storage; till exempel azcopy Lagringsutforskaren, Batch Explorer osv.

## <a name="using-azure-files-with-windows-vms"></a>Med hjälp av Azure Files med virtuella Windows-datorer

[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet.  Azure Files är baserad på Azure blobblagring. Det är [kostnadseffektiv](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med replikering av data till en annan region så globalt redundant.  [Skala mål](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) bör granskas för att avgöra om Azure Files ska användas beroende på prognoser poolstorlek och antalet tillgångsfiler.

Det finns en [blogginlägget](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) och [dokumentation](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) som beskriver hur du monterar en Azure-filresurs.

### <a name="mounting-an-azure-files-share"></a>Montera en Azure Files-resurs

Om du vill använda i Batch måste montering som ska utföras varje gång en aktivitet i Kör som det inte går att spara anslutningen mellan aktiviteter.  Det enklaste sättet att göra detta är att använda cmdkey för att spara autentiseringsuppgifter med hjälp av startaktiviteten i poolkonfigurationen och sedan montera filresursen innan varje aktivitet.

Exempel användning av cmdkey i en pool-mall (undantaget för användning i JSON-fil) – Observera att när att separera cmdkey anrop från net Använd anropet användarkontext för Startuppgiften måste vara samma som används för att köra aktiviteterna:

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

Exempel jobbet aktivitetens kommandorad:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Åtkomst till filer

Jobbuppgifter ange sökvägar för indata och utdatafiler med hjälp av den monterade filsystem, antingen med hjälp av en mappad enhet eller en UNC-sökväg.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgången filer från blob storage till Batch pool med virtuella datorer

Azure Files stöds av alla huvudsakliga API: er och verktyg som har Azure Storage stöder; t.ex. azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer osv.

[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) kan synkroniseras automatiskt filer mellan ett lokalt filsystem och en Azure-filresurs.

## <a name="next-steps"></a>Nästa steg

Mer information om lagringsalternativ för finns detaljerad dokumentation:

* [Azure blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
