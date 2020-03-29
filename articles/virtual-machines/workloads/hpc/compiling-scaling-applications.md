---
title: Skalning av HPC-program – virtuella Azure-datorer | Microsoft-dokument
description: Lär dig hur du skalar HPC-program på virtuella Azure-datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707845"
---
# <a name="scaling-hpc-applications"></a>Skala HPC-program

Optimal skalning och utskalningsprestanda för HPC-program på Azure kräver prestandajusterings- och optimeringsexperiment för den specifika arbetsbelastningen. Det här avsnittet och de VM-seriespecifika sidorna ger allmän vägledning för skalning av dina program.

## <a name="compiling-applications"></a>Kompilera program

Även om det inte är nödvändigt, ger kompilering av program med lämpliga optimeringsflaggor den bästa uppskalningsprestandan på virtuella datorer i HB- och HC-serien.

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimerar C/C++ Kompilator

Kompilatorsystemet AMD Optimizeimizing C/C++ Kompilator (AOCC) erbjuder en hög nivå av avancerade optimeringar, flertrådsteknik och processorstöd som inkluderar global optimering, vektorisering, interproceduriska analyser, loopomvandlingar och kodgenerering. AOCC kompilator binärer är lämpliga för Linux-system med GNU C Library (glibc) version 2.17 och uppåt. Kompilatorsviten består av en C/C++-kompilator (klang), en Fortran-kompilator (FLANG) och en Fortran-front till Clang (Dragon Egg).

### <a name="clang"></a>Klang

Clang är en C-, C++- och Objective-C-kompilator som hanterar förbearbetning, tolkning, optimering, kodgenerering, sammansättning och länkning. Clang stöder `-march=znver1` flaggan för att möjliggöra bästa kodgenerering och justering för AMD:s Zen-baserade x86-arkitektur.

### <a name="flang"></a>FLÄNS

FLANG-kompilatorn är ett nytt tillägg till AOCC-sviten (tillagd april 2018) och är för närvarande i förhandsutgåva för utvecklare att ladda ner och testa. Baserat på Fortran 2008 utökar AMD GitHub-versionen av FLANG (https://github.com/flangcompiler/flang). FLANG-kompilatorn stöder alla Clang-kompilatoralternativ och ytterligare ett antal FLANG-specifika kompilatoralternativ.

### <a name="dragonegg"></a>Dragonegg (på väg mot en)

DragonEgg är en GCC plugin som ersätter GCC: s optimerare och generatorer kod med dem från LLVM projektet. DragonEgg som levereras med AOCC fungerar med gcc-4.8.x, har testats för x86-32/x86-64 mål och har framgångsrikt använts på olika Linux-plattformar.

GFortran är den faktiska frontend för Fortran program som ansvarar för förbearbetning, tolkning och semantisk analys genererar GCC GIMPLE mellanliggande representation (IR). DragonEgg är en GNU plugin, ansluter till GFortran sammanställning flöde. Den implementerar GNU plugin API. Med plugin arkitektur, Blir DragonEgg kompilatorn föraren, kör de olika faserna av sammanställning.  Efter att ha följt hämtnings- och installationsinstruktionerna kan Dragon Egg anropas med hjälp av: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Kompilator för SGB
SGB Community Edition ver. 17 har bekräftats att arbeta med AMD EPYC. En SGB-kompilerad version av STREAM ger hela minnets bandbredd på plattformen. Den nyare Community Edition 18.10 (nov 2018) bör också fungera bra. Nedan är exempel CLI att kompilator optimalt med Intel Kompilator:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel-kompilator
Intel Kompilator ver. 18 har bekräftats att arbeta med AMD EPYC. Nedan är exempel CLI att kompilator optimalt med Intel Kompilator.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-kompilator 
För HPC rekommenderar AMD GCC-kompilator 7.3 eller nyare. Äldre versioner, till exempel 4.8.5 som medföljer RHEL/CentOS 7.4, rekommenderas inte. GCC 7.3, och nyare, kommer att leverera betydligt högre prestanda på HPL, HPCG och DGEMM-tester.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Skala program 

Följande förslag gäller för optimal programskalningseffektivitet, prestanda och konsekvens:

* Fäst processer på kärnor 0-59 med hjälp av en sekventiell nålningsmetod (i motsats till en metod för automatisk balans). 
* Bindning av Numa/Core/HwThread är bättre än standardbindning.
* För hybridparallella applikationer (OpenMP+MPI) använder du 4 trådar och 1 MPI-rankning per CCX.
* För rena MPI-program, experimentera med 1-4 MPI-leden per CCX för optimal prestanda.
* Vissa program med extrem känslighet för minnesbandbredd kan dra nytta av att använda ett minskat antal kärnor per CCX. För dessa program kan 3 eller 2 kärnor per CCX minska konkurrens om minnesbandbredd och ge högre prestanda i verkligheten eller mer konsekvent skalbarhet. I synnerhet kan MPI Allreduce dra nytta av detta.
* För betydligt större skalningskörningar rekommenderas att UD- eller hybrid-RC+UD-transporter används. Många MPI-bibliotek/körningsbibliotek gör detta internt (till exempel UCX eller MVAPICH2). Kontrollera om det finns stora körningar i transportkonfigurationerna.

## <a name="next-steps"></a>Nästa steg

Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
