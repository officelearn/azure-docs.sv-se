---
title: Skala HPC - program i Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du skalar HPC-program på Azure Virtual Machines.
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
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707845"
---
# <a name="scaling-hpc-applications"></a>Skala HPC-program

Skala upp och skala ut optimalt av HPC-program på Azure kräver prestandajustering och optimering experimenten för den specifika arbetsbelastningen. Det här avsnittet och VM-serien-specifika sidor erbjuder allmänna riktlinjer för att skala dina program.

## <a name="compiling-applications"></a>Kompilera program

Även om det är inte nödvändigt, innehåller kompilera program med lämpliga optimering flaggor bästa möjliga prestanda i skala upp om HB och HC-seriens virtuella datorer.

### <a name="amd-optimizing-cc-compiler"></a>AMD optimera C /C++ kompilatorn

AMD optimera C /C++ kompilatorn (AOCC) kompilatorn system erbjuder en hög nivå av avancerade optimeringar, flertrådsteknik, och processorstöd som omfattar globala optimering, vectorization, kommunikation mellan procedurmässig analyser, loop omvandlingar, och kodgenerering. AOCC kompilatorn binärfiler är lämpliga för Linux-system med GNU C-bibliotek (glibc) version 2.17 och senare. Kompilatorn suite består av ett C /C++ kompilator (clang), en Fortran kompilator (FLANG) och en klientdel för Fortran till Clang (Dragon ägg).

### <a name="clang"></a>Clang

Clang är en C C++, och hantering av Förbearbeta, parsning, optimering, kodgenerering, sammansättningen och länka Objective-C-kompilatorn. Clang stöder den `-march=znver1` flagga för att aktivera bästa code generation och justering för AMD: s Zen baserat x86 arkitektur.

### <a name="flang"></a>FLANG

FLANG-kompilatorn är ett nytt tillägg till AOCC suite (läggs till April 2018) och är för närvarande i förhandsversionen för utvecklare för att hämta och testa. Utifrån Fortran 2008 AMD utökar GitHub-versionen av FLANG (https://github.com/flangcompiler/flang). Kompilatorn FLANG stöder alla Clang kompilatoralternativ och ytterligare ett antal alternativ för FLANG-specifika-kompilatorn.

### <a name="dragonegg"></a>DragonEgg

DragonEgg är ett plugin-program i gcc som ersätter GCCS optimerare och kodgeneratorer med de från LLVM-projektet. DragonEgg som medföljer AOCC fungerar med gcc-4.8.x har testats för x86-32/x86-64-mål och har har använts på olika Linux-plattformar.

GFortran är faktiska klientdelen för Fortran program som är ansvarig för parsning, prognosmodellering och semantiska analysen genererar GCC GIMPLE mellanliggande representation (IR). DragonEgg är ett GNU-plugin-program, ansluta till GFortran kompilering flöde. Den implementerar GNU plugin-programmet API. Med plugin-programmet-arkitektur blir DragonEgg kompilatorn-drivrutinen och uppnå de olika faserna för kompilering.  När du har följt anvisningarna hämtningen och installationen kan du anropa Dragon ägg via: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI Compiler
SGB Community Edition ver. 17 bekräftas för att arbeta med AMD EPYC. En SGB-kompilerad version av STREAM levererar fullständig minnesbandbredd i plattformen. Nyare Community Edition 18.10 (november 2018) bör på samma sätt fungera bra. Nedan visas i exemplet CLI för att kompilatorn optimalt med Intel-kompilatorn:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Intel-kompilatorn ver. 18 bekräftas för att arbeta med AMD EPYC. Nedan visas i exemplet CLI för att kompilatorn optimalt med Intel-kompilatorn.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC Compiler 
För HPC rekommenderar AMD GCC-kompilatorn 7.3 eller senare. Äldre versioner, till exempel 4.8.5 som ingår i RHEL/CentOS 7.4 rekommenderas inte. GCC 7.3 och nyare, levererar avsevärt högre prestanda på HPL och HPCG DGEMM tester.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Skala program 

Följande rekommendationer gäller för optimal skalning effektivitet, prestanda och konsekvens-program:

* PIN-kod bearbetar till kärnor 0-59 med hjälp av en sekventiell fästa itu med (i motsats till en metod för automatisk saldo). 
* Bindning av Numa/kärnor/HwThread är bättre än standard-bindning.
* För hybrid parallella program (OpenMP + MPI) kan du använda 4 trådar och 1 MPI rangordning per CCX.
* Experimentera med 1 – 4 MPI rangordnar per CCX för optimala prestanda för ren MPI-program.
* Vissa program med extrem känslighet till minnesbandbredd kan dra nytta av ett lägre antal kärnor per CCX. För dessa program kan med 3 eller 2 kärnor per CCX minska minne bandbredd konkurrensen och ger högre verkliga prestanda och mer konsekvent skalbarhet. I synnerhet MPI Allreduce kan ha nytta av detta.
* För betydligt större skala körningar rekommenderas att använda UD eller hybrid RC + UD transporter. Många bibliotek för MPI-bibliotek/körning göra det internt (till exempel en UCX eller MVAPICH2). Kontrollera dina transport-konfigurationer för storskaliga körningar.

## <a name="next-steps"></a>Nästa steg

Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
