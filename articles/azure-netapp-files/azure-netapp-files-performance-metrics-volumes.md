---
title: Rekommenderade prestandariktmärken - Azure NetApp-filer
description: Lär dig mer om rekommendationer för benchmark-testning för volymprestanda och mått med Hjälp av Azure NetApp-filer.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551530"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Rekommendationer från benchmark-prestandatest för Azure NetApp Files

Den här artikeln innehåller rekommendationer för benchmark-testning för volymprestanda och mått med Hjälp av Azure NetApp-filer.

## <a name="overview"></a>Översikt

För att förstå prestandaegenskaperna för en Azure NetApp-filvolym kan du använda verktyget med öppen källkod [FIO](https://github.com/axboe/fio) för att köra en serie benchmarks för att simulera en mängd olika arbetsbelastningar. FIO kan installeras på både Linux och Windows-baserade operativsystem.  Det är ett utmärkt verktyg för att få en snabb ögonblicksbild av både IOPS och dataflöde för en volym.

### <a name="vm-instance-sizing"></a>Storlek på VM-instans

Bäst resultat får du om du måste använda en VM-instans (Virtual Machine) som är lämplig för att utföra testerna. I följande exempel används en Standard_D32s_v3-instans. Mer information om VM-instansstorlekar finns [i Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) för Windows-baserade virtuella datorer och storlekar för virtuella [Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för Linux-baserade virtuella datorer.

### <a name="azure-netapp-files-volume-sizing"></a>Storleksstorleken för Azure NetApp-filer

Se till att du väljer rätt servicenivå och volymkvotstorlek för den förväntade prestandanivån. Mer information [finns i tjänstnivåer för Azure NetApp-filer.](azure-netapp-files-service-levels.md)

### <a name="virtual-network-vnet-recommendations"></a>Rekommendationer för virtuellt nätverk (VNet)

Du bör utföra benchmark-testningen i samma virtuella nätverk som Azure NetApp Files. Exemplet nedan visar rekommendationen:

![Rekommendationer för virtuella nätverk](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installation av FIO

FIO finns i binärt format för både Linux och Windows. Följ avsnittet Binära paket i [FIO](https://github.com/axboe/fio) för att installera för valfri plattform.

## <a name="fio-examples-for-iops"></a>FIO-exempel för IOPS 

I FIO-exemplen i det här avsnittet används följande inställningar:
* VM-instansstorlek: D32s_v3
* Kapacitet pool servicenivå och storlek: Premium / 50 TiB
* Volymkvotstorlek: 48 TiB

Följande exempel visar FIO slumpmässiga läsningar och skrivningar.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k block storlek 100% slumpmässiga läser

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Utgång: 68k läsa IOPS visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k block storlek 100% slumpmässiga skriver

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Utgång: 73k skriva IOPS visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO-exempel för bandbredd

Exemplen i det här avsnittet visar FIO:s sekventiella läsningar och skrivningar.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k blockstorlek 100% sekventiella läsningar

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Utgång: 11,8 Gbit/s dataflöde visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k blockstorlek 100% sekventiella skrivningar

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Utgång: 12,2 Gbit/s dataflöde visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Volymmått

Prestandadata för Azure NetApp Files är tillgängliga via Azure Monitor-räknare. Räknarna är tillgängliga via Azure-portalen och REST API GET-begäranden. 

Du kan visa historiska data för följande information:
* Genomsnittlig läsfördröjning 
* Genomsnittlig skrivfördröjning 
* Läs IOPS (genomsnitt)
* Skriv IOPS (genomsnitt)
* Logiskt volymstorlek (medelvärde)
* Storlek för ögonblicksbilder av volym (medelvärde)

### <a name="using-azure-monitor"></a>Använda Azure Monitor 

Du kan komma åt Azure NetApp-filer räknare per volym från sidan Mått, som visas nedan:

![Azure Monitor-mått](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Du kan också skapa en instrumentpanel i Azure Monitor för Azure NetApp-filer genom att gå till sidan Mått, filtrera för NetApp och ange volymräknare av intresse: 

![Azure Monitor-instrumentpanel](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>API-åtkomst för Azure Monitor

Du kan komma åt Azure NetApp-filer räknare med hjälp av REST API-anrop. Se [Mått som stöds med Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) för räknare för kapacitetspooler och volymer.

I följande exempel visas en GET-URL för visning av logisk volymstorlek:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Nästa steg

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)