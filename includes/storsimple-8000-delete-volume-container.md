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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187390"
---
Om du vill ta bort en volymbehållare måste du
 - ta bort volymer i volymbehållaren. Om volymbehållaren har associerade volymer tar du dessa volymer offline först. Följ stegen i [Koppla från volymen](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). När volymerna är offline kan du ta bort dem. 
 - ta bort associerade principer för säkerhetskopiering och ögonblicksbilder av molnet. Kontrollera om volymbehållaren har associerade principer för säkerhetskopiering och ögonblicksbilder av molnet. Om så är fallet tar du [bort principerna för säkerhetskopiering](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Detta tar också bort ögonblicksbilder av molnet. 
 
När volymbehållaren inte har några associerade volymer, principer för säkerhetskopiering och ögonblicksbilder i molnet kan du ta bort den. Utför följande procedur för att ta bort en volymbehållare.

#### <a name="to-delete-a-volume-container"></a>Så här tar du bort en volymbehållare
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Markera och klicka på enheten och gå sedan till **Inställningar > Hantera > Volymbehållare**.

    ![Bladet volymbehållare](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Markera den volymbehållare som du vill ta bort i listan över volymbehållare, högerklicka **...** och välj sedan **Ta bort**.

    ![Ta bort volymbehållare](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Om en volymbehållare inte har några associerade volymer, principer för säkerhetskopiering och ögonblicksbilder i molnet kan den tas bort. När du uppmanas att bekräfta, granska och markera kryssrutan som anger effekten av att ta bort volymbehållaren. Klicka på **Ta bort** om du vill ta bort volymbehållaren.

    ![Bekräfta borttagning](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Listan över volymbehållare uppdateras för att återspegla den borttagna volymbehållaren.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


