---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472032"
---
Azure Shared disks (för hands version) är en ny funktion för Azure Managed disks som möjliggör anslutning av en Azure-hanterad disk till flera virtuella datorer samtidigt. Genom att ansluta en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure.

## <a name="how-it-works"></a>Hur det fungerar

Virtuella datorer i klustret kan läsa eller skriva till din anslutna disk baserat på den reservation som valts av det klustrade programmet med [SCSI-beständiga reservationer](https://www.t10.org/members/w_spc3.htm) (SCSI-PR). SCSI PR är en välkänd bransch standard som utnyttjas av program som körs på lokala SAN-nätverk (Storage Area Network). Genom att aktivera SCSI PR på en hanterad disk kan du migrera dessa program till Azure som de är.

Hanterade diskar med delade diskar aktiverade erbjuder delad block lagring som kan nås av flera virtuella datorer, detta visas som logiska enhets nummer (LUN). LUN visas sedan för en initierare (VM) från ett mål (disk). Dessa LUN ser ut som direktansluten lagring (DAS) eller en lokal enhet till den virtuella datorn.

Hanterade diskar med aktiverade delade diskar erbjuder inget fullständigt hanterat fil system som kan nås via SMB/NFS. Du måste använda en kluster hanterare, t. ex. Windows Server failover Cluster (WSFC) eller pacemaker, som hanterar kommunikation mellan kluster noder och skriv låsning.

## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Disk storlekar

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Exempel arbets belastningar

### <a name="windows"></a>Windows

De flesta Windows-baserade klustring bygger på WSFC, som hanterar all kärn infrastruktur för kommunikation i kluster noder, vilket gör att dina program kan dra nytta av parallella åtkomst mönster. WSFC aktiverar både CSV-och icke-CSV-baserade alternativ beroende på din version av Windows Server. Mer information finns i [skapa ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Några populära program som körs på WSFC inkluderar:

- SQL Server instanser av WSFC-kluster (FCI)
- Skalbar fil server (SoFS)
- Fil server för allmänt bruk (IW-arbetsbelastning)
- Användar profil disk för fjärr skrivbords server (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux-kluster kan utnyttja kluster hanterare som [pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker bygger på [corosync](http://corosync.github.io/corosync/), vilket möjliggör kluster kommunikation för program som distribueras i miljöer med hög tillgänglighet. Några vanliga klustrade fil system är [ocfs2](https://oss.oracle.com/projects/ocfs2/) och [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Du kan ändra reservationer och registreringar med hjälp av verktyg som [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) och [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Persistent reservation Flow

Följande diagram illustrerar ett exempel på klustrade databas program i två noder som utnyttjar SCSI PR för att aktivera redundans från en nod till en annan.

![Kluster med två noder. Ett program som körs i klustret hanterar åtkomst till disken](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Flödet är följande:

1. Det klustrade programmet som körs på både Azure VM1 och VM2 registrerar sin avsikt att läsa eller skriva till disken.
1. Program instansen på VM1 tar sedan exklusiv reservation att skriva till disken.
1. Den här reservationen tillämpas på din Azure-disk och databasen kan nu endast skriva till disken. Det går inte att utföra alla skrivningar från program instansen på VM2.
1. Om program instansen på VM1 kraschar kan instansen på VM2 nu initiera en databas växling vid fel och ta över disken.
1. Den här reservationen tillämpas nu på Azure-disken och disken tar inte längre emot skrivningar från VM1. Det tar bara emot skrivningar från VM2.
1. Det klustrade programmet kan slutföra redundansväxlingen av databasen och betjäna förfrågningar från VM2.

Följande diagram illustrerar en annan vanlig klustrad arbets belastning som består av flera noder som läser data från disken för att köra parallella processer, till exempel utbildning av Machine Learning-modeller.

![Fyra noder för virtuella datorer, varje nod registrerar sig för att skriva, programmet tar exklusiv reservation för att hantera Skriv resultaten korrekt](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Flödet är följande:

1. Det klustrade programmet som körs på alla virtuella datorer registrerar avsikten att läsa eller skriva till disken.
1. Program instansen på VM1 tar en exklusiv reservation att skriva till disken medan läsning av läsningar till disken från andra virtuella datorer öppnas.
1. Den här reservationen tillämpas på Azure-disken.
1. Alla noder i klustret kan nu läsa från disken. Endast en nod skriver tillbaka resultat till disken på uppdrag av alla noder i klustret.