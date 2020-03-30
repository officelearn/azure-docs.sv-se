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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472032"
---
Azure shared disks (preview) är en ny funktion för Azure-hanterade diskar som gör det möjligt att koppla en Azure-hanterad disk till flera virtuella datorer (VMs) samtidigt. Genom att koppla en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure.

## <a name="how-it-works"></a>Hur det fungerar

Virtuella datorer i klustret kan läsa eller skriva till den bifogade disken baserat på den reservation som valts av det klustrade programmet med [SCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR är en välkänd branschstandard som utnyttjas av program som körs på SAN (Storage Area Network) lokalt. Om du aktiverar SCSI PR på en hanterad disk kan du migrera dessa program till Azure som de är.

Hanterade diskar med delade diskar aktiverade erbjuder delad blocklagring som kan nås av flera virtuella datorer, detta exponeras som logiska enhetsnummer (LUN). LUN presenteras sedan för en initierare (VM) från ett mål (disk). Dessa LUN ser ut som direktansluten lagring (DAS) eller en lokal enhet till den virtuella datorn.

Hanterade diskar med aktiverade delade diskar erbjuder inte inbyggt ett fullständigt hanterat filsystem som kan nås med SMB/NFS. Du måste använda en klusterhanterare, till exempel WSFC (Windows Server Failover Cluster) eller Pacemaker, som hanterar kommunikation mellan klusternoder samt skrivlåsning.

## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Diskstorlekar

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Exempel på arbetsbelastningar

### <a name="windows"></a>Windows

De flesta Windows-baserade kluster bygger på WSFC, som hanterar all kärninfrastruktur för klusternodkommunikation, vilket gör att dina program kan dra nytta av parallella åtkomstmönster. WSFC aktiverar både CSV- och icke-CSV-baserade alternativ beroende på vilken version av Windows Server du har. Mer information finns i [Skapa ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Några populära program som körs på WSFC är:

- SQL Server Redundansklusterinstanser (FCI)
- Skala ut filserver (SoFS)
- Filserver för allmänt bruk (IW-arbetsbelastning)
- Användarprofildiskett för fjärrskrivbordsserver (UPD för fjärrskrivbord)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux-kluster kan utnyttja klusterhanterare som [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker bygger på [Corosync](http://corosync.github.io/corosync/), aktivera klusterkommunikation för program som distribueras i miljöer med högtillgänge. Några vanliga klustrade filsystem inkluderar [ocfs2](https://oss.oracle.com/projects/ocfs2/) och [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Du kan ändra reservationer och registreringar med verktyg som [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) och [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Beständigt reservationsflöde

Följande diagram illustrerar ett exempel på 2-nod klustrade databasprogram som utnyttjar SCSI PR för att aktivera redundans från en nod till en annan.

![Två nodkluster. Ett program som körs på klustret hanterar åtkomst till disken](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Flödet är följande:

1. Det klustrade programmet som körs på både Azure VM1 och VM2 registrerar sin avsikt att läsa eller skriva till disken.
1. Programinstansen på VM1 tar sedan exklusiv reservation för att skriva till disken.
1. Den här reservationen tillämpas på din Azure-disk och databasen kan nu endast skriva till disken. Alla skrivningar från programinstansen på VM2 kommer inte att lyckas.
1. Om programinstansen på VM1 går ned kan instansen på VM2 nu initiera en databasundangång och överta disken.
1. Den här reservationen tillämpas nu på Azure-disken och disken accepterar inte längre skrivningar från VM1. Det kommer bara att acceptera skrivningar från VM2.
1. Det klustrade programmet kan slutföra databasen redundans och hantera begäranden från VM2.

Följande diagram illustrerar en annan gemensam klustrad arbetsbelastning som består av flera noder som läser data från disken för att köra parallella processer, till exempel utbildning av maskininlärningsmodeller.

![Fyra nod VM-kluster, varje nod registrerar avsikt att skriva, programmet tar exklusiv reservation för att korrekt hantera skrivresultat](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Flödet är följande:

1. Det klustrade programmet som körs på alla virtuella datorer registrerar avsikten att läsa eller skriva till disken.
1. Programinstansen på VM1 tar en exklusiv reservation för att skriva till disken medan du öppnar läsningar till disken från andra virtuella datorer.
1. Den här reservationen tillämpas på din Azure-disk.
1. Alla noder i klustret kan nu läsa från disken. Endast en nod skriver tillbaka resultat till disken, på uppdrag av alla noder i klustret.