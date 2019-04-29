---
title: ta med fil
description: ta med fil
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724790"
---
Om du vill ta bort en volymbehållare, måste du
 - ta bort volymer i volymbehållaren. Om volymcontainern har tillhörande volymer, koppla från volymerna först. Följ stegen i [koppla från en volym](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). När volymerna som är offline kan du ta bort dem. 
 - ta bort tillhörande säkerhetskopieringsprinciper och molnbaserade ögonblicksbilder. Kontrollera om volymcontainern har associerade principer för säkerhetskopiering och ögonblicksbilder av molnet. I så fall, sedan [ta bort principer för säkerhetskopiering](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Detta tar även bort ögonblicksbilder av molnet. 
 
När volymcontainern har inga associerade volymer, säkerhetskopieringsprinciper och ögonblicksbilder av molnet, kan du ta bort den. Utför följande procedur för att ta bort en volymbehållare.

#### <a name="to-delete-a-volume-container"></a>Att ta bort en volymbehållare
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Markera och klicka på enheten och gå sedan till **Inställningar > Hantera > volymbehållare**.

    ![Volymen behållare bladet](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Från listan med volymbehållare, väljer du den volymbehållare som du vill ta bort, högerklicka på **...**  och välj sedan **ta bort**.

    ![Ta bort volymcontainern](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Om en volymbehållare har inga associerade volymer, säkerhetskopieringsprinciper och ögonblicksbilder av molnet, kan det raderas. När du uppmanas att bekräfta granska och markera kryssrutan om effekten av att ta bort volymbehållaren. Klicka på **ta bort** att ta bort volymbehållaren.

    ![Bekräfta borttagning](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Listan över volymbehållare uppdateras för att återspegla har tagits bort volymbehållaren.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


