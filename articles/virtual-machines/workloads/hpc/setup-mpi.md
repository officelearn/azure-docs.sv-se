---
title: Konfigurera Message Passing Interface för HPC - Azure-datorer | Microsoft Docs
description: Lär dig hur du ställer in MPI för HPC på Azure.
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
ms.openlocfilehash: 541e42a72ea604c4d71dc546b14dea2f0857bcc1
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797516"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurera Message Passing Interface för HPC

Arbetsbelastningar för Message Passing Interface (MPI) är en betydande del av traditionella HPC-arbetsbelastningar. SR-IOV aktiverade storlekar för Virtuella datorer på Azure Tillåt nästan vilken smak av MPI som ska användas. 

Kör MPI-jobb på virtuella datorer ska ställas in för partitionsnycklar (p-nycklar) mellan en klient. Följ stegen i den [identifiera partitionsnycklar](#discover-partition-keys) information om hur du bestämmer p-key-värden.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) ger bästa prestanda på IB och fungerar med MPICH och OpenMPI.

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

Skapa OpenMPI.

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

Kontrollera din partitionsnyckel som nämns ovan.

## <a name="mpich"></a>MPICH

Installera UCX enligt beskrivningen ovan.

Skapa MPICH.

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

Kontrollera din partitionsnyckel som nämns ovan.

## <a name="mvapich2"></a>MVAPICH2

Skapa MVAPICH2.

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

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Installera nödvändiga paket för MPI-plattformen.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Följ installationsprocessen.

## <a name="intel-mpi"></a>Intel MPI

[Ladda ned Intel MPI](https://software.intel.com/mpi-library/choose-download).

Ändra miljövariabeln I_MPI_FABRICS beroende på vilken version. Intel MPI 2018, använda `I_MPI_FABRICS=shm:ofa` och 2019, använda `I_MPI_FABRICS=shm:ofi`.

Processen fästa fungerar som 15, 30 och 60 PPN som standard.

## <a name="osu-mpi-benchmarks"></a>OSU MPI prestandamått

[Ladda ned OSU MPI prestandamått](http://mvapich.cse.ohio-state.edu/benchmarks/) och untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Skapa prestandamått som använder ett visst MPI-bibliotek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI prestandamått är `mpi/` mapp.


## <a name="discover-partition-keys"></a>Identifiera partitionsnycklar

Upptäck partitionsnycklar (p-nycklar) för att kommunicera med andra virtuella datorer i samma klient (Tillgänglighetsuppsättning eller VM-Skalningsuppsättning).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Det största värdet för två är klientnyckeln som ska användas med MPI. Exempel: Om följande är p-nycklar bör 0x800b användas tillsammans med MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Använd partition än standard (0x7fff) partitionsnyckel. UCX kräver MSB av p-nyckel som ska tas bort. Till exempel UCX_IB_PKEY som 0x000b för 0x800b.

Observera också att så länge klienten (AVSet eller VMSS) finns PKEYs förblir desamma. Detta gäller även när noderna är har lagts till/tas bort. Nya klienter få olika PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurera användarbegränsningar för MPI

Ställ in användarbegränsningar för MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Konfigurera SSH-nycklar för MPI

Konfigurera SSH-nycklar för MPI-typer som kräver detta.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Syntaxen ovan förutsätter en delad arbetskatalog, annat .ssh-katalogen måste kopieras till varje nod.

## <a name="next-steps"></a>Nästa steg

Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
