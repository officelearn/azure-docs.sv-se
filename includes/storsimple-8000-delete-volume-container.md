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
ms.openlocfilehash: e7f3f80c886f90a8bc3ae8c38e7d101c506439a6
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35250230"
---
Du måste ta bort en volymbehållare
 - ta bort volymer i volymbehållaren. Om volymbehållaren har associerade volymer, ta volymerna offline först. Följ stegen i [kopplar från en volym](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). När volymerna som är offline kan du ta bort dem. 
 - ta bort associerade principer för säkerhetskopiering och molnbaserade ögonblicksbilder. Kontrollera om volymbehållaren är kopplad till principer för säkerhetskopiering och molnögonblicksbilder. I så fall, sedan [ta bort principer för säkerhetskopiering](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Detta tar även bort ögonblicksbilderna molnet. 
 
När volymbehållaren har ingen associerad volymer, principer för säkerhetskopiering och molnögonblicksbilder, kan du ta bort den. Utför följande procedur för att ta bort en volymbehållare.

#### <a name="to-delete-a-volume-container"></a>Ta bort en volymbehållare
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Välj och klicka på enheten och gå sedan till **Inställningar > Hantera > volymbehållare**.

    ![Volymen behållare bladet](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Tabell listan över volymbehållare, Välj volymbehållare som du vill ta bort, högerklicka på **...**  och välj sedan **ta bort**.

    ![Ta bort volymbehållare](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Om en volymbehållare har inga associerade volymer, principer för säkerhetskopiering och molnögonblicksbilder, då kan tas bort. När du uppmanas att bekräfta, granska och markera kryssrutan om effekten av att ta bort volymbehållaren. Klicka på **ta bort** att ta bort volymbehållaren.

    ![Bekräfta borttagning](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Listan över volymbehållare uppdateras för att återspegla den borttagna volymbehållaren.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


