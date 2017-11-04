---
title: "Hantera din StorSimple-säkerhetskopieringsprinciper | Microsoft Docs"
description: "Beskriver hur du kan använda StorSimple Manager-tjänsten för att skapa och hantera manuella säkerhetskopieringar, scheman för säkerhetskopiering och lagring av säkerhetskopior."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 4a2db707-bbfc-425c-bfeb-bc5c97781873
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos
ms.openlocfilehash: 5448247428ab96887470c6b53f7a9b3dcd9238f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Använda StorSimple Manager-tjänsten för att hantera principer för säkerhetskopiering (uppdatering 2)
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du använder StorSimple Manager-tjänsten **Säkerhetskopieringsprinciper** sidan för att styra processer för säkerhetskopiering och lagring av säkerhetskopior för din StorSimple-volymer. Det beskriver också hur du utför en manuell säkerhetskopiering.

När du säkerhetskopierar en volym kan du skapa en lokal ögonblicksbild eller en ögonblicksbild i molnet. Om du säkerhetskopierar en lokalt Fäst volym rekommenderar vi att du anger en ögonblicksbild i molnet. Med ett stort antal lokala ögonblicksbilder av en lokalt Fäst volym tillsammans med en datauppsättning som har mycket omsättning resulterar i en situation där du snabbt kan köra out-of-lokalt utrymme. Om du väljer att ta lokala ögonblicksbilder, rekommenderar vi att du ta färre dagliga ögonblicksbilder för att säkerhetskopiera tillståndet senaste behålla dem under en dag och ta bort dem.

När du tar en ögonblicksbild i molnet för en lokalt Fäst volym måste kopiera du endast de ändrade data till molnet, där det är deduplicerad och komprimeras. 

## <a name="the-backup-policies-page"></a>Sidan för principer för säkerhetskopiering
Den **Säkerhetskopieringsprinciper** sidan kan du hantera principer för säkerhetskopiering och schemalägga lokala och molnbaserade ögonblicksbilder. (Säkerhetskopieringsprinciper används för att konfigurera scheman för säkerhetskopiering och lagring av säkerhetskopior för en samling av volymer.) Principer för säkerhetskopiering gör att du kan ta en ögonblicksbild av flera volymer samtidigt. Det innebär att säkerhetskopiorna som skapats av en princip för säkerhetskopiering är kraschkonsekvent kopior. Den **Säkerhetskopieringsprinciper** sidan listar principer för säkerhetskopiering, deras typer, associerade volymer, antal säkerhetskopior bevaras och att aktivera dessa principer.

Den **Säkerhetskopieringsprinciper** sidan kan du filtrera befintliga principer för säkerhetskopiering av en eller flera av följande fält:

* **Principnamn** – namnet som associeras med principen. Olika typer av principer är:
  
  * Schemalagda principer som uttryckligen har skapats av användaren.
  * Automatisk principer som skapas när standard säkerhetskopieringen för det här alternativet om volymen har aktiverats på volymen skapades. Dessa principer är namngivna som *VolumeName*_standardsökmappen där *VolumeName* refererar till namnet på StorSimple-volym som konfigurerats av användaren i den klassiska Azure-portalen. Automatisk principerna resultera i dagliga molnögonblicksbilder början på 22:30 tid.
  * Importerade principer som ursprungligen skapades i StorSimple Snapshot Manager. Dessa har en tagg som beskriver StorSimple Snapshot Manager värden som principerna som har importerats från.
* **Volymer** – de volymer som är associerade med principen. Alla volymer som är associerade med en princip för säkerhetskopiering är grupperade när säkerhetskopieringar skapas.
* **Senaste lyckade säkerhetskopiering** – datum och tid för senaste lyckade säkerhetskopian som skapades med den här principen.
* **Nästa säkerhetskopiering** – datum och tid för nästa schemalagda säkerhetskopian som initieras av den här principen.
* **Scheman** – antalet scheman som är associerade med principen för säkerhetskopiering.

Vanliga åtgärder som du kan utföra från den här sidan är:

* Lägga till en säkerhetskopieringspolicy 
* Lägg till eller ändra ett schema 
* Ta bort en princip för säkerhetskopiering 
* Gör en manuell säkerhetskopia 
* Skapa en anpassad princip för säkerhetskopiering med flera volymer och scheman 

## <a name="add-a-backup-policy"></a>Lägga till en säkerhetskopieringspolicy
Lägga till en princip för säkerhetskopiering för att automatiskt schemalägga säkerhetskopiorna. Utför följande steg i den klassiska Azure portalen för att lägga till en princip för säkerhetskopiering för din StorSimple-enhet. När du lägger till principen, kan du definiera ett schema (se [Lägg till eller ändra ett schema](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Video tillgänglig](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du skapar en lokal eller molnet säkerhetskopieringsprincip, klickar du på [här](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## <a name="add-or-modify-a-schedule"></a>Lägg till eller ändra ett schema
Du kan lägga till eller ändra ett schema som är kopplad till en säkerhetskopieringsprincip på StorSimple-enheten. Utför följande steg i den klassiska Azure portalen för att lägga till eller ändra ett schema.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering
Utför följande steg i den klassiska Azure portalen för att ta bort en princip för säkerhetskopiering på StorSimple-enheten.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Gör en manuell säkerhetskopia
Utför följande steg i den klassiska Azure portalen för att skapa en (manuell) säkerhetskopiering på begäran för en enskild volym.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Skapa en anpassad princip för säkerhetskopiering med flera volymer och scheman
Utför följande steg i den klassiska Azure portalen för att skapa en anpassad princip för säkerhetskopiering som har flera volymer och scheman.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

