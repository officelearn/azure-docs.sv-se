---
title: Konfigurera gränssnitt för meddelandepassning för HPC – virtuella Azure-datorer | Microsoft-dokument
description: Lär dig hur du konfigurerar MPI för HPC på Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023998"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurera gränssnitt för meddelandepassning för HPC

MPI-arbetsbelastningar (Message Passing Interface) är en betydande del av traditionella HPC-arbetsbelastningar. SR-IOV-aktiverade VM-storlekar på Azure gör att nästan alla smaker av MPI kan användas. 

För att köra MPI-jobb på virtuella datorer måste partitionsnycklar (p-nycklar) konfigureras över en klient. Följ stegen i avsnittet [Identifiera partitionsnycklar](#discover-partition-keys) för mer information om hur du fastställer p-nyckelvärdena.

## <a name="ucx"></a>UCX (på andra sätt)

[UCX](https://github.com/openucx/ucx) erbjuder bästa prestanda på IB och arbetar med MPICH och OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>Openmpi

Installera UCX som tidigare beskrivits.

```bash
sudo yum install –y openmpi
```

Bygg OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Kör OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Kontrollera din partitionsnyckel som nämnts ovan.

## <a name="mpich"></a>Mpich

Installera UCX som tidigare beskrivits.

Bygg MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Kör MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Kontrollera din partitionsnyckel som nämnts ovan.

## <a name="mvapich2"></a>MVAPICH2 (MVAPICH2)

Bygg MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Kör MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Plattform MPI Community Edition

Installera nödvändiga paket för Plattform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Följ installationsprocessen.

## <a name="intel-mpi"></a>Intel MPI

[Ladda ner Intel MPI](https://software.intel.com/mpi-library/choose-download).

Ändra I_MPI_FABRICS miljövariabeln beroende på version. Använd `I_MPI_FABRICS=shm:ofa` och för 2019 för Intel MPI `I_MPI_FABRICS=shm:ofi`2018.

Processnålning fungerar korrekt för 15, 30 och 60 PPN som standard.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-riktmärken

[Ladda ner OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) och untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Skapa riktmärken med ett visst MPI-bibliotek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI Benchmarks `mpi/` är under mapp.


## <a name="discover-partition-keys"></a>Upptäck partitionsnycklar

Upptäck partitionsnycklar (p-nycklar) för kommunikation med andra virtuella datorer inom samma klient (tillgänglighetsuppsättning eller VM-skaluppsättning).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Den större av de två är klientnyckeln som ska användas med MPI. Exempel: Om följande är p-tangenterna ska 0x800b användas med MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Använd partitionen förutom standardpartitionen (0x7fff). UCX kräver att MSB av p-nyckel ska rensas. Ange till exempel UCX_IB_PKEY som 0x000b för 0x800b.

Observera också att så länge klienten (AVSet eller VMSS) finns förblir PKEYs desamma. Detta gäller även när noder läggs till/tas bort. Nya hyresgäster får olika PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Ställ in användargränser för MPI

Ställ in användargränser för MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Konfigurera SSH-nycklar för MPI

Konfigurera SSH-nycklar för MPI-typer som kräver det.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Syntaxen ovan förutsätter att en delad arbetskatalog, annars måste .ssh-katalogen kopieras till varje nod.

## <a name="next-steps"></a>Nästa steg

Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
