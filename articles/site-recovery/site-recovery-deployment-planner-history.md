---
title: Versionshistorik för Azure Site Recovery Deployment Planner
description: Kända olika site recovery deployment planner-versioner korrigeringar och kända begränsningar tillsammans med deras utgivningsdatum.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433409"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Versionshistorik för Azure Site Recovery Deployment Planner

Den här artikeln innehåller historik över alla versioner av Azure Site Recovery Deployment Planner tillsammans med korrigeringar, kända begränsningar i varje och deras utgivningsdatum.

## <a name="version-251"></a>Version 2.51

**Utgivningsdatum: 22 augusti 2019**

**Fixar:**

- Fixade kostnadsrekommendationsproblemet med Deployment Planner version 2.5

## <a name="version-25"></a>Version 2.5

**Utgivningsdatum: 29 juli 2019**

**Fixar:**

- För virtuella VMware-datorer och fysiska datorer uppdateras rekommendationen för att baseras på replikering till hanterade diskar.
- Lagt till stöd för Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 eller senare

## <a name="version-24"></a>Version 2.4

**Utgivningsdatum: 17 april 2019**

**Fixar:**

- Förbättrad kompatibilitet för operativsystemet, särskilt vid hantering av lokaliseringsbaserade fel.
- Lade till virtuella datorer med upp till 20 Mbit/s dataändringshastighet (omsättning) i kompatibilitetschecklistan.
- Förbättrade felmeddelanden
- Lagt till stöd för vCenter 6.7.
- Lade till stöd för Windows Server 2019 och Red Hat Enterprise Linux (RHEL) arbetsstation.



## <a name="version-23"></a>Version 2.3

**Utgivningsdatum: 3 december 2018**

**Fixar:**

- Åtgärdade ett problem som hindrade distributionsplaneraren från att generera en rapport med den angivna målplatsen och prenumerationen.

## <a name="version-22"></a>Version 2.2 

**Utgivningsdatum: 25 april 2018**

**Fixar:**

- GetVMList-åtgärder:
  - Åtgärdade ett problem som gjorde att GetVMList misslyckades om den angivna mappen inte finns. Den skapar nu antingen standardkatalogen eller skapar den katalog som anges i parametern outputfile.
  - Lade till mer detaljerade felorsaker för GetVMList.
- Lade till information om vm-typ som en kolumn i det kompatibla virtuella datorer-bladet i rapporten Deployment Planner.
- Hyper-V till Azure haveriberedskap:
  - Uteslutna virtuella datorer med delade virtuella hårddiskar och PassThrough-diskar från profilering. Startprofiling-åtgärden visar listan över uteslutna virtuella datorer i konsolen.
  - Lade till virtuella datorer med mer än 64 diskar i listan över inkompatibla virtuella datorer.
  - Uppdaterade komprimeringsfaktorn (initial replication) och delta replication (DR).
  - Lade till begränsat stöd för SMB-lagring.

## <a name="version-21"></a>Version 2.1

**Utgivningsdatum: 3 januari 2018**

**Fixar:**

- Artikeln uppdaterade Excel-rapporten.
- Fasta fel i GetThroughput-åtgärden.
- Lagt till alternativ för att begränsa antalet virtuella datorer till profil eller generera rapporten. Standardgränsen är 1 000 virtuella datorer.
- VMware till Azure haveriberedskap:
  - Åtgärdade ett problem med windows server 2016 VM går in i den inkompatibla tabellen. 
  - Uppdaterade kompatibilitetsmeddelanden för EFI-datorer (Extensible Firmware Interface) .
- Uppdaterade VMware till Azure och Hyper-V till Azure, VM-dataomsättningsgräns per virtuell dator. 
- Förbättrad tillförlitlighet för VM-listfilstolkar.

## <a name="version-201"></a>Version 2.0.1

**Utgivningsdatum: 7 december 2017**

**Fixar:**

- Lade till rekommendation för att optimera nätverksbandbredden.

## <a name="version-20"></a>Version 2.0

**Utgivningsdatum: 28 november 2017**

**Fixar:**

- Lade till stöd för Hyper-V till Azure-haveriberedskap.
- Lade till kostnadskalkylator.
- Lade till OS-versionskontroll för VMware i Azure disaster recovery för att avgöra om den virtuella datorn är kompatibel eller inkompatibel för skyddet. Verktyget använder os-versionssträngen som returneras av vCenter-servern för den virtuella datorn. Det är gästoperativsystemets version som användaren har valt när den virtuella datorn skapas i VMware.

**Kända begränsningar:**

- För Hyper-V till Azure-haveriberedskap stöds `,`virtuell `"` `[`dator `]`med namn som innehåller tecknen som: , , och ``` ` ``` stöds inte. Om den är profilerad misslyckas rapportgenereringen eller har ett felaktigt resultat.
- För VMware till Azure haveriberedskap stöds inte virtuell dator med namn som innehåller komma. Om profilen är, misslyckas rapportgenereringen eller har ett felaktigt resultat.

## <a name="version-131"></a>Version 1.3.1

**Utgivningsdatum: 19 juli 2017** 

**Fixar:**

- Lade till stöd för stora diskar (> 1 TB) i rapportgenerering. Nu kan du använda Deployment Planner för att planera replikering för virtuella datorer som har diskstorlekar som är större än 1 TB (upp till 4095 GB).
Läs mer om [stöd för stora diskar i Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Version 1.3

**Utgivningsdatum: 9 maj 2017**

**Fixar:**

- Lade till stöd för hanterad disk i rapportgenerering. Antalet virtuella datorer som kan placeras i ett enda lagringskonto beräknas baserat på om den hanterade disken har valts för redundans/testundans.

## <a name="version-12"></a>Version 1.2

**Utgivningsdatum: 7 april 2017**

**Fixar:**

- Lade till starttyp (BIOS eller EFI) söker efter varje virtuell dator för att avgöra om den virtuella datorn är kompatibel eller inkompatibel för skyddet.
- Lade till OS-typinformation för varje virtuell dator i de kompatibla virtuella datorerna och inkompatibla virtuella datorer kalkylblad.
- Lade till stöd för GetThroughput-åtgärden för microsoft Azure-regioner i USA och Kina.
- Lade till några fler nödvändiga kontroller för vCenter- och ESXi-Server.
- Fixade ett problem med felaktig rapport som genereras när språkinställningarna är inställda på icke-engelska.

## <a name="version-11"></a>Version 1.1

**Utgivningsdatum: 9 mars 2017**

**Fixar:**

- Åtgärdade ett problem som förhindrade profilering virtuella datorer när det finns två eller flera virtuella datorer med samma namn eller IP-adress över olika vCenter ESXi-värdar.
- Åtgärdade ett problem som gjorde att kopiering och sökning inaktiverades för kompatibla virtuella datorer och inkompatibla virtuella datorer kalkylblad.

## <a name="version-10"></a>Version 1.0

**Utgivningsdatum: 23 februari 2017**

**Kända begränsningar:**

- Stöder endast för VMware till Azure-scenarier för haveriberedskap. För Hyper-V till Azure-scenarier för haveriberedskap använder du [verktyget Hyper-V-kapacitetsplanerare](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Stöder inte GetThroughput-åtgärden för den amerikanska regeringen och Kina Microsoft Azure-regioner.
- Verktyget kan inte profilera virtuella datorer om vCenter-servern har två eller flera virtuella datorer med samma namn eller IP-adress över olika ESXi-värdar.
I den här versionen hoppar verktyget över profilering för dubbletter av namn på virtuella datorer eller IP-adresser i VMListFile. Lösningen är att profilera de virtuella datorerna med hjälp av en ESXi-värd i stället för vCenter-servern. Se till att köra en instans för varje ESXi-värd.
