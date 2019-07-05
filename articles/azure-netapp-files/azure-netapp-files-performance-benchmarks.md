---
title: Prestandamått för Azure NetApp Files | Microsoft Docs
description: Beskriver resultaten från benchmark-testerna för prestanda för Azure NetApp filer på volymnivå.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449510"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Prestandamått för Azure NetApp Files

Den här artikeln beskrivs resultaten från benchmark-testerna för prestanda för Azure NetApp filer på volymnivå. 

## <a name="sample-application-used-for-the-tests"></a>Exempelprogrammet som används för testerna

Prestandatester kördes med ett exempelprogram som använder Azure NetApp-filer. Programmet har följande egenskaper: 

* Ett Linux-baserade program som skapats för molnet
* Kan skalas linjärt med har lagts till virtuella datorer (VM) för att öka beräkningskraft efter behov
* Kräver snabb tillgängligheten för data lake
* Har i/o-mönster som ibland är slumpmässig och ibland sekventiella 
    * Ett slumpmässigt mönster kräver låg fördröjning för stora mängder i/o. 
    * Ett sekventiellt mönster kräver stora mängder bandbredd. 

## <a name="about-the-workload-generator"></a>Om arbetsbelastningen generator

Resultatet kommer från Vdbench sammanfattning av filer. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) är ett kommandoradsverktyg som genererar diskens i/o-arbetsbelastningar för att verifiera lagringsprestanda. Konfigurationen för klient-server som används är skalbar.  Den innehåller en enda blandat master/klient och klient-14 dedikerade VM.

## <a name="about-the-tests"></a>Om testerna

Testerna har utformats för att identifiera de gränser som exempelprogrammet kan ha och svarstiden som kurvor upp till gränserna.  

Följande tester kördes: 

* 100% 8-KiB slumpmässiga Läs
* 100% 8 KiB slumpmässiga skrivåtgärder
* 100% 64-KiB sekventiella Läs
* 100% 64 KiB sekventiella skrivåtgärder
* 50% 64-KiB sekventiella Läs 50% 64 KiB sekventiella skrivåtgärder
* 50% 8-KiB slumpmässiga Läs 50% 8 KiB slumpmässiga skrivåtgärder

## <a name="bandwidth"></a>Bandbredd

NetApp-filer som Azure erbjuder flera [servicenivåer](azure-netapp-files-service-levels.md). Varje servicenivå erbjuder en annan mängd bandbredd per TiB etablerad kapacitet (kvot för samtalsvolym). Bandbreddsgräns för en volym etableras baserat på en kombination av servicenivån och kvot för samtalsvolym. Bandbreddsbegränsningen är bara en faktor för att fastställa den faktiska mängden dataflöde som kommer realiseras.  

4 500 MiB är för närvarande högsta dataflöde som har uppnåtts av en arbetsbelastning mot en enskild volym i testet.  En kvot för samtalsvolym av 70.31 TiB med önskad service Premium-nivå kommer att etablera tillräckligt med bandbredd för att använda den här dataflöde per beräkningen nedan: 

![Bandbredd formel](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvot- och tjänstnivå](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Dataflöde-intensiva arbetsbelastningar

Dataflöde testet används Vdbench och en kombination av 12xD32s V3 virtuella datorer. Exemplet volymen i testet uppnås följande dataflöde nummer:

![Testning av dataflöde](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-intensiva arbetsbelastningar

I/o-testet används Vdbench och en kombination av 12xD32s V3 virtuella datorer. Exemplet volymen i testet uppnås följande i/o-nummer:

![I/o-test](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Svarstid

Avståndet mellan testet virtuella datorer och Azure NetApp Files volymen påverkar ett i/o-prestanda.  I tabellen nedan jämförs IOPS jämfört med svarstid svar kurvor för två olika uppsättningar av virtuella datorer.  En uppsättning virtuella datorer som är nära Azure NetApp filer och den andra uppsättningen är mer direkt.  Ökad latens för ytterligare uppsättningen av virtuella datorer påverkar mängden IOPS uppnås på en viss nivå av parallellitet.  Oavsett kan läsning mot en volym överstiga 300 000 IOPS som på bilden nedan: 

![Svarstid-studien](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Sammanfattning

Känslig arbetsbelastningar (databaser) kan ha en en-millisekunds svarstid. Transaktionell prestanda kan vara än 300 kB IOPS för en enskild volym.

Dataflöde fördöjningskänsliga program (för direktuppspelning och avbildning) kan ha 4.5GiB / s genomströmning.

## <a name="example-scripts"></a>Exempelskript

Följande exempelskript är endast för demonstration.  De är inte ska användas för produktion.  

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
