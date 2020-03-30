---
title: Hantera principer för säkerhetskopiering av StorSimple 8000-serien | Microsoft-dokument
description: I artikeln beskrivs hur du kan använda Tjänsten StorSimple Device Manager för att skapa och hantera manuella säkerhetskopieringar, säkerhetskopieringsscheman och lagring av säkerhetskopiering på en StorSimple 8000-serieenhet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267799"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Använda Tjänsten StorSimple Device Manager i Azure-portalen för att hantera principer för säkerhetskopiering


## <a name="overview"></a>Översikt

Den här självstudien förklarar hur du använder **principbladet Säkerhetskopiering av** Tjänsten StorSimple-tjänsten Backup för att styra säkerhetskopieringsprocesser och lagring av säkerhetskopiering för dina StorSimple-volymer. Den beskriver också hur du slutför en manuell säkerhetskopiering.

När du säkerhetskopierar en volym kan du välja att skapa en lokal ögonblicksbild eller en ögonblicksbild i molnet. Om du säkerhetskopierar en lokalt fäst volym rekommenderar vi att du anger en ögonblicksbild i molnet. Om du tar ett stort antal lokala ögonblicksbilder av en lokalt fäst volym i kombination med en datauppsättning som har mycket omsättning kommer det att resultera i en situation där du snabbt kan få på lokalt utrymme. Om du väljer att ta lokala ögonblicksbilder rekommenderar vi att du tar färre dagliga ögonblicksbilder för att säkerhetskopiera det senaste tillståndet, behålla dem för en dag och sedan ta bort dem.

När du tar en ögonblicksbild av molnet av en lokalt fäst volym kopierar du bara de ändrade data till molnet, där dedupliceras och komprimeras.

## <a name="the-backup-policy-blade"></a>Bladet Säkerhetskopieringsprincip

**Med principbladet Säkerhetskopiering** för Din StorSimple-enhet kan du hantera principer för säkerhetskopiering och schemalägga lokala ögonblicksbilder och ögonblicksbilder i molnet. Principer för säkerhetskopiering används för att konfigurera säkerhetskopieringsscheman och lagring av säkerhetskopiering för en samling volymer. Med principer för säkerhetskopiering kan du ta en ögonblicksbild av flera volymer samtidigt. Det innebär att de säkerhetskopior som skapas av en säkerhetskopieringsprincip kommer att vara kraschkonsekventa kopior.

Med tabelllistan för principer för säkerhetskopieringsprinciper kan du också filtrera de befintliga principerna för säkerhetskopiering efter ett eller flera av följande fält:

* **Principnamn** – Namnet som är associerat med principen. De olika typerna av principer är:

  * Schemalagda principer, som uttryckligen skapas av användaren.
  * Importerade principer som ursprungligen skapades i StorSimple Snapshot Manager. Dessa har en tagg som beskriver StorSimple Snapshot Manager-värden som principerna importerades från.

  > [!NOTE]
  > Automatiska principer för säkerhetskopiering eller standardsäkerhetskopiering är inte längre aktiverade när volymen skapas.

* **Senast lyckad säkerhetskopiering** – Datum och tid för den senaste lyckade säkerhetskopieringen som togs med den här principen.

* **Nästa säkerhetskopia** – Datum och tid för nästa schemalagda säkerhetskopiering som initieras av den här principen.

* **Volymer** – De volymer som är associerade med principen. Alla volymer som är associerade med en säkerhetskopieringsprincip grupperas tillsammans när säkerhetskopieringar skapas.

* **Scheman** – Antalet scheman som är associerade med säkerhetskopieringsprincipen.

De åtgärder som ofta används som du kan utföra för principer för säkerhetskopiering är:

* Lägga till en säkerhetskopieringspolicy
* Lägga till eller ändra ett schema
* Lägga till eller ta bort en volym
* Ta bort en princip för säkerhetskopiering
* Gör en manuell säkerhetskopiering

## <a name="add-a-backup-policy"></a>Lägga till en säkerhetskopieringspolicy

Lägg till en princip för säkerhetskopiering för att automatiskt schemalägga dina säkerhetskopior. När du först skapar en volym finns det ingen standardprincip för säkerhetskopiering som är associerad med volymen. Du måste lägga till och tilldela en princip för säkerhetskopiering för att skydda volymdata.

Utför följande steg i Azure-portalen för att lägga till en princip för säkerhetskopiering för din StorSimple-enhet. När du har lagt till principen kan du definiera ett schema (se [Lägga till eller ändra ett schema](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Lägga till eller ändra ett schema

Du kan lägga till eller ändra ett schema som är kopplat till en befintlig principer för säkerhetskopiering på Din StorSimple-enhet. Utför följande steg i Azure-portalen för att lägga till eller ändra ett schema.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Lägga till eller ta bort en volym

Du kan lägga till eller ta bort en volym som tilldelats en principer för säkerhetskopiering på StorSimple-enheten. Utför följande steg i Azure-portalen för att lägga till eller ta bort en volym.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering

Utför följande steg i Azure-portalen för att ta bort en princip för säkerhetskopiering på din StorSimple-enhet.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Gör en manuell säkerhetskopiering

Utför följande steg i Azure-portalen för att skapa en säkerhetskopiering på begäran (manuell) för en enskild volym.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

