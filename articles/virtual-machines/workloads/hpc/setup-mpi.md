---
title: Konfigurera gränssnitt för meddelande överföring för HPC-Azure-Virtual Machines | Microsoft Docs
description: Lär dig hur du konfigurerar MPI för HPC i Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023998"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurera Message Passing Interface för HPC

MPI-arbetsbelastningar (Message Passing Interface) är en viktig del av traditionella HPC-arbetsbelastningar. SR-IOV-aktiverade VM-storlekar på Azure tillåter nästan vilken variant av MPI som ska användas. 

Att köra MPI-jobb på virtuella datorer kräver att du konfigurerar partitionsnyckel (p-Keys) över en klient. Följ stegen i avsnittet [identifiera partitionerings nycklar](#discover-partition-keys) om du vill ha mer information om att fastställa p-nyckel värden.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) erbjuder bästa prestanda i IB och fungerar med mpich och OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Installera UCX enligt beskrivningen ovan.

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

Kontrol lera partitionsnyckel som nämnts ovan.

## <a name="mpich"></a>MPICH

Installera UCX enligt beskrivningen ovan.

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

Kontrol lera partitionsnyckel som nämnts ovan.

## <a name="mvapich2"></a>MVAPICH2

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

Installera nödvändiga paket för plattformen MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Följ installations processen.

## <a name="intel-mpi"></a>Intel-MPI

[Hämta Intel-MPI](https://software.intel.com/mpi-library/choose-download).

Ändra I_MPI_FABRICS-miljövariabeln beroende på version. För Intel MPI 2018 använder `I_MPI_FABRICS=shm:ofa` du och för 2019. `I_MPI_FABRICS=shm:ofi`

Processen att fästa fungerar korrekt för 15, 30 och 60 PPN som standard.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-prestandatest

[Ladda ned Osu MPI-benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) och untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Bygg benchmarks med ett visst MPI-bibliotek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-benchmarks finns `mpi/` i mappen.


## <a name="discover-partition-keys"></a>Identifiera partitionerings nycklar

Identifiera partitionsalternativ (p-Keys) för att kommunicera med andra virtuella datorer inom samma klient organisation (tillgänglighets uppsättning eller VM Scale set).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Den större av de två är den klient nyckel som ska användas med MPI. Exempel: om följande är p-Keys ska 0x800b användas med MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Använd en annan partition än standard-0x7FFF (partition Key). UCX kräver att MSB för p-nyckeln har rensats. Ange till exempel UCX_IB_PKEY som 0x000B för 0x800b.

Observera också att PKEYs förblir samma så länge klient organisationen (AVSet eller VMSS) finns. Detta gäller även när noder läggs till/tas bort. Nya klienter får olika PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurera användar begränsningar för MPI

Konfigurera användar begränsningar för MPI.

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

Ovanstående syntax förutsätter en delad arbets katalog, annars måste du kopiera SSH-katalogen till varje nod.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
