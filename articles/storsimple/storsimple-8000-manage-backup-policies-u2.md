---
title: Hantera principer för StorSimple 8000-serien säkerhetskopiering | Microsoft Docs
description: Beskriver hur du kan använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera manuella säkerhetskopieringar, scheman för säkerhetskopiering och lagring av säkerhetskopior på en enhet för StorSimple 8000-serien.
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
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874873"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Använda tjänsten StorSimple Enhetshanteraren i Azure-portalen för att hantera principer för säkerhetskopiering


## <a name="overview"></a>Översikt

Den här självstudiekursen beskrivs hur du använder tjänsten StorSimple Enhetshanteraren **säkerhetskopiera princip** bladet för att styra processer för säkerhetskopiering och lagring av säkerhetskopior för din StorSimple-volymer. Det beskriver också hur du utför en manuell säkerhetskopiering.

När du säkerhetskopierar en volym kan du skapa en lokal ögonblicksbild eller en ögonblicksbild i molnet. Om du säkerhetskopierar en lokalt Fäst volym rekommenderar vi att du anger en ögonblicksbild i molnet. Med ett stort antal lokala ögonblicksbilder av en lokalt Fäst volym tillsammans med en datauppsättning som har mycket omsättning resulterar i en situation där du snabbt kan köra out-of-lokalt utrymme. Om du väljer att ta lokala ögonblicksbilder, rekommenderar vi att du ta färre dagliga ögonblicksbilder för att säkerhetskopiera tillståndet senaste behålla dem under en dag och ta bort dem.

När du tar en ögonblicksbild i molnet för en lokalt Fäst volym måste kopiera du endast de ändrade data till molnet, där det är deduplicerad och komprimeras.

## <a name="the-backup-policy-blade"></a>Principbladet säkerhetskopiering

Den **säkerhetskopiera princip** bladet för din StorSimple-enhet kan du hantera principer för säkerhetskopiering och schemalägga lokala och molnbaserade ögonblicksbilder. Principer för säkerhetskopiering används för att konfigurera scheman för säkerhetskopiering och lagring av säkerhetskopior för en samling av volymer. Principer för säkerhetskopiering gör att du kan ta en ögonblicksbild av flera volymer samtidigt. Det innebär att säkerhetskopiorna som skapats av en princip för säkerhetskopiering är kraschkonsekvent kopior.

Principer för säkerhetskopiering tabular lista kan du filtrera befintliga principer för säkerhetskopiering av en eller flera av följande fält:

* **Principnamn** – namnet som associeras med principen. Olika typer av principer är:

  * Schemalagda principer som uttryckligen har skapats av användaren.
  * Importerade principer som ursprungligen skapades i StorSimple Snapshot Manager. Dessa har en tagg som beskriver StorSimple Snapshot Manager värden som principerna som har importerats från.

  > [!NOTE]
  > Automatisk eller standard principer för säkerhetskopiering är inte längre aktiverade på volymen skapades.

* **Senaste lyckade säkerhetskopiering** – datum och tid för senaste lyckade säkerhetskopian som skapades med den här principen.

* **Nästa säkerhetskopiering** – datum och tid för nästa schemalagda säkerhetskopian som initieras av den här principen.

* **Volymer** – de volymer som är associerade med principen. Alla volymer som är associerade med en princip för säkerhetskopiering är grupperade när säkerhetskopieringar skapas.

* **Scheman** – antalet scheman som är associerade med principen för säkerhetskopiering.

Vanliga åtgärder som du kan utföra för principer för säkerhetskopiering är:

* Lägga till en säkerhetskopieringspolicy
* Lägg till eller ändra ett schema
* Lägg till eller ta bort en volym
* Ta bort en princip för säkerhetskopiering
* Gör en manuell säkerhetskopia

## <a name="add-a-backup-policy"></a>Lägga till en säkerhetskopieringspolicy

Lägga till en princip för säkerhetskopiering för att automatiskt schemalägga säkerhetskopiorna. När du skapar en volym, det finns ingen standardprincip för säkerhetskopiering som är kopplad till volymen. Du måste lägga till och tilldela en säkerhetskopieringsprincip för att skydda volymdata.

Utför följande steg i Azure portal för att lägga till en princip för säkerhetskopiering för din StorSimple-enhet. När du lägger till principen, kan du definiera ett schema (se [Lägg till eller ändra ett schema](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Lägg till eller ändra ett schema

Du kan lägga till eller ändra ett schema som är kopplad till en säkerhetskopieringsprincip på StorSimple-enheten. Utför följande steg i Azure portal för att lägga till eller ändra ett schema.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Lägg till eller ta bort en volym

Du kan lägga till eller ta bort en volym som tilldelats till en princip för säkerhetskopiering på StorSimple-enheten. Utför följande steg i Azure portal för att lägga till eller ta bort en volym.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering

Utför följande steg i Azure portal för att ta bort en princip för säkerhetskopiering på StorSimple-enheten.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Gör en manuell säkerhetskopia

Utför följande steg i Azure portal för att skapa en säkerhetskopiering på begäran (manuellt) för en enskild volym.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

