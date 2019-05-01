---
title: Versionshistorik för Azure Site Recovery Deployment Planner
description: Kända olika Site Recovery Deployment Planner versioner korrigeringar och kända begränsningar tillsammans med utgivningsdatum.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927379"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Versionshistorik för Azure Site Recovery Deployment Planner

Den här artikeln innehåller historik över alla versioner av Distributionshanteraren för Azure Site Recovery och de korrigeringar som kända begränsningar i varje och utgivningsdatum.

## <a name="version-24"></a>Version 2.4

**Utgivningsdatum: 17 april 2019**

**Korrigeringar:**

- Bättre Systemkompatibilitet för operativsystem, särskilt vid hantering av lokalisering-baserade fel.
- Har lagts till virtuella datorer med upp till 20 Mbit/s data ändra frekvensen (omsättningen) till checklista för kompatibilitet.
- Förbättrade felmeddelanden
  - Tillagt stöd för vCenter 6.7.
  - Tillagt stöd för Windows Server 2019 och Red Hat Enterprise Linux (RHEL) arbetsstation.



## <a name="version-23"></a>Version 2.3

**Utgivningsdatum: 3 december 2018**

**Korrigeringar:**

- Ett problem som förhindrade att Distributionshanteraren genererar rapporter med den angivna målplatsen och prenumerationen har åtgärdats.

## <a name="version-22"></a>Version 2.2 

**Utgivningsdatum: Den 25 april 2018**

**Korrigeringar:**

- GetVMList-åtgärder:
  - Ett problem som orsakade GetVMList misslyckas om den angivna mappen inte finns har åtgärdats. Den nu antingen skapar standardkatalogen eller skapar den katalog som anges i parametern utdatafil.
  - Lägga till mer detaljerad orsaker till Återställningsfel för GetVMList.
- Har lagt till information på grund av VM-typ som en kolumn i listan med kompatibla virtuella datorer i Distributionshanteraren för rapporten.
- Hyper-V till Azure-haveriberedskap:
  - Exkluderade virtuella datorer med delade virtuella hårddiskar och genomströmning diskar från profilering. Åtgärden Startprofiling visar listan över undantagna virtuella datorer i konsolen.
  - Har lagts till virtuella datorer med fler än 64 diskar i listan över inkompatibla virtuella datorer.
  - Uppdaterat den initiala replikeringen (IR) och delta (DR) komprimering replikeringsfaktor.
  - Tillagt begränsat stöd för SMB-lagring.

## <a name="version-21"></a>Version 2.1

**Utgivningsdatum: Den 3 januari 2018**

**Korrigeringar:**

- Uppdatera Excel-rapporten.
- Åtgärdat buggar i GetThroughput-åtgärden.
- Har lagts till med alternativet för att begränsa antalet virtuella datorer att profilera eller generera rapporten. Standardgränsen är 1 000 virtuella datorer.
- VMware till Azure-haveriberedskap:
  - Ett problem i Windows Server 2016-dator går in i tabellen inkompatibla har åtgärdats. 
  - Uppdaterat avfärdas Extensible Firmware Interface (EFI) Windows virtuella datorer.
- Uppdaterat med VMware till Azure och Hyper-V till Azure VM dataomsättning gränsen per virtuell dator. 
- Förbättrad tillförlitlighet för VM-lista fil-parsning.

## <a name="version-201"></a>Version 2.0.1

**Utgivningsdatum: 7 december 2017**

**Korrigeringar:**

- Rekommendationen har har lagts till för att optimera nätverkets bandbredd.

## <a name="version-20"></a>Version 2.0

**Utgivningsdatum: Den 28 november 2017**

**Korrigeringar:**

- Tillagt stöd för Hyper-V till Azure-haveriberedskap.
- Kalkylator för extra kostnad.
- Kontroll har lagts till OS-version för VMware till Azure-haveriberedskap att avgöra om den virtuella datorn är kompatibel eller inkompatibel för skyddet. Verktyget använder strängen för OS-version som returneras av vCenter-servern för den virtuella datorn. Det är gäst Operativsystemversionen som användaren har valt när du skapar den virtuella datorn i VMware.

**Kända begränsningar:**

- För Hyper-V till Azure-haveriberedskap, virtuell dator med namn som innehåller tecken som: `,`, `"`, `[`, `]`, och ``` ` ``` stöds inte. Om profilerats rapportgenerering misslyckas eller har ett felaktigt värde.
- Virtuella datorer med namn som innehåller kommatecken stöds inte för VMware till Azure-haveriberedskap. Om profileras, rapportera generation misslyckas eller har ett felaktigt värde.

## <a name="version-131"></a>Version 1.3.1

**Utgivningsdatum: 19 juli 2017** 

**Korrigeringar:**

- Tillagt stöd för stora diskar (> 1 TB) i rapportgenerering. Nu kan du använda Distributionshanteraren för att planera replikering av virtuella datorer som har diskar som är större än 1 TB (upp till 4 095 GB).
Läs mer om [stöd för stora diskar i Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Version 1.3

**Utgivningsdatum: Den 9 maj 2017**

**Korrigeringar:**

- Stöd har lagts till för hanterad disk i rapportgenerering. Hur många virtuella datorer som kan placeras på ett enda lagringskonto beräknas utifrån om den hanterade disken har valts för redundans/Redundanstest.

## <a name="version-12"></a>Version 1.2

**Utgivningsdatum: 7 april 2017**

**Korrigeringar:**

- Har lagts till Start-typ (BIOS eller EFI) söker efter varje virtuell dator för att avgöra om den virtuella datorn är kompatibel eller inkompatibel för skyddet.
- Har lagts till OS ange information för varje virtuell dator i kompatibla virtuella datorer och inkompatibla virtuella datorer-kalkylblad.
- Stöd har lagts till för åtgärden GetThroughput för US Government och Kina Microsoft Azure-regioner.
- Lade till några fler nödvändiga kontroller för vCenter- och ESXi-Server.
- Ett problem med felaktig rapporten komma genererades när nationella inställningar är inställda på icke-engelska har åtgärdats.

## <a name="version-11"></a>Version 1.1

**Utgivningsdatum: 9 mars 2017**

**Korrigeringar:**

- Åtgärdat ett problem som gjorde att profilera virtuella datorer när det finns två eller flera virtuella datorer med samma namn eller IP-adress hos olika ESXi-värdar för vCenter.
- Ett problem som orsakade kopiering och sökning är inaktiverad för kompatibla virtuella datorer och inkompatibla virtuella datorer-kalkylblad har åtgärdats.

## <a name="version-10"></a>Version 1.0

**Utgivningsdatum: 23 februari 2017**

**Kända begränsningar:**

- Stöder endast för VMware till Azure för katastrofåterställning. Hyper-V till Azure för katastrofåterställning, använda den [kapacitetsplaneringsverktyget för Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Stöder inte åtgärden GetThroughput för US Government och Kina Microsoft Azure-regioner.
- Verktyget SourceExpr profil virtuella datorer om vCenter-servern har två eller flera virtuella datorer med samma namn eller IP-adress hos olika ESXi-värdar.
I den här versionen hoppar verktyget över profilering för dubbletter av namn på virtuella datorer eller IP-adresser i VMListFile. Lösningen är att profilera de virtuella datorerna med hjälp av en ESXi-värd i stället för vCenter-servern. Se till att köra en instans för varje ESXi-värd.
