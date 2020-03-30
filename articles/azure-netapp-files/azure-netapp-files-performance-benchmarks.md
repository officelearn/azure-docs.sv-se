---
title: Testresultat för prestandariktmärken för Azure NetApp-filer | Microsoft-dokument
description: Beskriver resultaten av prestandariktmärken för Azure NetApp-filer på volymnivå.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881742"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Resultat från benchmark-prestandatest för Azure NetApp Files

I den här artikeln beskrivs resultaten av prestandariktmärken för Azure NetApp-filer på volymnivå. 

## <a name="sample-application-used-for-the-tests"></a>Exempel på program som används för testerna

Prestandatester kördes med ett exempelprogram med Azure NetApp Files. Ansökan har följande egenskaper: 

* Ett Linux-baserat program byggt för molnet
* Kan skala linjärt med tillagda virtuella datorer (VMs) för att öka beräkningskraften efter behov
* Kräver snabb tillgänglighet av datasjön
* Har I/O-mönster som ibland är slumpmässiga och ibland sekventiella 
    * Ett slumpmässigt mönster kräver låg latens för stora mängder I/O. 
    * Ett sekventiellt mönster kräver stora mängder bandbredd. 

## <a name="about-the-workload-generator"></a>Om arbetsbelastningsgeneratorn

Resultaten kommer från Vdbench sammanfattning filer. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) är ett kommandoradsverktyg som genererar disk-I/O-arbetsbelastningar för att validera lagringsprestanda. Klientserverkonfigurationen som används är skalbar.  Den innehåller en enda blandad master / klient och 14 dedikerade klient virtuella datorer.

## <a name="about-the-tests"></a>Om testerna

Testerna har utformats för att identifiera de gränser som exempelprogrammet kan ha och svarstiden som kurvor upp till gränserna.  

Följande tester kördes: 

* 100% 8-KiB slumpmässigt läsa
* 100% 8-KiB slumpmässig skriva
* 100% 64-KiB sekventiell läsa
* 100% 64-KiB sekventiell skriva
* 50% 64-KiB sekventiell läsa, 50% 64-KiB sekventiell skriva
* 50% 8-KiB slumpmässigt läsa, 50% 8-KiB slumpmässiga skriva

## <a name="bandwidth"></a>Bandbredd

Azure NetApp Files erbjuder flera [tjänstnivåer](azure-netapp-files-service-levels.md). Varje servicenivå erbjuder olika bandbredd per TiB av etablerad kapacitet (volymkvot). Bandbreddsgränsen för en volym etableras baserat på kombinationen av servicenivån och volymkvoten. Bandbreddsgränsen är bara en faktor för att bestämma den faktiska mängden dataflöde som ska realiseras.  

För närvarande är 4 500 MiB det högsta dataflödet som har uppnåtts genom en arbetsbelastning mot en enda volym i testningen.  Med Premium-servicenivån kommer en volymkvot på 70,31 TiB att ge tillräckligt med bandbredd för att realisera detta dataflöde per beräkningen nedan: 

![Formel för bandbredd](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvot- och servicenivå](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Dataflödesintensiva arbetsbelastningar

Genomströmstestet använde Vdbench och en kombination av 12xD32s V3-lagrings-virtuella datorer. Provvolymen i testet uppnådde följande dataflödesnummer:

![Test av dataflöde](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-intensiva arbetsbelastningar

I/O-testet använde Vdbench och en kombination av 12xD32s V3-lagrings-virtuella datorer. Provvolymen i testet uppnådde följande I/O-nummer:

![I/O-test](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Svarstid

Avståndet mellan test-virtuella datorer och Azure NetApp Files-volymen påverkar I/O-prestanda.  Diagrammet nedan jämför IOPS kontra svarssvarskurvor för två olika uppsättningar virtuella datorer.  En uppsättning virtuella datorer är nära Azure NetApp-filer och den andra uppsättningen är längre bort.  Den ökade latensen för den ytterligare uppsättningen virtuella datorer påverkar mängden IOPS som uppnås på en viss parallellnivå.  Oavsett, läser mot en volym kan överstiga 300.000 IOPS som visas nedan: 

![Latens studie](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Sammanfattning

Svarstidskänsliga arbetsbelastningar (databaser) kan ha en svarstid med en millisekund. Transaktionsprestanda kan vara över 300 k IOPS för en enskild volym.

Dataflödeskänsliga program (för direktuppspelning och bildbehandling) kan ha 4.5GiB/s dataflöde.

## <a name="example-scripts"></a>Exempel på skript

Följande exempelskript är endast avsedda för demonstration.  De får inte användas för produktionsändamål.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
