---
title: Benchmark-tester för prestanda och mått som använder Azure NetApp filer | Microsoft Docs
description: Innehåller benchmark testning rekommendationer för prestanda och mått som använder Azure NetApp-filer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478815"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Benchmark-testning för volymprestanda och mått med Azure NetApp Files

Den här artikeln innehåller benchmark testning rekommendationer för prestanda och mått som använder Azure NetApp-filer.

## <a name="overview"></a>Översikt

Du kan använda verktyg med öppen källkod för att förstå prestandaegenskaperna för en Azure NetApp Files-volym, [FIO](https://github.com/axboe/fio) att köra en serie prestandamått att simulera en rad olika arbetsbelastningar. FIO kan installeras på både Linux och Windows-baserade operativsystem.  Det är ett utmärkt verktyg för att få en snabb ögonblicksbild av både IOPS och dataflöden för en volym.

### <a name="vm-instance-sizing"></a>Storlek för VM-instans

För bästa resultat bör du kontrollera att du använder en virtuell dator (VM)-instans som är rätt storlek för att utföra testerna. I följande exempel används en Standard_D32s_v3-instans. Läs mer om Virtuella datorstorlekar [storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) för Windows-baserade virtuella datorer och [storlekar för Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för Linux-baserade virtuella datorer.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp filer volymstorlek

Se till att du väljer rätt tjänst och icke-kvotstorleken för den förväntade prestandanivån. Se [servicenivåer för Azure NetApp Files](azure-netapp-files-service-levels.md) för mer information.

### <a name="virtual-network-vnet-recommendations"></a>Rekommendationer för virtuella nätverk (VNet)

Du bör utföra benchmark testa i samma virtuella nätverk som Azure NetApp-filer. Exemplet nedan visar rekommendationen:

![Rekommendationer för virtuellt nätverk](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installation av FIO

FIO är tillgänglig i binärt format för både Linux och Windows. Följ avsnittet binärt paket i [FIO](https://github.com/axboe/fio) att installeras på valfri plattform.

## <a name="fio-examples-for-iops"></a>FIO exempel för IOPS 

FIO exemplen i det här avsnittet använder följande inställningar:
* Virtuell datorinstans-storlek: D32s_v3
* Kapacitet pool servicenivå och storlek: Premium / 50 TiB
* Volymstorlek kvot: 48 TiB

I följande exempel visas FIO slumpmässiga läsningar och skrivningar.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8 kB blockera icke-sekventiell läsning för storlek på 100%

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Utdata: 68k läsa IOPS visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8 kB blockera storlek på 100% slumpmässiga skrivningar

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Utdata: 73k skriva IOPS visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO exempel för bandbredd

Exemplen i det här avsnittet visar FIO sekventiella läser och skriver.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 kB blockera storlek på 100% sekventiella läsåtgärder

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Utdata: 11.8 Gbit/s genomströmning för visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 kB blockera storlek på 100% sekventiella skrivåtgärder

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Utdata: 12.2 Gbit/s genomströmning för visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Volymen mått

Azure NetApp filer prestandadata är tillgänglig via Azure Monitor-räknare. Räknarna är tillgängliga via Azure-portalen och REST API-GET-begäranden. 

Du kan visa historiska data för följande information:
* Genomsnittlig läsfördröjning 
* Genomsnittligt antal skrivåtgärder svarstid 
* Läs IOPS (genomsnitt)
* Skriva IOPS (genomsnitt)
* Logisk storlek (genomsnitt)
* Volymstorlek ögonblicksbild (genomsnitt)

### <a name="using-azure-monitor"></a>Använda Azure Monitor 

Du kan komma åt Azure NetApp Files räknare på basis av per volym från sidan mått som visas nedan:

![Azure Monitor-mått](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Du kan också skapa en instrumentpanel i Azure Monitor för Azure NetApp filer genom att gå till sidan mått, filtrering för NetApp och ange volym räknare intressanta: 

![Azure Monitor-instrumentpanel](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor, API-åtkomst

Du kan komma åt Azure NetApp Files räknare med hjälp av REST API-anrop. Se [stöds mått med Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) för räknare för kapacitet pooler och volymer.

I följande exempel visas en hämta URL för att visa logiska volymens storlek:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Nästa steg

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Azure NetApp-filer](azure-netapp-files-performance-benchmarks.md)