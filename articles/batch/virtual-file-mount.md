---
title: Montera ett virtuellt filsystem på en pool - Azure Batch | Microsoft-dokument
description: Lär dig hur du monterar ett virtuellt filsystem på en batchpool.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919013"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montera ett virtuellt filsystem på en batchpool

Azure Batch stöder nu montering av molnlagring eller ett externt filsystem på Windows- eller Linux-beräkningsnoder i dina batchpooler. När en beräkningsnod ansluter till en pool monteras det virtuella filsystemet och behandlas som en lokal enhet på den noden. Du kan montera filsystem som Azure Files, Azure Blob storage, Network File System (NFS) inklusive en [Avere vFXT-cache](../avere-vfxt/avere-vfxt-overview.md)eller CIFS (Common Internet File System).

I den här artikeln får du lära dig hur du monterar ett virtuellt filsystem på en pool med beräkningsnoder med [batchhanteringsbiblioteket för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> Montering av ett virtuellt filsystem stöds på batchpooler som skapats på eller efter 2019-08-19. Batchpooler som skapats före 2019-08-19 stöder inte den här funktionen.
> 
> API:erna för montering av filsystem på en beräkningsnod är en del av [batch.NET-biblioteket.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Fördelar med montering på en pool

Genom att montera filsystemet i poolen, i stället för att låta uppgifter hämta sina egna data från en stor datauppsättning, blir det enklare och effektivare för uppgifter att komma åt nödvändiga data.

Överväg ett scenario med flera uppgifter som kräver åtkomst till en gemensam uppsättning data, till exempel att göra en film. Varje uppgift återger en eller flera bildrutor i taget från scenfilerna. Genom att montera en enhet som innehåller scenfilerna är det enklare för beräkningsnoder att komma åt delade data. Dessutom kan det underliggande filsystemet väljas och skalas oberoende av varandra baserat på prestanda och skala (dataflöde och IOPS) som krävs av antalet beräkningsnoder som samtidigt kommer åt data. En [Avere vFXT-distribuerad](../avere-vfxt/avere-vfxt-overview.md) minnescache kan till exempel användas för att stödja stora återgivningar av filmskala med tusentals samtidiga renderingsnoder, åtkomst till källdata som finns lokalt. Alternativt, för data som redan finns i molnbaserad Blob-lagring, kan [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) användas för att montera dessa data som ett lokalt filsystem. Blobfuse är endast tillgängligt på Linux-noder, men [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) tillhandahåller ett liknande arbetsflöde och är tillgängligt på både Windows och Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montera ett virtuellt filsystem på en pool  

Om du monterar ett virtuellt filsystem på en pool blir filsystemet tillgängligt för varje beräkningsnod i poolen. Filsystemet konfigureras antingen när en beräkningsnod ansluter till en pool eller när noden startas om eller görs om.

Om du vill montera ett filsystem på en pool skapar du ett `MountConfiguration` objekt. Välj det objekt som passar `AzureBlobFileSystemConfiguration`ditt `AzureFileShareConfiguration` `NfsMountConfiguration`virtuella `CifsMountConfiguration`filsystem: , , eller .

Alla monteringskonfigurationsobjekt behöver följande basparametrar. Vissa monteringskonfigurationer har parametrar som är specifika för det filsystem som används, som diskuteras mer i detalj i kodexemplen.

- **Kontonamn eller källa**: För att montera en virtuell filresurs behöver du namnet på lagringskontot eller dess källa.
- **Relativ monteringssökväg eller källa:** Platsen för filsystemet monterad på `fsmounts` beräkningsnoden, `AZ_BATCH_NODE_MOUNTS_DIR`i förhållande till standardkatalogen som är tillgänglig på noden via . Den exakta platsen varierar beroende på vilket operativsystem som används på noden. Den fysiska platsen på en Ubuntu-nod `mnt\batch\tasks\fsmounts`mappas till exempel och på en `mnt\resources\batch\tasks\fsmounts`CentOS-nod mappas den till .
- **Monteringsalternativ eller blobfuse alternativ:** Dessa alternativ beskriver specifika parametrar för montering av ett filsystem.

När `MountConfiguration` objektet har skapats tilldelar `MountConfigurationList` du objektet till egenskapen när du skapar poolen. Filsystemet monteras antingen när en nod ansluter till en pool eller när noden startas om eller görs om.

När filsystemet är monterat skapas en miljövariabel `AZ_BATCH_NODE_MOUNTS_DIR` som pekar på platsen för de monterade filsystemen samt loggfiler, som är användbara för felsökning och felsökning. Loggfiler förklaras mer i detalj i avsnittet [Diagnostisera monteringsfel.](#diagnose-mount-errors)  

> [!IMPORTANT]
> Det maximala antalet monterade filsystem på en pool är 10. Se [Batch-tjänstkvoter och begränsningar](batch-quota-limit.md#other-limits) för detaljer och andra gränser.

## <a name="examples"></a>Exempel

Följande kodexempel visar montering av en mängd olika filresurser till en pool av beräkningsnoder.

### <a name="azure-files-share"></a>Azure-filresurs

Azure Files är standardbjudandet för Azure-molnfilsystem. Mer information om hur du hämtar någon av parametrarna i exemplet med monteringskonfigurationskoden finns i [Använda en Azure Files-resurs](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Filsystem för Azure Blob

Ett annat alternativ är att använda Azure Blob-lagring via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Om du monterar ett `AccountKey` `SasKey` blob-filsystem krävs ett eller för ditt lagringskonto. Information om hur du hämtar dessa nycklar finns i [Hantera åtkomstnycklar för lagringskonto](../storage/common/storage-account-keys-manage.md)eller [Använda SIGNATURER för delad åtkomst (SAS).](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Mer information om hur du använder blus finns i [vanliga frågor om](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)felsökning av blus. Om du vill få standardåtkomst till den blobfusemonterade katalogen kör du uppgiften som **administratör**. Blobfuse monterar katalogen på användarutrymmet, och vid pool skapande är monterad som rot. I Linux är alla **administratörsuppgifter** rot. Alla alternativ för FUSE-modulen beskrivs på [referenssidan för SÄKRING](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Förutom felsökningsguiden är GitHub-problem i blobfuse-arkivet ett användbart sätt att kontrollera aktuella problem och lösningar för blus. Mer information finns i [blobfuse-problem](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Nätverk filsystem

NFS (Network File Systems) kan också monteras på poolnoder som gör att traditionella filsystem enkelt kan nås av Azure Batch-noder. Detta kan vara en enda NFS-server som distribueras i molnet eller en lokal NFS-server som nås via ett virtuellt nätverk. Alternativt kan du dra nytta av den [avere vFXT-distribuerade](../avere-vfxt/avere-vfxt-overview.md) cachelösningen i minnet, som ger sömlös anslutning till lokal lagring, läser data på begäran i cacheminnet och levererar hög prestanda och skala till molnbaserade beräkningsnoder.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Gemensamt Internet-filsystem

Vanliga Internet File Systems (CIFS) kan också monteras på poolnoder så att traditionella filsystem enkelt kan nås av Azure Batch-noder. CIFS är ett fildelningsprotokoll som tillhandahåller en öppen mekanism över flera plattformar för att begära nätverksserverfiler och -tjänster. CIFS baseras på den förbättrade versionen av Microsofts SMB-protokoll (Server Message Block) för fildelning på Internet och intranät och används för att montera externa filsystem på Windows-noder. Mer information om SMB finns i [Filserver och SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnostisera monteringsfel

Om en monteringskonfiguration misslyckas misslyckas beräkningsnoden i poolen och nodtillståndet blir oanvändbart. Om du vill diagnostisera ett [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) monteringskonfigurationsfel kontrollerar du egenskapen för mer information om felet.

Om du vill hämta loggfilerna för felsökning använder `*.log` du [OutputFiles](batch-task-output-files.md) för att ladda upp filerna. Filerna `*.log` innehåller information om filsystemets `AZ_BATCH_NODE_MOUNTS_DIR` montering på platsen. Mount loggfiler har `<type>-<mountDirOrDrive>.log` formatet: för varje montering. Ett `cifs` fäste på en monteringskatalog med namnet `test` har `cifs-test.log`till exempel en monteringsloggfil med namnet: .

## <a name="supported-skus"></a>SKU:er som stöds

| Utgivare | Erbjudande | SKU | Azure-fildelning | Blus | NFS fäste | CIFS fäste |
|---|---|---|---|---|---|---|
| batch | rendering-centos73 | Rendering | :heavy_check_mark: <br>Obs: Kompatibel med CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ (credativ) | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ (credativ) | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-annonser | linux-data-science-vm | linuxdsvm linuxdsvm linuxdsvm linuxd | :heavy_check_mark: <br>Anmärkning: Kompatibel med CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-behållare | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Obs: Stöder A_8 eller 9 lagring</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-behållare | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-DSVM | linux-data-vetenskap-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du monterar en Azure Files-resurs med [Windows](../storage/files/storage-how-to-use-files-windows.md) eller [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Lär dig mer om hur du använder och monterar virtuella filsystem [för blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Läs [översikt över nätverksfilsystem](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) om du vill veta mer om NFS och dess program.
- Mer information om CIFS finns i [översikten över Microsoft SMB-protokoll och CIFS-protokoll.](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)
